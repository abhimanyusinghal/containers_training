# Docker Containers – Test Questions

Use these questions to assess participant understanding after completing the Docker Containers lab.

---

## Section 1: Multiple Choice

**1. What is the correct structure of the `docker container run` command?**

a) `docker run container [IMAGE] [OPTIONS]`  
b) `docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`  
c) `docker container [IMAGE] run [OPTIONS]`  
d) `docker run [IMAGE] container [OPTIONS]`  

---

**2. What does the `-d` flag do when running a container?**

a) Deletes the container after it exits  
b) Downloads the image before running  
c) Runs the container as a daemon (background process)  
d) Enables debug mode  

---

**3. Which command lists only running containers?**

a) `docker container ls -a`  
b) `docker container ls`  
c) `docker container list --running`  
d) `docker ps -a`  

---

**4. What happens when you run `docker container stop`?**

a) The container is immediately terminated  
b) Docker sends SIGTERM, waits 10 seconds, then sends SIGKILL if needed  
c) The container is paused  
d) Docker sends SIGKILL immediately  

---

**5. Which command removes a running container forcefully?**

a) `docker container delete --force mycontainer`  
b) `docker container rm mycontainer`  
c) `docker container rm -f mycontainer`  
d) `docker container kill mycontainer`  

---

**6. What do the `-i` and `-t` flags do together when running a container?**

a) Install and test the container  
b) Initialize and terminate the container  
c) Keep STDIN open and allocate a pseudo-TTY (interactive terminal)  
d) Import and tag the container  

---

**7. Which command shows detailed metadata about a container in JSON format?**

a) `docker container info mycontainer`  
b) `docker container describe mycontainer`  
c) `docker container inspect mycontainer`  
d) `docker container metadata mycontainer`  

---

**8. What is the difference between `docker container exec` and `docker container attach`?**

a) `exec` runs a new process; `attach` connects to the main process  
b) `attach` runs a new process; `exec` connects to the main process  
c) They are identical  
d) `exec` is for running containers; `attach` is for stopped containers  

---

**9. Which command shows the last 10 lines of container logs and follows new output?**

a) `docker container logs --last 10 --follow mycontainer`  
b) `docker container logs --tail 10 --follow mycontainer`  
c) `docker container logs -n 10 -f mycontainer`  
d) `docker container logs --lines 10 --watch mycontainer`  

---

**10. What does the `--name` flag do?**

a) Renames the image used by the container  
b) Assigns a custom name to the container  
c) Sets the hostname inside the container  
d) Creates a name alias for the container  

---

**11. Which restart policy ensures a container always restarts unless manually stopped?**

a) `--restart=always`  
b) `--restart=unless-stopped`  
c) `--restart=on-failure`  
d) `--restart=auto`  

---

**12. What does `docker container prune` do?**

a) Removes all containers  
b) Removes all stopped containers  
c) Removes containers older than 24 hours  
d) Removes containers without names  

---

**13. Which command shows filesystem changes made inside a container?**

a) `docker container changes mycontainer`  
b) `docker container diff mycontainer`  
c) `docker container history mycontainer`  
d) `docker container compare mycontainer`  

---

**14. What does the `--memory="256m"` flag do?**

a) Allocates minimum 256MB of memory  
b) Limits the container to a maximum of 256MB of memory  
c) Reserves 256MB of swap space  
d) Sets the memory warning threshold  

---

## Section 2: True or False

**15. Container names must be unique on a Docker host.**

☐ True  
☐ False  

---

**16. When a container exits, all data in its writable layer is preserved until the container is removed.**

☐ True  
☐ False  

---

**17. The `docker container start` command creates a new container from an image.**

☐ True  
☐ False  

---

**18. You can use either container name or container ID with most Docker commands.**

☐ True  
☐ False  

---

**19. Pressing `Ctrl+C` while attached to a container always stops the container.**

☐ True  
☐ False  

---

**20. The `--rm` flag automatically removes a container when it exits.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**21. Complete the command to run an interactive shell in an Alpine container:**

```bash
docker container run __________ alpine /bin/sh
```

---

**22. Complete the command to get only the IP address of a container:**

