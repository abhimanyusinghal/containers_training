# Common kubectl Commands – Test Questions

Use these questions to assess participant understanding after completing the kubectl Commands lab.

---

## Section 1: Multiple Choice

**1. What is the default namespace that kubectl uses when no namespace is specified?**

a) kube-system  
b) kube-public  
c) default  
d) kubernetes  

---

**2. Which flag allows you to view resources across all namespaces?**

a) `--namespace=all`  
b) `--all-namespaces` or `-A`  
c) `--global`  
d) `--everywhere`  

---

**3. What is the purpose of a Kubernetes context?**

a) To store container images  
b) To save namespace, cluster, and user configuration for easy switching  
c) To encrypt cluster communications  
d) To define resource limits  

---

**4. Which command creates a new context called `dev-context` with namespace `development`?**

a) `kubectl context create dev-context --namespace=development`  
b) `kubectl config set-context dev-context --namespace=development`  
c) `kubectl config new-context dev-context --ns=development`  
d) `kubectl create context dev-context -n development`  

---

**5. Which output format should you use to get the complete object definition with all fields?**

a) `-o wide`  
b) `-o name`  
c) `-o yaml` or `-o json`  
d) `-o full`  

---

**6. What does the `kubectl describe` command provide that `kubectl get` does not?**

a) JSON output  
b) Resource usage statistics  
c) Related events and detailed multi-line information  
d) Network configuration  

---

**7. Which command shows the API documentation for a Kubernetes resource type?**

a) `kubectl help pods`  
b) `kubectl explain pods`  
c) `kubectl docs pods`  
d) `kubectl info pods`  

---

**8. What does the `--dry-run=client` flag do when used with `kubectl apply`?**

a) Applies changes to a test cluster  
b) Shows what would happen without making actual changes  
c) Runs the command in debug mode  
d) Validates YAML syntax only  

---

**9. Which command streams container logs in real-time?**

a) `kubectl logs <pod> --stream`  
b) `kubectl logs <pod> --watch`  
c) `kubectl logs <pod> -f`  
d) `kubectl logs <pod> --tail=0`  

---

**10. What is the correct syntax to execute an interactive shell in a running container?**

a) `kubectl shell -it <pod>`  
b) `kubectl exec -it <pod> -- sh`  
c) `kubectl run -it <pod> sh`  
d) `kubectl attach -it <pod> sh`  

---

**11. Which command copies a file from a pod to your local machine?**

a) `kubectl copy <pod>:/path/file ./local-file`  
b) `kubectl get file <pod>:/path/file`  
c) `kubectl cp <pod>:/path/file ./local-file`  
d) `kubectl download <pod>:/path/file`  

---

**12. What does `kubectl port-forward pod/nginx 8080:80` do?**

a) Opens port 8080 on the pod  
b) Forwards traffic from local port 8080 to pod port 80  
c) Forwards traffic from pod port 8080 to local port 80  
d) Creates a service on port 8080  

---

**13. Which command shows CPU and memory usage for nodes?**

a) `kubectl get nodes --resources`  
b) `kubectl describe nodes`  
c) `kubectl top nodes`  
d) `kubectl stats nodes`  

---

**14. What does the `kubectl cordon` command do?**

a) Deletes a node from the cluster  
b) Restarts all pods on a node  
c) Prevents new pods from being scheduled on the node  
d) Enables pod scheduling on the node  

---

**15. Which command removes a label from a resource?**

a) `kubectl label pods my-pod color=`  
b) `kubectl label pods my-pod color-`  
c) `kubectl unlabel pods my-pod color`  
d) `kubectl label pods my-pod --remove color`  

---

**16. What is the difference between `kubectl apply` and `kubectl create`?**

a) `apply` is faster than `create`  
b) `apply` can update existing resources; `create` fails if resource exists  
c) `create` supports YAML; `apply` only supports JSON  
d) There is no difference  

---

**17. Which flag adds a label to an existing resource that already has that label?**

a) `--force`  
b) `--update`  
c) `--overwrite`  
d) `--replace`  

---

**18. What does the `-l` flag do when used with `kubectl get`?**

a) Lists resources in long format  
b) Filters resources by label selector  
c) Shows logs for resources  
d) Limits the number of results  

---

**19. Which command shows events sorted by timestamp?**

a) `kubectl get events --sort-by=time`  
b) `kubectl get events --sort-by='.lastTimestamp'`  
c) `kubectl get events --order-by=timestamp`  
d) `kubectl events --sorted`  

---

**20. What is required for `kubectl top` commands to work?**

a) Prometheus must be installed  
b) Metrics-server must be installed  
c) The dashboard must be running  
d) Resource quotas must be configured  

