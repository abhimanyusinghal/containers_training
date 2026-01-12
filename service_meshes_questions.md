# Kubernetes Service Meshes – Test Questions

Use these questions to assess participant understanding after completing the Kubernetes Service Meshes lab.

---

## Section 1: Multiple Choice

**1. What is the primary purpose of a Service Mesh?**

a) To replace Kubernetes Services  
b) To provide a dedicated infrastructure layer for service-to-service communication  
c) To manage container images  
d) To schedule Pods on nodes  

---

**2. What are the two main components of a Service Mesh architecture?**

a) Master and Worker  
b) Data Plane and Control Plane  
c) Frontend and Backend  
d) Client and Server  

---

**3. What proxy does Istio use as its sidecar?**

a) Nginx  
b) HAProxy  
c) Envoy  
d) Traefik  

---

**4. How is automatic sidecar injection enabled in Istio?**

a) By setting `sidecar: true` in the Deployment  
b) By labeling the namespace with `istio-injection=enabled`  
c) By adding a ConfigMap  
d) By modifying the container image  

---

**5. What Istio resource is used to define routing rules for traffic?**

a) Service  
b) Ingress  
c) VirtualService  
d) Endpoint  

---

**6. What does a DestinationRule in Istio define?**

a) The entry point for external traffic  
b) Policies that apply to traffic after routing (load balancing, circuit breaking)  
c) The DNS name for a service  
d) Network policies for security  

---

**7. How would you route 80% of traffic to v1 and 20% to v2 in Istio?**

a) Using multiple Services  
b) Using weight-based routing in a VirtualService  
c) Using ReplicaSet scaling  
d) Using Kubernetes Ingress  

---

**8. What is the Istio resource for configuring the ingress entry point?**

a) VirtualService  
b) Gateway  
c) DestinationRule  
d) ServiceEntry  

---

**9. What does mTLS stand for in the context of service meshes?**

a) Multi-tenant TLS  
b) Managed TLS  
c) Mutual TLS  
d) Master TLS  

---

**10. What is the default mTLS mode in Istio?**

a) STRICT  
b) PERMISSIVE  
c) DISABLE  
d) OPTIONAL  

---

**11. What Istio resource is used to configure mTLS settings?**

a) VirtualService  
b) DestinationRule  
c) PeerAuthentication  
d) Gateway  

---

**12. What happens when mTLS mode is set to STRICT?**

a) Only encrypted mTLS connections are accepted  
b) Both plain text and mTLS connections are accepted  
c) TLS is disabled  
d) Traffic is blocked completely  

---

**13. What is the purpose of circuit breaking in a service mesh?**

a) To encrypt traffic  
b) To prevent cascading failures by limiting connections and requests  
c) To route traffic to different versions  
d) To provide authentication  

---

**14. Which DestinationRule setting controls the maximum number of TCP connections?**

a) `maxRetries`  
b) `timeout`  
c) `maxConnections`  
d) `maxReplicas`  

---

**15. What is fault injection used for in Istio?**

a) To introduce errors and delays for testing resilience  
b) To fix application bugs  
c) To improve performance  
d) To encrypt traffic  

---

**16. How can you inject a 5-second delay in traffic using Istio?**

a) Using a DestinationRule with timeout  
b) Using a VirtualService with fault.delay configuration  
c) By modifying the Pod spec  
d) Using a PeerAuthentication policy  

---

**17. What Istio resource is used to configure fine-grained access control?**

a) VirtualService  
b) DestinationRule  
c) AuthorizationPolicy  
d) Gateway  

---

**18. What is the purpose of outlier detection in Istio?**

a) To find and remove unhealthy instances from the load balancing pool  
b) To detect security threats  
c) To route traffic to specific versions  
d) To encrypt traffic  

---

**19. Which observability tool provides service mesh visualization and topology?**

a) Prometheus  
b) Grafana  
c) Kiali  
d) Jaeger  

---

**20. Which tool provides distributed tracing in the Istio ecosystem?**

a) Kiali  
b) Prometheus  
c) Jaeger  
d) Grafana  

---

**21. What Istio command analyzes the mesh for configuration issues?**

a) `istioctl validate`  
b) `istioctl analyze`  
c) `istioctl check`  
d) `istioctl verify`  

---

**22. What is the purpose of a VirtualService's `match` section?**

a) To define the destination  
b) To specify conditions for routing (headers, URIs, etc.)  
c) To configure timeouts  
d) To enable mTLS  

