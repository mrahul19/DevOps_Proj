# DevOps Product Management Application

A complete DevOps solution demonstrating CI/CD pipeline, containerization, orchestration, configuration management, and monitoring.

## Project Structure

```
DevOps_Proj/
├── src/main/java/com/devops/servlets/
│   ├── AddProductServlet.java        # Product add endpoint
│   └── DisplayProductServlet.java    # Product display endpoint
├── k8s/
│   ├── k8s-deployment.yml            # Kubernetes deployment with HPA
│   └── k8s-service.yml               # Kubernetes service & config
├── monitoring/
│   └── prometheus.yml                # Prometheus monitoring config
├── Dockerfile                        # Multi-stage container build
├── Jenkinsfile                       # CI/CD pipeline (9 stages)
├── docker-compose.yml                # Local development stack
├── deploy-playbook.yml               # Ansible deployment automation
├── pom.xml                           # Maven build configuration
├── init.sql                          # Database initialization
└── README.md
```

## 5 DevOps Tasks

### Task 1: CI/CD Pipeline (Jenkins)
- 9-stage pipeline: Checkout → Build → Test → Package → Code Analysis → Docker Build → Docker Push → Deploy → Verify
- Automated testing with JUnit report collection
- Docker image build and push to registry

### Task 2: Containerization (Docker)
- Multi-stage Dockerfile for optimized image size
- Docker Compose for local development with MySQL
- Health checks for container reliability

### Task 3: Orchestration (Kubernetes)
- Deployment with 2 replicas across the cluster
- Horizontal Pod Autoscaler (HPA) scaling at 70% CPU
- ConfigMaps and Secrets for configuration management
- Readiness and liveness probes

### Task 4: Configuration Management (Ansible)
- Automated server provisioning with Docker
- Zero-downtime container deployment
- Health verification after deployment

### Task 5: Monitoring (Prometheus)
- Application metrics scraping
- Node exporter for system metrics
- MySQL exporter for database metrics

## Quick Start

### Local Development
```bash
docker-compose up -d
```
Access the app at http://localhost:8080

### API Endpoints
- `POST /addProduct` - Add a new product (params: name, price, description)
- `GET /displayProducts` - List all products

## Prerequisites
- Java 17+
- Maven 3.9+
- Docker & Docker Compose
- Kubernetes cluster (for deployment)
- Jenkins (for CI/CD)
