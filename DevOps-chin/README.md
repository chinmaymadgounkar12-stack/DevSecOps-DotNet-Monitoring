# DevSecOps .NET Monitoring Application

## Project Overview

This project is a **DevSecOps CI/CD pipeline for a .NET Monitoring web application**.

The main purpose of this project is to automate the complete process from source code to deployment.

The project uses GitHub for source code management, Jenkins for CI/CD automation, SonarQube for code quality analysis, Trivy and OWASP Dependency-Check for security scanning, Docker for containerization, Docker Hub for storing the Docker image, and Kubernetes for deployment.

### Complete Project Flow

```text
GitHub
   ↓
Jenkins
   ↓
SonarQube
   ↓
Trivy File Scan
   ↓
OWASP Dependency Check
   ↓
Docker Build
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

# Technologies Used

| Technology             | Purpose                           |
| ---------------------- | --------------------------------- |
| AWS EC2                | Cloud server                      |
| Ubuntu                 | Operating system                  |
| Git                    | Version control                   |
| GitHub                 | Source code repository            |
| Jenkins                | CI/CD automation                  |
| .NET 6                 | Application                       |
| SonarQube              | Code quality analysis             |
| Docker                 | Containerization                  |
| Docker Hub             | Docker image repository           |
| Trivy                  | Security scanning                 |
| OWASP Dependency-Check | Dependency vulnerability scanning |
| Kubernetes             | Container deployment              |

---

# Prerequisites

Before starting the project, make sure you have:

* AWS account
* GitHub account
* Docker Hub account
* Ubuntu server
* Jenkins
* Docker
* Trivy
* SonarQube
* Kubernetes
* kubectl
* .NET 6 SDK

You also need the required Jenkins plugins and credentials.

---

# Step 1 — Create AWS Ubuntu EC2 Instance

Create an Ubuntu EC2 instance in AWS.

### Recommended project setup

```text
Operating System : Ubuntu
Instance Type    : T2 Large
Storage          : 30 GB
```

The EC2 instance is used as the server where the DevSecOps tools and application are executed.

After connecting to the server, update the packages:

```bash
sudo apt update
sudo apt upgrade -y
```

Check the operating system:

```bash
cat /etc/os-release
```

---

# Step 2 — Install Jenkins, Docker and Trivy

## Install Java

Jenkins requires Java.

Install Java 21:

```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
```

Check Java:

```bash
java -version
```

Expected output should show Java 21.

---

## Install Jenkins

Install Jenkins on the Ubuntu server.

After installation, start Jenkins:

```bash
sudo systemctl start jenkins
```

Enable Jenkins to start automatically:

```bash
sudo systemctl enable jenkins
```

Check Jenkins:

```bash
sudo systemctl status jenkins
```

Jenkins normally runs on:

```text
http://SERVER-IP:8080
```

Replace `SERVER-IP` with the public IP address of the EC2 instance.

---

## Install Docker

Install Docker:

```bash
sudo apt update
sudo apt install docker.io -y
```

Start Docker:

```bash
sudo systemctl start docker
```

Enable Docker:

```bash
sudo systemctl enable docker
```

Check Docker:

```bash
docker --version
```

Check Docker service:

```bash
sudo systemctl status docker
```

---

## Allow Jenkins to use Docker

Add Jenkins to the Docker group:

```bash
sudo usermod -aG docker jenkins
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Check Docker:

```bash
docker ps
```

---

## Install Trivy

Trivy is used to scan files and Docker images for known vulnerabilities.

Install Trivy according to the official Trivy installation method for Ubuntu.

Check the installation:

```bash
trivy --version
```

---

# Create SonarQube Container

SonarQube is used to analyse the project source code.

Pull the SonarQube image:

```bash
docker pull sonarqube:9.9-community
```

Create the SonarQube container:

```bash
docker run -d \
  --name sonarqube \
  -p 9000:9000 \
  sonarqube:9.9-community
```

Check the container:

```bash
docker ps
```

SonarQube can be accessed using:

```text
http://SERVER-IP:9000
```

---

# Step 3 — Install and Configure Jenkins Plugins and Tools

Open Jenkins:

```text
http://SERVER-IP:8080
```

Go to:

```text
Manage Jenkins
    ↓
Plugins
```

Install the required plugins.

