# Docker Volumes – Hands-on Lab (Play with Docker)

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

- Understand why container storage is **ephemeral**
- Learn how **Docker volumes** work
- Use volumes via **Dockerfile**, **runtime flags**, and the **Volume API**
- Understand **bind mounts vs volumes**
- Apply volumes correctly in real infrastructure scenarios

### Intermediate Objectives (Optional)

- Use `--mount` instead of `-v`
- Manage volume lifecycle (dangling volumes, pruning, labels)
- Backup and restore volumes
- Troubleshoot common issues (mount overrides, permissions, "empty" volumes)
- Design multi-container setups with separate volumes

### Tip: Use a Name Prefix

On shared hosts or busy workstations, prefix container/volume names (e.g. `lab-c1`, `lab-html`) so you don't clash with existing resources.

---

## Quick Sanity Check (Optional)

```bash
docker version
docker info
```

---

## Part 1: Container Storage Is Ephemeral

Run a container and create data inside it:

```bash
docker container run --name c1 -ti alpine sh
```

Inside the container:

```sh
mkdir /data
echo "hello" > /data/hello.txt
ls -l /data
cat /data/hello.txt
exit
```

Now remove the container:

```bash
docker container rm c1
```

Re-run a new container and check `/data`:

```bash
docker container run --rm -ti alpine sh
ls -l /data || true
exit
```

### Observation

The data is gone.

### Conclusion

The container **writable layer** is deleted when the container is deleted.

---

## Part 2: Volumes via Dockerfile

Create a `Dockerfile`:

```bash
cat > Dockerfile <<'EOF'
FROM alpine
VOLUME ["/data"]
ENTRYPOINT ["/bin/sh"]
EOF
```

Build the image:

```bash
docker image build -t vol-img .
```

Run a container:

```bash
docker container run --name c2 -ti vol-img
```

Inside the container:

```sh
echo "persist-me" > /data/hello.txt
ls -l /data
cat /data/hello.txt
exit
```

Capture the anonymous volume name mounted at `/data`:

```bash
VOL2="$(docker container inspect -f '{{ range .Mounts }}{{ if eq .Destination "/data" }}{{ .Name }}{{ end }}{{ end }}' c2)"
echo "Volume name is: $VOL2"
```

Stop & remove the container (note: we are *not* using `--rm` so the volume is not auto-removed):

```bash
docker container rm c2
```

List volumes (you should still see the volume):

```bash
docker volume ls
```

Start a *new* container and re-attach the same volume to prove persistence:

```bash
docker container run --rm -ti -v "$VOL2:/data" alpine sh -c 'ls -l /data && cat /data/hello.txt'
```

### Observation

Even though the container is gone, the **anonymous volume** created for `/data` can still exist and be re-attached.

### Note

`VOLUME` declares a mount point. Each new container gets its own anonymous volume unless you explicitly mount an existing one.

---

## Part 3: Volumes at Runtime (Anonymous Volumes)

Run a container that appends timestamps to a file inside `/data`:

```bash
docker container run --name c3 -d -v /data alpine sh -c 'while true; do date >> /data/time.log; sleep 2; done'
```

Inspect mounts:

```bash
docker container inspect -f '{{ json .Mounts }}' c3
```

If `jq` is available, you can pretty-print:

```bash
docker container inspect -f '{{ json .Mounts }}' c3 | jq
```

Capture the anonymous volume name mounted at `/data`:

```bash
VOL="$(docker container inspect -f '{{ range .Mounts }}{{ if eq .Destination "/data" }}{{ .Name }}{{ end }}{{ end }}' c3)"
echo "Volume name is: $VOL"
```

Stop and remove the container (the volume will still remain):

```bash
docker container rm -f c3
```

Re-attach the volume using a new helper container and read the log:

```bash
docker container run --rm -ti -v "$VOL:/data" alpine sh -c 'ls -l /data && tail -n 5 /data/time.log'
```

### Observation

Anonymous volumes can persist **beyond the container lifecycle**.

### Cleanup (Optional)

```bash
docker volume rm "$VOL"
```