---

**23. How do you view the Envoy proxy configuration for a specific pod?**

a) `kubectl describe pod`  
b) `istioctl proxy-config clusters <pod>`  
c) `kubectl logs <pod>`  
d) `kubectl get endpoints`  

---

**24. What is a "subset" in Istio terminology?**

a) A partial Service  
b) A named group of service endpoints defined by labels in a DestinationRule  
c) A type of VirtualService  
d) A security policy  

---

**25. What Istio profile includes all features and is recommended for learning?**

a) default  
b) minimal  
c) demo  
d) production  

---

## Section 2: True or False

**26. Istio sidecars are automatically injected into all Pods by default.**

☐ True  
☐ False  

---

**27. VirtualServices can route traffic based on HTTP headers.**

☐ True  
☐ False  

---

**28. mTLS in Istio requires application code changes to enable encryption.**

☐ True  
☐ False  

---

**29. Circuit breaking can only be configured at the cluster level, not per-service.**

☐ True  
☐ False  

---

**30. Istio can inject delays and errors into traffic for testing purposes.**

☐ True  
☐ False  

---

**31. A Gateway resource defines both ingress and routing rules.**

☐ True  
☐ False  

---

**32. DestinationRules must be applied before VirtualServices can route to subsets.**

☐ True  
☐ False  

---

**33. Istio observability features require code instrumentation in applications.**

☐ True  
☐ False  

---

**34. Authorization policies in Istio can restrict access based on service account.**

☐ True  
☐ False  

---

**35. Traffic mirroring in Istio sends a copy of live traffic to a test service.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**36. Complete the command to install Istio with the demo profile:**

```bash
istioctl install --set profile=__________ -y
```

---

**37. Complete the namespace label to enable automatic sidecar injection:**

```bash
kubectl label namespace default __________=enabled
```

---

**38. Complete the VirtualService spec to set a 5-second timeout:**

```yaml
spec:
  hosts:
  - reviews
  http:
  - __________: 5s
    route:
    - destination:
        host: reviews
```

---

**39. Complete the PeerAuthentication spec to enable strict mTLS:**

```yaml
spec:
  mtls:
    mode: __________
```

---

**40. In a DestinationRule, the __________ field defines named groups of endpoints based on labels.**

---

**41. Complete the command to analyze the Istio mesh for issues:**

```bash
__________ analyze
```

---

**42. Complete the VirtualService spec to route 70% to v1 and 30% to v2:**

```yaml
- route:
  - destination:
      host: myapp
      subset: v1
    weight: __________
  - destination:
      host: myapp
      subset: v2
    weight: 30
```

---

**43. The __________ field in a VirtualService fault configuration specifies the HTTP error code to return.**

---

**44. Complete the circuit breaker setting for maximum pending HTTP requests:**

```yaml
connectionPool:
  http:
    __________: 10
```

---

**45. Complete the command to view proxy routes for a pod named `productpage-xxx`:**

```bash
istioctl proxy-config __________ productpage-xxx
```

---

## Section 4: Short Answer

**46. Explain the difference between a VirtualService and a DestinationRule in Istio.**

---

**47. Describe how canary deployments work with Istio traffic management.**

---

**48. What is the difference between PERMISSIVE and STRICT mTLS modes?**

---

**49. Explain how circuit breaking prevents cascading failures in a microservices architecture.**

---

**50. What are the benefits of using a service mesh for observability compared to application-level instrumentation?**

---

**51. Describe the relationship between Gateway, VirtualService, and DestinationRule resources.**

---

**52. How does Istio's sidecar injection work and why is it important?**

---

**53. Explain the purpose of outlier detection and when it would eject an instance from the load balancing pool.**

---

## Section 5: Practical Scenarios

**54. Write a VirtualService manifest that:**
- Routes traffic for service `api`
- Sends 90% of traffic to subset `v1`
- Sends 10% of traffic to subset `v2`
- Has a 3-second timeout

---

**55. Write a DestinationRule manifest that:**
- Applies to service `reviews`
- Defines subsets `v1`, `v2`, and `v3` based on version labels
- Configures a maximum of 100 TCP connections

---

**56. Write a PeerAuthentication manifest that enables strict mTLS for the `production` namespace.**

---

**57. Write a VirtualService manifest that:**
- Injects a 2-second delay for 50% of traffic
- Routes all traffic to subset `v1`

---

