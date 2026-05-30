# DevOps Assignment 3: 3-Tier Containerized & Orchestrated Architecture

## Project Overview

This repository contains the complete implementation for Assignment 3, focusing on containerizing, automating, and orchestrating a classic 3-tier microservice architecture. The application consists of an updated web proxy tier (**Nginx**), a backend application programming interface tier (**Flask API**), and a relational database storage engine tier (**MySQL**).

The core goal of this project was to transition the application from a raw local development environment into an automated, highly available, and self-healing infrastructure cluster using **Docker**, **GitHub Actions**, and **Kubernetes (Minikube)**.

---

## Architecture Blueprint

The system layout maps out how network traffic flows dynamically through the isolated cluster environments:

* **Edge Routing Tier:** A customized Nginx proxy server exposes an external `NodePort` endpoint on port `30080`. It intercepts incoming HTTP traffic, applies routing rules, and acts as a reverse proxy.
* **Application Compute Tier:** An isolated Flask API application processes dynamic database requests and provides a `/health` verification route. It scales dynamically up to 3 replicas to handle horizontal workloads.
* **Persistent Storage Tier:** A centralized MySQL 8.0 instance running within an isolated internal subnet. It connects to a persistent host-path block storage device to protect data across pod lifecycles.

---

## Repository Branching Strategy

The lifecycle tracking of this assignment was managed using an explicit branch isolation model. No development work was executed directly on the production tracks.

```
* 1d950a4 (HEAD -> main, develop, feature/k8s-deployment) feat: implement automated orchestration startup validation script
* 721d300 feat: implement persistent volume and storage claim binding for database lifecycle persistence
* 7ec7ca5 feat: deploy base64 encoded database secrets, plain-text configuration parameters, and nginx configmaps
* d7a9d10 feat: implemented internal ClusterIP and external NodePort service network endpoints
* fc32cd8 implemented K8s namespace and core deployment manifests for 3-tier microservice architecture
* c1e5ca4 Merge branch 'feature/docker-setup' into develop
* 80812e5 add GitHub Actions workflow for automated DockerHub compilation
* da48ab1 feat: completed and verified all docker requirements
* 4c35ac6 feat: added Flask API and Nginx source configuration files
* 60c3066 initial commit with .gitignore

```

---

## Directory Structure

```text
~/assignment3/
├── app/
│   ├── flask-api/
│   │   ├── app.py
│   │   ├── requirements.txt
│   │   └── Dockerfile
│   └── nginx/
│       ├── nginx.conf
│       └── Dockerfile
├── k8s/
│   ├── namespace.yml
│   ├── mysql-pv.yml
│   ├── mysql-pvc.yml
│   ├── mysql-secret.yml
│   ├── flask-configmap.yml
│   ├── nginx-configmap.yml
│   ├── mysql-deployment.yml
│   ├── flask-deployment.yml
│   ├── nginx-deployment.yml
│   ├── mysql-service.yml
│   ├── flask-service.yml
│   └── nginx-service.yml
├── .github/
│   └── workflows/
│       └── build-deploy.yml
├── .gitignore
├── README.md
└── start.sh

```

---

## Core Infrastructure Components

### 1. Automated CI/CD (GitHub Actions)

The integration mechanism tracks code modifications on the `develop` and `main` branches. When a push is executed, an automated workflow triggers a remote builder instance that:

* Sets up a secure Docker Buildx virtualization engine.
* Authenticates with the centralized remote image repository (**Docker Hub**).
* Compiles optimization layers for both the Flask API application container and the customized Nginx web server proxy container.
* Pushes the finalized binary images to the personal image registry (`ismailrzw/flask-api:latest` and `ismailrzw/nginx-proxy:latest`).

### 2. Kubernetes Resource Layer Definition

#### Foundational Network Isolations

* **`k8s/namespace.yml`**: Sets up a distinct logical partition called `assignment3` to keep all components safely separated from standard default system resources.

#### Permanent Data Volumes

* **`k8s/mysql-pv.yml` & `k8s/mysql-pvc.yml**`: Provisions a durable `manual` `PersistentVolume` mapping directly to the underlying host disk path `/data/mysql`. A matching resource allocation claim requests a 1Gi storage slice utilizing `ReadWriteOnce` access boundaries, guaranteeing that test metrics survive database application crashes.

#### Sensitive Variable Injections

* **`k8s/mysql-secret.yml`**: Encodes critical environmental backend variables (database root passwords, connection usernames, and user access phrases) via Base64 serialization algorithms to block plain-text leaks inside code files.
* **`k8s/flask-configmap.yml`**: Holds production configuration metadata parameters, explicitly binding routing endpoints to the internal DNS host resolution key `mysql`.

#### Compute Deployment Workloads

