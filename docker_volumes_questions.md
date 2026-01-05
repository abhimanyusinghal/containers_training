# Docker Volumes – Test Questions

Use these questions to assess participant understanding after completing the Docker Volumes lab.

---

## Section 1: Multiple Choice

**1. What happens to data stored in a container's writable layer when the container is deleted?**

a) It is automatically backed up to Docker Hub  
b) It is moved to a default volume  
c) It is permanently deleted  
d) It remains on the host filesystem  

---

**2. Which command creates a named volume called `appdata`?**

a) `docker container run -v appdata:/data alpine`  
b) `docker volume new appdata`  
c) `docker volume create appdata`  
d) `docker create volume appdata`  

---

**3. What is the difference between a named volume and an anonymous volume?**

a) Named volumes are faster than anonymous volumes  
b) Named volumes have a user-defined name; anonymous volumes have a random hash as their name  
c) Anonymous volumes persist longer than named volumes  
d) There is no difference  

---

**4. Which syntax correctly mounts the host directory `/tmp/data` to `/app/data` inside the container?**

a) `docker container run -v /app/data:/tmp/data alpine`  
b) `docker container run -v /tmp/data:/app/data alpine`  
c) `docker container run --volume /app/data=/tmp/data alpine`  
d) `docker container run -mount /tmp/data:/app/data alpine`  

---

**5. What does the `VOLUME` instruction in a Dockerfile do?**

a) Creates a named volume with a specific name  
b) Declares a mount point; each container gets its own anonymous volume at that path  
c) Copies data from the host to the container  
d) Shares a volume between all containers using that image  

---

**6. Which command lists all Docker volumes on the system?**

a) `docker volume show`  
b) `docker volumes`  
c) `docker volume ls`  
d) `docker list volumes`  

---

**7. When you mount a volume onto a container path that already contains data in the image, what happens?**

a) The volume data and image data are merged  
b) The image data is copied to the volume (if volume is empty), then the volume is mounted  
c) The image data at that path is hidden by the mount  
d) Docker throws an error  

---

**8. Which flag makes a volume mount read-only?**

a) `-v myvolume:/data:readonly`  
b) `-v myvolume:/data:ro`  
c) `-v myvolume:/data --readonly`  
d) `-v myvolume:/data:r`  

---

**9. What is the main advantage of using `--mount` over `-v` for volume mounts?**

a) `--mount` is faster  
b) `--mount` uses explicit key/value syntax, making it clearer and less error-prone  
c) `--mount` supports more volume drivers  
d) `-v` is deprecated  

---

**10. Which command removes all volumes that are not currently used by any container?**

a) `docker volume rm --all`  
b) `docker volume prune`  
c) `docker volume clean`  
d) `docker volume delete --unused`  

---

## Section 2: True or False

**11. Bind mounts require the host path to exist before running the container.**

☐ True  
☐ False  

---

**12. Anonymous volumes are automatically deleted when their container is removed.**

☐ True  
☐ False  

---

**13. You can share a single named volume between multiple running containers.**

☐ True  
☐ False  

---

**14. The `docker volume inspect` command shows the physical location of volume data on the host.**

☐ True  
☐ False  

---

**15. Volumes can only be used with Linux containers, not Windows containers.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**16. Complete the command to create a volume with a label:**

```bash
docker volume create --label purpose=database __________
```

---

**17. Complete the `--mount` syntax to mount a named volume called `html` to `/usr/share/nginx/html`:**

```bash
docker container run --mount type=volume,src=________,dst=________ nginx
```

---

**18. The default location where Docker stores volume data on a Linux host is:**

```
/var/lib/docker/__________
```

---

**19. Complete the command to backup a volume to a tar file:**

```bash
docker container run --rm \
  -v mydata:/data:__ \
  -v $(pwd):/backup \
  alpine tar -czf /backup/mydata.tgz -C /data .
```

---

**20. To find volumes that are not attached to any container, use the filter:**

```bash
docker volume ls -f __________=true
```

---

## Section 4: Short Answer

**21. Explain the difference between a volume and a bind mount. When would you use each?**

---

**22. A developer runs the following command but complains that the volume is empty. Explain why this happens:**

```bash
docker volume create myhtml
docker container run -d -v myhtml:/usr/share/nginx/html nginx
curl localhost  # Returns 403 Forbidden
```

---

**23. What is a "dangling" volume and how do they typically get created?**

---

**24. Describe the steps to migrate data from one volume to another.**

---

**25. Why might a container fail to write to a mounted volume, even though the mount was successful?**

---

## Section 5: Practical Scenarios

