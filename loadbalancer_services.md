# Kubernetes LoadBalancer Services – Hands-on Lab (KIND Cluster)

This lab is designed to be shared with participants. It includes **commands**, **explanations**, and **special notes** specific to **KIND (Kubernetes IN Docker)** clusters running on Ubuntu Linux VMs.

---

## Important: Lab Environment Setup

### Prerequisites

Before starting this lab, ensure:

1. Your Ubuntu VM has Docker installed and running
2. The KIND cluster has been created using the provided `install_kind.sh` script
3. **You have completed the kubectl Commands Lab, Pods Lab, and Services Lab** (this lab builds on those skills)

> **Important**: This lab assumes familiarity with basic kubectl commands, Pod concepts, and Service basics (ClusterIP, NodePort). If you haven't completed the previous labs, do those first.

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

- Understand **why LoadBalancer services don't work by default** in KIND/local clusters
- Install and configure **MetalLB** to enable LoadBalancer support
- Create and test **LoadBalancer services** that provide external IPs
- Understand **IP address pools** and how MetalLB assigns addresses
- Compare **LoadBalancer vs NodePort** for external access
- Implement **Layer 2 mode** for local/development clusters

### Intermediate Objectives (Optional)

- Configure **multiple IP address pools** for different services
- Understand **LoadBalancer annotations** for advanced configurations
- Explore **traffic policies** for LoadBalancer services
- Create **complete application stacks** with LoadBalancer access

---

## What is a LoadBalancer Service?

A LoadBalancer service is the **standard way to expose a service externally** in Kubernetes. It provisions an external IP address that routes traffic to your Pods.

### How LoadBalancer Works in Cloud Environments

| Step | Description |
|------|-------------|
| 1 | You create a Service with `type: LoadBalancer` |
| 2 | Kubernetes requests an external IP from the cloud provider |
| 3 | Cloud provider provisions a load balancer (AWS ELB, GCP LB, Azure LB) |
| 4 | External IP is assigned to the service |
| 5 | Traffic flows: External IP → Load Balancer → NodePort → Pods |

### The Problem with Local Clusters

In local/bare-metal environments like KIND:

| Challenge | Description |
|-----------|-------------|
| **No cloud integration** | KIND doesn't have a cloud provider to provision load balancers |
| **Pending forever** | LoadBalancer services stay in `Pending` state indefinitely |
| **No external IP** | The `EXTERNAL-IP` column shows `<pending>` |

### The Solution: MetalLB

**MetalLB** is a load balancer implementation for bare-metal Kubernetes clusters. It provides:

| Feature | Description |
|---------|-------------|
| **External IPs** | Assigns real external IPs to LoadBalancer services |
| **Layer 2 Mode** | Uses ARP to announce IPs on the local network |
| **BGP Mode** | Uses BGP for larger network environments |
| **IP Pool Management** | Configurable IP address ranges |

> **Note**: We'll use Layer 2 mode in this lab as it works seamlessly with KIND.

---

## LoadBalancer vs NodePort vs ClusterIP

| Feature | ClusterIP | NodePort | LoadBalancer |
|---------|-----------|----------|--------------|
| **Accessibility** | Internal only | External via node IP | External via dedicated IP |
| **IP Address** | Virtual cluster IP | Node IPs + port | Dedicated external IP |
| **Port** | Any | 30000-32767 | Standard ports (80, 443) |
| **Use Case** | Internal services | Development/testing | Production external access |
| **Load Balancing** | Internal only | Across nodes | True external LB |

---

## Part 1: Understanding the LoadBalancer Problem in KIND

Let's first observe what happens when you create a LoadBalancer service without MetalLB.

### Create a Lab Directory

```bash
mkdir -p ~/loadbalancer-lab
cd ~/loadbalancer-lab
```

### Create a Sample Deployment

```bash
cat > web-deployment.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 2
          periodSeconds: 5
EOF

kubectl apply -f web-deployment.yaml
```

### Wait for Pods to be Ready

```bash
kubectl get pods -l app=web --watch
```

Press `Ctrl+C` when all 3 pods show `1/1 Running`.

### Create a LoadBalancer Service (Before MetalLB)

```bash
cat > web-lb-service.yaml <<'EOF'
apiVersion: v1
kind: Service
metadata:
  name: web-lb
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
EOF

kubectl apply -f web-lb-service.yaml
```

