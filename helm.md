# Helm Package Manager – Hands-on Lab (KIND Cluster)

This lab is designed to be shared with participants. It includes **commands**, **explanations**, and **special notes** specific to **KIND (Kubernetes IN Docker)** clusters running on Ubuntu Linux VMs.

---

## Important: Lab Environment Setup

### Prerequisites

Before starting this lab, ensure:

1. Your Ubuntu VM has Docker installed and running
2. The KIND cluster has been created using the provided `install_kind.sh` script
3. **You have completed the previous labs** (kubectl Commands, Pods, Services, Deployments)

> **Important**: This lab assumes familiarity with kubectl commands, Deployments, Services, ConfigMaps, and Secrets. If you haven't completed the previous labs, do those first.

### Verify Your Cluster is Running

```bash
# Check if kind cluster exists
kind get clusters

# Verify kubectl can communicate with the cluster
kubectl cluster-info

# Check nodes are ready
kubectl get nodes
```

You should see output similar to:

```
NAME                         STATUS   ROLES           AGE   VERSION
kind-cluster-control-plane   Ready    control-plane   10m   v1.29.0
kind-cluster-worker          Ready    <none>          10m   v1.29.0
```

---

## Learning Objectives

### Core Helm Concepts

- Understand what **Helm** is and why it's needed
- Learn the key Helm concepts: **Charts**, **Releases**, **Repositories**
- Install and configure the **Helm CLI**
- Understand the **Chart structure** and key files

### Practical Skills

- **Search** for charts in repositories
- **Install** applications using Helm charts
- **Customize** installations with **values files** and `--set` flags
- **Upgrade** and **rollback** releases
- **Uninstall** releases and clean up
- **List** and **inspect** deployed releases

### Intermediate Objectives (Optional)

- **Create your own Helm chart** from scratch
- Use **templates** and **built-in functions**
- Understand **dependencies** and subcharts
- Use **Helm hooks** for lifecycle management
- **Package** and **share** charts

---

## Part 1: Understanding Helm

### What is Helm?

Helm is the **package manager for Kubernetes**. Think of it like `apt` for Ubuntu or `npm` for Node.js, but for Kubernetes applications.

| Feature | Description |
|---------|-------------|
| **Package Management** | Bundle K8s manifests into reusable packages (charts) |
| **Templating** | Use variables and logic in manifests |
| **Release Management** | Track installations, upgrades, and rollbacks |
| **Dependency Management** | Define and manage chart dependencies |

### Why Use Helm?

| Problem Without Helm | Helm Solution |
|---------------------|---------------|
| Managing many YAML files | Single chart packages everything |
| Repeating similar configs | Templates with variables |
| Tracking what's deployed | Release history and status |
| Environment differences | Values files per environment |
| Sharing configurations | Chart repositories |
| Complex upgrades | `helm upgrade` with rollback |

### Key Helm Concepts

| Concept | Description | Analogy |
|---------|-------------|---------|
| **Chart** | Package containing K8s resource templates | Software package (.deb, .rpm) |
| **Release** | Installed instance of a chart | Installed application |
| **Repository** | Collection of charts | Package repository (apt repo) |
| **Values** | Configuration for a chart | Config file / environment variables |

### Helm Architecture (v3)

```
┌─────────────────────────────────────────────────────────────┐
│                        Helm CLI                              │
│                           │                                  │
│            ┌──────────────┴──────────────┐                  │
│            ▼                              ▼                  │
│    ┌───────────────┐              ┌───────────────┐         │
│    │  Chart Repo   │              │  Kubernetes   │         │
│    │   (remote)    │              │    Cluster    │         │
│    └───────────────┘              └───────────────┘         │
│            │                              ▲                  │
│            │     helm install/upgrade     │                  │
│            └──────────────────────────────┘                  │
│                                                              │
│    No Tiller required (Helm v3)                             │
└─────────────────────────────────────────────────────────────┘
```

> **Note**: Helm v2 required a server-side component called Tiller. Helm v3 removed this requirement and communicates directly with the Kubernetes API.

---

## Part 2: Installing Helm

### Check if Helm is Already Installed

```bash
helm version
```

If Helm is not installed, continue with the installation.

