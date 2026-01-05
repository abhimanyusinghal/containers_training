# Container Registries – Test Questions

Use these questions to assess participant understanding after completing the Container Registries lab.

---

## Section 1: Multiple Choice

**1. What is a container registry?**

a) A database that stores container logs  
b) A storage and distribution system for container images  
c) A tool for monitoring running containers  
d) A configuration file for Docker  

---

**2. What is the default registry when you pull an image without specifying one?**

a) gcr.io  
b) ghcr.io  
c) docker.io (Docker Hub)  
d) registry.local  

---

**3. Given the image name `gcr.io/myproject/webapp:v2`, what is the repository?**

a) gcr.io  
b) myproject  
c) webapp  
d) v2  

---

**4. What happens when you omit the tag when pulling an image (e.g., `docker pull nginx`)?**

a) Docker pulls all available tags  
b) Docker prompts you to select a tag  
c) Docker pulls the `latest` tag  
d) The command fails  

---

**5. Which command searches Docker Hub for images?**

a) `docker find nginx`  
b) `docker search nginx`  
c) `docker hub search nginx`  
d) `docker image search nginx`  

---

**6. What is an image digest?**

a) A human-readable description of the image  
b) An immutable SHA256 hash that uniquely identifies an image  
c) A compressed version of the image  
d) The image's creation date  

---

**7. Which command authenticates you with Docker Hub?**

a) `docker auth`  
b) `docker connect`  
c) `docker login`  
d) `docker authenticate`  

---

**8. To push an image to Docker Hub, the image name must include:**

a) Only the repository name  
b) Your Docker Hub username as the namespace  
c) The `docker.io` prefix  
d) A digest  

---

**9. What does the command `docker image push --all-tags myuser/myapp` do?**

a) Pushes all images on the system  
b) Pushes all tags of the `myuser/myapp` repository  
c) Creates tags for all versions  
d) Pushes to all configured registries  

---

**10. Which of the following is a valid way to pull an image by digest?**

a) `docker pull nginx:sha256:abc123...`  
b) `docker pull nginx@sha256:abc123...`  
c) `docker pull nginx --digest sha256:abc123...`  
d) `docker pull nginx#sha256:abc123...`  

---

**11. Why should you avoid using the `latest` tag in production?**

a) It's slower than other tags  
b) It's mutable and may point to different images over time  
c) It's not supported by Kubernetes  
d) It only works with Docker Hub  

---

**12. Which command starts a local private registry?**

a) `docker run -d -p 5000:5000 registry:2`  
b) `docker registry start`  
c) `docker create registry --port 5000`  
d) `docker-registry run -p 5000`  

---

**13. What is a pull-through cache (registry mirror)?**

a) A registry that only accepts push operations  
b) A proxy that caches images from upstream registries  
c) A backup copy of Docker Hub  
d) A tool for compressing images  

---

**14. Which file stores Docker login credentials on Linux?**

a) `/etc/docker/credentials`  
b) `~/.docker/config.json`  
c) `/var/lib/docker/auth`  
d) `~/.dockerauth`  

---

## Section 2: True or False

**15. Image tags are immutable—once set, they always point to the same image.**

☐ True  
☐ False  

---

**16. Image digests are immutable and always refer to the exact same image content.**

☐ True  
☐ False  

---

**17. You must be authenticated to pull public images from Docker Hub.**

☐ True  
☐ False  

---

**18. A private registry running on `localhost:5000` requires HTTPS by default.**

☐ True  
☐ False  

---

**19. The `docker search` command can show all available tags for an image.**

☐ True  
☐ False  

---

**20. Multi-architecture images allow the same image tag to work on different CPU architectures.**

☐ True  
☐ False  

---

## Section 3: Fill in the Blank

**21. Complete the image name pattern:**

```
[registry/][namespace/]__________[:tag|@digest]
```

---

**22. Complete the command to tag an image for pushing to Docker Hub:**

```bash
docker image tag myapp __________/myapp:1.0
```

---

**23. Complete the command to login to GitHub Container Registry:**

```bash
docker login __________
```

---

**24. Complete the command to get an image's digest:**

```bash
docker image inspect nginx:alpine --format '{{index .__________ 0}}'
```

---

**25. Complete the API endpoint to list all repositories in a private registry:**

```bash
curl http://localhost:5000/v2/__________
```

---

**26. To run a registry with persistent storage, mount a volume to:**

```
/var/lib/__________
```

---

## Section 4: Short Answer

**27. Explain the difference between a tag and a digest. When would you use each?**

---

**28. Your CI/CD pipeline is hitting Docker Hub rate limits. Describe two solutions to this problem.**

