# Docker Images – Test Questions

Use these questions to assess participant understanding after completing the Docker Images lab.

---

## Section 1: Multiple Choice

**1. What is a Docker image?**

a) A running instance of a container  
b) A read-only template made up of layers used to create containers  
c) A configuration file for Docker  
d) A backup of a container's filesystem  

---

**2. Which of the following is NOT a valid way to create a Docker image?**

a) Using `docker container commit`  
b) Building from a Dockerfile  
c) Importing from a tarball  
d) Using `docker image create`  

---

**3. What does each instruction in a Dockerfile create?**

a) A new container  
b) A new layer in the image  
c) A new image  
d) A new volume  

---

**4. Which Dockerfile instruction sets the base image?**

a) `BASE`  
b) `IMAGE`  
c) `FROM`  
d) `INHERIT`  

---

**5. What is the difference between `COPY` and `ADD` in a Dockerfile?**

a) They are identical  
b) `ADD` can extract archives and fetch URLs; `COPY` only copies files  
c) `COPY` can extract archives; `ADD` only copies files  
d) `ADD` is deprecated in favor of `COPY`  

---

**6. What does the `.` at the end of `docker image build -t myapp .` represent?**

a) The current tag  
b) The build context (directory containing files for the build)  
c) All files in the current directory  
d) The Dockerfile location  

---

**7. Which command shows the layers and commands that created an image?**

a) `docker image layers myimage`  
b) `docker image history myimage`  
c) `docker image inspect myimage`  
d) `docker image show myimage`  

---

**8. What happens if you don't specify a tag when building an image?**

a) The build fails  
b) Docker uses `latest` as the default tag  
c) Docker generates a random tag  
d) The image is created without a tag  

---

**9. Which command exports an image to a tar file?**

a) `docker image export -o file.tar myimage`  
b) `docker image save -o file.tar myimage`  
c) `docker image backup -o file.tar myimage`  
d) `docker image tar -o file.tar myimage`  

---

**10. Which command imports an image from a tar file?**

a) `docker image import -i file.tar`  
b) `docker image load -i file.tar`  
c) `docker image restore -i file.tar`  
d) `docker image untar -i file.tar`  

---

**11. What is the purpose of a multi-stage build?**

a) To build images faster  
b) To create smaller production images by separating build and runtime stages  
c) To build multiple images at once  
d) To support multiple architectures  

---

**12. What does a `.dockerignore` file do?**

a) Lists images that should not be pushed  
b) Excludes files from the build context  
c) Ignores certain Dockerfile instructions  
d) Prevents certain containers from running  

---

**13. In a multi-stage Dockerfile, what does `COPY --from=build` do?**

a) Copies files from the host  
b) Copies files from a stage named "build"  
c) Copies files from the build context  
d) Copies files from a running container  

---

**14. Which Dockerfile instruction sets the default command when a container starts?**

a) `RUN`  
b) `EXEC`  
c) `CMD`  
d) `START`  

---

## Section 2: True or False

**15. Docker images are mutable—you can modify them after they are created.**

☐ True  
☐ False  

---

**16. Layer caching means unchanged Dockerfile instructions don't need to be rebuilt.**

☐ True  
☐ False  

---

**17. The `RUN` instruction executes commands when a container starts.**

☐ True  
☐ False  

---

**18. Multiple tags can point to the same image ID.**

☐ True  
☐ False  

---

**19. The `EXPOSE` instruction automatically publishes ports to the host.**

☐ True  
☐ False  

---

**20. You must be logged in to Docker Hub to push images.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**21. Complete the Dockerfile instruction to set the working directory:**

```dockerfile
__________ /app
```

---

**22. Complete the command to build an image with a specific Dockerfile name:**

```bash
docker image build -f __________ -t myapp .
```

---

**23. Complete the command to remove all dangling (untagged) images:**

```bash
docker image __________
```

---

**24. In a multi-stage build, name the first stage using:**

```dockerfile
FROM alpine __________ build
```

---

**25. Complete the Dockerfile instruction to set an environment variable:**

```dockerfile
__________ APP_VERSION=1.0.0
```

---

