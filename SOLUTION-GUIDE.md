# DevOps Solution Guide - Step by Step

## Task 1: CI/CD Pipeline with Jenkins

### Step 1: Install Jenkins
```bash
# Using Docker
docker run -d -p 8081:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

### Step 2: Configure Jenkins
1. Access Jenkins at http://localhost:8081
2. Install suggested plugins + Docker Pipeline plugin
3. Add credentials:
   - `dockerhub` - Docker Hub username/password
   - `kubeconfig` - Kubernetes config file

### Step 3: Create Pipeline
1. New Item → Pipeline
2. Point to SCM: `https://github.com/mrahul19/DevOps_Proj.git`
3. Script path: `Jenkinsfile`

### Step 4: Pipeline Stages
The Jenkinsfile defines 9 stages:
1. **Checkout** - Clone source code
2. **Build** - Compile Java code with Maven
3. **Unit Test** - Run JUnit tests
4. **Package** - Create WAR file
5. **Code Analysis** - Run Checkstyle
6. **Docker Build** - Build container image
7. **Docker Push** - Push to Docker Hub
8. **Deploy to Kubernetes** - Apply K8s manifests
9. **Verify Deployment** - Check rollout status

---

## Task 2: Containerization with Docker

### Step 1: Build the Image
```bash
docker build -t mrahul19/devops-product-app:latest .
```

### Step 2: Run with Docker Compose
```bash
docker-compose up -d
```
This starts:
- **app** - Tomcat with the WAR file on port 8080
- **mysql** - MySQL 8.0 with initialized database

### Step 3: Verify
```bash
curl http://localhost:8080/displayProducts
curl -X POST "http://localhost:8080/addProduct?name=Laptop&price=999.99&description=Gaming+Laptop"
```

### Dockerfile Breakdown
- **Stage 1 (build)**: Maven builds the WAR file
- **Stage 2 (runtime)**: Tomcat serves the application
- Health check ensures container readiness

---

## Task 3: Kubernetes Orchestration

### Step 1: Create Namespace
```bash
kubectl apply -f k8s/k8s-deployment.yml
```

### Step 2: Deploy Services
```bash
kubectl apply -f k8s/k8s-service.yml
```

### Step 3: Verify
```bash
kubectl get pods -n devops
kubectl get svc -n devops
kubectl get hpa -n devops
```

### Key Features
- **Deployment**: 2 replicas with rolling update strategy
- **HPA**: Auto-scales 2-5 pods based on CPU (70% threshold)
- **ConfigMap**: Externalizes database URL
- **Secret**: Secures database credentials
- **Probes**: Readiness (30s) and liveness (60s) checks

---

## Task 4: Ansible Configuration Management

### Step 1: Setup Inventory
Create `inventory.ini`:
```ini
[app_servers]
server1 ansible_host=192.168.1.10 ansible_user=ubuntu
server2 ansible_host=192.168.1.11 ansible_user=ubuntu
```

### Step 2: Run Playbook
```bash
ansible-playbook -i inventory.ini deploy-playbook.yml
```

### Playbook Tasks
1. Install Docker dependencies
2. Start Docker service
3. Pull latest application image
4. Stop existing container
5. Deploy new container
6. Wait for application health
7. Verify deployment

---

## Task 5: Monitoring with Prometheus

### Step 1: Deploy Prometheus
```bash
docker run -d -p 9090:9090 \
  -v $(pwd)/monitoring/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```

### Step 2: Deploy Grafana
```bash
docker run -d -p 3000:3000 grafana/grafana
```

### Step 3: Configure Dashboards
1. Access Grafana at http://localhost:3000 (admin/admin)
2. Add Prometheus data source: http://prometheus:9090
3. Import dashboard for JVM metrics (ID: 4701)

### Monitored Targets
- **devops-app**: Application metrics via `/actuator/prometheus`
- **node-exporter**: System-level metrics (CPU, memory, disk)
- **mysql-exporter**: Database metrics (connections, queries)

---

## Testing the Complete Pipeline

### End-to-End Test
```bash
# 1. Start local stack
docker-compose up -d

# 2. Add a product
curl -X POST "http://localhost:8080/addProduct?name=TestProduct&price=19.99&description=Test"

# 3. Verify product
curl http://localhost:8080/displayProducts

# 4. Check monitoring
open http://localhost:9090  # Prometheus
open http://localhost:3000  # Grafana
```

### Cleanup
```bash
docker-compose down -v
kubectl delete namespace devops
```