Important plugins include:

* Pipeline
* Git
* GitHub
* SonarQube Scanner
* OWASP Dependency-Check
* Docker-related plugins
* Kubernetes plugin
* Credentials Binding
* Pipeline Utility Steps

---

## Configure JDK

Go to:

```text
Manage Jenkins
    ↓
Tools
```

Add JDK 21.

Use the Jenkins tool name:

```text
jdk21
```

The Jenkinsfile uses:

```groovy
tools {
    jdk 'jdk21'
}
```

---

## Configure SonarQube Scanner

In:

```text
Manage Jenkins
    ↓
Tools
```

Add SonarQube Scanner.

Use the tool name:

```text
sonar-scanner
```

The Jenkinsfile uses:

```groovy
environment {
    SCANNER_HOME = tool 'sonar-scanner'
}
```

---

# Step 4 — Install OWASP Dependency-Check

Go to:

```text
Manage Jenkins
    ↓
Plugins
```

Install:

```text
OWASP Dependency-Check
```

Then go to:

```text
Manage Jenkins
    ↓
Tools
```

Configure Dependency-Check.

Use the installation name:

```text
DP-Check
```

The Jenkinsfile uses:

```groovy
odcInstallation: 'DP-Check'
```

---

# Step 5 — Configure SonarQube Server

Open Jenkins:

```text
Manage Jenkins
    ↓
System
```

Find:

```text
SonarQube servers
```

Add a SonarQube server.

Use:

```text
Name: sonar-server
```

Enter the SonarQube server URL.

Example:

```text
http://SERVER-IP:9000
```

The Jenkinsfile uses:

```groovy
withSonarQubeEnv('sonar-server')
```

---

## Create SonarQube Token

Open SonarQube:

```text
http://SERVER-IP:9000
```

Create a token for Jenkins.

Add the token to Jenkins credentials.

The Jenkinsfile uses:

```text
credentialsId: 'Sonar-token'
```

Do not write the actual token inside the Jenkinsfile.

---

# Step 6 — Create Jenkins Pipeline Project

Open Jenkins.

Select:

```text
New Item
```

Create a:

```text
Pipeline
```

project.

Give it a suitable name, for example:

```text
DevSecOps-DotNet-Monitoring
```

Go to the Pipeline section and select:

```text
Pipeline script
```

Paste the Jenkinsfile from the project repository.

The pipeline performs the following stages:

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

# Jenkinsfile

The main Jenkins pipeline used in this project is:

```groovy
pipeline {

    agent any

    tools {
        jdk 'jdk21'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout From Git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chinmaymadgounkar12-stack/DevSecOps-DotNet-Monitoring.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=Dotnet-Webapp \
                        -Dsonar.projectKey=Dotnet-Webapp
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate(
                        abortPipeline: false,
                        credentialsId: 'Sonar-token'
                    )
                }
            }
        }

        stage('Trivy File Scan') {
            steps {
                sh '''
                    trivy fs . > trivy-fs_report.txt
                '''
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck(
                    additionalArguments: '--scan ./ --format XML --nvdDatafeed "https://dependency-check.github.io/DependencyCheck_Builder/nvd_cache/nvdcve-{0}.json.gz"',
                    odcInstallation: 'DP-Check'
                )

                dependencyCheckPublisher(
                    pattern: '**/dependency-check-report.xml'
                )
            }
        }

        stage('Docker Build & Tag') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        printf "%s" "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker build \
                            -f DevOps-chin/DotNet-monitoring/build/Dockerfile \
                            -t chunmay123/dotnet-monitoring:latest \
                            DevOps-chin/DotNet-monitoring
                    '''
                }
            }
        }

        stage('Trivy Docker Image Scan') {
            steps {
                sh '''
                    trivy image chunmay123/dotnet-monitoring:latest > trivy.txt
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        printf "%s" "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker push chunmay123/dotnet-monitoring:latest
                    '''
                }
            }
        }

        stage('Deploy to Container') {
            steps {
                sh '''
                    docker rm -f dotnet 2>/dev/null || true

                    docker run -d \
                        --name dotnet \
                        -p 5000:5000 \
                        chunmay123/dotnet-monitoring:latest
                '''
            }
        }

        stage('Deploy to k8s') {
            steps {
                dir('DevOps-chin/DotNet-monitoring/K8S') {
                    withKubeConfig(
                        caCertificate: '',
                        clusterName: '',
                        contextName: '',
                        credentialsId: 'k8s',
                        namespace: '',
                        restrictKubeConfigAccess: false,
                        serverUrl: ''
                    ) {
                        sh 'kubectl apply -f deployment.yaml'
                    }
                }
            }
        }
    }

    post {

        always {
            echo 'Pipeline execution completed.'
        }

        success {
            echo 'SUCCESS: Build, security scans, Docker build, push, container deployment and Kubernetes deployment completed.'
        }

        failure {
            echo 'FAILED: Check the Jenkins console log for the failed stage.'
        }
    }
}
```