```bash
docker container inspect -f '{{range .NetworkSettings.Networks}}{{.__________}}{{end}}' mycontainer
```

---

**23. The default grace period for `docker container stop` is __________ seconds.**

---

**24. Complete the command to copy a file from a container to the host:**

```bash
docker container __________ mycontainer:/app/config.txt ./config.txt
```

---

**25. Complete the command to run a command as root inside a running container:**

```bash
docker container exec __________ mycontainer whoami
```

---

**26. In `docker container diff` output, the letter __________ indicates a file was added.**

---

## Section 4: Short Answer

**27. Explain the difference between `docker container stop` and `docker container kill`.**

---

**28. A container shows status "Exited (137)". What does exit code 137 typically indicate?**

---

**29. Why might you use `docker container exec` instead of `docker container attach` to troubleshoot a running container?**

---

**30. Describe what happens step-by-step when you run `docker container run alpine echo "Hello"`.**

---

**31. What is the purpose of container health checks, and how do they differ from just checking if a container is running?**

---

**32. Explain why the `--restart=unless-stopped` policy is often preferred over `--restart=always` in production.**

---

## Section 5: Practical Scenarios

**33. Write the command to run an Nginx container that:**
- Runs in the background
- Is named `webserver`
- Maps host port 8080 to container port 80
- Automatically restarts unless manually stopped

---

**34. Your container named `myapp` is misbehaving. Write the commands to:**
1. View the last 50 lines of logs
2. Follow new log output in real-time
3. Open an interactive shell to investigate

---

**35. Write the commands to:**
1. Run a detached container named `test` using Alpine that sleeps for 1 hour
2. Check if it's running
3. Stop it gracefully
4. Remove it

---

**36. A colleague created a container but forgot what they changed inside it. The container is named `modified`. Write the commands to:**
1. See what files were added, changed, or deleted
2. Copy a specific file `/app/config.json` from the container to your current directory

---

**37. Write a command to run a container with the following resource constraints:**
- Maximum 512MB of memory
- Limited to 0.5 CPU cores
- Named `limited-app`
- Using the `nginx:alpine` image

---

**38. Your production container keeps crashing. Write the commands to:**
1. Check the container's current state and exit code
2. View logs from the last crash
3. Set up the container to restart automatically on failure (max 3 attempts)

---

**39. Write the command to run an Ubuntu container interactively, install `curl` inside it, and then exit. Then write the commands to:**
1. Restart the stopped container
2. Execute a command to verify curl is still installed

---

**40. You need to pass environment variables to a container. Write the command to run an Alpine container that:**
- Has an environment variable `APP_ENV` set to `production`
- Has an environment variable `DEBUG` set to `false`
- Runs the command `env` to display all environment variables

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | b | Correct structure: `docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]` |
| 2 | c | `-d` runs the container in detached (daemon/background) mode |
| 3 | b | `docker container ls` shows only running containers; `-a` includes stopped |
| 4 | b | Docker sends SIGTERM first, waits 10 seconds grace period, then SIGKILL |
| 5 | c | `-f` or `--force` flag forces removal of running containers |
| 6 | c | `-i` keeps STDIN open, `-t` allocates a terminal; together enable interactive use |
| 7 | c | `docker container inspect` returns detailed JSON metadata |
| 8 | a | `exec` starts a new process; `attach` connects to PID 1 (main process) |
| 9 | b | `--tail 10 --follow` shows last 10 lines and follows new output |
| 10 | b | `--name` assigns a custom identifier to the container |
| 11 | b | `unless-stopped` restarts always except when manually stopped |
| 12 | b | `prune` removes all stopped (exited) containers |
| 13 | b | `diff` shows filesystem changes (A=added, C=changed, D=deleted) |
| 14 | b | `--memory` sets the maximum memory limit |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 15 | True | Container names must be unique; Docker returns an error if you reuse a name |
| 16 | True | The writable layer persists until the container is removed with `rm` |
| 17 | False | `start` resumes a stopped container; `run` creates a new one |
| 18 | True | Most commands accept either the name or ID (or ID prefix) |
| 19 | False | Depends on the process; use `Ctrl+P, Ctrl+Q` to detach without stopping |
| 20 | True | `--rm` removes the container and its writable layer on exit |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 21 | `-it` or `-i -t` |
| 22 | `IPAddress` |
| 23 | `10` |
| 24 | `cp` |
| 25 | `-u root` |
| 26 | `A` |

