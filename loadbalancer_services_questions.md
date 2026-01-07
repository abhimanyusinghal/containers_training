# Kubernetes LoadBalancer Services – Test Questions

Use these questions to assess participant understanding after completing the Kubernetes LoadBalancer Services lab.

---

## Section 1: Multiple Choice

**1. What is the main advantage of a LoadBalancer service over NodePort?**

a) LoadBalancer is faster  
b) LoadBalancer provides a dedicated external IP address  
c) LoadBalancer uses less memory  
d) LoadBalancer doesn't require a selector  

---

**2. What happens when you create a LoadBalancer service in a local KIND cluster without MetalLB?**

a) The service works normally  
b) The service is deleted automatically  
c) The EXTERNAL-IP stays in `<pending>` state  
d) An error is thrown and the service isn't created  

---

**3. What is MetalLB?**

a) A cloud provider for Kubernetes  
b) A load balancer implementation for bare-metal/local Kubernetes clusters  
c) A container image registry  
d) A Kubernetes monitoring tool  

---

**4. Which MetalLB mode uses ARP to announce IP addresses on the local network?**

a) BGP mode  
b) Layer 2 mode  
c) Layer 3 mode  
d) Proxy mode  

---

**5. What are the two main components of MetalLB?**

a) Master and Worker  
b) Controller and Speaker  
c) Scheduler and Executor  
d) Manager and Agent  

---

**6. What Kubernetes resource defines the IP range that MetalLB can assign?**

a) ConfigMap  
b) IPAddressPool  
c) Service  
d) Endpoints  

---

**7. What is the purpose of the L2Advertisement resource in MetalLB?**

a) To configure BGP routing  
b) To enable Layer 2 mode and associate it with IP pools  
c) To advertise services to cloud providers  
d) To manage DNS records  

---

**8. How do you request a specific IP address for a LoadBalancer service?**

a) Add an annotation `ip-address`  
b) Set the `loadBalancerIP` field in the spec  
c) Create a separate IPAddressPool  
d) Specify it in the Endpoints object  

---

**9. What happens if you request a `loadBalancerIP` that is not within any configured IPAddressPool?**

a) MetalLB creates a new pool automatically  
b) The service gets a random IP from an existing pool  
c) The IP assignment fails and stays pending  
d) The cluster crashes  

---

**10. What does `externalTrafficPolicy: Local` do?**

a) Routes traffic only to Pods on the node receiving the traffic  
b) Blocks all external traffic  
c) Enables local DNS resolution  
d) Creates a local copy of the service  

---

**11. What is the trade-off when using `externalTrafficPolicy: Local`?**

a) Higher latency but better security  
b) Preserved client IP but potential uneven load distribution  
c) Lower cost but no high availability  
d) Faster response but no encryption  

---

**12. Which command shows only LoadBalancer type services?**

a) `kubectl get svc --type=LoadBalancer`  
b) `kubectl get svc --field-selector spec.type=LoadBalancer`  
c) `kubectl get loadbalancer`  
d) `kubectl get svc -l type=LoadBalancer`  

---

**13. What does a LoadBalancer service include that a ClusterIP service does not?**

a) Selector  
b) NodePort and External IP  
c) Endpoints  
d) Port mapping  

---

**14. In which namespace are MetalLB components typically installed?**

a) kube-system  
b) metallb-system  
c) default  
d) metallb  

---

**15. What type of Kubernetes resource is the MetalLB speaker?**

a) Deployment  
b) StatefulSet  
c) DaemonSet  
d) Job  

---

**16. Why is the MetalLB speaker deployed as a DaemonSet?**

a) To ensure high availability  
b) To run on every node so it can announce IPs from any node  
c) To reduce resource usage  
d) To enable BGP routing  

---

**17. What happens to the external IP when you delete a LoadBalancer service?**

a) The IP is permanently reserved  
b) The IP is returned to the pool and can be reassigned  
c) The IP is assigned to another service automatically  
d) MetalLB must be restarted  

---

**18. How can you expose multiple ports through a single LoadBalancer service?**

a) Create multiple LoadBalancer services  
b) Define multiple ports in the `ports` array with unique names  
c) Use a sidecar container  
d) It's not possible  

---

**19. What is the recommended way to get the external IP of a LoadBalancer service programmatically?**

