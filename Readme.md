DevSecOps Project - Task Master Pro Application
This project showcases the implementation of DevSecOps practices using Task Master Pro, a simple to-do application. The project integrates several tools for CI/CD, security scanning, and deployment automation, ensuring secure and efficient development workflows.

Tools & Technologies Used
CI/CD Tool: Jenkins
Security Tools:
Trivy (Filesystem & Image Scanning)
SonarQube (Code Quality & Security Analysis)
OWASP Dependency Check (Vulnerability Detection)
Deployment: Docker, Kubernetes (Minikube)
Version Control System: Git and GitHub
Build Tool: Maven
CI/CD Pipeline with Jenkins
The project utilizes a Jenkins pipeline to automate build, test, and deployment processes. It integrates security and monitoring tools to ensure that the application is deployed with high quality and minimal security risks.

Key Pipeline Stages:
Build & Test: Jenkins automates builds using Maven, with unit testing.
Code Quality & Security Analysis: SonarQube is integrated to check for code quality issues, bugs, and potential security vulnerabilities.
Container Scanning: Trivy performs filesystem and image scans to detect vulnerabilities in dependencies and container images.
Vulnerability Detection: OWASP Dependency Check ensures that the application dependencies are free from known vulnerabilities.
Deployment: The app is containerized with Docker and deployed using Kubernetes (Minikube). The kubectl port forward command is used to expose the application locally.
Security Tools Integration
SonarQube Analysis
SonarQube is integrated into the Jenkins pipeline to perform static code analysis. It checks for code smells, bugs, and potential security vulnerabilities.

Trivy File System & Image Scans
Trivy performs filesystem scans to detect security issues in the project's dependencies. Additionally, image scanning ensures that Docker images are free from vulnerabilities before deployment.

OWASP Dependency Check
OWASP Dependency Check is used to analyze the project dependencies for known vulnerabilities. It integrates into the pipeline to halt deployment if critical vulnerabilities are found.

Application Demo
A fully functional demo of the Task Master Pro application is included, showcasing the final deployment and the integration of security tools. The application is accessed locally using the kubectl port forward command after deployment on Minikube.
