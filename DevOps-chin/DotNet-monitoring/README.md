# DevSecOps .NET Monitoring Application

## 1. Project Overview

This project is a **DevSecOps CI/CD pipeline for a .NET Monitoring Application**.

The main purpose of this project is to automate the process of taking application source code from GitHub, checking its quality and security, creating a Docker image, and deploying the application using Kubernetes.

The project uses:

* GitHub for source code management
* Jenkins for CI/CD automation
* .NET 6 for the application
* SonarQube for code quality analysis
* Trivy for security scanning
* OWASP Dependency-Check for dependency scanning
* Docker for containerization
* Docker Hub for storing the Docker image
* Kubernetes for application deployment
* AWS EC2 for the infrastructure

---

## 2. Project Objective

The objective of this project is to create an automated DevSecOps pipeline where security and code quality checks are included in the deployment process.

Instead of manually building and deploying the application, Jenkins performs the main steps automatically.

The pipeline performs:

```text
Developer Code
      ↓
GitHub
      ↓
Jenkins
      ↓
SonarQube Code Analysis
      ↓
Quality Gate
      ↓
Trivy File Scan
      ↓
OWASP Dependency Check
      ↓
Docker Image Build
      ↓
Trivy Docker Image Scan
      ↓
Docker Hub
      ↓
Docker Container
      ↓
Kubernetes
      ↓
.NET Monitoring Application
```

---

# 3. Technologies Used

| Technology             | Purpose                           |
| ---------------------- | --------------------------------- |
| AWS EC2                | Cloud infrastructure              |
| Ubuntu Linux           | Operating system                  |
| Git                    | Version control                   |
| GitHub                 | Source code repository            |
| Jenkins                | CI/CD automation                  |
| Java 21                | Jenkins runtime                   |
| .NET 6                 | Application platform              |
| SonarQube              | Code quality analysis             |
| SonarScanner           | Sends code to SonarQube           |
| Trivy                  | Security vulnerability scanning   |
| OWASP Dependency-Check | Dependency vulnerability scanning |
| Docker                 | Containerization                  |
| Docker Hub             | Docker image registry             |
| Kubernetes             | Application deployment            |
| kubectl                | Kubernetes command-line tool      |

---

# 4. AWS Infrastructure

The project uses **3 AWS EC2 instances**.

| Instance          | Type        | Purpose                  |
| ----------------- | ----------- | ------------------------ |
| `dev_ops_project` | `t2.large`  | Main DevSecOps server    |
| `k8s-master`      | `t2.medium` | Kubernetes control plane |
| `k8s-worker`      | `t2.medium` | Kubernetes worker node   |

### Main DevSecOps Server

The `dev_ops_project` instance is used for the main DevOps tools:

* Jenkins
* Docker
* SonarQube
* Trivy
* OWASP Dependency-Check
* CI/CD pipeline

### Kubernetes Control Plane

The `k8s-master` instance is used as the Kubernetes control-plane node.

It manages the Kubernetes cluster and controls the application deployment.

### Kubernetes Worker Node

The `k8s-worker` instance is used as the Kubernetes worker node.

Application workloads are scheduled on the worker node.

---

# 5. AWS Architecture

```text
                         AWS
                          |
          +---------------+---------------+
          |               |               |
          ↓               ↓               ↓
   dev_ops_project    k8s-master      k8s-worker
      t2.large         t2.medium       t2.medium
          |               |               |
          |               |               |
       Jenkins       Kubernetes      Application
       Docker        Control Plane   Workloads
       SonarQube
       Trivy
       OWASP
          |
          ↓
      CI/CD Pipeline
          |
          ↓
      Docker Image
          |
          ↓
      Docker Hub
          |
          ↓
      Kubernetes
          |
          ↓
   .NET Application
```

---

# 6. Application Overview

The application is a **.NET Monitoring Web Application**.

It provides a web interface and monitoring-related features.

The application contains pages and APIs for monitoring information.

One of the monitoring endpoints is:

```text
/api/monitor
```

This endpoint provides information such as:

* CPU usage
* Application working set / memory information

The application runs on port:

```text
5000
```

The Docker container also exposes port `5000`.

---

# 7. Project Structure

The important project structure is:

```text
DevSecOps-DotNet-Monitoring/
│
├── Jenkinsfile
│
└── DevOps-chin/
    └── DotNet-monitoring/
        │
        ├── K8S/
        │   └── deployment.yaml
        │
        ├── Properties/
        │   └── launchSettings.json
        │
        ├── build/
        │   ├── Dockerfile
        │   └── installation-script.sh
        │
        ├── makefile
        │
        ├── src/
        │   ├── Program.cs
        │   ├── ApiHelper.cs
        │   ├── dotnet-demoapp.csproj
        │   ├── appsettings.json
        │   ├── Pages/
        │   └── wwwroot/
        │
        └── tests/
            ├── UnitTest.cs
            ├── postman_collection.json
            └── tests.csproj
```

---

# 8. GitHub

GitHub is used to store the application source code and project configuration files.

The Jenkins pipeline checks out the `main` branch from the repository.

Repository:

```text
https://github.com/chinmaymadgounkar12-stack/DevSecOps-DotNet-Monitoring
```

The important DevOps files include:

```text
Jenkinsfile
Dockerfile
deployment.yaml
```

---

# 9. Jenkins CI/CD Pipeline

Jenkins is used to automate the CI/CD process.

The pipeline contains the following stages:

```text
Clean Workspace
       ↓
Checkout From Git
       ↓
SonarQube Analysis
       ↓
Quality Gate
       ↓
Trivy File Scan
       ↓
OWASP Dependency Check
       ↓
Docker Build & Tag
       ↓
Trivy Docker Image Scan
       ↓
Docker Push
       ↓
Deploy to Container
       ↓
Deploy to Kubernetes
```

---

# 10. Jenkins Pipeline Stages

## Stage 1 - Clean Workspace

Jenkins first cleans the old workspace.

```groovy
cleanWs()
```

This helps avoid problems caused by old files from previous builds.

---

## Stage 2 - Checkout From Git

Jenkins downloads the source code from GitHub.

The pipeline uses the `main` branch.

```groovy
git branch: 'main',
    url: 'https://github.com/chinmaymadgounkar12-stack/DevSecOps-DotNet-Monitoring.git'
```

---

## Stage 3 - SonarQube Analysis

SonarQube is used to check the source code for code quality issues.

The project is configured as:

```text
Project Name: Dotnet-Webapp
Project Key: Dotnet-Webapp
```

The Jenkins pipeline uses the SonarQube scanner to perform the analysis.

---

## Stage 4 - Quality Gate

After the SonarQube analysis, Jenkins waits for the Quality Gate result.

The Quality Gate helps check the result of the code quality analysis.

The pipeline uses:

```text
Sonar-token
```

for the configured SonarQube credentials.

---

## Stage 5 - Trivy File Scan

Trivy scans the project files for known security vulnerabilities.

The pipeline runs:

```bash
trivy fs .
```

The result is saved as:

```text
trivy-fs_report.txt
```

---

## Stage 6 - OWASP Dependency Check

OWASP Dependency-Check is used to check project dependencies for known vulnerabilities.

The generated report is published in Jenkins.

The configured Jenkins installation name is:

```text
DP-Check
```

---

## Stage 7 - Docker Build

After the security checks, Jenkins builds the Docker image.

The Dockerfile is located at:

```text
DevOps-chin/DotNet-monitoring/build/Dockerfile
```

The Docker image name is:

```text
chunmay123/dotnet-monitoring:latest
```

---

## Stage 8 - Trivy Docker Image Scan

After creating the Docker image, Trivy scans the image for vulnerabilities.

Command:

```bash
trivy image chunmay123/dotnet-monitoring:latest
```

The result is saved as:

```text
trivy.txt
```

---

## Stage 9 - Docker Push

After the Docker image is built and scanned, Jenkins pushes the image to Docker Hub.

Image:

```text
chunmay123/dotnet-monitoring:latest
```

Docker Hub credentials are stored securely in Jenkins.

The Jenkins credential ID is:

