# Docker Containers – Hands-on Lab (Play with Docker)

This lab is designed to be shared with participants. It includes **commands**, **explanations**, and **special notes** specific to **Play with Docker (PWD)**.

---

## Important: How to Use Play with Docker (PWD)

1. Open `https://labs.play-with-docker.com`
2. Login using Docker Hub or GitHub
3. Click **Add New Instance**
4. You will get a terminal with Docker available

### Copy-Paste Tip (PWD)

Play with Docker does **not** always handle normal paste reliably.

- **Use:** `Ctrl + Shift + V` (recommended)
- **Avoid relying on:** `Ctrl + V`

Some commands below are formatted to copy/paste cleanly; if something looks correct but fails, retry with `Ctrl + Shift + V`.

---

## Learning Objectives

- Understand the **docker container run** command and its components
- Learn how to **list**, **stop**, **start**, and **remove** containers
- Inspect container metadata using **docker container inspect**
- Execute commands inside running containers with **docker container exec**
- Attach to running containers and retrieve logs
- Understand the container lifecycle

### Intermediate Objectives (Optional)

- Use resource constraints (CPU, memory limits)
- Configure restart policies for production workloads
- Work with container health checks
- Understand and configure container networking basics
- Manage container processes and signals
- Troubleshoot containers by inspecting changes and copying files

### Tip: Use a Name Prefix

On shared hosts or busy workstations, prefix container names (e.g., `lab-web`, `lab-trivia`) so you don't clash with existing resources.

---

## Quick Sanity Check

Verify Docker is installed and running:

```bash
docker version
```

You should see output showing both **Client** and **Server** versions:

```
Client: Docker Engine - Community
 Version:           24.0.x
 API version:       1.43
 ...

Server: Docker Engine - Community
 Engine:
  Version:          24.0.x
  API version:      1.43 (minimum version 1.12)
  ...
```

For additional system information:

```bash
docker info
```

---

## Part 1: Running Your First Container

### Understanding the Command Structure

The `docker container run` command follows this pattern:

```
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

| Component | Description |
|-----------|-------------|
| `docker` | The Docker CLI tool |
| `container` | The context (working with containers) |
| `run` | The action (create and start a container) |
| `IMAGE` | The container image to use |
| `COMMAND` | The process to run inside the container |

### Run a Simple Container

```bash
docker container run alpine echo "Hello World"
```

**First run output:**

```
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
e7c96db7181b: Pull complete
Digest: sha256:769fddc7cc2f0a1c35abb2f91432e8beecf83916c421420e6a6da9f8975464b6
Status: Downloaded newer image for alpine:latest
Hello World
```

**Subsequent runs:**

```
Hello World
```

### What Just Happened?

1. Docker checked for the `alpine` image locally
2. Image not found → pulled from Docker Hub (`library/alpine`)
3. Created a new container from the image
4. Executed `echo "Hello World"` inside the container
5. Container exited after the command completed

### Note

`alpine` is a minimal Docker image based on Alpine Linux—only ~5 MB in size, with a complete package index. It's commonly used as a base image.

---

## Part 2: Running a Container with a Different Process

Run a CentOS container that pings localhost:

```bash
docker container run centos:centos7.9.2009 ping -c 5 127.0.0.1
```

**Expected output:**

```
Unable to find image 'centos:centos7.9.2009' locally
centos7.9.2009: Pulling from library/centos
...
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.104 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.059 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.081 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.050 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.055 ms