---

**29. Explain why you need to tag an image with your Docker Hub username before pushing it.**

---

**30. What is garbage collection in the context of a container registry, and why is it necessary?**

---

**31. A developer tagged an image with `myapp:1.0` but forgot to push it. Later, they rebuilt the image and pushed `myapp:1.0` again. What problem might this cause?**

---

**32. Describe the difference between these two image references:**
- `nginx:1.25`
- `nginx@sha256:abc123...`

---

## Section 5: Practical Scenarios

**33. Write the commands to:**
1. Search Docker Hub for official Node.js images
2. Pull the `node:20-alpine` image
3. Verify the image was downloaded

---

**34. You need to push an image called `webapp` to your Docker Hub account (username: `devuser`). Write the complete sequence of commands to:**
1. Tag the image correctly
2. Login to Docker Hub
3. Push the image with tag `v1.0`

---

**35. Your team needs a local registry for development. Write the commands to:**
1. Start a registry container with persistent storage on port 5000
2. Tag and push an Alpine image to it
3. Verify the image is in the registry

---

**36. Write the commands to:**
1. Pull an nginx image
2. Get its digest
3. Remove the image locally
4. Pull the same exact image using only its digest

---

**37. Your security team requires that your private registry uses authentication. Write the commands to:**
1. Create an htpasswd file with username `admin` and password `secret123`
2. Start a registry with basic authentication enabled

---

**38. You have an image tagged as `myapp:latest` that you want to push to multiple registries. Write the commands to tag and push the image to:**
1. Docker Hub (username: `myuser`)
2. GitHub Container Registry (username: `myuser`)
3. A private registry at `registry.company.com:5000`

---

**39. A production deployment is failing because an image tag was overwritten with a broken version. How would you:**
1. Find the digest of the previous working image (assume you have the old deployment manifest)
2. Update the deployment to use the digest instead of the tag
3. Prevent this problem in the future

---

**40. Your team develops on ARM Macs but deploys to x86 servers. Write the commands to:**
1. Create a buildx builder for multi-architecture builds
2. Build and push an image that works on both `amd64` and `arm64`
3. Verify the image supports both architectures

---

## Answer Key

### Section 1: Multiple Choice

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | b | Registries store and distribute container images |
| 2 | c | Docker Hub (docker.io) is the default registry |
| 3 | c | The repository is `webapp`; `gcr.io` is registry, `myproject` is namespace, `v2` is tag |
| 4 | c | Docker defaults to the `latest` tag when none is specified |
| 5 | b | `docker search` queries Docker Hub |
| 6 | b | A digest is an immutable SHA256 hash of the image content |
| 7 | c | `docker login` authenticates with registries |
| 8 | b | Image names must include your username/namespace for Docker Hub |
| 9 | b | `--all-tags` pushes all tags of that repository |
| 10 | b | The `@` symbol is used to specify a digest |
| 11 | b | `latest` is mutable and can change without warning |
| 12 | a | The official registry image runs on port 5000 |
| 13 | b | A pull-through cache proxies and caches images from upstream |
| 14 | b | Credentials are stored in `~/.docker/config.json` |

### Section 2: True or False

| Q | Answer | Explanation |
|---|--------|-------------|
| 15 | False | Tags are mutable; the same tag can point to different images over time |
| 16 | True | Digests are SHA256 hashes and are immutable |
| 17 | False | Public images can be pulled without authentication (but rate limits apply) |
| 18 | False | Localhost is treated as an insecure registry by default |
| 19 | False | `docker search` only shows repositories, not tags. Use Docker Hub web or registry API for tags |
| 20 | True | Multi-arch images contain manifests for multiple architectures under one tag |

### Section 3: Fill in the Blank

| Q | Answer |
|---|--------|
| 21 | `repository` |
| 22 | `<your-username>` (e.g., `myuser`) |
| 23 | `ghcr.io` |
| 24 | `RepoDigests` |
| 25 | `_catalog` |
| 26 | `registry` |

### Section 4: Short Answer

**27.** 
- **Tags** are human-readable labels (e.g., `v1.0`, `latest`) that are mutable—they can be moved to point to different images. Use tags during development for convenience.
- **Digests** are immutable SHA256 hashes that uniquely identify exact image content. Use digests in production deployments for guaranteed reproducibility.

**28.** Solutions for rate limits:
1. **Run a registry mirror/pull-through cache** - Caches images locally so subsequent pulls don't hit Docker Hub
2. **Authenticate with Docker Hub** - Authenticated users get higher rate limits
3. **Use a private registry** - Store your own images locally or in cloud registries (ECR, GCR, etc.)
4. **Upgrade to Docker Hub paid plan** - Higher or unlimited pull limits