### Install Helm on Ubuntu

```bash
# Download and install the latest Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Verify installation
helm version
```

Expected output:

```
version.BuildInfo{Version:"v3.x.x", GitCommit:"...", GitTreeState:"clean", GoVersion:"..."}
```

### Alternative: Install Specific Version

```bash
# Download specific version
HELM_VERSION="v3.14.0"
curl -LO https://get.helm.sh/helm-${HELM_VERSION}-linux-amd64.tar.gz

# Extract and install
tar -zxvf helm-${HELM_VERSION}-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm

# Cleanup
rm -rf linux-amd64 helm-${HELM_VERSION}-linux-amd64.tar.gz

# Verify
helm version
```

### Configure Helm Autocomplete

```bash
# Enable bash completion
echo 'source <(helm completion bash)' >> ~/.bashrc
source ~/.bashrc

# Test autocomplete (type "helm " and press Tab)
helm <TAB><TAB>
```

---

## Part 3: Working with Chart Repositories

### Understanding Repositories

Helm charts are stored in repositories. The most popular public repository is **ArtifactHub** (https://artifacthub.io/).

### Add Popular Repositories

```bash
# Add the Bitnami repository (one of the most popular)
helm repo add bitnami https://charts.bitnami.com/bitnami

# Add the official stable charts (legacy, but still useful)
helm repo add stable https://charts.helm.sh/stable

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# List added repositories
helm repo list
```

### Update Repository Cache

```bash
# Update repository index (like apt update)
helm repo update
```

### Search for Charts

```bash
# Search in all added repositories
helm search repo nginx

# Search with more details
helm search repo nginx -l  # List all versions

# Search on ArtifactHub (online)
helm search hub wordpress
```

### View Chart Information

```bash
# Show chart details
helm show chart bitnami/nginx

# Show all information (chart + values + readme)
helm show all bitnami/nginx

# Show default values
helm show values bitnami/nginx
```

### Remove a Repository

```bash
# Remove a repository
helm repo remove stable
```

---

## Part 4: Installing Your First Chart

### Create a Lab Namespace

```bash
kubectl create namespace helm-lab
```

### Install NGINX Using Helm

```bash
# Basic installation
helm install my-nginx bitnami/nginx --namespace helm-lab

# Watch the deployment
kubectl get pods -n helm-lab --watch
```

Press `Ctrl+C` when the pod is Running.

### Understand the Install Command

```
helm install <release-name> <chart-name> [flags]
           │               │
           │               └── Chart from repository
           └── Your name for this installation
```

### Check Release Status

```bash
# List all releases
helm list -n helm-lab

# Get detailed release information
helm status my-nginx -n helm-lab

# Get release history
helm history my-nginx -n helm-lab
```

### View Created Resources

```bash
# See what Helm created
kubectl get all -n helm-lab -l app.kubernetes.io/instance=my-nginx
```

### Access the Application

```bash
# Port forward to test
kubectl port-forward -n helm-lab svc/my-nginx 8080:80 &
sleep 2

# Test the application
curl http://localhost:8080

# Stop port forwarding
pkill -f "port-forward.*my-nginx"
```

### Uninstall the Release

```bash
helm uninstall my-nginx -n helm-lab

# Verify cleanup
kubectl get all -n helm-lab
```

---

## Part 5: Customizing Installations with Values

### Understanding Values

Every Helm chart has a `values.yaml` file containing default configuration. You can override these values:

1. Using `--set` flag (command line)
2. Using `-f` or `--values` flag (values file)

### View Default Values

```bash
# Show all default values for nginx chart
helm show values bitnami/nginx | head -100
```

### Install with Custom Values (--set)

```bash
# Install with custom replica count and service type
helm install custom-nginx bitnami/nginx \
  --namespace helm-lab \
  --set replicaCount=2 \
  --set service.type=ClusterIP

# Verify customization
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=custom-nginx
kubectl get svc -n helm-lab -l app.kubernetes.io/instance=custom-nginx
```

### Install with Values File

```bash
mkdir -p ~/helm-lab
cd ~/helm-lab

# Create a custom values file
cat > nginx-values.yaml <<'EOF'
# Custom values for nginx installation

replicaCount: 3

image:
  tag: latest

service:
  type: NodePort
  nodePorts:
    http: 30080

resources:
  requests:
    memory: "64Mi"
    cpu: "50m"
  limits:
    memory: "128Mi"
    cpu: "100m"

# Custom labels for all resources
commonLabels:
  environment: lab
  team: platform
EOF

# Install using values file
helm install nginx-custom bitnami/nginx \
  --namespace helm-lab \
  -f nginx-values.yaml
```

### Verify Custom Configuration

```bash
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=nginx-custom
kubectl get svc -n helm-lab nginx-custom
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=nginx-custom --show-labels
```

### Combining --set and Values File

```bash
# Values file provides base config, --set overrides specific values
helm install nginx-override bitnami/nginx \
  --namespace helm-lab \
  -f nginx-values.yaml \
  --set replicaCount=1
```

The `--set` flag takes precedence over values file.

### Cleanup Part 5

```bash
helm uninstall custom-nginx nginx-custom nginx-override -n helm-lab
```

---

## Part 6: Upgrading and Rolling Back Releases

### Install a Release for Upgrade Testing

```bash
# Install nginx version
helm install upgrade-demo bitnami/nginx \
  --namespace helm-lab \
  --set replicaCount=2

# Wait for pods
kubectl wait --for=condition=Ready pod -l app.kubernetes.io/instance=upgrade-demo -n helm-lab --timeout=120s
```

### View Release History

```bash
helm history upgrade-demo -n helm-lab
```

### Upgrade the Release

```bash
# Upgrade with new values
helm upgrade upgrade-demo bitnami/nginx \
  --namespace helm-lab \
  --set replicaCount=3 \
  --set resources.requests.memory=128Mi

# Watch the rolling update
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=upgrade-demo --watch
```

Press `Ctrl+C` when complete.

### View Updated History

```bash
helm history upgrade-demo -n helm-lab
```

You'll see revision 2.

### Perform Another Upgrade

```bash
# Upgrade again
helm upgrade upgrade-demo bitnami/nginx \
  --namespace helm-lab \
  --set replicaCount=4

# Check history
helm history upgrade-demo -n helm-lab
```

### Rollback to Previous Version

```bash
# Rollback to revision 2
helm rollback upgrade-demo 2 -n helm-lab

# Verify rollback
helm history upgrade-demo -n helm-lab
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=upgrade-demo
```

### Rollback to First Version

```bash
# Rollback to revision 1
helm rollback upgrade-demo 1 -n helm-lab

# Verify
helm history upgrade-demo -n helm-lab
```

### Cleanup Part 6

```bash
helm uninstall upgrade-demo -n helm-lab
```

---

## Part 7: Generating Manifests Without Installing

### Helm Template (Dry Run)

Use `helm template` to render charts locally without installing:

```bash
# Generate manifests without installing
helm template test-nginx bitnami/nginx --namespace helm-lab > nginx-manifests.yaml

# View generated manifests
head -100 nginx-manifests.yaml

# Count generated resources
grep "^kind:" nginx-manifests.yaml | sort | uniq -c
```

### Dry Run with Server Validation

```bash
# Dry run that validates against the cluster
helm install dry-run-test bitnami/nginx \
  --namespace helm-lab \
  --dry-run
```

### Get Manifests from Installed Release

```bash
# First install something
helm install manifests-demo bitnami/nginx --namespace helm-lab

# Get the manifests that were applied
helm get manifest manifests-demo -n helm-lab

# Get the values used
helm get values manifests-demo -n helm-lab

# Get all release info
helm get all manifests-demo -n helm-lab

# Cleanup
helm uninstall manifests-demo -n helm-lab
```

---

## Part 8: Creating Your Own Helm Chart

### Create a New Chart

```bash
cd ~/helm-lab

# Create chart scaffolding
helm create myapp

# View the structure
tree myapp || find myapp -type f
```

### Chart Structure Explained

```
myapp/
├── Chart.yaml          # Chart metadata (name, version, description)
├── values.yaml         # Default configuration values
├── charts/             # Dependencies (subcharts)
├── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt       # Post-installation notes
│   ├── _helpers.tpl    # Template helper functions
│   └── tests/
│       └── test-connection.yaml
└── .helmignore         # Files to ignore when packaging
```

### View Key Files

```bash
# View Chart.yaml
cat myapp/Chart.yaml

# View values.yaml
cat myapp/values.yaml
```

### Understanding Templates

```bash
# View the deployment template
cat myapp/templates/deployment.yaml
```

Key template syntax:

| Syntax | Description | Example |
|--------|-------------|---------|
| `{{ .Values.xxx }}` | Access values.yaml | `{{ .Values.replicaCount }}` |
| `{{ .Release.Name }}` | Release name | `{{ .Release.Name }}-nginx` |
| `{{ .Chart.Name }}` | Chart name | `{{ .Chart.Name }}` |
| `{{ include "xxx" . }}` | Include helper template | `{{ include "myapp.fullname" . }}` |
| `{{- if .Values.xxx }}` | Conditional | `{{- if .Values.ingress.enabled }}` |
| `{{- range .Values.xxx }}` | Loop | `{{- range .Values.env }}` |

### Modify the Chart

Let's simplify and customize the chart:

```bash
# Update Chart.yaml
cat > myapp/Chart.yaml <<'EOF'
apiVersion: v2
name: myapp
description: A simple web application chart for learning Helm
type: application
version: 0.1.0
appVersion: "1.0.0"
maintainers:
  - name: Lab User
    email: user@example.com
EOF
```

```bash
# Update values.yaml
cat > myapp/values.yaml <<'EOF'
# Default values for myapp

replicaCount: 1

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "alpine"

service:
  type: ClusterIP
  port: 80

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi

# Application-specific config
app:
  name: "My Helm App"
  environment: "development"
  message: "Hello from Helm!"
EOF
```

### Create a Custom ConfigMap Template

```bash
cat > myapp/templates/configmap.yaml <<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "myapp.fullname" . }}-config
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
data:
  APP_NAME: {{ .Values.app.name | quote }}
  ENVIRONMENT: {{ .Values.app.environment | quote }}
  MESSAGE: {{ .Values.app.message | quote }}
EOF
```

### Update Deployment to Use ConfigMap

```bash
cat > myapp/templates/deployment.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "myapp.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "myapp.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          envFrom:
            - configMapRef:
                name: {{ include "myapp.fullname" . }}-config
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
          livenessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 5
            periodSeconds: 5
EOF
```

### Validate the Chart

```bash
# Lint the chart (check for errors)
helm lint myapp

# Render templates locally
helm template test-release myapp
```

### Install Your Custom Chart

```bash
# Install from local directory
helm install my-release ./myapp --namespace helm-lab

# Wait for pod
kubectl wait --for=condition=Ready pod -l app.kubernetes.io/instance=my-release -n helm-lab --timeout=60s

# Check resources
kubectl get all -n helm-lab -l app.kubernetes.io/instance=my-release
kubectl get configmap -n helm-lab -l app.kubernetes.io/instance=my-release

# View ConfigMap
kubectl get configmap my-release-myapp-config -n helm-lab -o yaml
```

### Upgrade with Different Values

```bash
# Upgrade with production values
helm upgrade my-release ./myapp \
  --namespace helm-lab \
  --set app.environment=production \
  --set app.message="Hello from Production!" \
  --set replicaCount=2

# Verify changes
kubectl get configmap my-release-myapp-config -n helm-lab -o yaml
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=my-release
```

### Cleanup Part 8

```bash
helm uninstall my-release -n helm-lab
```

---

## Part 9: Packaging and Sharing Charts

### Package Your Chart

```bash
cd ~/helm-lab

# Package the chart
helm package myapp

# View the package
ls -la myapp-*.tgz
```

### Install from Package

```bash
# Install from .tgz file
helm install from-package ./myapp-0.1.0.tgz --namespace helm-lab

# Verify
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=from-package

# Cleanup
helm uninstall from-package -n helm-lab
```

### Create a Chart Repository Index

```bash
# Create a repository directory
mkdir -p ~/helm-repo
cp myapp-0.1.0.tgz ~/helm-repo/

# Generate index file
helm repo index ~/helm-repo

# View index
cat ~/helm-repo/index.yaml
```

### Serve the Repository Locally

```bash
# Start a simple HTTP server (in background)
cd ~/helm-repo
python3 -m http.server 8888 &
HTTP_PID=$!
cd ~/helm-lab

# Add the local repository
helm repo add local-repo http://localhost:8888

# Update and search
helm repo update
helm search repo local-repo
```

### Install from Local Repository

```bash
# Install from local repo
helm install from-repo local-repo/myapp --namespace helm-lab

# Verify
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=from-repo

# Cleanup
helm uninstall from-repo -n helm-lab
helm repo remove local-repo
kill $HTTP_PID
```

---

## Part 10: Real-World Chart Installation

### Install WordPress (Complex Application)

```bash
# Install WordPress with MySQL
helm install my-wordpress bitnami/wordpress \
  --namespace helm-lab \
  --set wordpressUsername=admin \
  --set wordpressPassword=adminpassword \
  --set wordpressEmail=admin@example.com \
  --set service.type=ClusterIP \
  --set mariadb.primary.persistence.enabled=false \
  --set persistence.enabled=false \
  --set resources.requests.memory=256Mi \
  --set resources.requests.cpu=100m

# This will take a few minutes
echo "WordPress is deploying... this takes 2-3 minutes"
```

### Watch Deployment Progress

```bash
# Watch pods
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=my-wordpress --watch
```

Press `Ctrl+C` when all pods are Running.

### Access WordPress

```bash
# Port forward
kubectl port-forward -n helm-lab svc/my-wordpress 8080:80 &
sleep 3

# Test access
curl -I http://localhost:8080

echo ""
echo "WordPress is available at http://localhost:8080"
echo "Login: admin / adminpassword"
```

### View WordPress Resources

```bash
# See all resources created
kubectl get all -n helm-lab -l app.kubernetes.io/instance=my-wordpress
kubectl get secrets -n helm-lab -l app.kubernetes.io/instance=my-wordpress
kubectl get configmaps -n helm-lab -l app.kubernetes.io/instance=my-wordpress
kubectl get pvc -n helm-lab -l app.kubernetes.io/instance=my-wordpress
```

### Cleanup WordPress

```bash
# Stop port forward
pkill -f "port-forward.*my-wordpress"

# Uninstall WordPress
helm uninstall my-wordpress -n helm-lab
```

---

## Scenario-Based Exercises: "CloudStore" E-Commerce Platform

You're the DevOps lead at **CloudStore Inc.**, tasked with deploying their microservices platform using Helm.

```bash
# Setup workspace
cd ~/helm-lab
```

---

### Exercise 1: Deploy the Product Catalog Service

**Scenario**: Deploy the Product Catalog API, which is a simple web service.

```bash
# Create values file for product catalog
cat > product-catalog-values.yaml <<'EOF'
replicaCount: 2

image:
  repository: nginx
  tag: alpine

service:
  type: ClusterIP
  port: 80

resources:
  requests:
    memory: "64Mi"
    cpu: "50m"
  limits:
    memory: "128Mi"
    cpu: "100m"

commonLabels:
  app: cloudstore
  component: product-catalog
  version: "1.0"
EOF

# Install using Bitnami nginx as base
helm install product-catalog bitnami/nginx \
  --namespace helm-lab \
  -f product-catalog-values.yaml

# Verify
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=product-catalog
kubectl get svc -n helm-lab product-catalog
```

---

### Exercise 2: Deploy Redis Cache

**Scenario**: CloudStore needs a Redis cache for session management and caching.

```bash
# Add Redis with specific configuration
helm install cloudstore-cache bitnami/redis \
  --namespace helm-lab \
  --set architecture=standalone \
  --set auth.password=cloudstore123 \
  --set master.persistence.enabled=false \
  --set master.resources.requests.memory=64Mi \
  --set master.resources.requests.cpu=50m

# Wait for Redis
kubectl wait --for=condition=Ready pod -l app.kubernetes.io/instance=cloudstore-cache -n helm-lab --timeout=120s

# Test Redis connection
REDIS_POD=$(kubectl get pods -n helm-lab -l app.kubernetes.io/instance=cloudstore-cache -o jsonpath='{.items[0].metadata.name}')
kubectl exec -n helm-lab $REDIS_POD -- redis-cli -a cloudstore123 ping
```

---

### Exercise 3: Create Custom CloudStore Chart

**Scenario**: Create a custom Helm chart for the CloudStore frontend.

```bash
# Create the chart
helm create cloudstore-frontend
```

```bash
# Customize values.yaml
cat > cloudstore-frontend/values.yaml <<'EOF'
replicaCount: 2

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "alpine"

service:
  type: NodePort
  port: 80
  nodePort: 30090

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi

# CloudStore specific config
cloudstore:
  siteName: "CloudStore"
  environment: "production"
  apiEndpoint: "http://product-catalog"
  cacheEndpoint: "cloudstore-cache-master:6379"
EOF
```

```bash
# Add a ConfigMap template
cat > cloudstore-frontend/templates/configmap.yaml <<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "cloudstore-frontend.fullname" . }}-config
  labels:
    {{- include "cloudstore-frontend.labels" . | nindent 4 }}
data:
  SITE_NAME: {{ .Values.cloudstore.siteName | quote }}
  ENVIRONMENT: {{ .Values.cloudstore.environment | quote }}
  API_ENDPOINT: {{ .Values.cloudstore.apiEndpoint | quote }}
  CACHE_ENDPOINT: {{ .Values.cloudstore.cacheEndpoint | quote }}
EOF
```

```bash
# Install the custom chart
helm install cloudstore-frontend ./cloudstore-frontend --namespace helm-lab

# Verify
kubectl get all -n helm-lab -l app.kubernetes.io/instance=cloudstore-frontend
kubectl get configmap -n helm-lab cloudstore-frontend-config -o yaml
```

---

### Exercise 4: Upgrade with Environment-Specific Values

**Scenario**: Create staging and production configurations.

```bash
# Create staging values
cat > cloudstore-staging.yaml <<'EOF'
replicaCount: 1

cloudstore:
  siteName: "CloudStore (STAGING)"
  environment: "staging"
  apiEndpoint: "http://staging-api"
EOF

# Create production values
cat > cloudstore-prod.yaml <<'EOF'
replicaCount: 3

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi

cloudstore:
  siteName: "CloudStore"
  environment: "production"
  apiEndpoint: "http://product-catalog"
EOF

# Upgrade to staging config
helm upgrade cloudstore-frontend ./cloudstore-frontend \
  --namespace helm-lab \
  -f cloudstore-staging.yaml

# Verify staging
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=cloudstore-frontend
kubectl get configmap -n helm-lab cloudstore-frontend-config -o yaml | grep -E "SITE_NAME|ENVIRONMENT"

# Upgrade to production config
helm upgrade cloudstore-frontend ./cloudstore-frontend \
  --namespace helm-lab \
  -f cloudstore-prod.yaml

# Verify production
kubectl get pods -n helm-lab -l app.kubernetes.io/instance=cloudstore-frontend
```

---

### Exercise 5: View Complete CloudStore Stack

```bash
echo "═══════════════════════════════════════════════════════"
echo "        CloudStore E-Commerce Platform Status          "
echo "═══════════════════════════════════════════════════════"
echo ""

echo "📦 Helm Releases:"
helm list -n helm-lab

echo ""
echo "🚀 All Pods:"
kubectl get pods -n helm-lab

echo ""
echo "🌐 Services:"
kubectl get svc -n helm-lab

echo ""
echo "📊 Release History - Frontend:"
helm history cloudstore-frontend -n helm-lab
```

---

### Cleanup Exercises

```bash
# Uninstall all CloudStore components
helm uninstall product-catalog cloudstore-cache cloudstore-frontend -n helm-lab

# Verify cleanup
kubectl get all -n helm-lab
```

---

## Key Takeaways

### Helm Basics

- **Helm** is the package manager for Kubernetes
- **Charts** package K8s resources for easy deployment
- **Releases** are instances of installed charts
- **Values** customize chart installations

### Working with Charts

- Use `helm search` to find charts
- Use `helm show values` to see configuration options
- Use `--set` or `-f values.yaml` to customize
- Use `helm template` to preview without installing

### Managing Releases

- `helm install` creates new releases
- `helm upgrade` updates existing releases
- `helm rollback` reverts to previous versions
- `helm uninstall` removes releases

### Creating Charts

- Use `helm create` to scaffold new charts
- Templates use Go templating syntax
- `values.yaml` provides default configuration
- Always lint and test charts before publishing

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `helm repo add <name> <url>` | Add a chart repository |
| `helm repo update` | Update repository cache |
| `helm search repo <keyword>` | Search for charts |
| `helm show values <chart>` | Show default values |
| `helm install <release> <chart>` | Install a chart |
| `helm install <release> <chart> -f values.yaml` | Install with values file |
| `helm install <release> <chart> --set key=value` | Install with inline values |
| `helm list` | List releases |
| `helm status <release>` | Show release status |
| `helm history <release>` | Show release history |
| `helm upgrade <release> <chart>` | Upgrade a release |
| `helm rollback <release> <revision>` | Rollback to revision |
| `helm uninstall <release>` | Remove a release |
| `helm template <release> <chart>` | Render templates locally |
| `helm lint <chart>` | Check chart for errors |
| `helm create <name>` | Create new chart |
| `helm package <chart>` | Package chart to .tgz |
| `helm get manifest <release>` | Get release manifests |
| `helm get values <release>` | Get release values |

### Common Flags

| Flag | Description |
|------|-------------|
| `-n, --namespace` | Kubernetes namespace |
| `-f, --values` | Specify values file |
| `--set` | Set values on command line |
| `--dry-run` | Simulate installation |
| `--wait` | Wait for resources to be ready |
| `--timeout` | Time to wait for operation |
| `--create-namespace` | Create namespace if missing |

### Values File Template

```yaml
# values.yaml
replicaCount: 2

image:
  repository: myapp
  tag: "1.0.0"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi

env:
  - name: APP_ENV
    value: production
```

---

## Cleanup (End of Lab)

```bash
# Uninstall all releases in helm-lab namespace
helm list -n helm-lab -q | xargs -r helm uninstall -n helm-lab

# Delete the namespace
kubectl delete namespace helm-lab

# Remove lab files
cd ~
rm -rf ~/helm-lab
rm -rf ~/helm-repo

# Verify cleanup
helm list -A
kubectl get namespaces | grep helm
```

---

## Troubleshooting Common Issues

### Chart Not Found

```bash
# Update repositories
helm repo update

# Search with debug
helm search repo <name> --debug

# Check repo is added
helm repo list
```

### Installation Fails

```bash
# Use --debug for more information
helm install <release> <chart> --debug

# Check events
kubectl get events -n <namespace> --sort-by='.lastTimestamp'

# Check pod logs
kubectl logs -n <namespace> <pod-name>
```

### Upgrade Fails

```bash
# Check current release status
helm status <release> -n <namespace>

# Get manifest to see current state
helm get manifest <release> -n <namespace>

# Force upgrade (use with caution)
helm upgrade <release> <chart> --force
```

### Rollback Issues

```bash
# Check revision history
helm history <release> -n <namespace>

# Get values from specific revision
helm get values <release> -n <namespace> --revision <number>

# Rollback with debug
helm rollback <release> <revision> -n <namespace> --debug
```

### Template Errors

```bash
# Lint the chart
helm lint <chart-path>

# Render templates to see errors
helm template test <chart-path> --debug

# Check specific values
helm template test <chart-path> --set key=value
```

### Values Not Applied

```bash
# Check actual values used
helm get values <release> -n <namespace>

# Compare with defaults
helm show values <chart>

# Use --dry-run to verify
helm upgrade <release> <chart> -f values.yaml --dry-run
```

---

## Additional Resources

- [Helm Documentation](https://helm.sh/docs/)
- [Artifact Hub](https://artifacthub.io/) - Find charts
- [Chart Best Practices](https://helm.sh/docs/chart_best_practices/)
- [Helm Template Guide](https://helm.sh/docs/chart_template_guide/)
- [Bitnami Charts](https://github.com/bitnami/charts) - Popular chart repository
- [CKAD Helm Tasks](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
