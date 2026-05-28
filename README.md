# Dockerized Flask Application Deployment on AWS EC2

## Project Overview

This project demonstrates a complete end-to-end DevOps workflow by deploying a Dockerized Flask application on an AWS EC2 Ubuntu server using Docker Hub as the container registry.

The primary objective of this project was to gain practical hands-on experience with:
- Docker containerization
- Linux server management
- AWS EC2 deployment
- Docker Hub image management
- Port mapping and networking
- Infrastructure troubleshooting
- Cloud-based application hosting

Instead of only learning concepts theoretically, this project focused on building and debugging a real deployment workflow similar to modern DevOps practices used in production environments.

---

# Project Architecture

```text
Developer Local Machine (WSL + VS Code)
                │
                │ Build Docker Image
                ▼
        Dockerized Flask Application
                │
                │ Push Docker Image
                ▼
          Docker Hub Registry
                │
                │ Pull Image
                ▼
      AWS EC2 Ubuntu Instance
                │
                │ Run Docker Container
                ▼
         Docker Container Runtime
                │
                │ Port Mapping (80 → 5000)
                ▼
        Flask Web Application
                │
                │ Public Access
                ▼
             End User Browser
```

---

# Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Cloud Virtual Server |
| Docker | Containerization |
| Docker Hub | Container Registry |
| Python Flask | Web Application |
| Ubuntu Linux | Server Operating System |
| WSL2 | Local Linux Environment |
| VS Code | Development Environment |
| Git & GitHub | Version Control |

---

# Project Structure

```text
dockerized-flask-app-deployment/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── screenshots/
├── architecture/
└── README.md
```

---

# Application Code

## Flask Application (app.py)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Bhoomi's Dockerized DevOps App 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

---

# Requirements File

## requirements.txt

```text
flask
```

---

# Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# Step-by-Step Implementation

---

# Phase 1 — Local Project Setup

## 1. Create Project Directory

```bash
mkdir docker-flask-devops-app
cd docker-flask-devops-app
```

---

## 2. Create Flask Application

Created:
- app.py
- requirements.txt
- Dockerfile

---

# Phase 2 — Docker Containerization

## 3. Build Docker Image

```bash
docker build -t flask-devops-app .
```

### Explanation
This command:
- Reads the Dockerfile
- Pulls Python base image
- Installs dependencies
- Packages the Flask application into a Docker image

---

## 4. Verify Docker Image

```bash
docker images
```

Expected Output:
```text
flask-devops-app
```

---

## 5. Run Docker Container Locally

```bash
docker run -p 5000:5000 flask-devops-app
```

### Explanation
- First 5000 → Local Machine Port
- Second 5000 → Container Port

Application accessible at:

```text
http://localhost:5000
```

---

# Phase 3 — Docker Hub Integration

## 6. Login to Docker Hub

```bash
docker login
```

---

## 7. Tag Docker Image

```bash
docker tag flask-devops-app devopsbhoomi/flask-devops-app:latest
```

### Explanation
Docker Hub requires images in the format:

```text
username/repository-name
```

---

## 8. Push Docker Image to Docker Hub

```bash
docker push devopsbhoomi/flask-devops-app:latest
```

This uploads the Docker image to the Docker Hub registry so it can be accessed from AWS EC2.

---

# Phase 4 — AWS EC2 Deployment

## 9. Launch AWS EC2 Instance

### EC2 Configuration
- Ubuntu Server
- Free Tier Eligible
- Security Group configured
- SSH enabled
- HTTP traffic allowed on Port 80

---

## 10. Connect to EC2 via SSH