### Observe the Pending State

```bash
kubectl get services web-lb
```

Output shows:

```
NAME     TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
web-lb   LoadBalancer   10.96.xxx.xxx   <pending>     80:xxxxx/TCP   30s
```

The `EXTERNAL-IP` shows `<pending>` and will **stay that way forever** without MetalLB!

### View Service Details

```bash
kubectl describe svc web-lb
```

Note that:
- The service is allocated a `NodePort` (LoadBalancer includes NodePort functionality)
- But there's no external IP assignment

### Cleanup Before Installing MetalLB

```bash
kubectl delete svc web-lb
```

---

## Part 2: Installing MetalLB

MetalLB consists of two components:
- **Controller**: Handles IP address assignment
- **Speaker**: Announces assigned IPs on the network

### Determine the Docker Network CIDR

Before installing MetalLB, we need to find the IP range used by the KIND Docker network:

```bash
# Get the Docker network CIDR used by KIND
docker network inspect kind | grep -A 5 "IPAM" | grep Subnet
```

This will show something like: `"Subnet": "172.18.0.0/16"`

> **Note**: KIND networks typically have both IPv4 and IPv6 subnets. We only need the IPv4 subnet for MetalLB.

### Get Available IP Range for MetalLB

```bash
# Find the IPv4 gateway IP of the kind network
docker network inspect kind -f '{{range .IPAM.Config}}{{if .Gateway}}{{.Gateway}}{{end}}{{end}}' | grep -E '^[0-9]+\.'
```

Typically this is `172.18.0.1`. We'll use a range in this subnet for MetalLB.

### Create a Script to Determine IP Range

```bash
cat > get-metallb-range.sh <<'EOF'
#!/bin/bash
# Get the kind network IPv4 subnet (filter out IPv6)
SUBNET=$(docker network inspect kind -f '{{range .IPAM.Config}}{{.Subnet}} {{end}}' | tr ' ' '\n' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/' | head -1)
echo "KIND network IPv4 subnet: $SUBNET"

# Extract the base (e.g., 172.18 from 172.18.0.0/16)
BASE=$(echo $SUBNET | cut -d'.' -f1-2)
echo "Base network: $BASE"

# Define a range for MetalLB (using .255.x range to avoid conflicts)
echo "Suggested MetalLB IP range: ${BASE}.255.200-${BASE}.255.250"
EOF

chmod +x get-metallb-range.sh
./get-metallb-range.sh
```

> **Note**: We use the `.255.x` range to minimize conflicts with Docker-assigned container IPs. The script filters out IPv6 addresses to get only the IPv4 subnet.

### Install MetalLB

```bash
# Apply MetalLB manifest
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
```

### Wait for MetalLB Pods to be Ready

```bash
kubectl get pods -n metallb-system --watch
```

Wait until you see both the `controller` and `speaker` pods in `Running` state. Press `Ctrl+C` when ready.

```bash
# Verify all MetalLB components are running
kubectl get all -n metallb-system
```

Expected output:

```
NAME                              READY   STATUS    RESTARTS   AGE
pod/controller-xxxxxxxxxx-xxxxx   1/1     Running   0          60s
pod/speaker-xxxxx                 1/1     Running   0          60s
pod/speaker-yyyyy                 1/1     Running   0          60s

NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/metallb-webhook   ClusterIP   10.96.xxx.xxx   <none>        443/TCP   60s

NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE
daemonset.apps/speaker   2         2         2       2            2
```

### MetalLB Components Explained

| Component | Type | Description |
|-----------|------|-------------|
| **controller** | Deployment | Manages IP address assignment |
| **speaker** | DaemonSet | Runs on each node; announces IPs via ARP/BGP |
| **webhook** | Service | Validates MetalLB configuration |

---

## Part 3: Configuring MetalLB IP Address Pool

MetalLB needs to know which IP addresses it can assign to LoadBalancer services.

### Create the IP Address Pool

First, get your specific IP range:

```bash
./get-metallb-range.sh
```

Then create the IPAddressPool (adjust the range based on your output):