---

# Step 7 — Install Make Package

Install the `make` package on Ubuntu:

```bash
sudo apt update
sudo apt install make -y
```

Check the installation:

```bash
make --version
```

---

# Step 8 — Docker Image Build and Push

The Jenkins pipeline builds the Docker image using the Dockerfile.

The Dockerfile is located at:

```text
DevOps-chin/DotNet-monitoring/build/Dockerfile
```

The Docker image is tagged as:

```text
chunmay123/dotnet-monitoring:latest
```

---

## Manual Docker Build

If you want to build the image manually:

```bash
cd DevOps-chin/DotNet-monitoring
```

Then:

```bash
docker build \
-f build/Dockerfile \
-t chunmay123/dotnet-monitoring:latest .
```

Check the image:

```bash
docker images
```

---

## Docker Hub Login

Login to Docker Hub:

```bash
docker login
```

Enter your Docker Hub username and password/token when requested.

Then push the image:

```bash
docker push chunmay123/dotnet-monitoring:latest
```

Check the image:

```bash
docker images
```

---

# Step 9 — Deploy the Image Using Docker

Before starting a new container, remove the old one if it exists:

```bash
docker rm -f dotnet 2>/dev/null || true
```

Run the application:

```bash
docker run -d \
  --name dotnet \
  -p 5000:5000 \
  chunmay123/dotnet-monitoring:latest
```

Check the running container:

```bash
docker ps
```

Check container logs:

```bash
docker logs dotnet
```

The application uses port:

```text
5000
```

Test locally on the server:

```bash
curl http://localhost:5000
```

---

# Step 10 — Access the Real World Application

The application runs on port `5000`.

Open the application using:

```text
http://SERVER-IP:5000
```

Replace:

```text
SERVER-IP
```

with the public IP address of the EC2 instance.

For example:

```text
http://YOUR-EC2-PUBLIC-IP:5000
```

Make sure port `5000` is allowed in the AWS EC2 Security Group.

---

# Step 11 — Kubernetes Setup

Kubernetes is used to deploy and manage the application.

The project contains Kubernetes configuration inside:

```text
DevOps-chin/DotNet-monitoring/K8S
```

The Jenkins pipeline uses:

```text
deployment.yaml
```

---

## Install kubectl

Install `kubectl` on the Ubuntu server.

Check the installation:

```bash
kubectl version --client
```

---

## Check Kubernetes Cluster

Check the nodes:

```bash
kubectl get nodes
```

Expected example:

```text
NAME          STATUS   ROLES           AGE   VERSION
control-plane Ready    control-plane   ...   v1.37.0
```

The exact output can be different depending on the Kubernetes setup.

---

# Kubernetes Deployment YAML

The deployment file should contain the Kubernetes Deployment and Service configuration for the application.

Example structure:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dotnet-monitoring
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dotnet-monitoring
  template:
    metadata:
      labels:
        app: dotnet-monitoring
    spec:
      containers:
        - name: dotnet-monitoring
          image: chunmay123/dotnet-monitoring:latest
          ports:
            - containerPort: 5000

---
apiVersion: v1
kind: Service
metadata:
  name: dotnet-monitoring
spec:
  selector:
    app: dotnet-monitoring
  type: NodePort
  ports:
    - port: 5000
      targetPort: 5000
```

> Use the `deployment.yaml` already present in the project repository as the source of truth if it differs from this example.

---

# Apply Kubernetes Deployment

Go to the Kubernetes folder:

```bash
cd DevOps-chin/DotNet-monitoring/K8S
```

Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

---

# Check Kubernetes Deployment

Check deployments:

```bash
kubectl get deployments
```

Check pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get services
```