--- 127.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4127ms
rtt min/avg/max/mdev = 0.050/0.069/0.104/0.022 ms
```

### Observation

- The image `centos:centos7.9.2009` uses a **tag** to specify a version
- The process `ping -c 5 127.0.0.1` runs inside the container's isolated environment
- Container exits automatically when the ping command completes

---

## Part 3: Running Containers as Background Services (Daemons)

Run a container in the background using the `-d` flag:

```bash
docker container run -d --name trivia fundamentalsofdocker/trivia:ed2
```

| Flag | Description |
|------|-------------|
| `-d` | Run as a daemon (background process) |
| `--name trivia` | Assign a meaningful name to the container |

Verify the container is running:

```bash
docker container ls -l
```

**Expected output:**

```
CONTAINER ID  IMAGE                            COMMAND    CREATED         STATUS        PORTS  NAMES
0ff3d7cf7634  fundamentalsofdocker/trivia:ed2  ...        11 seconds ago  Up 9 seconds         trivia
```

### Note

Container names must be **unique** on the system. If you try to create another container with the same name, Docker will return an error.

---

## Part 4: Listing Containers

### List Running Containers

```bash
docker container ls
```

### List All Containers (Including Stopped)

```bash
docker container ls -a
```

### List Only Container IDs

```bash
docker container ls -q
```

### List IDs of All Containers

```bash
docker container ls -a -q
```

### Practical Use: Remove All Containers

```bash
docker container rm -f $(docker container ls -a -q)
```

This command removes **all** containers (running and stopped) by:
1. Getting all container IDs with `docker container ls -a -q`
2. Passing them to `docker container rm -f`

### Get Help

```bash
docker container ls --help
```

---

## Part 5: Stopping and Starting Containers

### Ensure Trivia Container is Running

```bash
docker container run -d --name trivia fundamentalsofdocker/trivia:ed2
```

(If it already exists, you may need to remove it first: `docker container rm -f trivia`)

### Stop a Container

```bash
docker container stop trivia
```

### What Happens When You Stop a Container?

1. Docker sends **SIGTERM** to the main process
2. Process has 10 seconds (default) to gracefully shut down
3. If still running, Docker sends **SIGKILL** to force termination

### Get Container ID Programmatically

```bash
export CONTAINER_ID=$(docker container ls -a | grep trivia | awk '{print $1}')
echo "Container ID: $CONTAINER_ID"
```

You can also use:

```bash
docker container stop $CONTAINER_ID
```

### Check Container Status

```bash
docker container ls -a | grep trivia
```

Status should show `Exited`.

### Start a Stopped Container

```bash
docker container start trivia
```

### Verify It's Running Again

```bash
docker container ls | grep trivia
```

---

## Part 6: Removing Containers

### Remove by Name

```bash
docker container rm trivia
```

### Remove by ID

```bash
docker container rm <container_id>
```

### Force Remove (Even if Running)

```bash
docker container rm -f trivia
```

### Remove Multiple Containers

```bash
docker container rm container1 container2 container3
```

### Clean Up: Remove All Stopped Containers

```bash
docker container prune
```

---

## Part 7: Inspecting Containers

Get detailed metadata about a container:

```bash
docker container run -d --name trivia fundamentalsofdocker/trivia:ed2
docker container inspect trivia
```

**Output is a JSON object containing:**

- Container ID and name
- Creation timestamp
- Image information
- State (running, paused, exited, etc.)
- Network settings
- Mount points
- Environment variables
- And much more...

### Filter Specific Information

Get only the State section:

```bash
docker container inspect -f "{{json .State}}" trivia | jq .
```

**Output:**

```json
{
  "Status": "running",
  "Running": true,
  "Paused": false,
  "Restarting": false,
  "OOMKilled": false,
  "Dead": false,
  "Pid": 18252,
  "ExitCode": 0,
  "Error": "",
  "StartedAt": "2019-06-16T13:30:15.776272Z",
  "FinishedAt": "2019-06-16T13:29:38.6412298Z"
}
```

### More Filter Examples

Get the container's IP address:

```bash
docker container inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' trivia
```

Get the image used:

```bash
docker container inspect -f '{{.Config.Image}}' trivia
```

### Note

The filter uses **Go template syntax**. Pipe to `jq` for pretty JSON formatting (if available).

---

## Part 8: Executing Commands Inside Running Containers

### Open an Interactive Shell

```bash
docker container exec -it trivia /bin/sh
```

| Flag | Description |
|------|-------------|
| `-i` | Interactive mode (keep STDIN open) |
| `-t` | Allocate a pseudo-TTY (terminal) |

You'll see a new prompt like `/app #`.

### Run Commands Inside the Container

```sh
ps
ls -la
hostname
exit
```