---

## Section 2: True or False

**21. You can use `kubectl get pods,services,deployments` to view multiple resource types in a single command.**

☐ True  
☐ False  

---

**22. The `kubectl edit` command downloads the resource definition, opens it in an editor, and automatically applies changes when you save.**

☐ True  
☐ False  

---

**23. Labels and annotations serve the same purpose in Kubernetes.**

☐ True  
☐ False  

---

**24. The `kubectl delete` command always prompts for confirmation before deleting resources.**

☐ True  
☐ False  

---

**25. Deleting a namespace also deletes all resources within that namespace.**

☐ True  
☐ False  

---

**26. The `-o wide` flag shows the full YAML definition of a resource.**

☐ True  
☐ False  

---

**27. You can view logs from a previous (crashed) container instance using `kubectl logs --previous`.**

☐ True  
☐ False  

---

**28. The `kubectl drain` command only prevents new pods from being scheduled on a node.**

☐ True  
☐ False  

---

**29. JSONPath queries can be used with `kubectl get` to extract specific fields from resources.**

☐ True  
☐ False  

---

**30. The `kubectl attach` command is identical to `kubectl exec`.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**31. Complete the command to view pods in the `production` namespace:**

```bash
kubectl get pods __________ production
```

---

**32. Complete the command to switch to a context named `staging-context`:**

```bash
kubectl config __________ staging-context
```

---

**33. Complete the command to view only the IP address of a pod using JSONPath:**

```bash
kubectl get pod my-pod -o jsonpath='{.__________}'
```

---

**34. Complete the command to add an annotation to a pod:**

```bash
kubectl __________ pods my-pod description="My application"
```

---

**35. Complete the command to view pods with the label `app=web`:**

```bash
kubectl get pods __________ app=web
```

---

**36. Complete the command to view logs from a specific container named `sidecar` in a multi-container pod:**

```bash
kubectl logs my-pod __________ sidecar
```

---

**37. Complete the command to delete all pods in the current namespace:**

```bash
kubectl delete pods __________
```

---

**38. Complete the command to mark a node as schedulable again after cordoning:**

```bash
kubectl __________ my-node
```

---

**39. Complete the command to create a namespace called `testing`:**

```bash
kubectl __________ namespace testing
```

---

**40. The default termination grace period for pods is __________ seconds.**

---

## Section 4: Short Answer

**41. Explain the difference between labels and annotations in Kubernetes. When would you use each?**

---

**42. A pod is stuck in `Pending` state. List three possible causes and the commands you would use to diagnose the issue.**

---

**43. Describe the purpose of the `kubectl explain` command and how it can help when writing YAML manifests.**

---

**44. What is the difference between `kubectl logs -f` and `kubectl attach`? When would you use each?**

---

**45. Explain why the metrics-server requires the `--kubelet-insecure-tls` flag in KIND clusters.**

---

**46. Describe the relationship between contexts, clusters, and users in kubectl configuration.**

---

**47. A developer runs `kubectl apply -f deployment.yaml` but the resource isn't created. What steps would you take to troubleshoot?**

---

**48. Explain the difference between `kubectl drain` and `kubectl cordon`. When would you use each?**

---

## Section 5: Practical Scenarios

**49. You need to set up a development environment with the following:**
- A namespace called `dev`
- A context that uses the `dev` namespace by default
- A pod named `test-app` running in that namespace

Write all the commands needed.

---

**50. Write the commands to:**
1. Create three pods named `app1`, `app2`, and `app3`
2. Add the label `tier=frontend` to `app1` and `app2`
3. Add the label `tier=backend` to `app3`
4. List only the frontend pods using a label selector

---

**51. A production pod named `api-server` is experiencing issues. Write the commands to:**
1. View the pod's current status and recent events
2. Check the container logs
3. Execute a shell inside the container to investigate
4. Check if the pod is consuming excessive resources

---

**52. Write the commands to install metrics-server on a KIND cluster and verify it's working correctly.**

---