Check all resources:

```bash
kubectl get all
```

---

# Check Pod Details

If a pod is not running, first get the pod name:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod POD-NAME
```

Replace:

```text
POD-NAME
```

with the actual pod name.

---

# Check Kubernetes Logs

To check application logs:

```bash
kubectl logs POD-NAME
```

---

# Check Deployment Details

```bash
kubectl describe deployment dotnet-monitoring
```

---

# Kubernetes Service

Check the service:

```bash
kubectl get svc
```

If the service is a NodePort service, Kubernetes will show the assigned NodePort.

Example:

```text
NAME               TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)
dotnet-monitoring  NodePort   10.x.x.x       <none>        5000:30xxx/TCP
```

The exact port depends on the Kubernetes configuration.

---

# Access the Application Through Kubernetes

If the application is deployed using a NodePort service, access it using:

```text
http://SERVER-IP:NODEPORT
```

Replace `NODEPORT` with the port shown by:

```bash
kubectl get svc
```

If the Kubernetes environment is configured differently, use the service access method provided by that cluster.

---

# Kubernetes Commands Used in the Project

### Check nodes

```bash
kubectl get nodes
```

### Check pods

```bash
kubectl get pods
```

### Check deployments

```bash
kubectl get deployments
```

### Check services

```bash
kubectl get services
```

### Apply deployment

```bash
kubectl apply -f deployment.yaml
```

### Delete deployment

```bash
kubectl delete -f deployment.yaml
```

### Check all resources

```bash
kubectl get all
```

### Describe deployment

```bash
kubectl describe deployment dotnet-monitoring
```

### View pod logs

```bash
kubectl logs POD-NAME
```

---

# Kubernetes Deployment Flow

```text
Docker Hub
    |
    | Docker Image
    v
Kubernetes
    |
    v
Deployment
    |
    +----------+
    |          |
    v          v
  Pod 1      Pod 2
    |          |
    +----------+
         |
         v
      Service
         |
         v
.NET Monitoring Application
```

The project uses multiple replicas so Kubernetes can run more than one pod of the application.

---

# Security Scanning

Security is included at different stages of the pipeline.

## SonarQube

Checks source code quality.

```text
Source Code → SonarQube → Analysis
```

---

## Trivy File Scan

Scans project files:

```bash
trivy fs .
```

The pipeline saves the output to:

```text
trivy-fs_report.txt
```

---

## OWASP Dependency-Check

Checks project dependencies for known vulnerabilities.

The Jenkins pipeline generates a dependency-check report:

```text
dependency-check-report.xml
```

---

## Trivy Docker Image Scan

Scans the Docker image:

```bash
trivy image chunmay123/dotnet-monitoring:latest
```

The Jenkins pipeline saves the result to:

```text
trivy.txt
```

---

# Docker Commands

### List images

```bash
docker images
```

### List running containers

```bash
docker ps
```

### List all containers

```bash
docker ps -a
```

### View logs

```bash
docker logs dotnet
```

### Stop container

```bash
docker stop dotnet
```

### Remove container

```bash
docker rm dotnet
```

### Remove running container

```bash
docker rm -f dotnet
```

---

# Complete Jenkins Pipeline Explanation

## 1. Clean Workspace

```text
cleanWs()
```

Removes files from the previous Jenkins build.

---

## 2. Checkout From Git

Jenkins gets the source code from:

```text
GitHub → main branch
```

---

## 3. SonarQube Analysis

Jenkins sends the project to SonarQube for code analysis.

---

## 4. Quality Gate

Jenkins waits for the SonarQube quality gate result.

---

## 5. Trivy File Scan

Trivy scans the project files for known vulnerabilities.

---

## 6. OWASP Dependency Check

The project dependencies are checked for known vulnerabilities.

---

## 7. Docker Build

Jenkins creates the Docker image:

```text
chunmay123/dotnet-monitoring:latest
```

---

## 8. Trivy Docker Image Scan

The Docker image is scanned for vulnerabilities.

---

## 9. Docker Push

The image is pushed to Docker Hub.

---

## 10. Docker Container Deployment

Jenkins runs the Docker image as a container.

Application port:

```text
5000
```

---

## 11. Kubernetes Deployment

Jenkins applies:

```bash
kubectl apply -f deployment.yaml
```

Kubernetes creates and manages the application pods.

---

# Troubleshooting

## Jenkins is not running

Check:

```bash
sudo systemctl status jenkins
```

Restart:

```bash
sudo systemctl restart jenkins
```

---

## Docker is not running

Check:

```bash
sudo systemctl status docker
```

Start:

```bash
sudo systemctl start docker
```

---

## Check Jenkins Docker permission

Run:

```bash
sudo usermod -aG docker jenkins
```

Then:

```bash
sudo systemctl restart jenkins
```

---

## Port 5000 is not accessible

Check the container:

```bash
docker ps
```

Check logs:

```bash
docker logs dotnet
```

Check the port:

```bash
sudo ss -tulpn | grep 5000
```

Also check the AWS Security Group and make sure the required port is allowed.

---

## Kubernetes pod is not running

Run:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod POD-NAME
```