```bash
# Get the base network dynamically (IPv4 only, filtering out IPv6)
BASE=$(docker network inspect kind -f '{{range .IPAM.Config}}{{.Subnet}} {{end}}' | tr ' ' '\n' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/' | head -1 | cut -d'.' -f1-2)

# Verify the BASE is correct (should be something like 172.18)
echo "Using base network: $BASE"

cat > metallb-config.yaml <<EOF
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-pool
  namespace: metallb-system
spec:
  addresses:
  - ${BASE}.255.200-${BASE}.255.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: default-l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - default-pool
EOF

# Verify the generated file looks correct
cat metallb-config.yaml

kubectl apply -f metallb-config.yaml
```

### Understand the Configuration

| Resource | Purpose |
|----------|---------|
| **IPAddressPool** | Defines the range of IPs MetalLB can assign |
| **L2Advertisement** | Enables Layer 2 mode (ARP-based IP announcement) |

### Verify Configuration

```bash
kubectl get ipaddresspools -n metallb-system
kubectl get l2advertisements -n metallb-system
kubectl describe ipaddresspool default-pool -n metallb-system
```

---

## Part 4: Creating LoadBalancer Services

Now that MetalLB is configured, LoadBalancer services will receive external IPs!

### Create a LoadBalancer Service

```bash
cat > web-lb-service.yaml <<'EOF'
apiVersion: v1
kind: Service
metadata:
  name: web-lb
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
EOF

kubectl apply -f web-lb-service.yaml
```

### Watch the External IP Assignment

```bash
kubectl get svc web-lb --watch
```

Now you'll see an actual IP address assigned:

```
NAME     TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
web-lb   LoadBalancer   10.96.xxx.xxx   172.18.255.200   80:xxxxx/TCP   10s
```

Press `Ctrl+C` once the external IP is assigned.

### Verify the Service

```bash
kubectl describe svc web-lb
```

Key fields:
- `Type: LoadBalancer`
- `LoadBalancer Ingress: 172.18.255.200` (your assigned IP)

### Test the LoadBalancer Service

```bash
# Get the external IP
EXTERNAL_IP=$(kubectl get svc web-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo "LoadBalancer IP: $EXTERNAL_IP"

# Test connectivity (from within the Docker network)
docker run --rm --network kind curlimages/curl curl -s http://$EXTERNAL_IP
```

You should see the nginx welcome page HTML!

### Test from Inside the Cluster

```bash
kubectl run test-client --image=busybox:latest --rm -it --restart=Never -- sh
```

Inside the container:

```sh
# Get the external IP (use the IP from above)
wget -qO- http://172.18.255.200

# Or use the service name
wget -qO- http://web-lb:80

exit
```

---

## Part 5: Multiple LoadBalancer Services

Let's create multiple services to see how MetalLB assigns IPs from the pool.

### Create Additional Deployments and Services

```bash
# Create a second application
cat > api-app.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: api-lb
spec:
  type: LoadBalancer
  selector:
    app: api
  ports:
  - port: 8080
    targetPort: 80
EOF

kubectl apply -f api-app.yaml
```

### Create a Third Service

```bash
cat > admin-app.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: admin-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: admin
  template:
    metadata:
      labels:
        app: admin
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: admin-lb
spec:
  type: LoadBalancer
  selector:
    app: admin
  ports:
  - port: 9090
    targetPort: 80
EOF

kubectl apply -f admin-app.yaml
```

### View All LoadBalancer Services

```bash
kubectl get services -l '!k8s-app' --field-selector spec.type=LoadBalancer
```

Or simply:

```bash
kubectl get svc
```

You'll see each service has a unique IP from the pool:

```
NAME       TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
web-lb     LoadBalancer   10.96.xxx.xxx    172.18.255.200   80:xxxxx/TCP     5m
api-lb     LoadBalancer   10.96.yyy.yyy    172.18.255.201   8080:yyyyy/TCP   2m
admin-lb   LoadBalancer   10.96.zzz.zzz    172.18.255.202   9090:zzzzz/TCP   1m
```

### Test All Services

```bash
# Get all external IPs
kubectl get svc -o custom-columns='NAME:.metadata.name,TYPE:.spec.type,EXTERNAL-IP:.status.loadBalancer.ingress[0].ip,PORT:.spec.ports[0].port'

# Test each service
for svc in web-lb api-lb admin-lb; do
  IP=$(kubectl get svc $svc -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  PORT=$(kubectl get svc $svc -o jsonpath='{.spec.ports[0].port}')
  echo "Testing $svc at $IP:$PORT"
  docker run --rm --network kind curlimages/curl curl -s -o /dev/null -w "%{http_code}" http://$IP:$PORT
  echo ""
done
```