**53. You need to safely remove a node named `worker-2` from the cluster for maintenance. Write the commands to:**
1. Prevent new pods from being scheduled on the node
2. Verify the node is marked as unschedulable
3. (Describe what you would do next, but don't execute drain in the lab)
4. Re-enable scheduling after maintenance

---

**54. Write a YAML manifest for a pod with the following specifications:**
- Name: `my-app`
- Namespace: `production`
- Labels: `app=myapp`, `version=v1`
- Container name: `main`
- Image: `nginx:alpine`
- Container port: 80

---

**55. Write the commands to:**
1. Port-forward from local port 9000 to a pod named `web` on port 80
2. Test the connection
3. Stop the port forwarding

---

**56. You have a pod with multiple containers: `app`, `sidecar`, and `logger`. Write the commands to:**
1. View logs from only the `sidecar` container
2. Execute a command in the `app` container
3. Stream logs from all containers simultaneously

---

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | c | kubectl uses the `default` namespace when none is specified |
| 2 | b | `--all-namespaces` or `-A` shows resources across all namespaces |
| 3 | b | Contexts store namespace, cluster, and user settings for easy switching |
| 4 | b | `kubectl config set-context` creates or modifies contexts |
| 5 | c | `-o yaml` or `-o json` shows the complete object definition |
| 6 | c | `describe` shows events, conditions, and detailed multi-line output |
| 7 | b | `kubectl explain` shows API documentation for resources |
| 8 | b | `--dry-run=client` shows what would happen without making changes |
| 9 | c | `-f` (follow) flag streams logs in real-time |
| 10 | b | `kubectl exec -it <pod> -- sh` runs an interactive shell |
| 11 | c | `kubectl cp` copies files between pods and local filesystem |
| 12 | b | Port-forward maps local port to pod port (local:pod format) |
| 13 | c | `kubectl top nodes` shows resource usage (requires metrics-server) |
| 14 | c | `cordon` marks a node as unschedulable |
| 15 | b | Trailing `-` removes a label |
| 16 | b | `apply` is idempotent and can update; `create` fails if exists |
| 17 | c | `--overwrite` allows updating existing labels |
| 18 | b | `-l` filters by label selector |
| 19 | b | `--sort-by='.lastTimestamp'` sorts events by time |
| 20 | b | Metrics-server is required for `kubectl top` commands |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 21 | True | Comma-separated resource types can be viewed together |
| 22 | True | `kubectl edit` downloads, opens editor, and applies on save |
| 23 | False | Labels are for identification/selection; annotations store metadata |
| 24 | False | kubectl delete does NOT prompt for confirmation |
| 25 | True | Deleting a namespace cascades to all resources within it |
| 26 | False | `-o wide` adds columns; `-o yaml` shows full definition |
| 27 | True | `--previous` shows logs from the previous container instance |
| 28 | False | `drain` evicts pods AND marks unschedulable; `cordon` only marks |
| 29 | True | JSONPath can extract specific fields from resource output |
| 30 | False | `attach` connects to running process; `exec` runs new command |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 31 | `-n` or `--namespace` |
| 32 | `use-context` |
| 33 | `status.podIP` |
| 34 | `annotate` |
| 35 | `-l` |
| 36 | `-c` |
| 37 | `--all` |
| 38 | `uncordon` |
| 39 | `create` |
| 40 | 30 |

### Section 4: Short Answer

**41.** Labels are key-value pairs used for identification and selection. They are used by selectors (e.g., in services, deployments) to find matching resources. Annotations are key-value pairs for storing non-identifying metadata, such as build information, contact details, or tool configurations. Use labels when you need to select/filter resources; use annotations for additional metadata that tools or humans might need.

**42.** Possible causes and diagnostic commands:
1. **Insufficient resources**: `kubectl describe pod <name>` (look for "Insufficient cpu/memory" in events), `kubectl top nodes`
2. **No matching nodes (nodeSelector/affinity)**: `kubectl describe pod <name>` (look for "FailedScheduling"), `kubectl get nodes --show-labels`
3. **PersistentVolumeClaim not bound**: `kubectl get pvc`, `kubectl describe pvc <name>`
4. **Image pull issues**: `kubectl describe pod <name>` (look for ImagePullBackOff)
5. **Node taints**: `kubectl describe nodes | grep Taints`

**43.** `kubectl explain` displays the API documentation for Kubernetes resources directly from the cluster's API. It shows field descriptions, types, and required fields. When writing YAML manifests, you can use it to understand available fields (`kubectl explain pods.spec.containers`) without consulting external documentation. It's especially useful for nested fields and understanding which fields are required vs optional.

**44.** 
- `kubectl logs -f`: Streams stdout/stderr from the container to your terminal. Read-only, doesn't interact with the process.
- `kubectl attach`: Connects to the running process's stdin/stdout/stderr. Can send input to the process if it reads from stdin.

Use `logs -f` for monitoring output; use `attach` when you need to interact with a running process (rare, mainly for debugging).

**45.** KIND runs Kubernetes inside Docker containers. The kubelet in KIND nodes uses self-signed certificates that aren't trusted by the metrics-server by default. The `--kubelet-insecure-tls` flag tells metrics-server to skip TLS certificate verification when connecting to kubelets, which is necessary in development environments like KIND but should not be used in production.

**46.** The kubectl config file contains:
- **Clusters**: Define API server addresses and CA certificates
- **Users**: Define authentication credentials (tokens, certificates, etc.)
- **Contexts**: Combine a cluster + user + optional default namespace

A context ties together which cluster to connect to, which credentials to use, and optionally which namespace to default to. You can have multiple contexts for different environments (dev, staging, prod) and switch between them with `kubectl config use-context`.

**47.** Troubleshooting steps:
1. Check for YAML syntax errors: `kubectl apply -f deployment.yaml --dry-run=client`
2. Validate against API server: `kubectl apply -f deployment.yaml --dry-run=server`
3. Check for errors in output (typos in resource kind, API version issues)
4. Verify namespace exists: `kubectl get namespace <name>`
5. Check permissions: `kubectl auth can-i create deployments`
6. Look at events: `kubectl get events --sort-by='.lastTimestamp'`
7. Check if resource already exists: `kubectl get deployment <name>`

**48.** 
- **cordon**: Marks a node as unschedulable. Existing pods continue running; only new pods are prevented from scheduling.
- **drain**: Marks node as unschedulable AND evicts all pods (except DaemonSets by default). Pods are rescheduled to other nodes.

Use `cordon` when you want to stop new pods but let existing work finish. Use `drain` when you need to empty the node completely (for maintenance, decommissioning, or upgrades).

### Section 5: Practical Scenarios

**49.**
```bash
# Create namespace
kubectl create namespace dev

# Create context (adjust cluster/user names for your environment)
kubectl config set-context dev-context \
  --cluster=kind-kind-cluster \
  --user=kind-kind-cluster \
  --namespace=dev

# Switch to the context
kubectl config use-context dev-context

# Create pod (now defaults to dev namespace)
kubectl run test-app --image=alpine --command -- sleep 3600

# Verify
kubectl get pods
```

**50.**
```bash
# Create pods
kubectl run app1 --image=alpine --command -- sleep 3600
kubectl run app2 --image=alpine --command -- sleep 3600
kubectl run app3 --image=alpine --command -- sleep 3600

# Add labels
kubectl label pods app1 app2 tier=frontend
kubectl label pods app3 tier=backend

# List frontend pods only
kubectl get pods -l tier=frontend
```

**51.**
```bash
# View status and events
kubectl describe pod api-server

# Check logs
kubectl logs api-server

# If container keeps restarting, check previous logs
kubectl logs api-server --previous

# Get a shell
kubectl exec -it api-server -- sh

# Check resource consumption
kubectl top pod api-server
```

**52.**
```bash
# Clean any existing installation
kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml 2>/dev/null || true
kubectl delete pods -n kube-system -l k8s-app=metrics-server --force --grace-period=0 2>/dev/null || true
sleep 5

# Install and patch
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl patch deployment metrics-server -n kube-system --type='json' \
  -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'

# Wait for rollout
kubectl rollout status deployment/metrics-server -n kube-system

# Verify pod is running
kubectl get pods -n kube-system -l k8s-app=metrics-server

# Wait and test
sleep 60
kubectl top nodes
kubectl top pods -A
```

**53.**
```bash
# 1. Prevent new pods from being scheduled
kubectl cordon worker-2

# 2. Verify node is unschedulable
kubectl get nodes
# Should show "SchedulingDisabled"

# 3. To drain (evict pods), you would run:
# kubectl drain worker-2 --ignore-daemonsets --delete-emptydir-data
# (Don't run in lab to keep cluster functional)

# 4. Re-enable scheduling after maintenance
kubectl uncordon worker-2

# Verify
kubectl get nodes
```

**54.**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
  namespace: production
  labels:
    app: myapp
    version: v1
spec:
  containers:
  - name: main
    image: nginx:alpine
    ports:
    - containerPort: 80
```

**55.**
```bash
# 1. Port forward (run in background)
kubectl port-forward pod/web 9000:80 &

# 2. Test the connection
curl localhost:9000

# 3. Stop port forwarding
pkill -f "port-forward pod/web"
# Or find the process and kill it:
# jobs -l
# kill %1
```

**56.**
```bash
# 1. View logs from sidecar container only
kubectl logs my-pod -c sidecar

# 2. Execute command in app container
kubectl exec my-pod -c app -- <command>
# For shell:
kubectl exec -it my-pod -c app -- sh

# 3. Stream logs from all containers
kubectl logs my-pod --all-containers -f
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 50-56 | Expert – Ready for advanced Kubernetes administration |
| 42-49 | Proficient – Solid understanding, minor gaps |
| 34-41 | Intermediate – Review advanced topics |
| 25-33 | Beginner – Review core concepts |
| 0-24 | Needs Review – Retake the lab exercises |