Check logs:

```bash
kubectl logs POD-NAME
```

---

## Kubernetes deployment is not working

Check:

```bash
kubectl get deployments
```

Then:

```bash
kubectl describe deployment dotnet-monitoring
```

Check the image name:

```text
chunmay123/dotnet-monitoring:latest
```

Make sure the image exists in Docker Hub and Kubernetes can access it.

---

# Project Demonstration

During the final demonstration, show the following in order:

### 1. GitHub

Show the project repository and source code.

### 2. Jenkins

Show the pipeline and its stages.

### 3. SonarQube

Show the project analysis and quality information.

### 4. Trivy

Show the security scan result.

### 5. Docker

Show:

```bash
docker images
```

and:

```bash
docker ps
```

### 6. Docker Hub

Show the pushed Docker image.

### 7. Kubernetes

Run:

```bash
kubectl get nodes
kubectl get pods
kubectl get deployments
kubectl get services
```

### 8. Application

Open the running .NET Monitoring application.

---

# DevSecOps Benefits

This project demonstrates:

### Continuous Integration

Jenkins automatically gets and processes the latest source code.

### Code Quality

SonarQube checks the source code.

### Security

Trivy and OWASP Dependency-Check are used to find known security issues.

### Containerization

Docker packages the application into a container.

### Continuous Deployment

The Docker image can be deployed using Docker and Kubernetes.

### Automation

Jenkins connects the different stages into one pipeline.

---

# Step 12 — Terminate the AWS EC2 Instance

After completing the project demonstration, the EC2 instance can be stopped or terminated if it is no longer required.

Before terminating the instance, make sure you have saved:

* Jenkins configuration
* Project screenshots
* SonarQube results
* Trivy reports
* Docker image information
* Kubernetes configuration
* Project documentation

## Stop the instance

Stopping the instance keeps the instance configuration but may still have storage-related charges.

## Terminate the instance

Termination permanently removes the EC2 instance and can remove its associated storage depending on the configuration.

**Do not terminate the instance until all project work and screenshots have been saved.**

---

# Final Project Flow

```text
                 GitHub
                    |
                    v
                 Jenkins
                    |
                    v
             SonarQube Analysis
                    |
                    v
             Quality Gate
                    |
                    v
              Trivy FS Scan
                    |
                    v
        OWASP Dependency Check
                    |
                    v
              Docker Build
                    |
                    v
          Trivy Image Scan
                    |
                    v
              Docker Hub
                    |
             +------+------+
             |             |
             v             v
        Docker         Kubernetes
        Container       Deployment
             |             |
             |         +---+---+
             |         |       |
             |         v       v
             |       Pod 1   Pod 2
             |         |       |
             +---------+-------+
                       |
                       v
             .NET Monitoring App
```

---

# Final Result

The final result of this project is an automated DevSecOps pipeline for a .NET Monitoring application.

The project starts with source code stored in GitHub. Jenkins automates the pipeline, SonarQube checks code quality, Trivy and OWASP Dependency-Check perform security checks, Docker creates the application image, Docker Hub stores the image, and Kubernetes is used to deploy and manage the application.

### Final Workflow

**GitHub → Jenkins → SonarQube → Security Scan → Docker → Docker Hub → Kubernetes → Running .NET Application**

---

# Author

**Chinmay A. Madgaonkar**

## Project

**DevSecOps .NET Monitoring Application**