---

## Part 6: Specifying a Static IP

You can request a specific IP address for your LoadBalancer service.

### Create a Service with a Specific IP

```bash
# Get your base network (IPv4 only)
BASE=$(docker network inspect kind -f '{{range .IPAM.Config}}{{.Subnet}} {{end}}' | tr ' ' '\n' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/' | head -1 | cut -d'.' -f1-2)

cat > static-ip-service.yaml <<EOF
apiVersion: v1
kind: Service
metadata:
  name: static-lb
spec:
  type: LoadBalancer
  loadBalancerIP: ${BASE}.255.210
  selector:
    app: web
  ports:
  - port: 8888
    targetPort: 80
EOF

kubectl apply -f static-ip-service.yaml
```

### Verify the Static IP Assignment

```bash
kubectl get svc static-lb
```

The service should have the exact IP you specified!

### Important Notes on Static IPs

| Consideration | Description |
|---------------|-------------|
| **Must be in pool** | The requested IP must be within a configured IPAddressPool |
| **First come, first served** | If the IP is already assigned, the request fails |
| **Use sparingly** | Reserve static IPs for critical services only |

---

## Part 7: LoadBalancer with ExternalTrafficPolicy

Control how traffic is routed from the LoadBalancer to Pods.

### External Traffic Policy Options

| Policy | Description | Use Case |
|--------|-------------|----------|
| `Cluster` | Default; traffic may hop between nodes | Even load distribution |
| `Local` | Traffic only goes to Pods on receiving node | Preserve client IP, reduce latency |

### Create Service with Local Traffic Policy

```bash
cat > local-policy-service.yaml <<'EOF'
apiVersion: v1
kind: Service
metadata:
  name: local-lb
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
EOF

kubectl apply -f local-policy-service.yaml
```

### View the Configuration

```bash
kubectl describe svc local-lb | grep -A 2 "External Traffic Policy"
```

### Trade-offs

| Aspect | Cluster Policy | Local Policy |
|--------|----------------|--------------|
| **Load Distribution** | Balanced across all Pods | Only to local Pods |
| **Client IP** | SNAT'd (hidden) | Preserved |
| **Availability** | Any node works | Node must have Pod |
| **Network Hops** | May cross nodes | Direct to local Pod |

---

## Part 8: Multi-Port LoadBalancer Services

Expose multiple ports through a single LoadBalancer.

### Create a Multi-Port Service

```bash
cat > multi-port-lb.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multi-port-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: multi-port
  template:
    metadata:
      labels:
        app: multi-port
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
          name: http
        - containerPort: 443
          name: https
---
apiVersion: v1
kind: Service
metadata:
  name: multi-port-lb
spec:
  type: LoadBalancer
  selector:
    app: multi-port
  ports:
  - name: http
    port: 80
    targetPort: 80
  - name: https
    port: 443
    targetPort: 443
EOF

kubectl apply -f multi-port-lb.yaml
```

### Verify Multi-Port Service

```bash
kubectl get svc multi-port-lb
kubectl describe svc multi-port-lb
```

The service exposes both ports through the same external IP:

```
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)
multi-port-lb   LoadBalancer   10.96.xxx.xxx   172.18.255.203   80:xxx/TCP,443:xxx/TCP
```

### Test Both Ports

```bash
MULTI_IP=$(kubectl get svc multi-port-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo "Multi-port service IP: $MULTI_IP"

# Test HTTP
docker run --rm --network kind curlimages/curl curl -s http://$MULTI_IP:80 | head -5

# Test HTTPS (will fail SSL but shows port is open)
docker run --rm --network kind curlimages/curl curl -sk https://$MULTI_IP:443 2>&1 | head -5
```

---

## Part 9: Monitoring MetalLB and Services

### View MetalLB Status

```bash
# Check MetalLB pods
kubectl get pods -n metallb-system

# View controller logs
kubectl logs -n metallb-system -l app=metallb,component=controller --tail=20

# View speaker logs
kubectl logs -n metallb-system -l app=metallb,component=speaker --tail=20
```

