# CI/CD Pipeline Implementation for Task Master Pro Application

This project demonstrates the implementation of CI/CD practices using **Task Master Pro**, a simple to-do application. The project integrates several tools for CI/CD, security scanning, and deployment automation, ensuring secure and efficient development workflows.

## Tools & Technologies Used

- **CI/CD Tool**: Jenkins
- **Security Tools**: 
  - Trivy (Filesystem & Image Scanning)
  - SonarQube (Code Quality & Security Analysis)
- **Deployment**: Docker, Kubernetes (Minikube)
- **Version Control System**: Git and GitHub
- **Build Tool**: Maven

## CI/CD Pipeline with Jenkins

The project utilizes a Jenkins pipeline to automate build, test, and deployment processes. It integrates security and monitoring tools to ensure that the application is deployed with high quality and minimal security risks.

### Key Pipeline Stages:
1. **Build & Test**: Jenkins automates builds using Maven, with unit testing.
2. **Code Quality & Security Analysis**: SonarQube is integrated to check for code quality issues, bugs, and potential security vulnerabilities.
3. **Container Scanning**: Trivy performs filesystem and image scans to detect vulnerabilities in dependencies and container images.
4. **Deployment**: The app is containerized with Docker and deployed using Kubernetes (Minikube). The `kubectl port forward` command is used to expose the application locally.

## Security Tools Integration

### SonarQube Analysis
SonarQube is integrated into the Jenkins pipeline to perform static code analysis. It checks for code smells, bugs, and potential security vulnerabilities.

### Trivy File System & Image Scans
Trivy performs filesystem scans to detect security issues in the project's dependencies. Additionally, image scanning ensures that Docker images are free from vulnerabilities before deployment.

## Application Demo

A fully functional demo of the Task Master Pro application is included, showcasing the final deployment and the integration of security tools. The application is accessed locally using the `kubectl port forward` command after deployment on Minikube.