```text
dockerhub-credentials
```

The Docker Hub password or access token is not stored directly in the Jenkinsfile.

---

## Stage 10 - Deploy to Docker Container

Jenkins also runs the Docker image as a container.

The container name is:

```text
dotnet
```

The application port is mapped as:

```text
5000:5000
```

Command:

```bash
docker run -d \
--name dotnet \
-p 5000:5000 \
chunmay123/dotnet-monitoring:latest
```

---

## Stage 11 - Deploy to Kubernetes

The final deployment stage uses Kubernetes.

Jenkins moves into:

```text
DevOps-chin/DotNet-monitoring/K8S
```

Then applies:

```bash
kubectl apply -f deployment.yaml
```

This creates or updates the Kubernetes resources defined in the YAML file.

---

# 11. Docker

Docker is used to package the .NET application into a container.

The project uses a multi-stage Dockerfile.

### Build stage

The .NET SDK image is used to:

* Copy the source code
* Restore dependencies
* Build the application
* Publish the application

### Runtime stage

The ASP.NET runtime image is used to run the published application.

The application listens on:

```text
5000
```

Docker image:

```text
chunmay123/dotnet-monitoring:latest
```

---

# 12. Docker Commands

Build the image:

```bash
docker build \
-f build/Dockerfile \
-t chunmay123/dotnet-monitoring:latest .
```

Check images:

```bash
docker images
```

Run the container:

```bash
docker run -d \
--name dotnet \
-p 5000:5000 \
chunmay123/dotnet-monitoring:latest
```

Check running containers:

```bash
docker ps
```

Check logs:

```bash
docker logs dotnet
```

Test the application:

```bash
curl http://localhost:5000
```

---

# 13. Docker Hub

Docker Hub is used as the container image registry.

The project image is:

```text
chunmay123/dotnet-monitoring:latest
```

The pipeline performs:

```text
Docker Build
     ↓
Trivy Image Scan
     ↓
Docker Push
     ↓
Docker Hub
```

The Docker Hub access token is stored in Jenkins credentials instead of putting it directly inside the Jenkinsfile.

---

# 14. Security Scanning

Security is included at different stages of the pipeline.

```text
Source Code
     ↓
SonarQube
     ↓
Trivy File Scan
     ↓
OWASP Dependency Check
     ↓
Docker Image
     ↓
Trivy Docker Image Scan
     ↓
Deployment
```

---

## SonarQube

SonarQube is used for:

* Code quality analysis
* Finding code issues
* Checking maintainability
* Quality Gate evaluation

---

## Trivy

Trivy is used for:

* File system vulnerability scanning
* Docker image vulnerability scanning

The project uses two Trivy scans:

```text
Trivy File Scan
       +
Trivy Docker Image Scan
```

---

## OWASP Dependency-Check

OWASP Dependency-Check is used to find known vulnerabilities in project dependencies.

It helps identify dependencies that may have known security problems.

---

# 15. Kubernetes

Kubernetes is used to deploy and manage the application.

The Kubernetes setup contains:

```text
k8s-master
     |
     ↓
Kubernetes Control Plane
     |
     ↓
k8s-worker
     |
     ↓
.NET Application
```

The Kubernetes configuration is stored in:

```text
K8S/deployment.yaml
```

---

# 16. Kubernetes Deployment

The deployment YAML defines the application Deployment and Service.

The container uses the Docker Hub image:

```text
chunmay123/dotnet-monitoring:latest
```

The application container listens on:

```text
5000
```

The Service is used to expose the application outside the Kubernetes cluster.

---

# 17. Kubernetes Commands

Check the Kubernetes nodes:

```bash
kubectl get nodes
```

Check pods:

```bash
kubectl get pods
```

Check pods with node information:

```bash
kubectl get pods -o wide
```

Check deployments:

```bash
kubectl get deployments
```

Check services:

```bash
kubectl get services
```

Check all resources:

```bash
kubectl get all
```

Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

Check application logs:

```bash
kubectl logs <POD-NAME>
```

Describe a pod:

```bash
kubectl describe pod <POD-NAME>
```

---

# 18. Application Access