### Check IP Address Pool Usage

```bash
# View pool configuration
kubectl describe ipaddresspool default-pool -n metallb-system

# List all LoadBalancer services and their IPs
kubectl get svc -A --field-selector spec.type=LoadBalancer \
  -o custom-columns='NAMESPACE:.metadata.namespace,NAME:.metadata.name,EXTERNAL-IP:.status.loadBalancer.ingress[0].ip,PORT:.spec.ports[*].port'
```

### Troubleshoot IP Assignment

```bash
# Check events for a service
kubectl describe svc <service-name> | grep -A 10 Events

# Check MetalLB controller logs for assignment
kubectl logs -n metallb-system -l component=controller | grep -i assign
```

---

## Exercises

> **Note**: These exercises focus on LoadBalancer-specific features. Basic kubectl commands were covered in the **kubectl Commands Lab**, Pod concepts in the **Pods Lab**, and Service basics in the **Services Lab**.

### Exercise 1: Create a Complete Application with LoadBalancer

Deploy a frontend application accessible via LoadBalancer:

```bash
cd ~/loadbalancer-lab

cat > frontend-app.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
        tier: frontend
    spec:
      containers:
      - name: web
        image: nginx:alpine
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 2
          periodSeconds: 5
        resources:
          requests:
            cpu: "50m"
            memory: "32Mi"
          limits:
            cpu: "100m"
            memory: "64Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-lb
  labels:
    tier: frontend
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
EOF

kubectl apply -f frontend-app.yaml
```

Wait for external IP and test:

```bash
kubectl get svc frontend-lb --watch
# Press Ctrl+C once IP is assigned

FRONTEND_IP=$(kubectl get svc frontend-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
docker run --rm --network kind curlimages/curl curl -s http://$FRONTEND_IP
```

Cleanup:

```bash
kubectl delete -f frontend-app.yaml
```

### Exercise 2: Compare LoadBalancer vs NodePort

Create the same application with both service types and compare:

```bash
# Create deployment
kubectl create deployment compare-app --image=nginx:alpine --replicas=2

# Expose as LoadBalancer
kubectl expose deployment compare-app --name=compare-lb --type=LoadBalancer --port=80

# Expose as NodePort
kubectl expose deployment compare-app --name=compare-np --type=NodePort --port=80

# Compare
kubectl get svc compare-lb compare-np
```

Observe:
- LoadBalancer has an external IP
- NodePort only has a high port number (30000+)

Test both:

```bash
# LoadBalancer
LB_IP=$(kubectl get svc compare-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
docker run --rm --network kind curlimages/curl curl -s http://$LB_IP

# NodePort (need node IP and port)
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
NODE_PORT=$(kubectl get svc compare-np -o jsonpath='{.spec.ports[0].nodePort}')
docker run --rm --network kind curlimages/curl curl -s http://$NODE_IP:$NODE_PORT
```

Cleanup:

```bash
kubectl delete deployment compare-app
kubectl delete svc compare-lb compare-np
```

### Exercise 3: Reserve a Specific IP for Critical Service

Create a database service with a reserved IP:

```bash
# Get base network (IPv4 only)
BASE=$(docker network inspect kind -f '{{range .IPAM.Config}}{{.Subnet}} {{end}}' | tr ' ' '\n' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/' | head -1 | cut -d'.' -f1-2)

cat > db-service.yaml <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      containers:
      - name: db
        image: nginx:alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: database-lb
  annotations:
    description: "Critical database service with reserved IP"
spec:
  type: LoadBalancer
  loadBalancerIP: ${BASE}.255.250
  selector:
    app: database
  ports:
  - port: 5432
    targetPort: 80
EOF

kubectl apply -f db-service.yaml
```

Verify the exact IP was assigned:

```bash
kubectl get svc database-lb
```

Cleanup:

```bash
kubectl delete -f db-service.yaml
```

---

## Optional Advanced Exercises

### Exercise 4: Create Multiple IP Address Pools

Configure separate pools for different environments:

```bash
# Get base network (IPv4 only)
BASE=$(docker network inspect kind -f '{{range .IPAM.Config}}{{.Subnet}} {{end}}' | tr ' ' '\n' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/' | head -1 | cut -d'.' -f1-2)

cat > multi-pool.yaml <<EOF
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: production-pool
  namespace: metallb-system
spec:
  addresses:
  - ${BASE}.255.100-${BASE}.255.110
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: development-pool
  namespace: metallb-system
spec:
  addresses:
  - ${BASE}.255.150-${BASE}.255.160
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: prod-l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - production-pool
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: dev-l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - development-pool
EOF

kubectl apply -f multi-pool.yaml
```

Create services using specific pools:

```bash
# Service using production pool (via IP selection)
cat > prod-service.yaml <<EOF
apiVersion: v1
kind: Service
metadata:
  name: prod-service
spec:
  type: LoadBalancer
  loadBalancerIP: ${BASE}.255.100
  selector:
    app: web
  ports:
  - port: 80
EOF

kubectl apply -f prod-service.yaml
kubectl get svc prod-service
```

Cleanup:

```bash
kubectl delete -f prod-service.yaml
kubectl delete -f multi-pool.yaml
```

### Exercise 5: Monitor MetalLB Events

Watch MetalLB assign and announce IPs:

```bash
# Terminal 1: Watch MetalLB controller logs
kubectl logs -n metallb-system -l component=controller -f

# Terminal 2: Create a new service
kubectl create deployment event-test --image=nginx:alpine
kubectl expose deployment event-test --type=LoadBalancer --port=80 --name=event-test-lb

# Watch the logs show IP assignment
# Then delete and recreate to see the cycle
kubectl delete svc event-test-lb
kubectl expose deployment event-test --type=LoadBalancer --port=80 --name=event-test-lb
```

Cleanup:

```bash
kubectl delete deployment event-test
kubectl delete svc event-test-lb
```

### Exercise 6: High Availability Testing

Test what happens when pods are unavailable:

```bash
# Create deployment and LB service
kubectl create deployment ha-test --image=nginx:alpine --replicas=3
kubectl expose deployment ha-test --type=LoadBalancer --port=80 --name=ha-test-lb

# Get the IP
HA_IP=$(kubectl get svc ha-test-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

# Continuously test connectivity
while true; do
  docker run --rm --network kind curlimages/curl curl -s -o /dev/null -w "%{http_code} " http://$HA_IP
  sleep 1
done &

# In another terminal, scale down the deployment
kubectl scale deployment ha-test --replicas=0
# Watch the requests fail

# Scale back up
kubectl scale deployment ha-test --replicas=3
# Watch the requests succeed again

# Stop the background test
kill %1
```

Cleanup:

```bash
kubectl delete deployment ha-test
kubectl delete svc ha-test-lb
```

---

## Key Takeaways

- **LoadBalancer services** require external IP provisioning, which cloud providers handle automatically
- **MetalLB** provides LoadBalancer functionality for bare-metal/local clusters like KIND
- **Layer 2 mode** uses ARP to announce IPs on the local network
- **IP Address Pools** define the range of IPs MetalLB can assign
- **Static IPs** can be requested using `loadBalancerIP` field
- **ExternalTrafficPolicy** controls routing behavior (Cluster vs Local)
- **Multi-port services** expose multiple ports through a single external IP
- MetalLB is a **requirement** for LoadBalancer services in KIND clusters

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `kubectl get svc --field-selector spec.type=LoadBalancer` | List LoadBalancer services |
| `kubectl get svc <name> -o jsonpath='{.status.loadBalancer.ingress[0].ip}'` | Get external IP |
| `kubectl get ipaddresspools -n metallb-system` | List MetalLB IP pools |
| `kubectl get l2advertisements -n metallb-system` | List L2 advertisements |
| `kubectl logs -n metallb-system -l component=controller` | MetalLB controller logs |
| `kubectl logs -n metallb-system -l component=speaker` | MetalLB speaker logs |
| `kubectl describe ipaddresspool <name> -n metallb-system` | IP pool details |

### LoadBalancer Service Manifest Template

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer
  labels:
    app: myapp
spec:
  type: LoadBalancer
  # Optional: request specific IP (must be in pool)
  # loadBalancerIP: 172.18.255.200
  # Optional: preserve client IP
  # externalTrafficPolicy: Local
  selector:
    app: myapp
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
```

### MetalLB Configuration Template

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: my-pool
  namespace: metallb-system
spec:
  addresses:
  - 172.18.255.200-172.18.255.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: my-l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - my-pool
```