**58. Write an AuthorizationPolicy that:**
- Applies to pods with label `app: backend`
- Only allows traffic from pods in the same namespace
- Denies all other traffic

---

**59. Write the commands to:**
1. Install Istio with demo profile
2. Enable sidecar injection for namespace `myapp`
3. Deploy a sample application
4. Verify sidecars are injected

---

**60. You need to implement a gradual rollout of a new version. Write the sequence of VirtualService configurations to:**
1. Start with 100% v1
2. Move to 90% v1, 10% v2
3. Move to 50% v1, 50% v2
4. Complete migration to 100% v2

---

**61. A service is experiencing intermittent failures. Write a VirtualService configuration that:**
- Retries failed requests up to 3 times
- Has a 2-second timeout per attempt
- Only retries on 5xx errors and connection failures

---

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | b | Service mesh is a dedicated infrastructure layer for service-to-service communication |
| 2 | b | Data Plane (sidecars) handles traffic; Control Plane (istiod) manages configuration |
| 3 | c | Istio uses Envoy proxy as its sidecar |
| 4 | b | Label namespace with `istio-injection=enabled` |
| 5 | c | VirtualService defines routing rules |
| 6 | b | DestinationRule defines policies applied after routing |
| 7 | b | Weight-based routing in VirtualService |
| 8 | b | Gateway configures the ingress entry point |
| 9 | c | mTLS = Mutual TLS (both sides authenticate) |
| 10 | b | PERMISSIVE mode accepts both mTLS and plaintext |
| 11 | c | PeerAuthentication configures mTLS |
| 12 | a | STRICT mode only accepts mTLS connections |
| 13 | b | Circuit breaking prevents cascading failures |
| 14 | c | `maxConnections` controls TCP connection limit |
| 15 | a | Fault injection tests resilience by introducing failures |
| 16 | b | VirtualService with fault.delay configuration |
| 17 | c | AuthorizationPolicy for access control |
| 18 | a | Outlier detection removes unhealthy instances |
| 19 | c | Kiali provides service mesh visualization |
| 20 | c | Jaeger provides distributed tracing |
| 21 | b | `istioctl analyze` checks for issues |
| 22 | b | `match` specifies routing conditions |
| 23 | b | `istioctl proxy-config` shows Envoy configuration |
| 24 | b | Subset is a named group of endpoints based on labels |
| 25 | c | `demo` profile includes all features for learning |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 26 | False | Namespace must be labeled for automatic injection |
| 27 | True | VirtualServices can match on headers, URIs, etc. |
| 28 | False | mTLS is transparent to applications |
| 29 | False | Circuit breaking can be configured per-service via DestinationRule |
| 30 | True | Fault injection supports delays and aborts |
| 31 | False | Gateway defines entry point; VirtualService defines routing |
| 32 | True | Subsets must be defined in DestinationRule before routing |
| 33 | False | Istio provides observability without code changes |
| 34 | True | AuthorizationPolicy can use principals (service accounts) |
| 35 | True | Traffic mirroring sends copies to test services |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 36 | `demo` |
| 37 | `istio-injection` |
| 38 | `timeout` |
| 39 | `STRICT` |
| 40 | `subsets` |
| 41 | `istioctl` |
| 42 | `70` |
| 43 | `httpStatus` |
| 44 | `http1MaxPendingRequests` |
| 45 | `routes` |

### Section 4: Short Answer

**46.** 
- **VirtualService**: Defines HOW traffic is routed (matching conditions, weights, destinations, timeouts, retries)
- **DestinationRule**: Defines WHAT happens after routing (load balancing, circuit breaking, connection pool settings, TLS settings, subsets)
- VirtualServices route to hosts/subsets; DestinationRules define what those subsets are and how to handle traffic to them.

**47.** Canary deployments with Istio:
1. Deploy new version (v2) alongside stable version (v1)
2. Create DestinationRule with subsets for v1 and v2
3. Start with VirtualService routing 100% to v1
4. Gradually shift traffic (e.g., 90/10, 80/20, 50/50)
5. Monitor metrics and errors in observability tools
6. If issues, roll back by setting weight to 0 for v2
7. If successful, complete migration to 100% v2
8. This requires no application changes and is declarative

**48.** 
- **PERMISSIVE**: Accepts both plaintext and mTLS connections. Allows gradual migration to mTLS. Services can communicate with mesh and non-mesh workloads.
- **STRICT**: Only accepts mTLS connections. Provides stronger security. Non-mesh workloads cannot connect. Requires all services to be in the mesh.

