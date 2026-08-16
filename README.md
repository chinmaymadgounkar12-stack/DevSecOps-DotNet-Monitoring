# 🌻 Real-Time DevSecOps Pipeline for a DotNet Web App 🌻

## 🚀 Project Overview

This project demonstrates a **Real-Time DevSecOps CI/CD Pipeline for a .NET Web Application**.

The project integrates source code management, continuous integration, security scanning, containerization, and Kubernetes deployment into an automated DevSecOps workflow.

The objective is to build a secure and reliable software delivery pipeline where application code can be automatically built, tested, scanned, containerized, and prepared for deployment.

**Project:** DotNet Monitoring

---

## 🏗️ DevSecOps Architecture

![DevSecOps CI/CD Architecture](architecture.png)

### 🔄 Pipeline Flow

```text
Developer
    |
    v
   Git
    |
    v
 Jenkins
    |
    +----------------------+
    |                      |
    v                      v
OWASP Dependency       Trivy File Scan
    |                      |
    +----------+-----------+
               |
               v
          SonarQube
          Code Scan
               |
               v
          Docker Build
               |
               v
          Docker Push
               |
               v
       Docker Container
               |
               v
         Kubernetes
```

---

# 🛠️ Technologies Used

| Technology                 | Purpose                                |
| -------------------------- | -------------------------------------- |
| **Git**                    | Source Code Management                 |
| **GitHub**                 | Remote Code Repository                 |
| **Jenkins**                | CI/CD Pipeline Automation              |
| **.NET**                   | Application Development                |
| **Maven / Build Tools**    | Application Build Process              |
| **SonarQube**              | Code Quality and Static Code Analysis  |
| **OWASP Dependency-Check** | Dependency Security Scanning           |
| **Trivy**                  | Vulnerability and File System Scanning |
| **Docker**                 | Application Containerization           |
| **Docker Hub**             | Container Image Registry               |
| **Kubernetes**             | Container Orchestration                |
| **Linux / Ubuntu**         | Server Environment                     |

---

# 🔐 DevSecOps Pipeline

The pipeline follows the principle of **"Shift Left Security"**, where security checks are included during the development and CI/CD process instead of being performed only after deployment.

## Pipeline Stages

### 1. 📥 Source Code

The .NET application source code is maintained in a GitHub repository.

Jenkins retrieves the latest source code from the repository when the pipeline is triggered.

---

### 2. 🔨 Build

Jenkins builds the application and prepares it for the next stages of the pipeline.

The build stage verifies that the application can be compiled successfully.

---

### 3. 🧪 Testing

Automated testing is performed as part of the CI/CD process.

Testing helps identify application problems before the application is packaged and deployed.

---

### 4. 🔍 SonarQube Code Scan

**SonarQube** is used for static code analysis.

It helps identify:

* Bugs
* Code smells
* Security issues
* Maintainability problems
* Code quality issues

The SonarQube analysis provides visibility into the overall quality of the application source code.

---

### 5. 🛡️ OWASP Dependency Scan

**OWASP Dependency-Check** is used to identify known vulnerabilities in application dependencies.

This helps detect potentially vulnerable libraries before they reach the deployment stage.

---

### 6. 🐳 Trivy Security Scan

**Trivy** is used as a security scanning tool in the pipeline.

It can be used to scan files, dependencies, and container images for known vulnerabilities.

This adds another security layer to the DevSecOps workflow.

---

### 7. 📦 Docker Build

After the application passes the required pipeline stages, a Docker image is created.

Docker packages the application and its required runtime environment into a portable container image.

Example:

```bash
docker build -t dotnet-monitoring .
```

---

### 8. 🚢 Docker Push

The Docker image can be pushed to a container registry such as Docker Hub.

Example:

```bash
docker push <dockerhub-username>/dotnet-monitoring:latest
```

The image can then be pulled by the deployment environment.

---

### 9. ☸️ Kubernetes Deployment

Kubernetes is used for container orchestration.

The Docker image can be deployed to a Kubernetes cluster, where Kubernetes manages the application container.

Typical Kubernetes resources include:

* Deployment
* Service
* Pods
* ConfigMaps
* Secrets

---

# 🔄 Complete CI/CD Workflow

```text
        GitHub
           |
           v
        Jenkins
           |
           v
      Application
         Build
           |
           v
        Testing
           |
           v
       SonarQube
       Code Scan
           |
           v
  OWASP Dependency Check
           |
           v
       Trivy Scan
           |
           v
      Docker Build
           |
           v
      Docker Push
           |
           v
      Kubernetes
           |
           v
    Running Application
```

---

# 📁 Project Structure

The project contains the .NET application source code along with the files required for the DevSecOps pipeline.

```text
DotNet-Monitoring/
│
├── .github/
│
├── Controllers/
│
├── Models/
│
├── Views/
│
├── wwwroot/
│
├── Dockerfile
│
├── Jenkinsfile
│
├── *.csproj
│
├── appsettings.json
│
├── Program.cs
│
└── README.md
```

> The exact folders and files may vary depending on the application source code.

---

# 🐳 Docker

Docker is used to containerize the .NET web application.

## Build Docker Image

```bash
docker build -t dotnet-monitoring .
```