a) `kubectl get svc <name> -o jsonpath='{.spec.clusterIP}'`  
b) `kubectl get svc <name> -o jsonpath='{.status.loadBalancer.ingress[0].ip}'`  
c) `kubectl describe svc <name> | grep IP`  
d) `kubectl get endpoints <name>`  

---

**20. Which of the following is TRUE about LoadBalancer services?**

a) They replace ClusterIP functionality  
b) They include both ClusterIP and NodePort functionality  
c) They only work with stateless applications  
d) They cannot have readiness probe integration  

---

## Section 2: Short Answer

**21. Explain why LoadBalancer services don't work out-of-the-box in local Kubernetes clusters like KIND or minikube.**

---

**22. Describe the process of how MetalLB assigns an external IP to a new LoadBalancer service.**

---

**23. What is the difference between the MetalLB controller and speaker components?**

---

**24. When would you choose to use `externalTrafficPolicy: Local` instead of the default `Cluster` policy?**

---

**25. Explain how you would reserve a specific IP address for a critical production service.**

---

**26. What are the advantages of using LoadBalancer services over NodePort for production workloads?**

---

**27. Describe what happens to client connections when a Pod backing a LoadBalancer service becomes unhealthy.**

---

**28. How would you troubleshoot a LoadBalancer service that shows `<pending>` for its external IP?**

---

## Section 3: Scenario-Based Questions

**29. You create a LoadBalancer service but the EXTERNAL-IP shows `<pending>` after several minutes. List at least 4 things you should check.**

---

**30. Your team needs to expose a web application externally with a specific IP address (172.18.255.100). The MetalLB IPAddressPool is configured with range 172.18.255.200-172.18.255.250. What will happen and how do you fix it?**

---

**31. You have 10 LoadBalancer services but your IPAddressPool only has 5 available IPs. What happens to services 6-10, and how would you resolve this?**

---

**32. A developer complains that their application isn't receiving the client's real IP address when accessed through a LoadBalancer service. What could be the issue and how do you fix it?**

---

**33. You need to migrate an application from NodePort to LoadBalancer without changing the port that external clients use (port 80). How would you accomplish this?**

---

**34. After a cluster upgrade, all LoadBalancer services show `<pending>` for external IPs. What might have happened and what should you check?**

---

## Section 4: YAML Analysis

**35. Identify the errors in this LoadBalancer service manifest:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  type: loadBalancer
  selector:
    app: web
  ports:
    port: 80
    targetPort: 8080
```

---

**36. What's wrong with this MetalLB IPAddressPool configuration?**

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: my-pool
spec:
  addresses:
  - 172.18.255.200-172.18.255.250
```

---

**37. Review this service configuration. Will the static IP assignment work? Why or why not?**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: critical-app
spec:
  type: LoadBalancer
  loadBalancerIP: 10.0.0.50
  selector:
    app: critical
  ports:
  - port: 443
    targetPort: 8443
```

(Assume the IPAddressPool is configured with range 172.18.255.200-172.18.255.250)

---

**38. What will this service expose and how will traffic be routed?**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: multi-port-app
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  selector:
    app: myapp
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: https
    port: 443
    targetPort: 8443
  - name: metrics
    port: 9090
    targetPort: 9090
```

---

## Section 5: Command Exercises

**39. Write the kubectl command to:**

a) List all LoadBalancer services across all namespaces  
b) Get just the external IP of a service named `frontend`  
c) Check the MetalLB controller logs  
d) View the configured IPAddressPools  

---

**40. Write the commands to:**

a) Create a deployment named `api` with image `nginx:alpine` and 3 replicas  
b) Expose it as a LoadBalancer service on port 8080 targeting port 80  
c) Wait until the external IP is assigned and display it  

---

## Section 6: Architecture Understanding

**41. Draw or describe the traffic flow when a client connects to a LoadBalancer service with `externalTrafficPolicy: Cluster`.**

---

**42. Explain the difference between these two scenarios:**

Scenario A: LoadBalancer service with 3 Pod replicas across 3 nodes  
Scenario B: LoadBalancer service with 3 Pod replicas, all on 1 node, with `externalTrafficPolicy: Local`

---

**43. Why does MetalLB require L2Advertisement in addition to IPAddressPool for Layer 2 mode to work?**

---

---

# Answer Key

## Section 1: Multiple Choice