**26. Complete the command to view the full (untruncated) history of an image:**

```bash
docker image history __________ myimage
```

---

## Section 4: Short Answer

**27. Explain why Docker uses layers, and what benefit layer caching provides during builds.**

---

**28. Why is it recommended to use Dockerfiles instead of `docker container commit` for creating production images?**

---

**29. Explain the difference between `CMD` and `ENTRYPOINT` in a Dockerfile.**

---

**30. A Dockerfile has this structure. Why is this considered bad practice for layer caching?**

```dockerfile
FROM python:3.11
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

**31. What is the purpose of using a minimal base image like Alpine or Distroless in production?**

---

**32. Explain what "build context" means and why it matters for build performance.**

---

## Section 5: Practical Scenarios

**33. Write a Dockerfile that:**
- Uses Alpine as the base image
- Installs `curl` and `jq`
- Sets the working directory to `/app`
- Sets the default command to display curl's version

---

**34. You have a working container named `dev-container` with some manual changes. Write the commands to:**
1. Create an image from this container named `myapp:v1`
2. View the new image in your image list
3. Run a new container from this image

---

**35. Write the commands to:**
1. Build an image tagged `myapp:1.0` from the current directory
2. Add additional tags `myapp:1` and `myapp:latest`
3. List all tags of `myapp`

---

**36. Your image is too large at 500MB. Rewrite this Dockerfile using a multi-stage build to reduce size:**

```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
CMD ["node", "dist/server.js"]
```

---

**37. Write the commands to:**
1. Save an image named `myapp:latest` to a file called `myapp-backup.tar`
2. Remove the image locally
3. Load the image back from the file
4. Verify the image is restored

---

**38. This Dockerfile is inefficient for layer caching. Rewrite it to optimize cache usage:**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
CMD ["python", "app.py"]
```

---

**39. Write a Dockerfile that uses build arguments to:**
- Accept a build argument `APP_ENV` with default value `development`
- Set it as an environment variable inside the container
- Print the environment at container startup

---

**40. You're building an image and the build context is 2GB, making builds slow. Write a `.dockerignore` file that excludes:**
- `node_modules` directory
- All `.log` files
- The `.git` directory
- Any `*.md` files
- The `dist` directory

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | b | Images are read-only templates made of layers; containers are running instances |
| 2 | d | There is no `docker image create` command |
| 3 | b | Each instruction creates a new layer in the image |
| 4 | c | `FROM` specifies the base image for the build |
| 5 | b | `ADD` has extra features (URL fetch, archive extraction); `COPY` is simpler |
| 6 | b | The `.` specifies the build context directory |
| 7 | b | `docker image history` shows layers and the commands that created them |
| 8 | b | Docker defaults to the `latest` tag |
| 9 | b | `docker image save` exports to a tar file |
| 10 | b | `docker image load` imports from a tar file |
| 11 | b | Multi-stage builds separate build tools from runtime, reducing image size |
| 12 | b | `.dockerignore` excludes files from the build context |
| 13 | b | `--from=build` copies from the stage named "build" |
| 14 | c | `CMD` sets the default command for container startup |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 15 | False | Images are immutable (read-only); you create new images with changes |
| 16 | True | Unchanged layers are cached and reused, speeding up builds |
| 17 | False | `RUN` executes during build; `CMD`/`ENTRYPOINT` execute at container start |
| 18 | True | Tags are just pointers; multiple tags can reference the same image ID |
| 19 | False | `EXPOSE` documents ports but doesn't publish them; use `-p` at runtime |
| 20 | True | Authentication is required to push to Docker Hub |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 21 | `WORKDIR` |
| 22 | `Dockerfile.custom` (or any filename) |
| 23 | `prune` |
| 24 | `AS` |
| 25 | `ENV` |
| 26 | `--no-trunc` |

### Section 4: Short Answer

**27.** Docker uses layers for:
- **Efficiency**: Layers are shared between images, saving disk space
- **Faster builds**: Unchanged layers are cached; only modified layers rebuild
- **Faster distribution**: When pulling, only missing layers are downloaded

Layer caching benefit: If you change only your application code (last layers), dependency installation (earlier layers) uses cache, dramatically speeding up builds.

