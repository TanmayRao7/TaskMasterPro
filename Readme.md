# CI/CD and Kubernetes Pipeline Implementation for Task Master Pro Application

This project demonstrates the implementation of CI/CD practices with Kubernetes deployment using **Task Master Pro**, a simple to-do application. The project integrates several tools for CI/CD, security scanning, and deployment automation, ensuring secure and efficient development workflows.

## Tools & Technologies Used

- **CI/CD Tool**: Jenkins
- **Security Tools**: 
  - Trivy (Filesystem & Image Scanning)
  - SonarQube (Code Quality & Security Analysis)
- **Deployment**: Docker, Kubernetes (Minikube)
- **Version Control System**: Git and GitHub
- **Build Tool**: Maven
- **Artifact Management**: Nexus & Docker

## CI/CD Pipeline with Jenkins

The project utilizes a Jenkins pipeline to automate build, test, and deployment processes. It integrates security tools to ensure that the application is deployed with high quality and minimal security risks.

![image](https://github.com/user-attachments/assets/3d8fd4cb-adb2-4502-a789-ff6c0f5ac9fa)

### Key Pipeline Stages:
1. **Build & Test**: Jenkins automates builds using Maven, with unit testing.
2. **Code Quality & Security Analysis**: SonarQube is integrated to check for code quality issues, bugs, and potential security vulnerabilities.
3. **Container Scanning**: Trivy performs filesystem and image scans to detect vulnerabilities in dependencies and container images.
4. **Artifact Management**: Nexus is used to manage the build artifacts (JAR/WAR files), while Docker manages container images.
5. **Kubernetes Deployment**: The app is containerized with Docker and deployed on Kubernetes (Minikube). The `kubectl port forward` command is used to expose the application locally.

## Security Tools Integration

### SonarQube Analysis
SonarQube is integrated into the Jenkins pipeline to perform static code analysis. It checks for code smells, bugs, and potential security vulnerabilities.

![image](https://github.com/user-attachments/assets/86ffb4c3-94f5-4e3d-b553-6eb1cfeb06a2)

### Trivy File System & Image Scans
Trivy performs filesystem scans to detect security issues in the project's dependencies. Additionally, image scanning ensures that Docker images are free from vulnerabilities before deployment.

<img width="714" alt="image" src="https://github.com/user-attachments/assets/8e04180e-200c-48b5-ba96-cd27433c2346">


### Nexus Artifact Management
The project uses Nexus for managing build artifacts. Artifacts such as WAR or JAR files are stored in Nexus and versioned for tracking deployment progress.

![image](https://github.com/user-attachments/assets/6df30b81-a028-4469-acad-7cc0a509b767)

### Docker Images
Docker is used to containerize the Task Master Pro application. The images are built, scanned using Trivy, and then pushed to a container registry before deployment in Kubernetes.

![image](https://github.com/user-attachments/assets/3762dbac-cb10-4614-8478-699143b8ed7d)

## Kubernetes Deployment

The application is deployed on Kubernetes using Minikube for local development. Kubernetes manifests, including deployment and service files, manage the containerized application's lifecycle. After deployment, the app can be accessed locally using the `kubectl port forward` command.

![image](https://github.com/user-attachments/assets/4e763bda-c344-472f-af28-ebc0f2d30171)

## Application Demo

A fully functional demo of the Task Master Pro application is included, showcasing the final deployment and the integration of security tools. The application is accessed locally after deployment on Minikube.

![image](https://github.com/user-attachments/assets/c28d580e-546c-4ddc-9f19-0c565a690ff3)

![image](https://github.com/user-attachments/assets/50e31994-65bc-477d-b2b5-e176bb0dfb17)

![image](https://github.com/user-attachments/assets/b761dd7c-1eac-4e91-8240-854890b9e00d)

``` pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }
    
    environment {
        TRIVY_HOME="/<PATH>/trivy/0.53.0/bin"
        DOCKER_HOME="/<PATH>/docker/25.0.3/bin"
        SCANNER_HOME= tool name: 'sonar-scanner'
        DOCKER_CREDENTIALS_ID='docker-hub'
        DOCKER_PATH="/<PATH>//bin"
        KUBECTL_PATH="/<PATH>/bin/"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'NewGitCreds', poll: false, url: 'https://github.com/TanmayRao7/JenkinsCode'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Trivy FS Scan') {
            steps {
                sh '${TRIVY_HOME}/trivy fs --format table -o fs-report.html .'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=TaskMaster \
                    -Dsonar.projectName=TaskMaster \
                    -Dsonar.java.binaries=target'''
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publish to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'settings', jdk: '', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy'    
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '${DOCKER_PATH}/docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD https://index.docker.io/v1/'
                    }
                }
            }
        }
        stage('Build and Tag Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '${DOCKER_PATH}/docker build -t tanmayrao7/taskmaster:latest .'
                    }
                }
            }
        }
        stage('Scan Docker Image') {
            steps {
                sh '${TRIVY_HOME}/trivy image --format table -o image-report.html tanmayrao7/taskmaster:latest'
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '${DOCKER_PATH}/docker push tanmayrao7/taskmaster:latest'
                    }
                }
            }
        }
        stage('Deploy To K8s') {
            steps {
                sh '${KUBECTL_PATH}/kubectl apply -f k8s.yaml'
                sh '${KUBECTL_PATH}/kubectl get all'
            }
        }
    }
}