## Run Docker Container

```bash
docker run -d -p 8080:8080 dotnet-monitoring
```

## Check Running Containers

```bash
docker ps
```

## View Docker Images

```bash
docker images
```

---

# ☸️ Kubernetes

Kubernetes provides container orchestration and helps manage the application after containerization.

Typical deployment workflow:

```text
Docker Image
     |
     v
Kubernetes Deployment
     |
     v
     Pod
     |
     v
Kubernetes Service
     |
     v
Application
```

Useful commands:

```bash
kubectl get pods
```

```bash
kubectl get deployments
```

```bash
kubectl get services
```

```bash
kubectl get all
```

---

# 🔧 Jenkins

Jenkins is responsible for automating the CI/CD pipeline.

The Jenkins pipeline can be defined using a `Jenkinsfile`.

Typical pipeline stages include:

```text
Checkout
   ↓
Build
   ↓
Test
   ↓
SonarQube Scan
   ↓
OWASP Scan
   ↓
Trivy Scan
   ↓
Docker Build
   ↓
Docker Push
   ↓
Kubernetes Deployment
```

---

# 🔒 Security Tools

Security is an important part of this project.

### SonarQube

Used for:

* Static code analysis
* Code quality analysis
* Bug detection
* Security analysis

### OWASP Dependency-Check

Used for:

* Dependency vulnerability detection
* Identifying vulnerable third-party libraries

### Trivy

Used for:

* File system scanning
* Dependency scanning
* Container image vulnerability scanning

---

# 📊 DevSecOps Benefits

This project demonstrates several important DevSecOps practices:

* ✅ Continuous Integration
* ✅ Continuous Delivery
* ✅ Automated testing
* ✅ Static code analysis
* ✅ Dependency vulnerability scanning
* ✅ Container security scanning
* ✅ Docker containerization
* ✅ Container image management
* ✅ Kubernetes deployment
* ✅ Automated pipeline execution
* ✅ Security integrated into CI/CD

---

# 📸 Project Screenshots

The following screenshots can be added as evidence of the project implementation.

## GitHub Repository

Add your GitHub repository screenshot here.

```text
docs/screenshots/github.png
```

## Jenkins Dashboard

Add your Jenkins dashboard screenshot here.

```text
docs/screenshots/jenkins-dashboard.png
```

## Jenkins Pipeline

Add the successful Jenkins pipeline screenshot here.

```text
docs/screenshots/jenkins-pipeline.png
```

## SonarQube

Add the SonarQube analysis screenshot here.

```text
docs/screenshots/sonarqube.png
```

## OWASP Scan

Add the OWASP Dependency-Check result screenshot here.

```text
docs/screenshots/owasp.png
```

## Trivy Scan

Add the Trivy scan result screenshot here.

```text
docs/screenshots/trivy.png
```

## Docker

Add the Docker image/container screenshot here.

```text
docs/screenshots/docker.png
```

## Kubernetes

Add the Kubernetes deployment screenshot here.

```text
docs/screenshots/kubernetes.png
```

---

# 🎯 Project Objectives

The main objectives of this project are:

1. Implement a CI/CD pipeline using Jenkins.
2. Integrate GitHub with Jenkins.
3. Build and test a .NET web application automatically.
4. Integrate security scanning into the CI/CD pipeline.
5. Perform code quality analysis using SonarQube.
6. Perform dependency security analysis using OWASP Dependency-Check.
7. Scan files and container images using Trivy.
8. Containerize the application using Docker.
9. Push the Docker image to a container registry.
10. Deploy the containerized application using Kubernetes.

---

# 🌐 Expected Workflow

When a developer updates the application source code:

```text
Developer makes changes
          ↓
       Git Push
          ↓
       GitHub
          ↓
       Jenkins
          ↓
       Build/Test
          ↓
   Security Analysis
          ↓
      Docker Build
          ↓
      Docker Push
          ↓
      Kubernetes
          ↓
   Running Application
```

This provides an automated and repeatable software delivery process.

---

# 📌 Key Learning Outcomes

Through this project, the following DevOps and DevSecOps concepts are demonstrated:

* Git and GitHub
* Jenkins CI/CD
* .NET application deployment
* Linux administration
* Docker containerization
* Docker image management
* SonarQube
* OWASP Dependency-Check
* Trivy
* Kubernetes
* CI/CD automation
* DevSecOps security practices

---

# 👨‍💻 Author

**Chinmay A. Madgaonkar**

**BCA Graduate | DevOps Trainee**

This project was developed as part of practical DevOps/DevSecOps training to demonstrate CI/CD automation, security integration, containerization, and Kubernetes deployment.

---

# ⭐ Conclusion

The **Real-Time DevSecOps Pipeline for a DotNet Web App** demonstrates how modern DevOps and security tools can be integrated into a single automated workflow.

The project combines:

**GitHub → Jenkins → Build & Test → SonarQube → OWASP → Trivy → Docker → Docker Registry → Kubernetes**

This approach helps automate application delivery while integrating security and quality checks throughout the development lifecycle.

---

## 🌟 DevSecOps Pipeline

**Code → Build → Test → Scan → Secure → Containerize → Push → Deploy**

---