### Run a Single Command (Non-Interactive)

```bash
docker container exec trivia ps aux
```

### Pass Environment Variables

```bash
docker container exec -it -e MY_VAR="Hello World" trivia /bin/sh
```

Inside the container:

```sh
echo $MY_VAR
```

Output: `Hello World`

Exit with `Ctrl + D` or type `exit`.

### Run as a Different User

```bash
docker container exec -u root trivia whoami
```

---

## Part 9: Attaching to Running Containers

Attach your terminal to a container's main process:

### Run an Nginx Container

```bash
docker container run -d --name nginx -p 8080:80 nginx:alpine
```

### Test It Works

```bash
curl -4 localhost:8080
```

**Expected:** Nginx welcome page HTML.

### PWD Note (Browser Access)

In Play with Docker, use the UI to **open port 8080** (clickable port link near the top).

### Attach to the Container

```bash
docker container attach nginx
```

You won't see output immediately. Open **another terminal** and run:

```bash
for n in {1..10}; do curl -4 localhost:8080; done
```

Back in the first terminal, you'll see Nginx access logs:

```
172.17.0.1 - - [16/Jun/2019:14:14:02 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.54.0" "-"
172.17.0.1 - - [16/Jun/2019:14:14:02 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.54.0" "-"
...
```

### Detaching

| Key Combination | Effect |
|-----------------|--------|
| `Ctrl + P`, `Ctrl + Q` | Detach without stopping container |
| `Ctrl + C` | Detach AND stop the container |

### Cleanup

```bash
docker container rm -f nginx
```

---

## Part 10: Retrieving Container Logs

### View All Logs

```bash
docker container logs trivia
```

### View Last N Lines

```bash
docker container logs --tail 5 trivia
```

### Follow Logs in Real-Time

```bash
docker container logs --follow trivia
```

### Combine: Last 5 Lines + Follow

```bash
docker container logs --tail 5 --follow trivia
```

### Show Timestamps

```bash
docker container logs --timestamps trivia
```

### Logs Since a Specific Time

```bash
docker container logs --since 10m trivia
```

Press `Ctrl + C` to stop following logs.

---

## Exercises

### Exercise 1: Container Lifecycle Practice

1. Run an Alpine container that sleeps for 60 seconds:

```bash
docker container run -d --name sleeper alpine sleep 60
```

2. Check it's running:

```bash
docker container ls | grep sleeper
```

3. Stop it before the 60 seconds:

```bash
docker container stop sleeper
```

4. Start it again:

```bash
docker container start sleeper
```

5. Remove it:

```bash
docker container rm -f sleeper
```

### Exercise 2: Interactive Container Session

1. Run an Ubuntu container interactively:

```bash
docker container run -it --name myubuntu ubuntu:22.04 /bin/bash
```

2. Inside the container:

```bash
apt update
apt install -y curl
curl --version
exit
```

3. The container stops when you exit. Start it again and attach:

```bash
docker container start myubuntu
docker container exec -it myubuntu /bin/bash
```

4. Verify curl is still installed:

```bash
curl --version
```

### Exercise 3: Inspect and Filter

1. Run an Nginx container:

```bash
docker container run -d --name myweb -p 9090:80 nginx:alpine
```

2. Use inspect with filters to find:
   - The container's IP address
   - The exposed ports
   - The container's hostname

Hints:

```bash
docker container inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' myweb
docker container inspect -f '{{json .Config.ExposedPorts}}' myweb
docker container inspect -f '{{.Config.Hostname}}' myweb
```

3. Cleanup:

```bash
docker container rm -f myweb
```

---

## Optional Advanced Exercises (Intermediate)

> **Context:** You're the on-call engineer for a small product team. The team runs several containerized services in production. Your job: keep them running, troubleshoot issues, and prevent future outages.

---

## Part 11 (Optional): Resource Constraints

A container on your shared dev server started consuming all available memory, causing SSH to become unresponsive. Other team members are complaining they can't work.

You need to set resource limits to prevent any single container from hogging the host.

Set a memory limit:

```bash
docker container run -d --name limited-mem --memory="256m" nginx:alpine
```