1. **b** - LoadBalancer provides a dedicated external IP address
2. **c** - The EXTERNAL-IP stays in `<pending>` state
3. **b** - A load balancer implementation for bare-metal/local Kubernetes clusters
4. **b** - Layer 2 mode
5. **b** - Controller and Speaker
6. **b** - IPAddressPool
7. **b** - To enable Layer 2 mode and associate it with IP pools
8. **b** - Set the `loadBalancerIP` field in the spec
9. **c** - The IP assignment fails and stays pending
10. **a** - Routes traffic only to Pods on the node receiving the traffic
11. **b** - Preserved client IP but potential uneven load distribution
12. **b** - `kubectl get svc --field-selector spec.type=LoadBalancer`
13. **b** - NodePort and External IP
14. **b** - metallb-system
15. **c** - DaemonSet
16. **b** - To run on every node so it can announce IPs from any node
17. **b** - The IP is returned to the pool and can be reassigned
18. **b** - Define multiple ports in the `ports` array with unique names
19. **b** - `kubectl get svc <name> -o jsonpath='{.status.loadBalancer.ingress[0].ip}'`
20. **b** - They include both ClusterIP and NodePort functionality

---

## Section 2: Short Answer

**21.** LoadBalancer services require integration with a cloud provider or external system to provision actual load balancers and assign external IPs. Local clusters like KIND run in Docker containers without any cloud provider integration. Without something like MetalLB to handle IP assignment and announcement, there's nothing to allocate external IPs, so they remain in pending state.

**22.** When a new LoadBalancer service is created:
1. The MetalLB controller watches for new services of type LoadBalancer
2. It finds an available IP from the configured IPAddressPool
3. It assigns the IP to the service (updates the `.status.loadBalancer.ingress` field)
4. The MetalLB speaker (running on each node) starts announcing this IP via ARP (Layer 2 mode) or BGP

**23.** 
- **Controller**: A Deployment that manages IP address assignment. It watches for LoadBalancer services and assigns IPs from configured pools.
- **Speaker**: A DaemonSet running on every node that handles network-level advertisement of assigned IPs using ARP (L2) or BGP protocols.

**24.** Use `externalTrafficPolicy: Local` when:
- You need to preserve the original client IP address for logging/security
- Your application needs to know the real client IP
- You want to minimize network hops for latency-sensitive applications
- You have Pods distributed across nodes and want direct routing

**25.** 
1. Ensure the desired IP is within a configured IPAddressPool
2. Set the `loadBalancerIP` field in the service spec to the desired IP
3. Apply the service before any other service can claim that IP
4. Optionally, create a dedicated IPAddressPool for reserved IPs

**26.** 
- Standard ports (80, 443) instead of high-numbered ports (30000+)
- Dedicated external IP per service (cleaner than node IP:port)
- No need to know node IPs
- Works with DNS more naturally
- Better suited for production traffic patterns

**27.** When a Pod becomes unhealthy (fails readiness probe):
- The Pod's IP is removed from the Endpoints object
- The LoadBalancer stops sending new connections to that Pod
- Existing connections may be terminated or allowed to drain
- Traffic is distributed to remaining healthy Pods

**28.** Check in order:
1. MetalLB pods are running: `kubectl get pods -n metallb-system`
2. IPAddressPool exists: `kubectl get ipaddresspools -n metallb-system`
3. L2Advertisement exists: `kubectl get l2advertisements -n metallb-system`
4. IP pool has available addresses
5. MetalLB controller logs for errors
6. MetalLB webhook is ready

---

## Section 3: Scenario-Based Questions

**29.** Things to check:
1. MetalLB is installed and running (`kubectl get pods -n metallb-system`)
2. IPAddressPool is configured (`kubectl get ipaddresspools -n metallb-system`)
3. L2Advertisement is configured (`kubectl get l2advertisements -n metallb-system`)
4. IP pool has available addresses (not exhausted)
5. MetalLB controller logs (`kubectl logs -n metallb-system -l component=controller`)
6. MetalLB webhook is running
7. Service events (`kubectl describe svc <name>`)
8. Requested `loadBalancerIP` (if any) is within the pool range

**30.** The IP assignment will fail because 172.18.255.100 is not within the configured pool range (172.18.255.200-172.18.255.250). The external IP will stay pending.

To fix:
- Either change the requested IP to one within the pool (e.g., 172.18.255.200)
- Or expand/modify the IPAddressPool to include 172.18.255.100