**28.** Dockerfiles are preferred because:
- **Reproducible**: Anyone can rebuild the exact same image from the Dockerfile
- **Documented**: The Dockerfile serves as documentation of the build process
- **Version controlled**: Can be stored in Git with application code
- **Auditable**: Security teams can review what's in the image
- **Automated**: Works with CI/CD pipelines

`docker commit` creates "mystery images" where nobody knows exactly what changed.

**29.** 
- `CMD` provides default arguments that can be completely overridden at runtime
- `ENTRYPOINT` sets the main executable; CMD becomes its arguments
- When both are used, `ENTRYPOINT` is the command and `CMD` is the default arguments
- Example: `ENTRYPOINT ["python"]` with `CMD ["app.py"]` runs `python app.py`, but you can override with `docker run myimage other.py`

**30.** The problem is `COPY . .` comes before `pip install`:
- Any code change invalidates the COPY layer
- This invalidates all subsequent layers, including pip install
- Dependencies reinstall on every code change

Better approach: Copy requirements.txt first, install dependencies, then copy code:
```dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

**31.** Minimal base images provide:
- **Smaller size**: Less to download, faster deployments
- **Smaller attack surface**: Fewer packages = fewer potential vulnerabilities
- **Faster scanning**: Security scanners process smaller images faster
- **Lower CVE count**: Fewer pre-installed packages means fewer vulnerabilities to manage
- Alpine is ~7MB vs Ubuntu's ~77MB; Distroless is ~2MB

**32.** Build context is the set of files sent to the Docker daemon when building:
- Everything in the specified directory is sent to the daemon
- Large contexts slow down builds (network transfer to daemon)
- Unnecessary files waste time and may accidentally be included in images
- Use `.dockerignore` to exclude large/unnecessary files
- Keep context minimal: only include files needed for the build

### Section 5: Practical Scenarios

**33.**
```dockerfile
FROM alpine
RUN apk update && apk add --no-cache curl jq
WORKDIR /app
CMD ["curl", "--version"]
```

**34.**
```bash
# Create image from container
docker container commit dev-container myapp:v1

# View the image
docker image ls | grep myapp

# Run a container from the new image
docker container run --rm myapp:v1
```

**35.**
```bash
# Build initial image
docker image build -t myapp:1.0 .

# Add additional tags
docker image tag myapp:1.0 myapp:1
docker image tag myapp:1.0 myapp:latest

# List all tags
docker image ls myapp
```

**36.**
```dockerfile
# Stage 1: Build
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production (smaller image)
FROM node:18-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
CMD ["node", "dist/server.js"]
```

Or even smaller with only production dependencies:
```dockerfile
# Stage 1: Build
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY --from=build /app/dist ./dist
CMD ["node", "dist/server.js"]
```

**37.**
```bash
# Save image to file
docker image save -o myapp-backup.tar myapp:latest

# Remove local image
docker image rm myapp:latest

# Load from file
docker image load -i myapp-backup.tar

# Verify restoration
docker image ls myapp
```

**38.**
```dockerfile
FROM python:3.11-slim
WORKDIR /app

# Copy only requirements first (changes less frequently)
COPY requirements.txt .

# Install dependencies (cached if requirements unchanged)
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code (changes frequently)
COPY . .

CMD ["python", "app.py"]
```

**39.**
```dockerfile
FROM alpine

# Define build argument with default
ARG APP_ENV=development

# Set as environment variable
ENV APP_ENV=${APP_ENV}

# Print environment at startup
CMD ["env"]
```

Build with different environments:
```bash
docker image build -t myapp:dev .
docker image build --build-arg APP_ENV=production -t myapp:prod .
```

**40.**
```
# Dependencies
node_modules/

# Build output
dist/

# Version control
.git/

# Logs
*.log

# Documentation
*.md
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 36-40 | Expert – Ready for production image management |
| 29-35 | Proficient – Solid understanding, minor gaps |
| 22-28 | Intermediate – Review advanced topics |
| 15-21 | Beginner – Review core concepts |
| 0-14 | Needs Review – Retake the lab exercises |