* **`k8s/mysql-deployment.yml`**: Sets up a dedicated instance running an official `mysql:8.0` server engine image. It bounds container ports to `3306`, binds secret references to systemic variable matrices, and links storage mounts to the data claim.
* **`k8s/flask-deployment.yml`**: Deploys the dynamic runtime API engine pulling directly from the updated Docker Hub repository. Features integrated `readinessProbe` routing to block request tracking until connection links clear, alongside an automated `livenessProbe` to recover from deadlocks.
* **`k8s/nginx-deployment.yml`**: Scales out an instances array pulling from the custom proxy repository. Mounts specialized configuration scripts using decoupled ConfigMaps to allow proxy routing modifications without rebuilding container images.

#### Cluster Service Endpoints

* **`k8s/mysql-service.yml`**: Exposes a localized internal `ClusterIP` route on port `3306`, completely isolating the raw relational engine from outside networks.
* **`k8s/flask-service.yml`**: Configures an internal internal proxy map on port `5000` to expose the microservice processing nodes to the edge servers.
* **`k8s/nginx-service.yml`**: Opens up an external edge routing `NodePort` endpoint tracking precisely across port `30080` to route consumer client traffic straight into the proxy clusters.

---

## Local Verification & Cluster Bootstrap Protocol

To rehydrate this environment inside a clean Ubuntu VirtualBox instance or verify operations locally on your host environment, execute the sequential run book outlined below:

### Phase 1: Environment Engine Activation

Ensure your internal local container runtimes and local cluster controllers are completely online and operational:

```bash
# Start Minikube with the local container runtime driver boundary
minikube start --driver=docker

# Grant absolute execution privileges to the centralized initialization automation wrapper script
chmod +x start.sh

```

### Phase 2: Structural Cluster Provisioning

Execute the automated wrapper compilation pipeline to load all configurations, bind structural components, and initialize application tracking loops:

```bash
./start.sh

```

Alternatively, you can apply the manifest tracking graph manually using the terminal toolbelt by following the strict dependency hierarchy order:

```bash
# 1. Instantiate the logical cluster room boundary
kubectl apply -f k8s/namespace.yml

# 2. Build out underlying persistent volume mappings 
kubectl apply -f k8s/mysql-pv.yml
kubectl apply -f k8s/mysql-pvc.yml

# 3. Mount credential arrays and non-volatile config settings
kubectl apply -f k8s/mysql-secret.yml
kubectl apply -f k8s/flask-configmap.yml
kubectl apply -f k8s/nginx-configmap.yml

# 4. Spin up the infrastructure workloads
kubectl apply -f k8s/mysql-deployment.yml
kubectl apply -f k8s/flask-deployment.yml
kubectl apply -f k8s/nginx-deployment.yml

# 5. Bring external and internal communication wires online
kubectl apply -f k8s/mysql-service.yml
kubectl apply -f k8s/flask-service.yml
kubectl apply -f k8s/nginx-service.yml

```

### Phase 3: Runtime Quality Audit Check

Confirm that all components have cleared initialization phases and have reached a functional execution status:

```bash
# Audit deployment scaling status tracking inside the project namespace
kubectl get deployments -n assignment3

# Audit physical pod operational health codes
kubectl get pods -n assignment3

# Inspect structural service endpoint routing tables
kubectl get services -n assignment3

```

---

## Operational Verification Playbook

### 1. Microservice Scaling Audit

To test horizontal expansion behaviors, run the scaling command to balance processing traffic across distinct nodes:

```bash
kubectl scale deployment flask-api --replicas=3 -n assignment3
kubectl get pods -n assignment3 -l app=flask-api

```

### 2. Rolling Update & Fallback (Zero-Downtime Verification)

Simulate an active version update, track its execution flow, and execute a rollback recovery path without breaking active cluster connectivity lines:

```bash
# Trigger an active update mapping to a temporary version marker
kubectl set image deployment/flask-api flask-api=python:3.11-slim -n assignment3

# Instantly watch deployment progression metrics
kubectl rollout status deployment/flask-api -n assignment3

# Execute a rollback to restore the stable production tracking image
kubectl rollout undo deployment/flask-api -n assignment3

# Verify that the cluster deployment config has cleanly reset to the production image
kubectl describe deployment flask-api -n assignment3 | grep Image

```

### 3. End-to-End Application Testing

Examine the entry routes using command-line interface web transfer tools:

```bash
# Acquire the live IP route allocated inside Minikube
minikube service nginx -n assignment3 --url

# Execute a structural health route ping
curl http://$(minikube ip):30080/health

# Post a test data point payload to the running cluster architecture
curl -X POST http://$(minikube ip):30080/api/items \
  -H "Content-Type: application/json" \
  -d '{"name": "Student Item", "description": "Verified on Ubuntu Local Environment"}'

# Read back saved metric structures from the cluster data tier
curl http://$(minikube ip):30080/api/items

```

---

## Safe Maintenance Protocol

When pausing project development tracks or before exporting environment configurations to protect local states against corruption, execute the safe parking sequence:

```bash
# Flush in-memory tracking structures down to your local physical storage drive
minikube stop

```
