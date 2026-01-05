# Docker Networking – Test Questions

Use these questions to assess participant understanding after completing the Docker Networking lab.

---

## Section 1: Multiple Choice

**1. Which three networks does Docker create by default?**

a) bridge, overlay, none  
b) bridge, host, none  
c) bridge, host, macvlan  
d) default, host, isolated  

---

**2. What happens when you try to ping a container by name on the default bridge network?**

a) The ping succeeds because Docker provides automatic DNS  
b) The ping fails because the default bridge does not provide DNS resolution  
c) The ping fails because containers cannot communicate on the default bridge  
d) The ping is redirected to the host  

---

**3. Which command creates a custom bridge network called `myapp`?**

a) `docker network add myapp`  
b) `docker network create myapp`  
c) `docker create network myapp`  
d) `docker network new --driver bridge myapp`  

---

**4. What does the `-p 8080:80` flag do when running a container?**

a) Maps container port 8080 to host port 80  
b) Maps host port 8080 to container port 80  
c) Opens ports 8080 and 80 on the container  
d) Creates a port range from 80 to 8080  

---

**5. Which network driver should you use when you want a container to share the host's network stack directly?**

a) bridge  
b) overlay  
c) host  
d) none  

---

**6. What is the main advantage of custom bridge networks over the default bridge network?**

a) Custom networks are faster  
b) Custom networks provide automatic DNS resolution by container name  
c) Custom networks support more containers  
d) Custom networks use less memory  

---

**7. Which command connects an existing container named `app` to a network called `backend`?**

a) `docker container attach app backend`  
b) `docker network add backend app`  
c) `docker network connect backend app`  
d) `docker container connect --network backend app`  

---

**8. What does the `--network none` flag do?**

a) Creates a new network called "none"  
b) Uses the default network  
c) Completely disables networking for the container  
d) Creates an isolated network with no external access  

---

**9. Which command shows the port mappings for a container named `webserver`?**

a) `docker container ports webserver`  
b) `docker container port webserver`  
c) `docker port show webserver`  
d) `docker inspect --ports webserver`  

---

**10. What is a network alias used for?**

a) To rename a network  
b) To give a container an additional DNS name that other containers can use  
c) To create a shortcut command for network operations  
d) To hide a container's real name  

---

**11. Which command lists all containers connected to a specific network?**

a) `docker network containers mynet`  
b) `docker container ls --network mynet`  
c) `docker network inspect mynet`  
d) `docker network ls --containers mynet`  

---

**12. What does the `--internal` flag do when creating a network?**

a) Makes the network only accessible from localhost  
b) Prevents containers on the network from accessing external networks  
c) Creates a network visible only to the current user  
d) Enables internal DNS only  

---

## Section 2: True or False

**13. Containers on different custom bridge networks can communicate with each other by default.**

☐ True  
☐ False  

---

**14. A container can be connected to multiple networks simultaneously.**

☐ True  
☐ False  

---

**15. When using the host network, you still need to publish ports with `-p` to access container services.**

☐ True  
☐ False  

---

**16. The `docker network prune` command removes all networks, including those with running containers.**

☐ True  
☐ False  

---

**17. Custom bridge networks automatically provide DNS resolution using container names.**

☐ True  
☐ False  

---

**18. The `-P` flag (uppercase) publishes all exposed ports to random host ports.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**19. Complete the command to run an Nginx container that binds only to localhost on port 8080:**

```bash
docker container run -d -p __________:8080:80 nginx
```

---

**20. Complete the command to disconnect a container named `app` from a network called `frontend`:**

```bash
docker network __________ frontend app
```

---

**21. To inspect a network and view only the subnet configuration, you can use:**

```bash
docker network inspect mynet | jq '.[0].IPAM.__________'
```

---

**22. Complete the command to create a network with a custom subnet:**

```bash
docker network create --subnet=__________ mynet
```

---

**23. To add a custom entry to a container's `/etc/hosts` file, use the flag:**