When the application is running directly through Docker, it can be accessed using:

```text
http://<MAIN-SERVER-IP>:5000
```

For local testing on the server:

```bash
curl http://localhost:5000
```

When the application is deployed through Kubernetes, it is accessed through the NodePort configured in `deployment.yaml`.

The exact URL depends on the NodePort and the Kubernetes node being used.

---

# 19. Jenkins Configuration

The Jenkins pipeline requires the following configuration.

### JDK

Jenkins JDK name:

```text
jdk21
```

This matches the Jenkinsfile:

```groovy
tools {
    jdk 'jdk21'
}
```

### SonarQube Scanner

Scanner name:

```text
sonar-scanner
```

This matches:

```groovy
environment {
    SCANNER_HOME = tool 'sonar-scanner'
}
```

### SonarQube Server

Server name:

```text
sonar-server
```

### SonarQube Credentials

Credential ID:

```text
Sonar-token
```

### OWASP Dependency-Check

Installation name:

```text
DP-Check
```

### Docker Hub

Credential ID:

```text
dockerhub-credentials
```

### Kubernetes

Credential ID:

```text
k8s
```

These names must match the configuration used in the Jenkinsfile.

---

# 20. Important Jenkins Plugins

The Jenkins installation requires plugins for the pipeline and the tools used in the project.

Important plugins include:

```text
Pipeline
Git
GitHub
Docker Pipeline
SonarQube Scanner
OWASP Dependency-Check
Kubernetes
Kubernetes CLI
Credentials Binding
Pipeline: Stage View
```

The exact plugin list can depend on the Jenkins installation.

---

# 21. Useful System Commands

Check Java:

```bash
java -version
```

Check .NET:

```bash
dotnet --version
```

Check Docker:

```bash
docker --version
```

Check Trivy:

```bash
trivy --version
```

Check Jenkins:

```bash
sudo systemctl status jenkins
```

Check Docker service:

```bash
sudo systemctl status docker
```

Check SonarQube:

```bash
docker ps
```

Check SonarQube logs:

```bash
docker logs sonarqube
```

---

# 22. Troubleshooting

## Jenkins is not running

Check:

```bash
sudo systemctl status jenkins
```

Restart:

```bash
sudo systemctl restart jenkins
```

View logs:

```bash
sudo journalctl -u jenkins -f
```

---

## Docker is not running

Check:

```bash
sudo systemctl status docker
```

Restart:

```bash
sudo systemctl restart docker
```

Test:

```bash
docker ps
```

---

## SonarQube is not opening

Check:

```bash
docker ps
```

Check logs:

```bash
docker logs sonarqube
```

Restart:

```bash
docker restart sonarqube
```

---

## Jenkins cannot access Docker

Make sure the Jenkins user has Docker permission:

```bash
sudo usermod -aG docker jenkins
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

---

## Kubernetes node is NotReady

Check:

```bash
kubectl get nodes
```

Check all Kubernetes pods:

```bash
kubectl get pods -A
```

Check kubelet:

```bash
sudo systemctl status kubelet
```

Check containerd:

```bash
sudo systemctl status containerd
```

---

## Kubernetes pod is not running

Check:

```bash
kubectl get pods -o wide
```

Describe the pod:

```bash
kubectl describe pod <POD-NAME>
```

Check logs:

```bash
kubectl logs <POD-NAME>
```

---

## Docker image is not available

Check local images:

```bash
docker images
```

Check the image name:

```text
chunmay123/dotnet-monitoring:latest
```

Push the image again if required:

```bash
docker push chunmay123/dotnet-monitoring:latest
```

---

# 23. Project Demonstration

For the final project demonstration, the following order can be used.

### Step 1 - GitHub

Show the GitHub repository.

Explain:

> "This is my source code repository. The application and DevOps configuration files are stored here."

### Step 2 - Jenkins

Show the Jenkins pipeline.

Explain:

> "Jenkins automatically gets the code from GitHub and runs the CI/CD pipeline."

### Step 3 - SonarQube

Show the SonarQube project.

Explain:

> "SonarQube checks the source code for code quality issues."

### Step 4 - Trivy

Show the Trivy scan result.

Explain:

> "Trivy is used to scan the project files and Docker image for security vulnerabilities."

### Step 5 - OWASP

Show the dependency-check result.

Explain:

> "OWASP Dependency-Check checks the project dependencies for known vulnerabilities."

### Step 6 - Docker

Show:

```bash
docker images
```

Explain:

> "After the checks, Jenkins creates the Docker image and pushes it to Docker Hub."

### Step 7 - Kubernetes

Show:

```bash
kubectl get nodes
```

Then:

```bash
kubectl get pods
```

Then:

```bash
kubectl get deployments
```

Then:

```bash
kubectl get services
```

Explain:

> "The application is deployed using Kubernetes. The cluster has a control-plane node and a worker node."

### Step 8 - Application

Open the application in the browser.

Explain:

> "Finally, the .NET Monitoring Application is running and can be accessed through the configured service."

---

# 24. Complete DevSecOps Workflow

The complete workflow is:

```text
Developer
    |
    ↓