---

## Part 4: Docker Volume API (Named Volumes)

Create a named volume:

```bash
docker volume create html
docker volume ls
docker volume inspect html
```

Run `nginx` with the named volume mounted at the default web root:

```bash
docker container run --name web -d -p 8080:80 -v html:/usr/share/nginx/html nginx
```

Write content using a helper container:

```bash
docker container run --rm -ti -v html:/data alpine sh
```

Inside the helper container:

```sh
cat > /data/index.html <<'EOF'
<html>
  <body>
    <h1>Hello from a Docker Volume</h1>
    <p>This content lives in a named volume.</p>
  </body>
</html>
EOF
exit
```

Verify with `curl`:

```bash
curl -sS localhost:8080 | head -n 20
```

### PWD Note (Browser Access)

In Play with Docker, use the UI to **open port 8080** (often shown as an “Open Port” or clickable port link).

---

## Part 5: Bind Mounts

Bind mount syntax:

- `-v HOST_PATH:CONTAINER_PATH`

Example (host `/tmp` mounted into container `/data`):

```bash
mkdir -p /tmp/binddemo
echo "from-host" > /tmp/binddemo/host.txt

docker container run --rm -ti -v /tmp/binddemo:/data alpine sh -c 'ls -l /data && cat /data/host.txt'
```

### Notes

- Bind mounts **override** container paths (what’s in the image at that path is hidden by the mount).
- Bind mounts are commonly used in **development** (live-edit source code, share config files).
- In PWD, the “host” is the PWD VM instance (not your laptop).

---

## Exercises

### Exercise 1: Share a Named Volume Between Two Containers

- Create a named volume (e.g., `shared`)
- Start container `a` and write a file into the volume
- Start container `b` and verify it can read the same file

Helpful commands:

```bash
docker volume create shared
docker container run --name a -ti -v shared:/data alpine sh
```

Inside `a`:

```sh
echo "hello-from-a" > /data/msg.txt
exit
```

Then:

```bash
docker container run --name b --rm -ti -v shared:/data alpine sh -c 'cat /data/msg.txt'
```

### Exercise 2: Read-Only Volume Mounts

- Mount the volume read-only and try to write

```bash
docker container run --rm -ti -v shared:/data:ro alpine sh -c 'echo test > /data/should-fail.txt'
```

Expected: write fails with a “read-only file system” error.

### Exercise 3: Design a Practical Volume Layout

Design a setup that separates:

- **app** (code/config)
- **logs** (rotating logs)
- **data** (database or persistent state)

Write down:

- Which paths should be **volumes** (production)
- Which paths should be **bind mounts** (development)
- Which mounts should be **read-only**

---

## Optional Advanced Exercises (Intermediate)

> **Context:** You're the on-call engineer for a small product team. The team runs a website (like Part 4) and is adding a database. Your job: make storage predictable, operable, and safe.

---

## Part 6 (Optional): Prefer `--mount`

A teammate keeps mixing `-v` syntaxes and occasionally breaks mounts. You want a consistent standard.

The `--mount` flag uses explicit key/value fields—easier to review and less error-prone than `-v`.

Named volume:

```bash
docker container run --rm \
  --mount type=volume,src=html,dst=/usr/share/nginx/html \
  alpine sh -c 'ls -l /usr/share/nginx/html'
```

Bind mount:

```bash
docker container run --rm \
  --mount type=bind,src=/tmp/binddemo,dst=/data \
  alpine sh -c 'ls -l /data'
```

Read-only mount:

```bash
docker container run --rm \
  --mount type=volume,src=shared,dst=/data,readonly \
  alpine sh -c 'echo test > /data/should-fail.txt'
```

The mounts behave exactly like `-v`, but the intent is clearer—especially `readonly`.

---

## Part 7 (Optional): Volume Lifecycle, Labels, and Pruning

After a few weeks, the host has dozens of old anonymous volumes. Nobody knows what's safe to delete.

Add labels so you can identify ownership and clean up safely:

```bash
docker volume create --label purpose=training --label owner=$USER training-data
docker volume inspect training-data
```