```bash
docker container run --__________=db.local:192.168.1.100 alpine
```

---

**24. The default subnet for Docker's default bridge network is typically:**

```
172.17.0.0/__________
```

---

## Section 4: Short Answer

**25. Explain why the default bridge network does not provide DNS resolution between containers, while custom bridge networks do.**

---

**26. Describe a scenario where you would use the `host` network driver instead of the `bridge` driver. What are the trade-offs?**

---

**27. A developer has two containers on the same custom network, but one container cannot reach the other. List three things you would check to troubleshoot this issue.**

---

**28. What is the purpose of network aliases, and how do they enable simple load balancing?**

---

**29. Explain the difference between these two port publishing options:**
- `-p 8080:80`
- `-p 80`

---

**30. Why might you use the `--network none` option when running a container?**

---

## Section 5: Practical Scenarios

**31. You need to create a multi-tier application with the following requirements:**
- A frontend network for web containers
- A backend network for API and database containers
- An API container that can communicate with both frontend and backend

Write the commands to:
1. Create both networks
2. Run a container named `api` that is connected to both networks

---

**32. Your security team requires that development and production containers cannot communicate with each other under any circumstances. Write the commands to:**
1. Create isolated networks for dev and prod
2. Run a container in each network
3. Demonstrate (with a command) that they cannot communicate

---

**33. You need to run a web server that:**
- Is accessible on host port 9090
- Has the hostname `mywebserver`
- Uses Google's DNS server (8.8.8.8)
- Is connected to a network called `webnet`

Write the complete `docker container run` command.

---

**34. Write the commands to:**
1. Create a custom network with subnet `10.20.0.0/24` and gateway `10.20.0.1`
2. Run a container with a specific IP address of `10.20.0.100`

---

**35. You have multiple API containers that should all respond to the DNS name `api-service`. Write the commands to:**
1. Create a network called `services`
2. Run three containers (api1, api2, api3) that all respond to the alias `api-service`
3. Demonstrate that the alias resolves to multiple IPs

---

**36. Your container cannot reach external websites. Write the troubleshooting commands you would run inside the container to diagnose the issue.**

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | b | Docker creates bridge, host, and none networks by default |
| 2 | b | The default bridge network does not have an embedded DNS server |
| 3 | b | `docker network create` is the correct command |
| 4 | b | Format is `-p HOST_PORT:CONTAINER_PORT` |
| 5 | c | The host driver shares the host's network stack |
| 6 | b | Custom networks have an embedded DNS server for container name resolution |
| 7 | c | `docker network connect NETWORK CONTAINER` is the syntax |
| 8 | c | The none network completely disables networking |
| 9 | b | `docker container port` shows port mappings |
| 10 | b | Aliases provide additional DNS names for a container |
| 11 | c | `docker network inspect` shows all containers on a network |
| 12 | b | `--internal` prevents external network access |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 13 | False | Containers on different networks are isolated and cannot communicate |
| 14 | True | A container can be connected to multiple networks using `docker network connect` |
| 15 | False | With host network, the container uses host ports directly; no publishing needed |
| 16 | False | `prune` only removes networks that have no connected containers |
| 17 | True | Custom bridge networks have embedded DNS for automatic name resolution |
| 18 | True | `-P` publishes all EXPOSE'd ports to random available host ports |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 19 | `127.0.0.1` |
| 20 | `disconnect` |
| 21 | `Config` |
| 22 | `10.0.0.0/24` (or any valid CIDR) |
| 23 | `add-host` |
| 24 | `16` |

### Section 4: Short Answer

**25.** The default bridge network was designed for backward compatibility with older Docker versions and doesn't include an embedded DNS server. Custom (user-defined) bridge networks were introduced later with an embedded DNS server that automatically resolves container names to IP addresses. This is a deliberate design choice to encourage users to use custom networks for better isolation and functionality.

**26.** Use cases for host network:
- Performance-critical applications that can't afford NAT overhead
- Network monitoring tools that need to see all host traffic
- Applications that need to bind to many ports dynamically