**29.** Docker Hub uses the namespace (username or organization) to determine where to push images. Without your username, Docker wouldn't know which account's repository to push to. The format `username/repository:tag` maps directly to `docker.io/username/repository:tag`.

**30.** Garbage collection removes unreferenced image layers (blobs) from the registry's storage. It's necessary because:
- When tags are deleted or overwritten, the old layers remain on disk
- Over time, these orphaned layers consume significant storage
- Running `garbage-collect` reclaims this space by removing layers not referenced by any manifest

**31.** The second push overwrites the `1.0` tag with a different image. Anyone who already pulled `myapp:1.0` has a different image than someone pulling it later. This can cause:
- Inconsistent deployments across environments
- Difficulty debugging ("works on my machine")
- Broken rollbacks if the old image can't be recovered
Solution: Use immutable tags (e.g., semantic versioning with patch numbers) or digests.

**32.** 
- `nginx:1.25` uses a **tag** - it's human-readable but mutable. The image it points to may change if the maintainer updates it.
- `nginx@sha256:abc123...` uses a **digest** - it's immutable and always refers to the exact same image bytes. Even if someone pushes a new image with tag `1.25`, this reference still points to the original.

### Section 5: Practical Scenarios

**33.**
```bash
# Search for official Node.js images
docker search --filter is-official=true node

# Pull node:20-alpine
docker image pull node:20-alpine

# Verify download
docker image ls node
```

**34.**
```bash
# Tag the image
docker image tag webapp devuser/webapp:v1.0

# Login to Docker Hub
docker login

# Push the image
docker image push devuser/webapp:v1.0
```

**35.**
```bash
# Create volume and start registry
docker volume create registry-data
docker container run -d \
  -p 5000:5000 \
  --name registry \
  -v registry-data:/var/lib/registry \
  registry:2

# Tag and push Alpine
docker image tag alpine localhost:5000/alpine:latest
docker image push localhost:5000/alpine:latest

# Verify
curl http://localhost:5000/v2/_catalog
```

**36.**
```bash
# Pull nginx
docker image pull nginx:alpine

# Get digest
docker image inspect nginx:alpine --format '{{index .RepoDigests 0}}'
# Example output: nginx@sha256:abc123...

# Remove the image
docker image rm nginx:alpine

# Pull by digest
docker image pull nginx@sha256:abc123...
```

**37.**
```bash
# Create htpasswd file
mkdir -p ~/auth
docker container run --rm \
  --entrypoint htpasswd \
  httpd:2 -Bbn admin secret123 > ~/auth/htpasswd

# Start registry with auth
docker container run -d \
  -p 5000:5000 \
  --name auth-registry \
  -v ~/auth:/auth \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2
```

**38.**
```bash
# Tag for Docker Hub
docker image tag myapp:latest myuser/myapp:latest

# Tag for GitHub Container Registry
docker image tag myapp:latest ghcr.io/myuser/myapp:latest

# Tag for private registry
docker image tag myapp:latest registry.company.com:5000/myapp:latest

# Login and push to each
docker login
docker image push myuser/myapp:latest

docker login ghcr.io
docker image push ghcr.io/myuser/myapp:latest

docker login registry.company.com:5000
docker image push registry.company.com:5000/myapp:latest
```

**39.**
```bash
# 1. Find the digest from old deployment manifest
# Look for: image: myapp@sha256:abc123...
# Or if you have the old image locally:
docker image inspect myapp:old-version --format '{{index .RepoDigests 0}}'

# 2. Update deployment to use digest
# Change: image: myapp:v1.0
# To:     image: myapp@sha256:abc123...

# 3. Prevention strategies:
# - Always use digests in production deployments
# - Use immutable tags (never reuse version numbers)
# - Implement image signing and verification
# - Use CI/CD to automatically pin digests
# - Enable registry immutable tags if supported
```

**40.**
```bash
# Create buildx builder
docker buildx create --name multiarch --use
docker buildx inspect --bootstrap

# Build and push multi-arch image
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myuser/myapp:1.0 \
  --push \
  .

# Verify architectures
docker manifest inspect myuser/myapp:1.0

# Or with jq for cleaner output
docker manifest inspect myuser/myapp:1.0 | jq '.[].platform'
```

---

## Scoring Guide

| Score | Level |
|-------|-------|
| 36-40 | Expert – Ready for production registry management |
| 29-35 | Proficient – Solid understanding, minor gaps |
| 22-28 | Intermediate – Review advanced topics |
| 15-21 | Beginner – Review core concepts |
| 0-14 | Needs Review – Retake the lab exercises |