```bash
ssh -i ~/.ssh/devops-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

# Phase 5 — Docker Installation on EC2

## 11. Update Packages

```bash
sudo apt update
```

---

## 12. Install Docker

```bash
sudo apt install docker.io -y
```

---

## 13. Start Docker Service

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 14. Verify Docker Installation

```bash
docker --version
```

---

# Phase 6 — Deploy Container on EC2

## 15. Pull Docker Image from Docker Hub

```bash
docker pull devopsbhoomi/flask-devops-app:latest
```

---

## 16. Run Docker Container

```bash
docker run -d -p 80:5000 devopsbhoomi/flask-devops-app:latest
```

### Explanation
- `-d` → Runs container in detached/background mode
- `80:5000` → Maps EC2 Port 80 to Container Port 5000

---

## 17. Access Application Publicly

```text
http://<EC2-PUBLIC-IP>
```

---

# Problems Faced & Troubleshooting

This project involved several real-world infrastructure and deployment issues which were diagnosed and resolved during implementation.

---

# Problem 1 — Docker Desktop Stuck on Loading

## Issue
Docker Desktop remained stuck on the loading screen for a long time.

## Root Cause
WSL integration was not properly initialized.

## Resolution
- Restarted Docker Desktop
- Executed:
```powershell
wsl --shutdown
```
- Enabled Ubuntu WSL integration inside Docker Desktop settings

---

# Problem 2 — Docker Command Not Found in WSL

## Issue

```text
docker: command not found
```

## Root Cause
Docker Desktop WSL Integration was disabled for Ubuntu.

## Resolution
Enabled:
```text
Docker Desktop → Settings → Resources → WSL Integration
```

Then enabled:
```text
Ubuntu Distribution
```

---

# Problem 3 — Docker Permission Denied

## Issue

```text
permission denied while trying to connect to the docker API socket
```

## Root Cause
Current Linux user was not added to the Docker group.

## Resolution

```bash
sudo usermod -aG docker $USER
```

Then:
- Closed terminal
- Logged in again

---

# Problem 4 — EC2 SSH Permission Issues

## Issue

```text
UNPROTECTED PRIVATE KEY FILE
```

## Root Cause
SSH private key had incorrect permissions.

## Resolution

```bash
chmod 400 devops-key.pem
```

Additionally:
- Moved key from Windows filesystem to WSL home directory for proper Linux permission handling

---

# Problem 5 — Port 80 Already in Use

## Issue

```text
failed to bind host port 80
```

## Root Cause
Nginx service was already running on EC2 and occupying Port 80.

## Diagnosis

```bash
sudo lsof -i :80
```

## Resolution

```bash
sudo systemctl stop nginx
```

After stopping Nginx:
- Docker container deployed successfully

---

# Problem 6 — Website Not Accessible

## Issue
Application was not loading in browser.

## Root Cause
Incorrect protocol used:
```text
https://
```

while the application was hosted only on:
```text
http://
```

## Resolution
Used:
```text
http://<EC2-PUBLIC-IP>
```

---

# Key DevOps Concepts Learned

This project helped in understanding:

- Docker image lifecycle
- Containerization workflow
- Docker Hub registry usage
- Linux permissions and groups
- SSH authentication
- Cloud deployment workflow
- EC2 server management
- Port mapping and networking
- Infrastructure troubleshooting
- Service conflict debugging
- Public application hosting

---

# Future Improvements

Planned future enhancements include:

- GitHub Actions CI/CD Pipeline
- Automated EC2 Deployment
- Nginx Reverse Proxy Setup
- HTTPS using SSL Certificates
- Docker Compose Integration
- Monitoring & Logging
- Infrastructure as Code using Terraform
- Kubernetes Deployment

---

# Screenshots

Add screenshots for:
- EC2 instance
- Docker Hub repository
- Docker container running
- Public application access
- Terminal commands
- Architecture diagram

---

# Conclusion

This project provided practical exposure to real-world DevOps workflows involving:
- cloud infrastructure
- Docker containerization
- Linux administration
- deployment troubleshooting
- public application hosting

The implementation focused not only on successful deployment but also on understanding and resolving real operational challenges during the deployment lifecycle.

---

# Author

## Bhoomi Samadhiya

DevOps & Cloud Engineer 