Trade-offs:
- No port isolation (container uses host ports directly)
- Potential port conflicts with host services
- Reduced security isolation
- No port mapping flexibility

**27.** Troubleshooting steps:
1. Verify both containers are on the same network: `docker network inspect <network>`
2. Check that containers are running: `docker container ls`
3. Verify the target container name/alias is correct
4. Check if there's a firewall or security group blocking traffic
5. Test with IP address instead of name to isolate DNS issues
6. Check container logs for application-level issues

**28.** Network aliases allow multiple containers to share the same DNS name. When you resolve an alias, Docker's embedded DNS returns all IP addresses of containers with that alias. Docker performs round-robin DNS, so each resolution can return IPs in a different order, distributing requests across containers. This provides a simple form of load balancing without external tools.

**29.** 
- `-p 8080:80` maps a specific host port (8080) to container port 80. You control exactly which port is used on the host.
- `-p 80` maps a random available host port to container port 80. Docker picks an ephemeral port. Use `docker container port` to find which port was assigned.

**30.** Use cases for `--network none`:
- Security-sensitive batch processing that shouldn't have network access
- Testing how applications behave without network connectivity
- Running untrusted code in complete network isolation
- Compliance requirements that mandate no network access for certain workloads

### Section 5: Practical Scenarios

**31.**
```bash
# Create networks
docker network create frontend
docker network create backend

# Run API container on frontend
docker container run -d --name api --network frontend alpine sleep 3600

# Connect API to backend as well
docker network connect backend api

# Verify
docker container inspect -f '{{json .NetworkSettings.Networks}}' api | jq 'keys'
```

**32.**
```bash
# Create isolated networks
docker network create dev-network
docker network create prod-network

# Run containers
docker container run -d --name dev-app --network dev-network alpine sleep 3600
docker container run -d --name prod-app --network prod-network alpine sleep 3600

# Get prod-app IP
PROD_IP=$(docker container inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' prod-app)

# Attempt to ping (will fail/timeout)
docker container exec dev-app ping -c 3 -W 2 $PROD_IP
# Expected: 100% packet loss or timeout
```

**33.**
```bash
docker network create webnet

docker container run -d \
  --name webserver \
  --network webnet \
  --hostname mywebserver \
  --dns 8.8.8.8 \
  -p 9090:80 \
  nginx:alpine
```

**34.**
```bash
# Create network with custom subnet
docker network create \
  --subnet=10.20.0.0/24 \
  --gateway=10.20.0.1 \
  customnet

# Run container with specific IP
docker container run -d \
  --name fixed-ip \
  --network customnet \
  --ip 10.20.0.100 \
  alpine sleep 3600

# Verify
docker container inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' fixed-ip
```

**35.**
```bash
# Create network
docker network create services

# Run containers with shared alias
docker container run -d --name api1 --network services --network-alias api-service alpine sleep 3600
docker container run -d --name api2 --network services --network-alias api-service alpine sleep 3600
docker container run -d --name api3 --network services --network-alias api-service alpine sleep 3600

# Run a client to test
docker container run -d --name client --network services alpine sleep 3600

# Demonstrate multiple IPs
docker container exec client nslookup api-service
```

**36.**
```bash
# Check IP configuration
ip addr

# Check default gateway
ip route

# Check DNS configuration
cat /etc/resolv.conf

# Test DNS resolution
nslookup google.com

# Test connectivity to gateway
ping -c 2 <gateway-ip>

# Test external connectivity
ping -c 2 8.8.8.8

# Test DNS with explicit server
nslookup google.com 8.8.8.8

# Check if HTTP works (if curl available)
curl -v https://google.com
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 32-36 | Expert – Ready for complex Docker networking |
| 26-31 | Proficient – Solid understanding, minor gaps |
| 20-25 | Intermediate – Review advanced topics |
| 14-19 | Beginner – Review core concepts |
| 0-13 | Needs Review – Retake the lab exercises |