**26. You need to run a PostgreSQL container with persistent data storage. Write the command to:**
- Create a named volume called `pgdata`
- Run the official `postgres:15` image
- Mount the volume to `/var/lib/postgresql/data`
- Set the `POSTGRES_PASSWORD` environment variable to `secret`

---

**27. Your team has a volume with important data. Write the commands to:**
- Create a backup of the volume `important-data` to a file called `backup.tgz`
- Restore the backup to a new volume called `restored-data`

---

**28. You're debugging an issue and need to inspect the contents of a volume called `app-logs`. Write a command to start an interactive container that mounts this volume so you can explore its contents.**

---

**29. Write a Dockerfile that:**
- Uses `alpine` as the base image
- Declares a volume at `/app/data`
- Sets the working directory to `/app`
- Copies a script called `app.sh` and makes it the entrypoint

---

**30. A production container runs as a non-root user (UID 1000). Before starting the application, you need to ensure the volume has correct permissions. Write the command to set ownership of the volume `appdata` to UID 1000.**

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | c | Container writable layers are deleted with the container |
| 2 | c | `docker volume create` is the correct command |
| 3 | b | Named volumes have user-friendly names; anonymous volumes have random hashes |
| 4 | b | Syntax is `-v HOST_PATH:CONTAINER_PATH` |
| 5 | b | VOLUME declares a mount point; each container gets its own anonymous volume |
| 6 | c | `docker volume ls` lists all volumes |
| 7 | c | The mount hides (overlays) the image data at that path |
| 8 | b | `:ro` suffix makes the mount read-only |
| 9 | b | `--mount` is more explicit and less error-prone |
| 10 | b | `docker volume prune` removes unused volumes |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 11 | False | Docker creates the host path if it doesn't exist (for bind mounts) |
| 12 | False | Anonymous volumes persist unless `--rm` flag is used or manually removed |
| 13 | True | Multiple containers can mount the same named volume |
| 14 | True | `Mountpoint` field shows the host path |
| 15 | False | Volumes work with both Linux and Windows containers |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 16 | `mydb` (or any volume name) |
| 17 | `src=html,dst=/usr/share/nginx/html` |
| 18 | `volumes` |
| 19 | `ro` (read-only to prevent accidental modification during backup) |
| 20 | `dangling` |

### Section 4: Short Answer

**21.** 
- **Volumes** are managed by Docker, stored in Docker's storage area, portable, and work across different hosts. Best for production data persistence.
- **Bind mounts** map a specific host path into the container, depend on host filesystem structure. Best for development (live code editing) and sharing config files.

**22.** 
When you mount an empty volume onto `/usr/share/nginx/html`, it hides the default nginx files that were in the image. The volume is empty, so nginx has no index.html to serve (403 Forbidden). Solution: populate the volume with content before or after mounting.

**23.** 
A dangling volume is a volume not referenced by any container. They're created when:
- Containers are removed without the `--rm` flag
- Anonymous volumes are created but containers are deleted
- Named volumes remain after all containers using them are removed

**24.** 
```bash
# 1. Create new volume
docker volume create new-volume

# 2. Copy data using a temporary container
docker container run --rm \
  -v old-volume:/source:ro \
  -v new-volume:/dest \
  alpine sh -c "cp -a /source/. /dest/"
```

**25.** 
Permission issues—the container process runs as a user (often non-root) that doesn't have write permissions on the volume. The volume may be owned by root (UID 0) while the app runs as a different UID. Solution: run an init container to `chown` the volume to the correct UID.

### Section 5: Practical Scenarios

**26.**
```bash
docker volume create pgdata

docker container run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:15
```

**27.**
```bash
# Backup
docker container run --rm \
  -v important-data:/data:ro \
  -v $(pwd):/backup \
  alpine tar -czf /backup/backup.tgz -C /data .

# Restore
docker volume create restored-data

docker container run --rm \
  -v restored-data:/data \
  -v $(pwd):/backup \
  alpine tar -xzf /backup/backup.tgz -C /data
```

**28.**
```bash
docker container run -it --rm -v app-logs:/logs alpine sh
# Then: ls -la /logs, cat /logs/somefile.log, etc.
```

**29.**
```dockerfile
FROM alpine
VOLUME ["/app/data"]
WORKDIR /app
COPY app.sh .
ENTRYPOINT ["/app/app.sh"]
```

**30.**
```bash
docker container run --rm -v appdata:/data alpine chown -R 1000:1000 /data
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 27-30 | Expert – Ready for production Docker workloads |
| 22-26 | Proficient – Solid understanding, minor gaps |
| 17-21 | Intermediate – Review advanced topics |
| 12-16 | Beginner – Review core concepts |
| 0-11 | Needs Review – Retake the lab exercises |