### Section 4: Short Answer

**27.** 
- `docker container stop` sends SIGTERM first, giving the process time to clean up gracefully (save state, close connections). After 10 seconds, it sends SIGKILL.
- `docker container kill` sends SIGKILL immediately (or a specified signal with `-s`), terminating the process without graceful shutdown.
Use `stop` for normal operations; `kill` for unresponsive containers.

**28.** Exit code 137 = 128 + 9 (SIGKILL). This means the container was killed, typically because:
- It exceeded memory limits and was OOM-killed
- `docker container kill` was used
- `docker container stop` timed out and sent SIGKILL
- The host system killed it due to resource pressure

**29.** `docker container exec` is preferred because:
- It starts a new, independent process—exiting won't affect the main container process
- You can run specific commands without interrupting the application
- `attach` connects to PID 1; pressing Ctrl+C might stop the entire container
- `exec` allows running as different users (`-u`) and with different environments

**30.** Step-by-step:
1. Docker checks if `alpine` image exists locally
2. If not found, pulls `alpine:latest` from Docker Hub
3. Creates a new container from the image (new writable layer)
4. Allocates a filesystem and network interface
5. Executes `/bin/echo "Hello"` as PID 1 inside the container
6. `echo` outputs "Hello" to stdout
7. The `echo` process exits with code 0
8. Container transitions to "Exited (0)" state
9. Container remains until explicitly removed

**31.** Health checks test if the **application** is functioning correctly, not just if the container process is running. A container can be "running" (PID 1 alive) while the application inside is:
- Deadlocked
- Out of memory internally
- Unable to handle requests
- In an error state

Health checks run commands periodically to verify actual application health (e.g., HTTP requests, database connections).

**32.** `unless-stopped` is preferred because:
- `always` restarts the container even after Docker daemon restarts or system reboot
- If you intentionally stop a container with `docker stop`, `always` will restart it when Docker starts
- `unless-stopped` respects manual stops—container stays stopped until you start it
- Gives operators more control during maintenance windows

### Section 5: Practical Scenarios

**33.**
```bash
docker container run -d \
  --name webserver \
  -p 8080:80 \
  --restart=unless-stopped \
  nginx
```

**34.**
```bash
# View last 50 lines
docker container logs --tail 50 myapp

# Follow logs in real-time
docker container logs --follow myapp

# Open interactive shell
docker container exec -it myapp /bin/sh
# (or /bin/bash if available)
```

**35.**
```bash
# Run detached container
docker container run -d --name test alpine sleep 3600

# Check if running
docker container ls | grep test

# Stop gracefully
docker container stop test

# Remove it
docker container rm test
```

**36.**
```bash
# See filesystem changes
docker container diff modified

# Copy file to current directory
docker container cp modified:/app/config.json ./config.json
```

**37.**
```bash
docker container run -d \
  --name limited-app \
  --memory="512m" \
  --cpus="0.5" \
  nginx:alpine
```

**38.**
```bash
# Check state and exit code
docker container inspect -f '{{json .State}}' mycontainer | jq

# View logs (including from previous instance)
docker container logs mycontainer

# Run with restart policy (for new container)
docker container run -d \
  --name mycontainer \
  --restart=on-failure:3 \
  myimage

# Or update existing container
docker container update --restart=on-failure:3 mycontainer
```

**39.**
```bash
# Run interactive Ubuntu and install curl
docker container run -it --name myubuntu ubuntu:22.04 /bin/bash
# Inside container:
apt update && apt install -y curl
exit

# Restart the stopped container
docker container start myubuntu

# Verify curl is installed
docker container exec myubuntu curl --version
```

**40.**
```bash
docker container run --rm \
  -e APP_ENV=production \
  -e DEBUG=false \
  alpine env
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 36-40 | Expert – Ready for production container management |
| 29-35 | Proficient – Solid understanding, minor gaps |
| 22-28 | Intermediate – Review advanced topics |
| 15-21 | Beginner – Review core concepts |
| 0-14 | Needs Review – Retake the lab exercises |