### Service Types Comparison

| Aspect | ClusterIP | NodePort | LoadBalancer |
|--------|-----------|----------|--------------|
| **External Access** | No | Yes (node IP:port) | Yes (dedicated IP) |
| **Port Range** | Any | 30000-32767 | Any |
| **Includes ClusterIP** | Yes | Yes | Yes |
| **Includes NodePort** | No | Yes | Yes |
| **Cloud Integration** | No | No | Yes (or MetalLB) |
| **Production Use** | Internal only | Dev/testing | External services |

---

## Cleanup (End of Lab)

```bash
# Delete all services and deployments created in this lab
kubectl delete svc web-lb api-lb admin-lb static-lb local-lb multi-port-lb frontend-lb 2>/dev/null || true
kubectl delete deployment web-app api-app admin-app multi-port-app 2>/dev/null || true

# Optional: Remove MetalLB (if not needed for other labs)
# kubectl delete -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
# kubectl delete namespace metallb-system

# Keep MetalLB configuration for future use:
# kubectl delete ipaddresspool default-pool -n metallb-system
# kubectl delete l2advertisement default-l2 -n metallb-system

# Remove lab directory
cd ~
rm -rf ~/loadbalancer-lab

# Verify cleanup
kubectl get svc
kubectl get deployments
```

---

## Troubleshooting Common Issues

### External IP Stuck in Pending

```bash
# Check if MetalLB is installed and running
kubectl get pods -n metallb-system

# Check if IPAddressPool is configured
kubectl get ipaddresspools -n metallb-system

# Check if L2Advertisement is configured
kubectl get l2advertisements -n metallb-system

# Check MetalLB controller logs
kubectl logs -n metallb-system -l component=controller

# Common causes:
# - MetalLB not installed
# - IPAddressPool not created
# - L2Advertisement not created
# - IP pool exhausted
```

### IP Pool Exhausted

```bash
# Check how many IPs are in use
kubectl get svc -A --field-selector spec.type=LoadBalancer -o custom-columns='NAME:.metadata.name,IP:.status.loadBalancer.ingress[0].ip'

# Expand the IP pool
kubectl edit ipaddresspool default-pool -n metallb-system
# Change the addresses range to include more IPs
```

### Service Gets Wrong IP

```bash
# If you specified loadBalancerIP but got a different one:
# Check if the requested IP is in the pool
kubectl describe ipaddresspool default-pool -n metallb-system

# Check if the IP is already in use
kubectl get svc -A --field-selector spec.type=LoadBalancer | grep <requested-ip>

# The requested IP must be:
# 1. Within a configured IPAddressPool
# 2. Not already assigned to another service
```

### Cannot Connect to External IP

```bash
# Verify the service has endpoints
kubectl get endpoints <service-name>

# Check pods are ready
kubectl get pods -l <selector> -o wide

# Test from within the Docker network (for KIND)
docker run --rm --network kind curlimages/curl curl -v http://<external-ip>

# Check MetalLB speaker is running on all nodes
kubectl get pods -n metallb-system -o wide | grep speaker

# Check speaker logs for ARP issues
kubectl logs -n metallb-system -l component=speaker
```

### MetalLB Webhook Errors

```bash
# If you see webhook validation errors:
# Wait for webhook to be ready
kubectl wait --namespace metallb-system \
  --for=condition=ready pod \
  --selector=app=metallb \
  --timeout=90s

# Check webhook service
kubectl get svc -n metallb-system metallb-webhook
```

### KIND Network Issues

```bash
# Verify you're using the correct network range
docker network inspect kind | grep -A 5 IPAM

# Make sure your IP pool is within the KIND network subnet
# The subnet is typically 172.18.0.0/16 or similar

# Test connectivity within Docker network
docker run --rm --network kind curlimages/curl curl -v http://<external-ip>
```

---

## Additional Resources

- [Kubernetes LoadBalancer Services](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)
- [MetalLB Official Documentation](https://metallb.universe.tf/)
- [MetalLB Installation](https://metallb.universe.tf/installation/)
- [MetalLB Configuration](https://metallb.universe.tf/configuration/)
- [KIND LoadBalancer Guide](https://kind.sigs.k8s.io/docs/user/loadbalancer/)
- [ExternalTrafficPolicy](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#preserving-the-client-source-ip)


