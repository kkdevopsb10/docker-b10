# Docker & Kubernetes Introduction

## Syllabus

### Docker
- Introduction to Docker  
- Containerization vs Virtualization  
- Docker Installation & Architecture  
- Docker Adhoc Commands  
- Dockerfile & Its Keywords  
- Docker Images & Image Creation  
- Docker Containers  
- Docker Networks  
- Docker Volumes  
- Docker Registries (Docker Hub, ECR, Nexus)  
- Docker Compose  

### Kubernetes
- Introduction to Kubernetes  
- Kubernetes Architecture  
- Kubernetes Cluster (Self-Managed) Setup Using Kubeadm  
- Kubernetes Namespaces  
- Kubernetes Objects:  
  - Pods  
  - Replication Controller  
  - Replica Set  
  - Daemon Set  
  - Deployment  
  - Rolling Update & Recreate Strategies  
  - Stateful Sets  
  - Services  
  - Persistent Volumes & Persistent Volume Claims  
  - Dynamic Volumes  
  - ConfigMaps & Secrets  
  - Horizontal Pod Autoscaler (HPA) & Metrics Server  
  - Liveness & Readiness Probes  
- **Scheduling & Maintenance:**  
  - Node Selector  
  - Node Affinity  
  - Pod Affinity & Anti-Affinity  
  - Taints & Tolerations  
  - Resource Quotas & Limit Ranges  
  - Network Policies  
- **EKS Kubernetes Cluster Setup**  
- Load Balancer Service  
- Ingress Controller & Resource  
- Kubernetes RBAC (Role-Based Access Control)  
- Deploying Applications using Jenkins (CI/CD)  
- Helm Package Manager  
- Monitoring Kubernetes with Prometheus & Grafana  

---

## What is Docker?
Docker is a containerization platform that allows developers to package applications and their dependencies into a standardized unit called a **container**. This ensures consistency across different computing environments and improves efficiency in application deployment and scaling.  

### Similar Tools to Docker:
- **Podman**  
- **Rkt (Rocket)**  
- **LXC (Linux Containers)**  
- **Singularity**  

### Docker Editions:
1. **Community Edition (CE):** Open-source and free to use.  
2. **Enterprise Edition (EE):** Commercial version with advanced security and support features.  

**Vendor:** Docker Inc.  
**Type:** Containerization  

### Related Technologies:
- **SCM Tools:** Git  
- **Build Tools:** Ant, Maven, Gradle  
- **CI/CD Tools:** Jenkins, Bamboo, Travis CI  
- **Containerization Tools:** Docker, Podman, Rkt  

**Cross-Platform:** Docker is supported on Linux, Windows, and macOS.  

---

## Installing Docker

**Official Website:** [https://www.docker.com/](https://www.docker.com/)  
**Documentation:** [https://docs.docker.com/](https://docs.docker.com/)  

### Installation Steps (Ubuntu EC2 Instance):

1. **Create an EC2 instance** (Ubuntu-based)  
2. **Connect to the server** using SSH  
3. **Install Docker using one of the following methods:**  

#### Method 1: Using Docker’s official script  
```sh
sudo apt update -y
sudo curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
```

#### Method 2: Installing from Ubuntu Repository  
```sh
sudo apt update -y
sudo apt install docker.io -y
```

#### Method 3: Running Docker’s install command directly  
```sh
sudo apt update -y
sudo curl -fsSL https://get.docker.com | /bin/bash
```

4. **Verify installation**  
```sh
docker -v
```

5. **Check Docker service status**  
```sh
ps -ef | grep -i "dockerd"
sudo service docker status
```

6. **Fix Docker permission issues**  
```sh
sudo usermod -aG docker ubuntu
```
**(After this, log out and log back in for changes to take effect.)**  

7. **Confirm that Docker runs without sudo**  
```sh
docker ps
```

---

## Docker Architecture

![Docker Architecture](../images/docker-architecture.png)

### 1. Docker Engine
**Docker Engine** is the core component responsible for building, running, and managing containers. It consists of:  
- **Docker Daemon (dockerd):** Runs in the background, manages containers, images, and networking.  
- **Docker API:** Enables interaction between the daemon and the Docker CLI or other programs.  

### 2. Docker CLI (Command-Line Interface)
The CLI allows users to interact with Docker. Common commands include:  
```sh
docker run
docker build
docker ps
docker images
```

### 3. Docker Images
Docker images are the templates used to create containers. They are:  
- **Immutable** (cannot be modified once built)  
- **Layered** (built from multiple layers for efficiency)  
- **Version-controlled**  

### 4. Docker Containers
Containers are lightweight, portable, and run applications in an isolated environment.  
- Share the host OS kernel but have separate filesystem, processes, and network.  
- Created from Docker images using `docker run`.  

### 5. Docker Registry
A registry is a centralized storage system for Docker images.  
- **Public Registry:** Docker Hub (default)  
- **Private Registries:** Nexus, JFrog Artifactory, AWS ECR  

---

## Managed Docker Registries

- **AWS ECR (Elastic Container Registry)** – Amazon’s managed registry for AWS  
- **Azure ACR (Azure Container Registry)** – Microsoft Azure’s container registry  
- **GCP GCR (Google Container Registry)** – Google Cloud’s registry  

### Docker Repository
A Docker **repository** is a collection of images with the same name but different tags (versions).  

---

## Next Steps
- Learn how to create and manage Docker images  
- Deploy containers in real-world applications  
- Use Kubernetes to orchestrate Docker containers  
- Implement CI/CD pipelines with Docker and Kubernetes  

---