GitHub
    |
    ↓
Jenkins
    |
    +----------------------+
    |                      |
    ↓                      ↓
SonarQube              Security Scans
    |                  /           \
    ↓                 ↓             ↓
Quality Gate       Trivy          OWASP
    |                 |
    +--------+--------+
             |
             ↓
       Docker Build
             |
             ↓
       Trivy Image Scan
             |
             ↓
        Docker Hub
             |
       +-----+-----+
       |           |
       ↓           ↓
 Docker        Kubernetes
 Container          |
                    ↓
              .NET Application
```

---

# 25. Why DevSecOps Is Used

In a traditional deployment process, security checks may happen separately from development and deployment.

In this project, security checks are included inside the CI/CD pipeline.

The basic idea is:

```text
Code
 ↓
Build
 ↓
Analyze
 ↓
Security Scan
 ↓
Containerize
 ↓
Deploy
```

This helps make the deployment process more automated and includes security checks before deployment.

---

# 26. Benefits of the Project

This project provides the following benefits:

* Automates the CI/CD process
* Reduces manual deployment work
* Checks source code quality
* Scans project files for vulnerabilities
* Checks application dependencies
* Creates Docker images
* Stores images in Docker Hub
* Deploys the application using Kubernetes
* Provides a repeatable deployment process
* Combines development, security and operations in one workflow

---

# 27. Final Architecture

```text
                         GITHUB
                           |
                           ↓
                        JENKINS
                           |
          +----------------+----------------+
          |                |                |
          ↓                ↓                ↓
      SonarQube          Trivy           OWASP
      Code Quality     File Scan       Dependency
          |                                Scan
          +----------------+---------------+
                           |
                           ↓
                     DOCKER BUILD
                           |
                           ↓
                  TRIVY IMAGE SCAN
                           |
                           ↓
                     DOCKER HUB
                           |
                           ↓
                    KUBERNETES
                           |
                  +--------+--------+
                  |                 |
                  ↓                 ↓
           Control Plane       Worker Node
           k8s-master          k8s-worker
                                    |
                                    ↓
                          .NET Monitoring App
```

---

28. Final Result

The final result is an automated DevSecOps pipeline that takes application source code from GitHub and moves it through code quality checks, security scans, Docker containerization and Kubernetes deployment.

The complete process is:

GitHub
   ↓
Jenkins
   ↓
SonarQube
   ↓
Quality Gate
   ↓
Trivy
   ↓
OWASP Dependency-Check
   ↓
Docker Build
   ↓
Trivy Image Scan
   ↓
Docker Hub
   ↓
Docker Container
   ↓
Kubernetes
   ↓
.NET Monitoring Application

This project demonstrates how CI/CD, security scanning, Docker and Kubernetes can be combined to automate the application deployment process.

Author

Chinmay A. Madgaonkar

Project: DevSecOps .NET Monitoring Application

Technologies:

AWS
Linux
Git
GitHub
Jenkins
.NET 6
SonarQube
Trivy
OWASP Dependency-Check
Docker
Docker Hub
Kubernetes