**49.** Circuit breaking prevents cascading failures by:
1. Setting limits on connections, requests, and pending requests
2. When limits are exceeded, immediately returning errors (503)
3. Using outlier detection to track error rates per instance
4. Ejecting unhealthy instances that exceed error thresholds
5. Allowing ejected instances to re-enter after recovery time
6. This prevents a slow/failing service from consuming all resources and causing upstream failures

**50.** Service mesh observability benefits:
- **Zero code changes**: Automatic metrics, traces, and logs
- **Consistent**: Same data format across all services regardless of language
- **Complete**: Captures all traffic, not just instrumented paths
- **Infrastructure level**: Sees network issues application code can't detect
- **Centralized**: Single source of truth for the entire mesh
- **Correlation**: Links traces, metrics, and logs automatically

**51.** Relationship:
- **Gateway**: Defines the entry point (ports, hosts, TLS) for external traffic entering the mesh
- **VirtualService**: Attached to Gateway, defines routing rules for incoming traffic (which subset/version to route to)
- **DestinationRule**: Defines subsets (versions) and traffic policies (circuit breaking, load balancing) for destinations

Flow: Gateway → VirtualService (routing) → DestinationRule (policy) → Service → Pods

**52.** Sidecar injection:
- Istio automatically adds an Envoy proxy container to Pods
- Enabled by labeling namespace with `istio-injection=enabled`
- Istio webhook intercepts Pod creation and modifies the spec
- Sidecar intercepts all inbound and outbound traffic
- Importance:
  - Enables traffic management without app changes
  - Provides automatic mTLS encryption
  - Collects metrics and traces
  - Applies policies (circuit breaking, retries)

**53.** Outlier detection:
- Monitors error rates and latencies per endpoint
- Ejection triggers:
  - `consecutive5xxErrors`: Number of 5xx responses before ejection
  - High latency compared to baseline
- Configuration:
  - `interval`: How often to check for outliers
  - `baseEjectionTime`: How long to eject unhealthy hosts
  - `maxEjectionPercent`: Maximum percentage of hosts to eject
- Re-entry: After ejection time passes, host is re-added and monitored

### Section 5: Practical Scenarios

**54.**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: api
spec:
  hosts:
  - api
  http:
  - timeout: 3s
    route:
    - destination:
        host: api
        subset: v1
      weight: 90
    - destination:
        host: api
        subset: v2
      weight: 10
```

**55.**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
```

**56.**
```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: production
spec:
  mtls:
    mode: STRICT
```

**57.**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
  - my-service
  http:
  - fault:
      delay:
        percentage:
          value: 50
        fixedDelay: 2s
    route:
    - destination:
        host: my-service
        subset: v1
```

**58.**
```yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: backend-policy
spec:
  selector:
    matchLabels:
      app: backend
  action: ALLOW
  rules:
  - from:
    - source:
        namespaces: ["default"]
```

**59.**
```bash
# 1. Install Istio
istioctl install --set profile=demo -y

# 2. Enable sidecar injection
kubectl label namespace myapp istio-injection=enabled

# 3. Deploy application
kubectl apply -f my-app.yaml -n myapp

# 4. Verify sidecars
kubectl get pods -n myapp
# Should show 2/2 READY (app + sidecar)

kubectl describe pod <pod-name> -n myapp | grep -A 5 "Containers:"
# Should show istio-proxy container
```

**60.**
```yaml
# Step 1: 100% v1
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 100
---
# Step 2: 90% v1, 10% v2
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 90
    - destination:
        host: myapp
        subset: v2
      weight: 10
---
# Step 3: 50% v1, 50% v2
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 50
    - destination:
        host: myapp
        subset: v2
      weight: 50
---
# Step 4: 100% v2
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - route:
    - destination:
        host: myapp
        subset: v2
      weight: 100
```

**61.**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
  - my-service
  http:
  - retries:
      attempts: 3
      perTryTimeout: 2s
      retryOn: 5xx,reset,connect-failure
    route:
    - destination:
        host: my-service
        subset: v1
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 55-61 | Expert – Ready for production service mesh management |
| 46-54 | Proficient – Solid understanding, minor gaps |
| 37-45 | Intermediate – Review traffic management and security concepts |
| 27-36 | Beginner – Review core service mesh concepts |
| 0-26 | Needs Review – Retake the lab exercises |