Find dangling volumes (usually anonymous volumes left behind):

```bash
docker volume ls -f dangling=true
```

Prune unused volumes (⚠ removes volumes not referenced by any container):

```bash
docker volume prune
```

You'll notice volumes created for short-lived containers often remain. Labels help you identify what's yours and what a job created.

---

## Part 8 (Optional): Backup, Restore, and Migration

You need to upgrade a stateful service. Before you do, you want a backup you can restore quickly.

Export a volume to a tarball:

```bash
docker volume create backup-src
docker container run --rm -v backup-src:/data alpine sh -c 'echo hello > /data/hello.txt'

docker container run --rm \
  -v backup-src:/data:ro \
  -v "$PWD":/backup \
  alpine sh -c 'tar -czf /backup/backup-src.tgz -C /data .'
```

Restore into a new volume:

```bash
docker volume create backup-dst

docker container run --rm \
  -v backup-dst:/data \
  -v "$PWD":/backup \
  alpine sh -c 'tar -xzf /backup/backup-src.tgz -C /data'

docker container run --rm -v backup-dst:/data alpine sh -c 'cat /data/hello.txt'
```

`backup-src.tgz` is your portable snapshot. `backup-dst` ends up with the same data. This is how you do volume migration and disaster recovery.

---

## Part 9 (Optional): "Why Is My Volume Empty?"

You deploy a new `nginx` container and the default page is "gone".

When you mount a volume onto a path, it **hides** whatever the image had at that path.

```bash
docker volume create hide-demo
docker container run --rm nginx sh -c 'ls -l /usr/share/nginx/html | head'

# This will likely look "empty" until you put content into the volume:
docker container run --rm -v hide-demo:/usr/share/nginx/html nginx sh -c 'ls -l /usr/share/nginx/html'
```

Populate the volume (one-time “init” step):

```bash
docker container run --rm \
  -v hide-demo:/usr/share/nginx/html \
  nginx sh -c 'echo "<h1>Now it is not empty</h1>" > /usr/share/nginx/html/index.html'

docker container run --rm -v hide-demo:/usr/share/nginx/html nginx sh -c 'ls -l /usr/share/nginx/html && head -n 5 /usr/share/nginx/html/index.html'
```

Once you write into the mounted path, the volume has content—and future containers mounting it will see it.

---

## Part 10 (Optional): Permissions and UID/GID

Your app image runs as a non-root user. It suddenly can't write logs or data to the mounted volume.

Many production images run as non-root. If your app can't write to a volume, it's usually a **permissions** issue.

Create a volume and simulate a non-root writer:

```bash
docker volume create perms
docker container run --rm -v perms:/data alpine sh -c 'adduser -D -u 10001 app && su app -c \"echo hi > /data/hi.txt\"'
```

If this fails in some environments, fix by setting ownership from a one-time init container:

```bash
docker container run --rm -v perms:/data alpine sh -c 'chown -R 10001:10001 /data'
```

After setting ownership, the non-root writer can create files reliably.

---

## Part 11 (Optional): Multi-Container Pattern

The team is adding a database. You want a clean design:

- Database files persist in a **data volume**
- Backups go to a separate **backup volume**
- You can inspect or restore without touching the app container

```bash
docker volume create pgdata
docker volume create pgbackup
```

Design a setup that:

- mounts `pgdata` into Postgres at `/var/lib/postgresql/data`
- mounts `pgbackup` into a backup job container that dumps to `/backup`
- (optional) mounts `pgdata` read-only into an admin container for inspection

Test it: delete and recreate the app container. The database state should survive. Restore from backup and verify.

---

## Key Takeaways

- Containers are **stateless by default**
- Volumes provide **persistent storage**
- **Named volumes** are production-friendly and portable within Docker’s volume system
- **Bind mounts** are best for development workflows and host-file sharing

---

## Cleanup (Optional)

```bash
docker container rm -f web a 2> /dev/null || true
docker volume rm html shared 2> /dev/null || true
docker image rm vol-img 2> /dev/null || true
rm -f Dockerfile
```


