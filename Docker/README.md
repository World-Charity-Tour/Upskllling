# Docker and Kubernetes Guide

This guide provides a comprehensive introduction to Docker and Kubernetes, covering everything from basic Docker concepts to deploying applications with Kubernetes. You'll learn about Docker images, containers, networks, and volumes, get an introduction to Kubernetes architecture and commands, and explore OVH Managed Kubernetes with an example deployment. By the end of this guide, you'll know how to create a container, serve it using Kubernetes services, and generate SSL certificates.

## Table of Contents

### Docker Section
- [Introduction to Docker](#introduction-to-docker)
  - [What is Docker?](#what-is-docker)
  - [Why Use Docker?](#why-use-docker)
  - [Docker Images](#docker-images)
  - [Docker Containers](#docker-containers)
  - [Docker Networks](#docker-networks)
  - [Docker Volumes](#docker-volumes)
- [Prerequisites](#prerequisites)
- [Step 1: Create and Set Up an OVH VPS](#step-1-create-and-set-up-an-ovh-vps)
- [Step 2: Install Docker](#step-2-install-docker)
- [Step 3: Run a "Hello World" Docker Container](#step-3-run-a-hello-world-docker-container)
- [Step 4: Clone the Frontend Project](#step-4-clone-the-frontend-project)
- [Step 5: Deploy the Frontend with Docker](#step-5-deploy-the-frontend-with-docker)
- [Step 6: Push the Docker Image to Docker Hub](#step-6-push-the-docker-image-to-docker-hub)
- [Step 7: Set Up Docker Compose](#step-7-set-up-docker-compose)
- [Step 8: Access the Application](#step-8-access-the-application)
- [Step 9: Cleanup (Optional)](#step-9-cleanup-optional)

### Kubernetes Section
- [Introduction to Kubernetes](#introduction-to-kubernetes)
  - [What is Kubernetes?](#what-is-kubernetes)
  - [Kubernetes Architecture](#kubernetes-architecture)
- [Basic Kubernetes Commands](#basic-kubernetes-commands)
- [Step 1: Create a Kubernetes Cluster](#step-1-create-a-kubernetes-cluster)
- [Step 2: Deploy a Container Using Kubernetes](#step-2-deploy-a-container-using-kubernetes)
- [Step 3: Expose the Container Using Kubernetes Services](#step-3-expose-the-container-using-kubernetes-services)
- [Step 4: Generate SSL Certificates and Secure Your Service](#step-4-generate-ssl-certificates-and-secure-your-service)
- [OVH Managed Kubernetes](#ovh-managed-kubernetes)
  - [What is OVH Managed Kubernetes?](#what-is-ovh-managed-kubernetes)
  - [Example Deployment on OVH Managed Kubernetes](#example-deployment-on-ovh-managed-kubernetes)
- [Conclusion](#conclusion)

---

## Docker Section

### Introduction to Docker

#### What is Docker?

Docker is a platform that enables developers to automate the deployment, scaling, and management of applications using containerization. Containers package software and its dependencies into a single, isolated unit, making it easy to run consistently across different environments.

#### Why Use Docker?

- **Portability**: Containers can run on any system that supports Docker, ensuring consistent environments across development, testing, and production.
- **Efficiency**: Containers are lightweight and use fewer resources compared to traditional virtual machines.
- **Scalability**: Docker makes it easy to scale applications horizontally by deploying multiple instances of containers.
- **Isolation**: Containers provide an isolated environment for applications, reducing conflicts between different software running on the same host.

### Docker Images

A Docker image is a read-only template that contains the application code, runtime, system tools, libraries, and settings needed to run an application. Images are the blueprint used to create Docker containers.

- **Creating Images**: Images are typically created from a Dockerfile, which specifies the base image and instructions for setting up the application.
- **Layers**: Images are made up of layers. Each command in a Dockerfile creates a new layer in the image, and layers are reused to optimize storage and performance.

### Docker Containers

A Docker container is a runnable instance of a Docker image. Containers encapsulate the application and its dependencies, running in an isolated environment.

- **Running Containers**: Containers are started from images and can be managed using Docker commands.
- **Isolation**: Each container runs in its own environment, isolated from other containers and the host system.
- **Lifecycle**: Containers can be started, stopped, paused, and removed as needed.

### Docker Networks

Docker networks allow containers to communicate with each other and with external systems. Docker provides several types of networks:

- **Bridge Network**: The default network type. Containers on the same bridge network can communicate with each other.
- **Host Network**: The container shares the host’s networking namespace, giving it access to the host’s network interfaces.
- **Overlay Network**: Used for multi-host networking, allowing containers running on different hosts to communicate securely.

### Docker Volumes

Docker volumes provide persistent storage for containers. Unlike container storage, which is ephemeral, volumes retain data even when the container is removed.

- **Creating Volumes**: Volumes can be created and managed independently of containers.
- **Mounting Volumes**: Volumes can be mounted to a container’s filesystem, allowing the container to read and write data to the volume.

---

## Prerequisites

Before you begin, ensure you have the following:

- An OVHcloud account
- Basic knowledge of Linux command-line operations
- SSH access to your OVH VPS
- A Docker Hub account
- Git installed on your local machine

## Step 1: Create and Set Up an OVH VPS

1. **Log in** to your OVHcloud Control Panel.
2. **Create a new VPS** with the following specifications:
   - **RAM**: 2 GB
   - **vCPUs**: 2
   - **Operating System**: Ubuntu 22.04 LTS or Debian 11
3. **SSH into your VPS** using the provided credentials:
   ```bash
   ssh root@<VPS_IP_ADDRESS>
   ```
4. **Update your system packages**:
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```

## Step 2: Install Docker

1. **Install Docker** by running the following commands:
   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io -y
   ```

2. **Verify Docker installation**:
   ```bash
   sudo docker --version
   ```

## Step 3: Run a "Hello World" Docker Container

Now that Docker is installed, you can run your first container to ensure everything is set up correctly.

1. **Run the Docker "Hello World" container**:
   ```bash
   sudo docker run hello-world
   ```

   This command pulls a simple Docker image from Docker Hub and runs it as a container. The container runs a script that prints "Hello from Docker!" along with some additional information.

   You should see a message indicating that Docker is installed and functioning correctly.

## Step 4: Clone the Frontend Project

Next, you will clone the frontend project repository to your VPS.

1. **Install Git** if it’s not already installed:
   ```bash
   sudo apt-get install git -y
   ```

2. **Clone the frontend project repository**:
   ```bash
   git clone <repository-url>
   cd <repository-folder>
   ```

   Replace `<repository-url>` with the actual URL of your frontend project’s repository.

## Step 5: Deploy the Frontend with Docker

With the project cloned, you can now create a Docker image and deploy it.

1. **Build the Docker image** for the frontend:
   ```bash
   sudo docker build -t frontend-project .
   ```

   This command creates a Docker image named `frontend-project` using the Dockerfile in the current directory.

2. **Run the Docker container**:
   ```bash
   sudo docker run -d -p 80:3000 --name frontend-project frontend-project
   ```

   This command runs the image as a container, mapping port 3000 inside the container to port 80 on the VPS.

3. **Verify the container is running**:
   ```bash
   sudo docker ps
   ```

   This command lists all running containers. You should see your `frontend-project` container listed.

## Step 6: Push the Docker Image to Docker Hub

Once you’ve verified the container is running correctly, you can push the Docker image to Docker Hub.

1. **Log in to Docker Hub** from the command line:
   ```bash
   sudo docker login
   ```

   Enter your Docker Hub username and password when prompted.

2. **Tag the Docker image** with your Docker Hub username and repository name:
   ```bash
   sudo docker tag frontend-project <your-dockerhub-username>/frontend-project:latest
   ```

   Replace `<your-dockerhub-username>` with your Docker Hub username.

3. **Push the image** to Docker Hub:
   ```bash
   sudo docker push <your-dockerhub-username>/frontend-project:latest
   ```

4. **Verify** the image is available on Docker Hub by visiting your Docker Hub repository.

## Step 7: Set Up Docker Compose

Docker Compose allows you to define and run multi-container Docker applications. Although this example uses a single container, Docker Compose simplifies the process of managing and scaling your containers.

1. **Create a `docker-compose.yml` file** in your project directory:
   ```yaml
   version: '3.8'

   services:
     frontend-project:
       image: <your-dockerhub-username>/frontend-project:latest
       ports:
         - "80:3000"
       environment:
         - NODE_ENV=production
         - REACT_APP_API_URL=<API_URL>
   ```

   Replace `<your-dockerhub-username>` with your Docker Hub username and `<API_URL>` with the actual URL of your backend API.

2. **Run Docker Compose** to start the services:
   ```bash
   sudo docker-compose up -d
   ```

   Docker Compose will pull the image from Docker Hub and start the container.

## Step 8: Access the Application

Once the container is running, you can access the application from a web browser.

1. **Open your web browser** and navigate to:
   ```
   http://<VPS_IP_ADDRESS>
   ```
   
   Replace `<VPS_IP_ADDRESS>` with the actual IP address of your VPS.

2. **Verify** that the frontend is accessible and functioning correctly.

## Step 9: Cleanup (Optional)

After completing your deployment, you may want to clean up the Docker environment.

1. **Stop and remove the Docker container**:
   ```bash
   sudo docker stop frontend-project
   sudo docker rm frontend-project
   ```

2. **Remove the Docker image**:
   ```bash
   sudo docker rmi frontend-project
   ```

3. **Bring down the Docker Compose services**:
   ```bash
   sudo docker-compose down
   ```

   This will stop and remove all containers defined in the `docker-compose.yml` file.

---

## Kubernetes Section

### Introduction to Kubernetes

#### What is Kubernetes?

Kubernetes, often abbreviated as K8s, is an open-source platform designed to automate deploying, scaling, and operating application containers. It manages containerized applications across a cluster of machines, providing a flexible and reliable way to scale and maintain applications in production.

#### Kubernetes Architecture

Kubernetes architecture is built around the following key components:

- **Cluster**: A set of nodes (machines) where Kubernetes runs.
- **Master Node**: The control plane that manages the Kubernetes cluster. It includes components like the API server, scheduler, controller manager, and etcd (a key-value store).
- **Worker Nodes**: These nodes run the containerized applications. They include components like kubelet, kube-proxy, and the container runtime (e.g., Docker).
- **Pods**: The smallest deployable units in Kubernetes, which can contain one or more containers.
- **Services**: An abstraction that defines a logical set of Pods and a policy to access them.

### Basic Kubernetes Commands

Here are some basic commands to interact with a Kubernetes cluster:

- **Get cluster information**:
  ```bash
  kubectl cluster-info
  ```
- **List all nodes in the cluster**:
  ```bash
  kubectl get nodes
  ```
- **Deploy an application**:
  ```bash
  kubectl apply -f <deployment-file>.yaml
  ```
- **Get the status of Pods**:
  ```bash
  kubectl get pods
  ```
- **Expose a deployment as a service**:
  ```bash
  kubectl expose deployment <deployment-name> --type=LoadBalancer --port=80 --target-port=8080
  ```

## Step 1: Create a Kubernetes Cluster

1. **Set up a Kubernetes cluster** using a managed service like Google Kubernetes Engine (GKE), Amazon Elastic Kubernetes Service (EKS), or Azure Kubernetes Service (AKS), or set up a local cluster using Minikube or kind.

2. **Install kubectl**, the command-line tool for Kubernetes:
   ```bash
   sudo apt-get install -y kubectl
   ```

3. **Configure kubectl** to connect to your Kubernetes cluster.

## Step 2: Deploy a Container Using Kubernetes

1. **Create a Deployment YAML file** (`deployment.yaml`):
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: frontend-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: frontend
     template:
       metadata:
         labels:
           app: frontend
       spec:
         containers:
         - name: frontend-container
           image: <your-dockerhub-username>/frontend-project:latest
           ports:
           - containerPort: 3000
   ```

   Replace `<your-dockerhub-username>` with your Docker Hub username.

2. **Deploy the container**:
   ```bash
   kubectl apply -f deployment.yaml
   ```

3. **Verify the deployment**:
   ```bash
   kubectl get deployments
   ```

## Step 3: Expose the Container Using Kubernetes Services

1. **Create a Service YAML file** (`service.yaml`):
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: frontend-service
   spec:
     selector:
       app: frontend
     ports:
       - protocol: TCP
         port: 80
         targetPort: 3000
     type: LoadBalancer
   ```

2. **Expose the deployment as a service**:
   ```bash
   kubectl apply -f service.yaml
   ```

3. **Get the external IP address** of the service:
   ```bash
   kubectl get services frontend-service
   ```

   Use this IP address to access your application.

## Step 4: Generate SSL Certificates and Secure Your Service

1. **Install cert-manager** (an add-on for Kubernetes that automates the management and issuance of TLS certificates):
   ```bash
   kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.5.3/cert-manager.yaml
   ```

2. **Create a ClusterIssuer** to define how certificates should be issued:
   ```yaml
   apiVersion: cert-manager.io/v1
   kind: ClusterIssuer
   metadata:
     name: letsencrypt
   spec:
     acme:
       server: https://acme-v02.api.letsencrypt.org/directory
       email: your-email@example.com
       privateKeySecretRef:
         name: letsencrypt-account-key
       solvers:
       - http01:
           ingress:
             class: nginx
   ```

3. **Create an Ingress Resource** to automatically generate and manage SSL certificates:
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: frontend-ingress
     annotations:
       cert-manager.io/cluster-issuer: "letsencrypt"
   spec:
     rules:
     - host: <your-domain.com>
       http:
         paths:
         - path: /
           pathType: Prefix
           backend:
             service:
               name: frontend-service
               port:
                 number: 80
     tls:
     - hosts:
       - <your-domain.com>
       secretName: frontend-tls
   ```

4. **Apply the Ingress Resource**:
   ```bash
   kubectl apply -f ingress.yaml
   ```

   Your application will now be served over HTTPS.

## OVH Managed Kubernetes

### What is OVH Managed Kubernetes?

OVH Managed Kubernetes is a fully managed Kubernetes service provided by OVHcloud. It simplifies the deployment, management, and scaling of containerized applications using Kubernetes. With OVH Managed Kubernetes, you don't need to worry about the underlying infrastructure, as OVH handles the control plane, networking, and node management.

### Example Deployment on OVH Managed Kubernetes

In this section, we'll go through an example of deploying a containerized application on OVH Managed Kubernetes.

1. **Create a Kubernetes Cluster in OVHcloud**:
   - Log in to your OVHcloud Control Panel.
   - Navigate to the **Public Cloud** section and create a new Kubernetes cluster.
   - Choose the desired region, number of nodes, and node specifications.
   - Once the cluster is created, download the kubeconfig file for accessing your cluster.

2. **Access the Cluster**:
   - Move the kubeconfig file to the `~/.kube` directory:
     ```bash
     mv <path-to-kubeconfig-file> ~/.kube/config
     ```
   - Verify access to the cluster:
     ```bash
     kubectl cluster-info
     ```

3. **Deploy an Application**:
   - Create a Deployment YAML file (`deployment.yaml`):
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: ovh-frontend-deployment
     spec:
       replicas: 2
       selector:
         matchLabels:
           app: ovh-frontend
       template:
         metadata:
           labels:
             app: ovh-frontend
         spec:
           containers:
           - name: ovh-frontend-container
             image: <your-dockerhub-username>/frontend-project:latest
             ports:
             - containerPort: 3000
     ```
   - Deploy the application:
     ```bash
     kubectl apply -f deployment.yaml
     ```
   - Verify the deployment:
     ```bash
     kubectl get deployments
     ```

4. **Expose the Application**:
   - Create a Service YAML file (`service.yaml`):
     ```yaml
     apiVersion: v1
     kind: Service
     metadata:
       name: ovh-frontend-service
     spec:
       selector:
         app: ovh-frontend
       ports:
         - protocol: TCP
           port: 80
           targetPort: 3000
       type: LoadBalancer
     ```
   - Expose the deployment as a service:
     ```bash
     kubectl apply -f service.yaml
     ```
   - Retrieve the external IP address:
     ```bash
     kubectl get services ovh-frontend-service
     ```
   - Access your application using the external IP.

5. **Secure the Application with SSL**:
   - Install cert-manager and set up Ingress as described in the earlier section to secure your OVH Managed Kubernetes service with SSL.

## Conclusion

You have successfully completed a comprehensive session on Docker and Kubernetes. You've learned the basics of Docker, including images, containers, networks, and volumes. You’ve also deployed a Dockerized application to Kubernetes, explored OVH Managed Kubernetes, and secured your services with SSL certificates. This knowledge forms a solid foundation for working with containerized applications in production environments.