**31.** Services 6-10 will have EXTERNAL-IP stuck in `<pending>` state because there are no available IPs in the pool.

Solutions:
- Expand the IPAddressPool range
- Create additional IPAddressPools
- Delete unnecessary LoadBalancer services
- Convert some services to NodePort or ClusterIP if external access isn't required

**32.** The issue is likely `externalTrafficPolicy: Cluster` (the default), which performs SNAT and hides the client IP.

Fix: Set `externalTrafficPolicy: Local` in the service spec:
```yaml
spec:
  externalTrafficPolicy: Local
```

**33.** 
1. Create the LoadBalancer service with port 80:
```yaml
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: <app-port>
```
2. Update DNS to point to the new external IP
3. Delete the old NodePort service
4. The external port (80) remains the same for clients

**34.** Possible causes:
- MetalLB was not reinstalled/upgraded during cluster upgrade
- MetalLB configurations (IPAddressPool, L2Advertisement) were deleted
- MetalLB namespace was removed

Check:
1. MetalLB pods exist and are running
2. CRDs exist: `kubectl get crds | grep metallb`
3. IPAddressPool and L2Advertisement exist
4. Reinstall MetalLB if necessary

---

## Section 4: YAML Analysis

**35.** Errors:
1. `type: loadBalancer` should be `type: LoadBalancer` (capitalization matters)
2. `ports` should be an array with `-` prefix:
```yaml
ports:
- port: 80
  targetPort: 8080
```

**36.** The IPAddressPool is missing the `namespace: metallb-system` in the metadata:
```yaml
metadata:
  name: my-pool
  namespace: metallb-system
```

**37.** No, it will not work. The requested IP (10.0.0.50) is not within the configured IPAddressPool range (172.18.255.200-172.18.255.250). The service will stay in pending state until either:
- The requested IP is changed to one within the pool
- The pool is expanded to include 10.0.0.50

**38.** This service will:
- Expose 3 ports (80, 443, 9090) through a single external IP
- Use `externalTrafficPolicy: Local` meaning:
  - Client IP is preserved
  - Traffic only routes to Pods on the receiving node
  - Potential uneven distribution if Pods aren't on all nodes
- Traffic on port 80 → Pod port 8080
- Traffic on port 443 → Pod port 8443
- Traffic on port 9090 → Pod port 9090

---

## Section 5: Command Exercises

**39.**
a) `kubectl get svc -A --field-selector spec.type=LoadBalancer`

b) `kubectl get svc frontend -o jsonpath='{.status.loadBalancer.ingress[0].ip}'`

c) `kubectl logs -n metallb-system -l component=controller`

d) `kubectl get ipaddresspools -n metallb-system`

**40.**
```bash
# a) Create deployment
kubectl create deployment api --image=nginx:alpine --replicas=3

# b) Expose as LoadBalancer
kubectl expose deployment api --type=LoadBalancer --port=8080 --target-port=80

# c) Wait and display external IP
kubectl wait --for=jsonpath='{.status.loadBalancer.ingress}' svc/api --timeout=60s
kubectl get svc api -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

---

## Section 6: Architecture Understanding

**41.** Traffic flow with `externalTrafficPolicy: Cluster`:
```
Client → External IP → Any Node (kube-proxy) → SNAT → Any Pod (on any node)
```
1. Client sends request to external IP
2. MetalLB speaker on a node receives the traffic
3. kube-proxy forwards to any Pod in the service (may cross nodes)
4. Source NAT is performed (client IP is masked)
5. Response returns through the same path

**42.** 
**Scenario A** (3 Pods on 3 nodes):
- Traffic is evenly distributed across all nodes
- Each node can serve traffic directly to its local Pod
- Good load distribution

**Scenario B** (3 Pods on 1 node, Local policy):
- Only the node with Pods can serve traffic
- Other nodes receiving traffic would have no local Pods to route to
- All traffic goes to one node, potentially creating a bottleneck
- If that node fails, no traffic can be served despite the Pods being schedulable elsewhere

**43.** 
- **IPAddressPool** only defines which IPs are available for assignment
- **L2Advertisement** enables the actual network-level behavior:
  - Activates Layer 2 mode (ARP-based)
  - Associates IP pools with the L2 announcement mechanism
  - Tells MetalLB speakers to respond to ARP requests for the assigned IPs
- Without L2Advertisement, MetalLB knows which IPs to assign but doesn't know how to make them reachable on the network