Set a CPU limit (half a CPU core):

```bash
docker container run -d --name limited-cpu --cpus="0.5" nginx:alpine
```

Combine both limits for a production-ready container:

```bash
docker container run -d --name limited \
  --memory="256m" \
  --cpus="0.5" \
  nginx:alpine
```

Watch resource usage in real-time:

```bash
docker container stats --no-stream
```

Verify the limits are applied:

```bash
docker container inspect -f '{{.HostConfig.Memory}}' limited-mem
docker container inspect -f '{{.HostConfig.NanoCpus}}' limited-cpu
```

Now even if the container tries to use more, Docker will enforce the limits.

Cleanup:

```bash
docker container rm -f limited-mem limited-cpu limited
```

---

## Part 12 (Optional): Restart Policies

It's 3 AM and you get paged. A critical container crashed 2 hours ago and nobody restarted it. Customers have been affected. You need to prevent this from happening again.

Docker can automatically restart containers when they fail. Here are your options:

| Policy | Description |
|--------|-------------|
| `no` | Never restart (default) |
| `on-failure[:max]` | Restart only on non-zero exit code |
| `always` | Always restart unless explicitly stopped |
| `unless-stopped` | Like `always`, but not after manual stop |

Run a container that will always restart:

```bash
docker container run -d --name resilient --restart=always nginx:alpine
```

Simulate a crash by killing the main process:

```bash
docker container exec resilient kill 1
```

Check if Docker restarted it automatically:

```bash
docker container ls | grep resilient
```

The container should be running again with a fresh uptime. No page at 3 AM needed.

You can also update the policy on an existing container:

```bash
docker container update --restart=unless-stopped resilient
```

Cleanup:

```bash
docker container rm -f resilient
```

---

## Part 13 (Optional): Health Checks

Users are complaining the app is "up" but not responding. You check and the container shows as running. But the application inside has hung—Docker doesn't know it's broken.

Health checks let Docker monitor your application and detect when it's unhealthy, even if the process is still running.

Run a container with a health check that verifies the web server responds:

```bash
docker container run -d --name healthy \
  --health-cmd="curl -f http://localhost/ || exit 1" \
  --health-interval=10s \
  --health-timeout=5s \
  --health-retries=3 \
  nginx:alpine
```

Wait 10-15 seconds, then check the health status:

```bash
docker container inspect -f '{{json .State.Health}}' healthy | jq .
```

You can also see the health in the container list:

```bash
docker container ls
```

Look for `(healthy)` or `(unhealthy)` in the STATUS column. Now you (and your monitoring tools) can tell when the app is actually working, not just running.

Cleanup:

```bash
docker container rm -f healthy
```

---

## Part 14 (Optional): Container Networking Basics

Your team is deploying a web frontend that needs to talk to a backend API container. They've been hardcoding IP addresses, and it keeps breaking every time containers restart.

Custom Docker networks let containers find each other by name—no IP addresses needed.

First, see what networks exist:

```bash
docker network ls
```

Create a custom network for your application:

```bash
docker network create mynet
```

Run two containers on the same network:

```bash
docker container run -d --name web1 --network mynet nginx:alpine
docker container run -d --name web2 --network mynet nginx:alpine
```

Now `web1` can reach `web2` by name:

```bash
docker container exec web1 ping -c 3 web2
```

It works! No hardcoded IPs. When containers restart with new IPs, the names still resolve correctly.

Cleanup:

```bash
docker container rm -f web1 web2
docker network rm mynet
```

---

## Part 15 (Optional): Understanding Signals and Graceful Shutdown

Your application isn't saving its state before shutdown. Users are losing their work every time you deploy updates.

When you run `docker stop`, Docker sends a SIGTERM signal to your container's main process (PID 1). Well-behaved applications catch this signal and shut down gracefully—saving data, closing connections, finishing requests.

Let's see this in action. Run a container that traps the SIGTERM signal:

```bash
docker container run -d --name signal-test alpine sh -c '
trap "echo Received SIGTERM; exit 0" TERM
echo "Container started, PID $$"
while true; do sleep 1; done
'
```

Check the logs:

```bash
docker container logs signal-test
```

Now stop the container and watch it handle the signal:

```bash
docker container stop signal-test
docker container logs signal-test
```

You should see "Received SIGTERM" in the logs. The container caught the signal and exited cleanly.

If your app takes longer to shut down, you can increase the timeout (default is 10 seconds):

```bash
docker container run -d --name slow-app --stop-timeout=30 nginx:alpine
docker container stop slow-app
```

Cleanup:

```bash
docker container rm signal-test slow-app 2>/dev/null || true
```

---

## Part 16 (Optional): Finding What Changed in a Container

A container started misbehaving after a colleague "fixed something" inside it. Nobody remembers exactly what was changed. You need to find out before you can reproduce the fix properly.

Docker tracks filesystem changes made to a container. Let's see how.

Run a container and make some changes:

```bash
docker container run --name diff-demo alpine sh -c 'echo hello > /newfile.txt && rm /etc/motd'
```

Now view what changed:

```bash
docker container diff diff-demo
```

The output shows:

| Symbol | Meaning |
|--------|---------|
| `A` | File was Added |
| `C` | File was Changed |
| `D` | File was Deleted |

Now you know exactly what your colleague modified. You can document it properly and update the image instead of relying on manual changes.

Cleanup:

```bash
docker container rm diff-demo
```

---

## Part 17 (Optional): Copy Files To/From Containers

An application is failing but only logs to a file inside the container (not stdout). You need to grab the logs before removing the container to investigate.

Docker lets you copy files in and out of containers—even stopped ones.

Run a container:

```bash
docker container run -d --name copy-demo nginx:alpine
```

Copy a file FROM the container to your host:

```bash
docker container cp copy-demo:/etc/nginx/nginx.conf ./nginx.conf
cat nginx.conf
```

You can also copy files INTO a container. This is useful for quick config changes during debugging:

```bash
echo "<h1>Custom Page</h1>" > index.html
docker container cp index.html copy-demo:/usr/share/nginx/html/index.html
```

Verify the file was copied:

```bash
docker container exec copy-demo cat /usr/share/nginx/html/index.html
```

This technique saved your logs. Now you can analyze the problem even after the container is gone.

Cleanup:

```bash
docker container rm -f copy-demo
rm nginx.conf index.html
```

---

## Key Takeaways

- **docker container run** creates and starts a container from an image
- Containers are **ephemeral** by default—data is lost when removed
- Use **-d** to run containers as background daemons
- Use **--name** to give containers meaningful identifiers
- **docker container ls** shows running containers; add **-a** for all
- **stop** sends SIGTERM → SIGKILL; **start** resumes stopped containers
- **exec** runs additional processes in running containers
- **attach** connects your terminal to the main process
- **logs** retrieves stdout/stderr from containers
- **inspect** provides detailed container metadata

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `docker container run IMAGE` | Create and start a container |
| `docker container run -d IMAGE` | Run as daemon (background) |
| `docker container run -it IMAGE sh` | Run interactively with shell |
| `docker container ls` | List running containers |
| `docker container ls -a` | List all containers |
| `docker container stop NAME` | Stop a container |
| `docker container start NAME` | Start a stopped container |
| `docker container rm NAME` | Remove a container |
| `docker container rm -f NAME` | Force remove (even if running) |
| `docker container inspect NAME` | View container metadata |
| `docker container exec -it NAME sh` | Open shell in running container |
| `docker container attach NAME` | Attach to main process |
| `docker container logs NAME` | View container logs |
| `docker container logs -f NAME` | Follow logs in real-time |
| `docker container stats` | Live resource usage |
| `docker container prune` | Remove all stopped containers |

---

## Cleanup (End of Lab)

```bash
docker container rm -f trivia nginx myubuntu myweb sleeper 2>/dev/null || true
docker container rm -f limited-mem limited-cpu limited resilient healthy 2>/dev/null || true
docker container rm -f web1 web2 signal-test slow-app diff-demo copy-demo 2>/dev/null || true
docker network rm mynet 2>/dev/null || true
docker container prune -f
```


