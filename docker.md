# 🐳 Docker for Android Penetration Testers
## Complete Reference Guide — 190 Topics with Definitions & Real-World Examples

---

## 1. Docker Fundamentals

### 1. What is Docker?
Docker is an open-source platform that packages applications and their dependencies into lightweight, portable units called **containers**, ensuring they run identically across any environment.

**Real-World Example:** Package MobSF (Mobile Security Framework) into a Docker container so it runs the same way on your MacBook, a Kali Linux VM, or a CI/CD pipeline — no dependency conflicts.

```bash
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
# MobSF is now running at http://localhost:8000 — no Python/pip setup required
```

---

### 2. Why Docker is Used
Docker eliminates "works on my machine" problems by bundling the app, runtime, libraries, and config together, enabling reproducible, isolated, and fast deployments.

**Real-World Example:** An Android pentester shares a Docker image containing Frida, apktool, jadx-gui, and adb pre-configured. Any team member can `docker pull` and instantly have the same toolchain without a 3-hour manual setup.

| Benefit | Without Docker | With Docker |
|---|---|---|
| Setup time | Hours | Minutes |
| Reproducibility | Inconsistent | Guaranteed |
| Isolation | None | Full process isolation |
| Portability | OS-dependent | Runs anywhere |

---

### 3. Docker Architecture
Docker follows a **client-server architecture**: the Docker Client sends commands to the Docker Daemon (server), which manages images, containers, networks, and volumes. A Registry stores and distributes images.

**Text-Based Architecture Diagram:**
```
┌─────────────────────────────────────────────────────────────┐
│                        Docker Host                          │
│                                                             │
│   ┌─────────────┐        ┌──────────────────────────────┐  │
│   │ Docker CLI  │───────▶│       Docker Daemon           │  │
│   │  (Client)   │  REST  │        (dockerd)              │  │
│   └─────────────┘  API   │                              │  │
│                          │  ┌──────────┐ ┌──────────┐  │  │
│                          │  │Container │ │Container │  │  │
│                          │  │  MobSF   │ │  Frida   │  │  │
│                          │  └──────────┘ └──────────┘  │  │
│                          │                              │  │
│                          │  ┌──────────┐ ┌──────────┐  │  │
│                          │  │  Image   │ │  Volume  │  │  │
│                          │  │ (mobsf)  │ │ /uploads │  │  │
│                          │  └──────────┘ └──────────┘  │  │
│                          └──────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                                    ▲
                                    │ pull/push
                                    ▼
                         ┌──────────────────┐
                         │  Docker Registry │
                         │   (Docker Hub)   │
                         └──────────────────┘
```

**Real-World Example:** When you run `docker run opensecurity/mobile-security-framework-mobsf`, the Docker Client tells the Daemon to pull the image from Docker Hub and start a container.

---

### 4. Docker Client
The Docker Client is the primary CLI tool (`docker`) that users interact with, sending commands to the Docker Daemon via REST API over a Unix socket or TCP.

**Real-World Examples:**
```bash
# Build a custom Android pentest image
docker build -t android-pentest:1.0 .

# Run MobSF container
docker run -d -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest

# Execute a command inside a running container
docker exec -it mobsf bash

# Pull the latest drozer image
docker pull fsecurelabs/drozer
```

The client can communicate with a **remote** daemon too (useful for running pentest containers on a powerful remote server while issuing commands from your laptop).

---

### 5. Docker Engine
Docker Engine is the core runtime that consists of the Docker Daemon (`dockerd`), the REST API, and the CLI client — together they build, run, and manage containers.

**Real-World Example:** When you install Docker Desktop on macOS, you're installing Docker Engine. It's what allows you to spin up a MobSF or Burp Suite container locally and tear it down in seconds.

**Components of Docker Engine:**
```
Docker Engine
├── dockerd        (Daemon — background service)
├── REST API       (Interface for client-daemon communication)
├── docker CLI     (Command-line client)
└── containerd     (Low-level container runtime)
    └── runc       (OCI-compliant container runner)
```

---

### 6. Docker Daemon (`dockerd`)
`dockerd` is the persistent background process that listens for Docker API requests and manages Docker objects: images, containers, networks, and volumes.

**Real-World Example:** On a Linux pentest server, `dockerd` runs as a systemd service. All `docker` commands you run (start MobSF, pull Frida server, inspect containers) are handled by this daemon.

```bash
# Check if the daemon is running
systemctl status docker

# View daemon logs (Linux)
journalctl -u docker.service -f

# Daemon listens on a Unix socket by default
ls -la /var/run/docker.sock
# srw-rw---- 1 root docker ... /var/run/docker.sock

# Start daemon manually (rarely needed)
dockerd --debug
```

> **Security Note for Pentesters:** Access to `/var/run/docker.sock` = root-equivalent privilege escalation. Always check for this during Android app server assessments.

---

### 7. Docker Images
A Docker Image is a read-only, layered template used to create containers — it contains the OS, runtime, libraries, and application code.

**Real-World Example (MobSF Image):**
```bash
# Pull the MobSF image from Docker Hub
docker pull opensecurity/mobile-security-framework-mobsf:latest

# List all local images
docker images
# REPOSITORY                                    TAG       IMAGE ID       SIZE
# opensecurity/mobile-security-framework-mobsf  latest    a1b2c3d4e5f6   1.2GB

# The image contains: Ubuntu base + Python + MobSF source + all dependencies
# You never touch a running system — every pentest starts from a clean image
```

Images are **immutable** — every time you run a container from an image, you start with a guaranteed clean state. Perfect for repeatable Android app assessments.

---

### 8. Docker Containers
A Docker Container is a running (or stopped) instance of a Docker Image — a lightweight, isolated process with its own filesystem, networking, and process space.

**Real-World Example (Running MobSF Container):**
```bash
# Start MobSF container in detached mode
docker run -d \
  --name mobsf \
  -p 8000:8000 \
  -v /home/pentester/mobsf_uploads:/home/mobsf/.MobSF \
  opensecurity/mobile-security-framework-mobsf:latest

# Access at http://localhost:8000
# Upload your APK → MobSF performs static + dynamic analysis
# All findings stored in the mounted volume

# Stop when done
docker stop mobsf

# Remove container (image is still available for next time)
docker rm mobsf
```

Containers are **ephemeral** by design — stop and remove one, start a fresh one for the next APK assessment.

---

### 9. Docker Registry
A Docker Registry is a storage and distribution system for Docker images — it hosts repositories of images that can be pushed and pulled.

**Real-World Example:**
```bash
# Pull from public Docker Hub registry (default)
docker pull opensecurity/mobile-security-framework-mobsf

# Pull from a private pentest team registry
docker pull registry.pentestlab.internal:5000/android-tools:latest

# Push a custom Android pentest image to a private registry
docker tag android-pentest:1.0 registry.pentestlab.internal:5000/android-pentest:1.0
docker push registry.pentestlab.internal:5000/android-pentest:1.0
```

| Registry Type | Example | Use Case |
|---|---|---|
| Public | Docker Hub | Community pentest tools |
| Private (cloud) | AWS ECR, GCR | Team-shared custom images |
| Private (self-hosted) | registry:2 | Air-gapped pentest environments |

---

### 10. Docker Hub
Docker Hub is the default public Docker Registry at `hub.docker.com` — the largest repository of container images including official and community images.

**Real-World Examples for Android Pentesters:**
```bash
# Search for Android pentest tools on Docker Hub
docker search mobsf
docker search drozer
docker search frida

# Pull popular Android pentest images
docker pull opensecurity/mobile-security-framework-mobsf  # Static + Dynamic analysis
docker pull fsecurelabs/drozer                             # Android attack framework
docker pull citizenlab/androguard                         # APK reverse engineering

# Browsing Docker Hub
# https://hub.docker.com/r/opensecurity/mobile-security-framework-mobsf
```

Docker Hub gives Android pentesters instant access to pre-built analysis environments without manual tool installation.

---

### 11. Docker Desktop
Docker Desktop is a GUI application for macOS and Windows that bundles Docker Engine, Docker CLI, Docker Compose, and a graphical dashboard for managing containers and images.

**Real-World Example (macOS use case):**
```bash
# After installing Docker Desktop on macOS:
# 1. Docker Engine runs inside a lightweight Linux VM (Apple Hypervisor)
# 2. Your Mac terminal talks to it transparently

# Start MobSF for an Android app pentest directly from Mac terminal
docker run -d -p 8000:8000 --name mobsf \
  opensecurity/mobile-security-framework-mobsf:latest

# Docker Desktop Dashboard lets you:
# - See running containers (MobSF, Burp, Frida server)
# - View container logs
# - Open container terminals
# - Manage volumes with APK uploads
```

Docker Desktop eliminates the need for a separate Linux VM for most Android pentesting workflows on macOS.

---

### 12. Docker vs Virtual Machines
Docker containers share the host OS kernel and are process-isolated; VMs run a full OS on virtualized hardware. Containers are faster, lighter, and better for tool isolation; VMs provide stronger isolation.

**Comparison Table:**

| Feature | Docker Container | Virtual Machine |
|---|---|---|
| OS | Shares host kernel | Full OS per VM |
| Startup time | Seconds (<2s) | Minutes |
| Disk size | MBs (layered) | GBs |
| Memory overhead | Low (~MBs) | High (~GBs) |
| Isolation | Process-level | Hardware-level |
| Portability | Excellent | Good |
| Security isolation | Moderate | Strong |
| Use for pentesting | Tool packaging/sharing | Full OS environments (e.g., Genymotion) |

**Real-World Example:**
- Use Docker to run MobSF (tool isolation, quick spin-up).
- Use a VM (Genymotion/Android Emulator) to run the Android device environment itself.
- Android pentesting often uses **both**: Docker for analysis tools + VM for the Android device.

---

### 13. Docker vs Kubernetes
Docker manages containers on a single host; Kubernetes (K8s) orchestrates containers across a cluster of multiple hosts, providing auto-scaling, self-healing, and load balancing.

**Comparison Table:**

| Feature | Docker | Kubernetes |
|---|---|---|
| Scope | Single host | Multi-host cluster |
| Scaling | Manual | Automatic |
| Self-healing | No | Yes (restarts failed pods) |
| Load balancing | Basic | Built-in |
| Complexity | Low | High |
| Use case | Dev/pentest tooling | Production deployments |
| CLI | `docker` | `kubectl` |

**Real-World Example:**
- **Docker:** Run MobSF locally to analyze one APK at a time during a pentest engagement.
- **Kubernetes:** Deploy a fleet of MobSF instances to analyze hundreds of APKs simultaneously in a large-scale app security audit pipeline.

---

## 2. Docker Installation and CLI

### 14. Docker Installation on macOS/Linux

**macOS Installation:**
```bash
# Method 1: Docker Desktop (recommended for macOS)
# Download from: https://www.docker.com/products/docker-desktop/
# Installs Docker Engine, CLI, Compose, and Dashboard

# Method 2: Homebrew
brew install --cask docker
# Then launch Docker Desktop app to start the daemon

# Verify installation
docker --version
# Docker version 24.0.7, build afdd53b
docker run hello-world
```

**Linux (Ubuntu/Debian) Installation:**
```bash
# Step 1: Remove old versions
sudo apt-get remove docker docker-engine docker.io containerd runc

# Step 2: Add Docker's official GPG key and repo
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Step 3: Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Step 4: Add user to docker group (avoid sudo for every command)
sudo usermod -aG docker $USER
newgrp docker

# Step 5: Verify
docker run hello-world
```

**Post-install verification for Android pentest readiness:**
```bash
docker pull opensecurity/mobile-security-framework-mobsf:latest
docker run --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
```

---

### 15. Docker CLI
The Docker CLI (`docker`) is the command-line interface for interacting with Docker. Commands follow the structure: `docker [OPTIONS] COMMAND [ARGS]`.

**CLI Command Structure:**
```
docker [global options] <management-command> <subcommand> [options] [arguments]

Management Commands (new style):
  docker image    ls / pull / rm / inspect / build
  docker container  run / start / stop / rm / logs / exec
  docker volume   create / ls / rm / inspect
  docker network  create / ls / rm / inspect
  docker system   df / info / prune

Legacy Commands (still work):
  docker run      → docker container run
  docker images   → docker image ls
  docker ps       → docker container ls
  docker rmi      → docker image rm
```

**Real-World Example:**
```bash
# Modern style — more explicit
docker container run -d --name mobsf -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Legacy style — shorter, commonly used
docker run -d --name mobsf -p 8000:8000 opensecurity/mobile-security-framework-mobsf
```

---

### 16. Basic Docker Commands

| Command | Description | Android Pentest Example |
|---|---|---|
| `docker pull <image>` | Download image from registry | `docker pull opensecurity/mobile-security-framework-mobsf` |
| `docker run <image>` | Create and start a container | `docker run -d -p 8000:8000 opensecurity/mobile-security-framework-mobsf` |
| `docker ps` | List running containers | `docker ps` |
| `docker ps -a` | List all containers (including stopped) | `docker ps -a` |
| `docker stop <name>` | Gracefully stop a container | `docker stop mobsf` |
| `docker start <name>` | Start a stopped container | `docker start mobsf` |
| `docker rm <name>` | Remove a stopped container | `docker rm mobsf` |
| `docker images` | List local images | `docker images` |
| `docker rmi <image>` | Remove an image | `docker rmi mobsf:latest` |
| `docker logs <name>` | View container stdout/stderr | `docker logs mobsf` |
| `docker exec -it <name> bash` | Open shell in container | `docker exec -it mobsf bash` |
| `docker build -t <name> .` | Build image from Dockerfile | `docker build -t android-pentest:1.0 .` |
| `docker inspect <name>` | Detailed JSON info | `docker inspect mobsf` |
| `docker stats` | Live resource usage | `docker stats` |
| `docker cp <src> <dst>` | Copy files to/from container | `docker cp mobsf:/uploads/report.pdf ./` |

---

### 17. Docker Help and Documentation
Docker has built-in help at every level — use `--help` to discover flags and subcommands without leaving the terminal.

```bash
# Top-level help — all commands
docker help
docker --help

# Help for a specific command
docker run --help
docker build --help
docker exec --help

# Help for management commands
docker image --help
docker container --help
docker volume --help

# Example: learn all flags for docker run
docker run --help
# Output shows: -d, -it, -p, --name, --rm, -v, --env, etc.

# Get help for a subcommand
docker image ls --help
docker container inspect --help
```

**Real-World Example:** Before running MobSF with custom options, check available flags:
```bash
docker run --help | grep -E "port|volume|name|detach"
# Quickly find -p, -v, --name, -d flags
```

---

### 18. Docker Version and System Information

```bash
# Client and server (daemon) version info
docker version
# Client: Docker Engine - Community
#  Version:           24.0.7
#  API version:       1.43
# Server: Docker Engine - Community
#  Engine Version:    24.0.7

# Detailed system info: containers, images, storage driver, kernel
docker info
# Containers: 3 (Running: 1, Paused: 0, Stopped: 2)
# Images: 12
# Server Version: 24.0.7
# Storage Driver: overlay2
# Operating System: Ubuntu 22.04.3 LTS
# Total Memory: 15.51GiB

# Disk usage: images, containers, volumes, build cache
docker system df
# TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
# Images          8         2         4.521GB   3.1GB (68%)
# Containers      3         1         12.5MB    10MB (80%)
# Local Volumes   2         1         892MB     0B (0%)
# Build Cache     15        0         1.2GB     1.2GB

# Verbose disk usage breakdown
docker system df -v
```

**Real-World Example:** Before starting a long pentest session, run `docker system df` to check available disk space — MobSF and Android emulator images can consume 5–10 GB easily.

---

### 19. Docker Contexts
A Docker context stores connection settings (endpoint, TLS certs) for different Docker Daemons, allowing you to switch between local and remote Docker hosts with one command.

**Real-World Example:** An Android pentester has:
- `default` context → local Docker Desktop (for quick local tests)
- `pentest-server` context → remote Linux server (for resource-heavy MobSF analysis)

```bash
# List all contexts
docker context ls
# NAME              DESCRIPTION                               ENDPOINT
# default *         Current DOCKER_HOST based configuration  unix:///var/run/docker.sock
# pentest-server    Remote pentest analysis server           tcp://192.168.1.50:2376

# Create a context for a remote pentest server
docker context create pentest-server \
  --description "Remote pentest analysis server" \
  --docker "host=tcp://192.168.1.50:2376"

# Switch to remote context — all docker commands now run on the remote server
docker context use pentest-server

# Run MobSF on the remote server
docker run -d -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Switch back to local
docker context use default

# Remove a context
docker context rm pentest-server
```

---

### 20. Docker Configuration
Docker configuration is stored in `~/.docker/config.json` (client settings: registry auth, credential helpers) and `/etc/docker/daemon.json` (daemon settings: storage, logging, proxies).

**Client Config (`~/.docker/config.json`):**
```json
{
  "auths": {
    "https://index.docker.io/v1/": {
      "auth": "base64encodedcredentials=="
    },
    "registry.pentestlab.internal:5000": {
      "auth": "base64encodedcredentials=="
    }
  },
  "credsStore": "osxkeychain",
  "currentContext": "default"
}
```

**Daemon Config (`/etc/docker/daemon.json`):**
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "insecure-registries": ["registry.pentestlab.internal:5000"],
  "dns": ["8.8.8.8", "1.1.1.1"],
  "data-root": "/mnt/docker-data"
}
```

```bash
# Apply daemon config changes
sudo systemctl restart docker

# Verify config is applied
docker info | grep "Storage Driver\|Logging Driver"

# Login to Docker Hub (saves credentials to config.json)
docker login

# Login to private pentest registry
docker login registry.pentestlab.internal:5000
```

> **Security Note:** `~/.docker/config.json` may contain base64-encoded credentials. Check this file during server-side pentest assessments — it's a common credential leak vector.

---

## 3. Docker Images

### 21. Image Layers
Docker images are built from stacked read-only layers, where each instruction in a Dockerfile creates a new layer. Layers are cached and shared between images, saving disk space and download time.

**How Layers Stack:**
```
┌─────────────────────────────┐  ← Layer 5: ADD mobsf_source/ (App code)
├─────────────────────────────┤  ← Layer 4: RUN pip install -r requirements.txt
├─────────────────────────────┤  ← Layer 3: RUN apt-get install python3
├─────────────────────────────┤  ← Layer 2: RUN apt-get update
└─────────────────────────────┘  ← Layer 1: FROM ubuntu:22.04 (Base OS)

When container runs → writable layer added on top (container layer)
```

**Real-World Example:**
```bash
# See layers of MobSF image
docker image history opensecurity/mobile-security-framework-mobsf:latest
# IMAGE         CREATED        CREATED BY                       SIZE
# a1b2c3d4e5f6  2 weeks ago    CMD ["python3" "manage.py"]      0B
# <missing>     2 weeks ago    COPY . /home/mobsf               245MB
# <missing>     2 weeks ago    RUN pip install -r requirements  890MB
# <missing>     2 weeks ago    FROM python:3.10-slim            125MB

# If you pull a new MobSF version that shares the python:3.10-slim base,
# Docker reuses the already-downloaded base layer — only downloads the diff
```

---

### 22. Image Tags
An image tag is a human-readable label (alias) for a specific image version, in the format `image:tag`. The default tag is `latest` if not specified.

**Real-World Examples:**
```bash
# Pull specific MobSF versions
docker pull opensecurity/mobile-security-framework-mobsf:latest   # most recent
docker pull opensecurity/mobile-security-framework-mobsf:3.9.7    # pinned version
docker pull opensecurity/mobile-security-framework-mobsf:v3.8.0   # older version

# Tag a custom Android pentest image
docker tag android-pentest:1.0 android-pentest:stable
docker tag android-pentest:1.0 registry.internal/android-pentest:1.0

# List images with tags
docker images opensecurity/mobile-security-framework-mobsf
# REPOSITORY                     TAG       IMAGE ID       CREATED        SIZE
# opensecurity/mobile-...mobsf   latest    a1b2c3d4e5f6   2 weeks ago    1.2GB
# opensecurity/mobile-...mobsf   3.9.7     b2c3d4e5f6a7   1 month ago    1.1GB
```

> **Best Practice for Pentesting:** Pin specific tags in your tooling (e.g., `mobsf:3.9.7`) to ensure consistent, reproducible results across all team members and engagements.

---

### 23. Image Digests
An image digest is a unique, immutable SHA-256 hash of an image's content — unlike tags (which can be overwritten), a digest always refers to the exact same image.

**Real-World Examples:**
```bash
# Pull image by digest — guaranteed exact version
docker pull opensecurity/mobile-security-framework-mobsf@sha256:a7b3c9d2e8f1...

# View digests of local images
docker images --digests
# REPOSITORY          TAG     DIGEST                              IMAGE ID
# opensecurity/...    latest  sha256:a7b3c9d2e8f1...4a2b         a1b2c3d4e5f6

# Get digest after pulling
docker inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{index .RepoDigests 0}}'
# opensecurity/mobile-security-framework-mobsf@sha256:a7b3c9d2e8f1...

# In Dockerfile — use digest for tamper-proof base image
FROM ubuntu@sha256:77906da86b60585ce12215807090eb327e7386c8fafb5402369e421f44eff17e
```

> **Security Note:** In sensitive pentest pipeline environments, always use digests instead of tags to prevent supply-chain attacks where a tag is re-pointed to a malicious image.

---

### 24. Pulling Images
`docker pull` downloads an image from a registry to the local host, pulling each layer independently with parallel downloads.

```bash
# Pull latest MobSF image
docker pull opensecurity/mobile-security-framework-mobsf:latest
# latest: Pulling from opensecurity/mobile-security-framework-mobsf
# 7b1a6ab2e44d: Pull complete
# 3a4f6c2e1b5d: Pull complete
# ...
# Status: Downloaded newer image for opensecurity/...:latest

# Pull specific version
docker pull opensecurity/mobile-security-framework-mobsf:3.9.7

# Pull from a private registry
docker pull registry.pentestlab.internal:5000/android-tools:latest

# Pull by digest (immutable reference)
docker pull ubuntu@sha256:77906da86b60585ce12215807090eb327e7386c8fafb5402369e421f44eff17e

# Pull all tags of an image
docker pull --all-tags opensecurity/mobile-security-framework-mobsf

# Pull for a specific platform (e.g., ARM for Apple Silicon)
docker pull --platform linux/amd64 opensecurity/mobile-security-framework-mobsf:latest
```

---

### 25. Listing Images

```bash
# List all local images
docker images
docker image ls
# REPOSITORY                                    TAG       IMAGE ID       CREATED        SIZE
# opensecurity/mobile-security-framework-mobsf  latest    a1b2c3d4e5f6   2 weeks ago    1.2GB
# fsecurelabs/drozer                            latest    b2c3d4e5f6a7   1 month ago    450MB
# ubuntu                                        22.04     c3d4e5f6a7b8   2 months ago   77.8MB

# Filter by name
docker images opensecurity/mobile-security-framework-mobsf

# Show only image IDs (useful for scripting)
docker images -q
# a1b2c3d4e5f6
# b2c3d4e5f6a7

# Show all images including intermediates
docker images -a

# Filter dangling images (untagged, orphaned layers)
docker images -f dangling=true

# Custom format output
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
# REPOSITORY                    TAG       SIZE
# opensecurity/mobile-...mobsf  latest    1.2GB
# fsecurelabs/drozer            latest    450MB

# Filter images with specific label
docker images --filter "label=maintainer=pentestteam"
```

---

### 26. Inspecting Images
`docker image inspect` returns detailed JSON metadata about an image: layers, environment variables, entry point, exposed ports, labels, and architecture.

```bash
# Inspect MobSF image
docker image inspect opensecurity/mobile-security-framework-mobsf:latest

# Extract specific fields with --format
# Get the entry point / CMD (what command runs when container starts)
docker image inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{.Config.Cmd}}'
# [python3 manage.py runserver 0.0.0.0:8000]

# Get exposed ports
docker image inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{json .Config.ExposedPorts}}'
# {"8000/tcp":{}}

# Get environment variables (may reveal default credentials or paths)
docker image inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{json .Config.Env}}'

# Get all layers (layer IDs)
docker image inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{json .RootFS.Layers}}'

# Get image architecture
docker image inspect opensecurity/mobile-security-framework-mobsf:latest \
  --format '{{.Architecture}}'
# amd64
```

> **Pentest Tip:** `docker image inspect` can reveal hardcoded environment variables, default passwords, and exposed service ports in third-party images — always inspect before trusting.

---

### 27. Removing Images

```bash
# Remove a specific image by name:tag
docker rmi opensecurity/mobile-security-framework-mobsf:3.9.7
docker image rm opensecurity/mobile-security-framework-mobsf:3.9.7

# Remove by image ID
docker rmi a1b2c3d4e5f6

# Force remove even if a container uses the image
docker rmi -f a1b2c3d4e5f6

# Remove multiple images at once
docker rmi mobsf:3.9.7 drozer:latest ubuntu:20.04

# Remove all unused images (not referenced by any container)
docker image prune

# Remove ALL images including those referenced by stopped containers
docker image prune -a

# Remove all images (nuclear option — use with caution)
docker rmi $(docker images -q)

# Real-world: clean up after a pentest engagement
docker rmi opensecurity/mobile-security-framework-mobsf:latest
docker image prune -a --force
```

> **Note:** You cannot remove an image if a container (even stopped) is using it — stop and remove the container first with `docker rm`.

---

### 28. Image History
`docker image history` shows the build history of an image — each layer, its size, and the command that created it. Useful for understanding what's inside a third-party image.

```bash
# View full history of MobSF image
docker image history opensecurity/mobile-security-framework-mobsf:latest
# IMAGE         CREATED        CREATED BY                                   SIZE
# a1b2c3d4e5f6  2 weeks ago    /bin/sh -c #(nop)  CMD ["python3" "manage…   0B
# <missing>     2 weeks ago    /bin/sh -c #(nop)  EXPOSE 8000               0B
# <missing>     2 weeks ago    COPY . /home/mobsf                           245MB
# <missing>     2 weeks ago    RUN pip install -r requirements.txt           890MB
# <missing>     2 weeks ago    RUN apt-get install -y python3 git wget       180MB
# <missing>     2 weeks ago    FROM python:3.10-slim                         125MB

# Show full commands (not truncated)
docker image history --no-trunc opensecurity/mobile-security-framework-mobsf:latest

# Show only sizes
docker image history --format "{{.Size}}\t{{.CreatedBy}}" opensecurity/mobile-security-framework-mobsf:latest
```

> **Pentest Tip:** Use `docker image history --no-trunc` to reconstruct a partial Dockerfile from any image — useful when auditing Docker images for secrets embedded during build time.

---

### 29. Image Caching
Docker caches each image layer during builds. If a layer's instruction and inputs haven't changed, Docker reuses the cached layer instead of rebuilding — dramatically speeding up builds.

**How Cache Works:**
```dockerfile
# Dockerfile for Android pentest tools
FROM ubuntu:22.04                          # Layer 1: cached after first build
RUN apt-get update && apt-get install -y \ # Layer 2: cached if unchanged
    python3 adb apktool wget curl
COPY requirements.txt /app/               # Layer 3: cache BUSTED if file changes
RUN pip install -r /app/requirements.txt  # Layer 4: re-runs if layer 3 busted
COPY . /app/                              # Layer 5: always changes (source code)
```

**Cache Best Practices:**
```bash
# Force rebuild without cache
docker build --no-cache -t android-pentest:1.0 .

# Build shows cache hits
docker build -t android-pentest:1.1 .
# Step 1/6 : FROM ubuntu:22.04
#  ---> Using cache           ← Cache hit!
# Step 2/6 : RUN apt-get install...
#  ---> Using cache           ← Cache hit!
# Step 3/6 : COPY requirements.txt
#  ---> a1b2c3d4e5f6          ← Cache MISS (file changed)
# Step 4/6 : RUN pip install...
#  ---> Running in b2c3d4e5f6 ← Re-runs
```

**Optimization Tip:** Put frequently-changing instructions (COPY source code) **after** rarely-changing ones (apt-get installs) to maximize cache hits.

---

### 30. Image Pruning
Image pruning removes unused images to free up disk space — dangling images are unnamed/untagged layers left over from builds.

```bash
# Remove only dangling images (untagged intermediate layers)
docker image prune
# WARNING! This will remove all dangling images.
# Total reclaimed space: 1.2GB

# Remove all unused images (not used by any container)
docker image prune -a
# Removes: all images not referenced by a running or stopped container

# Skip confirmation prompt
docker image prune -a --force

# Filter by age — remove images older than 24 hours
docker image prune -a --filter "until=24h"

# Full system prune (images + containers + volumes + networks)
docker system prune -a

# Check space before/after
docker system df
# Before: Images: 8 images, 4.5GB
docker image prune -a --force
docker system df
# After:  Images: 2 images, 1.3GB (reclaimed 3.2GB)
```

**Real-World Example:** After completing an Android pentest engagement, run `docker image prune -a` to clean up all pulled analysis tools (MobSF, Drozer, Androguard) before handing back the pentest machine.

---

### 31. Image Naming Conventions
Docker images follow the naming format: `[registry/][user-or-org/]image-name[:tag][@digest]`.

**Format Breakdown:**
```
registry.pentestlab.internal:5000 / pentestteam / android-tools : 1.0
└──────────────────────────────┘   └───────────┘  └───────────┘   └─┘
        Registry (optional)          Namespace      Image name      Tag
        Default: docker.io           (org/user)

Examples:
  ubuntu                                    → docker.io/library/ubuntu:latest
  opensecurity/mobile-security-framework-mobsf → docker.io/opensecurity/mobile-security-framework-mobsf:latest
  registry.pentestlab.internal:5000/android-tools:1.0 → private registry
```

**Real-World Naming Examples:**
```bash
# Official images (no namespace = Docker Official Image)
docker pull ubuntu:22.04
docker pull python:3.11-slim

# Community/org images
docker pull opensecurity/mobile-security-framework-mobsf:latest
docker pull fsecurelabs/drozer:latest

# Private pentest team registry
docker pull registry.pentestlab.internal:5000/custom-frida-server:16.1.3-arm64

# Tagging your custom image properly before pushing
docker tag my-android-tools:latest \
  registry.pentestlab.internal:5000/pentestteam/android-tools:2024.01
docker push registry.pentestlab.internal:5000/pentestteam/android-tools:2024.01
```

---

### 32. Private Image Registries
A private Docker registry is a self-hosted or cloud-hosted repository for storing proprietary Docker images — used when you don't want to expose images publicly.

**Set Up a Self-Hosted Private Registry:**
```bash
# Run Docker's official registry image
docker run -d \
  --name pentest-registry \
  -p 5000:5000 \
  -v /opt/pentest-registry:/var/lib/registry \
  --restart always \
  registry:2

# Push a custom Android pentest image to it
docker tag android-frida-server:16.1.3 localhost:5000/android-frida-server:16.1.3
docker push localhost:5000/android-frida-server:16.1.3

# Pull from the private registry
docker pull localhost:5000/android-frida-server:16.1.3

# List images in private registry
curl http://localhost:5000/v2/_catalog
# {"repositories":["android-frida-server","mobsf-custom","drozer-patched"]}
```

**Use Cases for Android Pentesters:**
| Scenario | Private Registry Benefit |
|---|---|
| Custom Frida server builds | Share team-built Frida server images with specific gadgets |
| Pre-configured MobSF | Share MobSF with custom rules pre-loaded |
| Air-gapped environments | Pull images without internet access |
| Client engagement images | Store client-specific tooling privately |

---

## 4. Docker Containers

### 33. Container Lifecycle
A Docker container transitions through defined states from creation to removal. Understanding the lifecycle helps manage pentest tool containers efficiently.

**Lifecycle Diagram:**
```
docker create          docker start         docker pause
    │                      │                    │
    ▼                      ▼                    ▼
┌─────────┐   start   ┌─────────┐   pause  ┌─────────┐
│ CREATED │──────────▶│ RUNNING │─────────▶│ PAUSED  │
└─────────┘           └─────────┘          └─────────┘
                           │  │                 │
                docker stop│  │docker kill       │docker unpause
                           ▼  ▼                 │
                      ┌─────────┐◀──────────────┘
                      │ STOPPED │  (also: Exited)
                      └─────────┘
                           │
                       docker rm
                           │
                           ▼
                      ┌─────────┐
                      │ REMOVED │
                      └─────────┘
```

**State Transitions:**
```bash
# Create (but don't start)
docker create --name mobsf -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Start the created container
docker start mobsf

# Or combine: create + start in one command
docker run -d --name mobsf -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Pause (freeze processes)
docker pause mobsf

# Unpause
docker unpause mobsf

# Stop (SIGTERM then SIGKILL after 10s)
docker stop mobsf

# Remove (must be stopped first)
docker rm mobsf

# Check current state
docker ps -a --format "{{.Names}}\t{{.Status}}"
# mobsf    Up 2 hours
# drozer   Exited (0) 1 hour ago
```

---

### 34. Creating and Running Containers
`docker run` is the primary command for creating and starting a container. Key flags control detachment, interactivity, port mapping, naming, and auto-removal.

**Essential `docker run` Flags:**

| Flag | Purpose | Example |
|---|---|---|
| `-d` | Detached (background) mode | `-d` |
| `-it` | Interactive + TTY (terminal) | `-it` |
| `-p host:container` | Port mapping | `-p 8000:8000` |
| `--name` | Assign container name | `--name mobsf` |
| `--rm` | Auto-remove on exit | `--rm` |
| `-v host:container` | Volume mount | `-v /data:/uploads` |
| `--env` | Set environment variable | `--env API_KEY=abc123` |
| `--network` | Connect to network | `--network pentest-net` |
| `--restart` | Restart policy | `--restart unless-stopped` |

**Real-World Android Pentest Examples:**
```bash
# Run MobSF in background, map port, persist uploads
docker run -d \
  --name mobsf \
  -p 8000:8000 \
  -v $(pwd)/mobsf-data:/home/mobsf/.MobSF \
  --restart unless-stopped \
  opensecurity/mobile-security-framework-mobsf:latest

# Run interactive Drozer container and auto-remove when done
docker run -it --rm \
  --name drozer \
  --network host \
  fsecurelabs/drozer:latest bash

# Run quick one-off APK analysis with apktool (auto-removed after)
docker run --rm \
  -v $(pwd)/apks:/apks \
  androguard/androguard:latest \
  apktool d /apks/target.apk -o /apks/output
```

---

### 35. Starting and Stopping Containers
Use `docker start`/`docker stop` for graceful lifecycle control; `docker kill` for immediate termination.

```bash
# Start a stopped container (preserves container state)
docker start mobsf

# Start and attach to its output
docker start -a mobsf

# Graceful stop — sends SIGTERM, waits 10s, then SIGKILL
docker stop mobsf

# Stop with custom timeout (wait 30s before SIGKILL)
docker stop --time 30 mobsf

# Stop multiple containers
docker stop mobsf drozer frida-server

# Immediate kill — sends SIGKILL instantly (no graceful shutdown)
docker kill mobsf

# Send specific signal
docker kill --signal SIGHUP mobsf

# Stop ALL running containers
docker stop $(docker ps -q)

# Real-world: end-of-day cleanup after Android pentest session
docker stop mobsf drozer burpsuite
docker ps  # Verify all stopped
```

**When to use each:**
| Command | Signal | Use Case |
|---|---|---|
| `docker stop` | SIGTERM → SIGKILL | Normal shutdown, allows graceful close |
| `docker kill` | SIGKILL | Container hung/unresponsive |
| `docker pause` | SIGSTOP | Temporarily freeze without losing state |

---

### 36. Restarting Containers
`docker restart` stops and then starts a container; restart policies define automatic restart behavior when a container exits or the Docker daemon starts.

```bash
# Manually restart a container
docker restart mobsf

# Restart with custom stop timeout
docker restart --time 15 mobsf

# Restart policies (set at run time)
# no            → never restart (default)
# always        → always restart regardless of exit code
# unless-stopped → restart unless explicitly stopped
# on-failure[:N] → restart only on non-zero exit (up to N times)

# MobSF: always restart (persistent analysis service)
docker run -d --name mobsf \
  --restart always \
  -p 8000:8000 \
  opensecurity/mobile-security-framework-mobsf:latest

# Frida server: restart on failure up to 5 times
docker run -d --name frida-server \
  --restart on-failure:5 \
  my-frida-server:16.1.3

# Update restart policy on existing container
docker update --restart unless-stopped mobsf

# Check current restart policy
docker inspect mobsf --format '{{.HostConfig.RestartPolicy.Name}}'
# unless-stopped
```

---

### 37. Removing Containers
`docker rm` removes stopped containers; `docker container prune` removes all stopped containers in bulk to reclaim disk space.

```bash
# Remove a stopped container
docker rm mobsf

# Force remove a running container (sends SIGKILL first)
docker rm -f mobsf

# Remove multiple containers
docker rm mobsf drozer frida-server

# Remove container and its volumes
docker rm -v mobsf

# Remove all stopped containers
docker container prune

# Skip confirmation
docker container prune --force

# Remove containers older than 24 hours
docker container prune --filter "until=24h"

# Remove all containers (stop then remove)
docker rm -f $(docker ps -aq)

# One-liner: stop all running + remove all
docker stop $(docker ps -q) && docker rm $(docker ps -aq)

# Real-world: post-engagement cleanup
docker stop mobsf drozer burp
docker rm mobsf drozer burp
docker container prune --force  # catches any leftovers
```

---

### 38. Interactive Containers
Running containers with `-it` (`--interactive --tty`) opens an interactive shell session inside the container — essential for manual exploration and hands-on APK analysis.

**Real-World Use Case: Manual APK Inspection Shell**
```bash
# Start an interactive Ubuntu container with Android tools
docker run -it --rm \
  --name apk-inspector \
  -v $(pwd)/apks:/apks \
  ubuntu:22.04 bash

# Inside the container — manually inspect the APK
root@container:/# apt-get install -y apktool jadx adb unzip
root@container:/# cd /apks
root@container:/apks# apktool d target.apk -o decoded/
root@container:/apks# grep -r "API_KEY\|password\|secret" decoded/

# Interactive Drozer session
docker run -it --rm \
  --name drozer-console \
  --network host \
  fsecurelabs/drozer bash
root@drozer:/# drozer console connect --server 192.168.1.100

# Interactive Androguard Python session for APK analysis
docker run -it --rm \
  -v $(pwd)/apks:/apks \
  citizenlab/androguard:latest python3
>>> from androguard.misc import AnalyzeAPK
>>> a, d, dx = AnalyzeAPK("/apks/target.apk")
>>> print(a.get_permissions())

# Interactive shell into existing running container
docker exec -it mobsf bash
```

The `-i` keeps stdin open; `-t` allocates a pseudo-TTY so you get a proper interactive shell experience.

---

### 39. Detached Mode
Running a container with `-d` (`--detach`) starts it in the background, returning control to your terminal immediately — the container keeps running independently.

**Real-World Use Case: MobSF Running in Background**
```bash
# Start MobSF in detached mode — runs in background
docker run -d \
  --name mobsf \
  -p 8000:8000 \
  -v $(pwd)/mobsf-data:/home/mobsf/.MobSF \
  --restart unless-stopped \
  opensecurity/mobile-security-framework-mobsf:latest
# Output: a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6  ← container ID

# Your terminal is free — access MobSF at http://localhost:8000
# Continue your pentest work while MobSF analyses in background

# Check that it's running
docker ps
# CONTAINER ID   IMAGE          COMMAND   CREATED   STATUS         PORTS
# a1b2c3d4e5f6   opensecurity/… …         2 min     Up 2 minutes   0.0.0.0:8000->8000/tcp

# Attach to the container's output (to see logs in real-time)
docker attach mobsf
# Detach without stopping: Ctrl+P, Ctrl+Q

# View logs without attaching
docker logs -f mobsf
```

Detached mode is the standard for long-running services like MobSF, Burp Suite proxy, or Frida gadget servers during an Android pentest engagement.

---

### 40. Container Naming
The `--name` flag assigns a human-readable name to a container instead of a random auto-generated one (like `vigilant_hopper`). Names must be unique on the host.

**Benefits of Naming:**
- Easier to reference in commands
- Self-documenting in `docker ps` output
- Required for container-to-container DNS resolution in networks

```bash
# Named containers vs unnamed
docker run -d --name mobsf -p 8000:8000 opensecurity/mobile-security-framework-mobsf
docker run -d --name drozer -p 31415:31415 fsecurelabs/drozer
docker run -d --name burpsuite -p 8080:8080 my-burpsuite:latest

# Reference by name in all commands
docker stop mobsf
docker logs mobsf
docker exec -it mobsf bash
docker inspect mobsf
docker rm mobsf

# vs unnamed — must use random container ID
docker run -d -p 8000:8000 opensecurity/mobile-security-framework-mobsf
# Must reference by ID: docker stop a1b2c3d4e5f6

# Rename an existing container
docker rename vigilant_hopper mobsf

# Check container names
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
# NAMES      STATUS         PORTS
# mobsf      Up 2 hours     0.0.0.0:8000->8000/tcp
# drozer     Up 45 minutes  0.0.0.0:31415->31415/tcp
# burpsuite  Up 1 hour      0.0.0.0:8080->8080/tcp
```

---

### 41. Container Logs
Docker captures everything a container writes to stdout/stderr and stores it as logs, accessible via `docker logs`.

```bash
# View all logs
docker logs mobsf

# Follow (tail -f equivalent) — stream new log lines live
docker logs -f mobsf

# Show last 50 lines
docker logs --tail 50 mobsf

# Show logs with timestamps
docker logs -t mobsf

# Combine: last 100 lines with timestamps, follow
docker logs -f -t --tail 100 mobsf

# Show logs since a specific time
docker logs --since 2024-01-15T10:00:00 mobsf
docker logs --since 1h mobsf   # logs from the last hour

# Real-world: Watch MobSF process an uploaded APK
docker logs -f --tail 20 mobsf
# [INFO] Starting static analysis of com.target.app
# [INFO] Extracting APK...
# [WARNING] Found hardcoded API key: AIza...
# [INFO] Analysis complete. Score: 35/100

# Save logs to file for reporting
docker logs mobsf > mobsf-analysis-2024-01-15.log

# Drozer: see connection attempts
docker logs -f drozer
```

---

### 42. Container Inspection
`docker inspect` returns a comprehensive JSON object containing all configuration and runtime state of a container — IP address, mounts, environment variables, network settings, and more.

```bash
# Full JSON inspection
docker inspect mobsf

# Extract the container's IP address
docker inspect mobsf --format '{{.NetworkSettings.IPAddress}}'
# 172.17.0.2

# Get all mounted volumes
docker inspect mobsf --format '{{json .Mounts}}' | python3 -m json.tool
# [{"Source": "/home/pentester/mobsf-data", "Destination": "/home/mobsf/.MobSF", ...}]

# Get all environment variables (may contain API keys, credentials)
docker inspect mobsf --format '{{json .Config.Env}}' | python3 -m json.tool

# Get the container's status and exit code
docker inspect mobsf --format '{{.State.Status}} (exit: {{.State.ExitCode}})'
# running (exit: 0)

# Get restart policy
docker inspect mobsf --format '{{.HostConfig.RestartPolicy.Name}}'
# unless-stopped

# Get port bindings
docker inspect mobsf --format '{{json .NetworkSettings.Ports}}' | python3 -m json.tool
# {"8000/tcp": [{"HostIp": "0.0.0.0", "HostPort": "8000"}]}

# Inspect multiple containers
docker inspect mobsf drozer burpsuite
```

> **Pentest Tip:** `docker inspect` on containers in a compromised environment reveals network topology, mounted secrets, environment variable credentials, and internal IPs — high-value data for lateral movement assessment.

---

### 43. Executing Commands Inside Containers
`docker exec` runs a new command in an already-running container without stopping it — essential for debugging, file retrieval, and manual analysis.

```bash
# Open an interactive bash shell in the running MobSF container
docker exec -it mobsf bash
# root@a1b2c3d4:/# ls /home/mobsf/.MobSF/

# Open shell in drozer container
docker exec -it drozer sh  # Use sh if bash isn't available

# Run a one-off command (non-interactive)
docker exec mobsf ls /home/mobsf/.MobSF/uploads/

# Run command as specific user
docker exec -u root mobsf whoami
docker exec -u 1000 mobsf id

# Run command with environment variable
docker exec -e DEBUG=true mobsf python3 manage.py check

# Run in a specific working directory
docker exec -w /home/mobsf mobsf pwd

# Real-world scenarios:
# 1. Extract analysis results from MobSF container
docker exec mobsf cat /home/mobsf/.MobSF/results/com.target.app.json

# 2. Check running processes inside container
docker exec mobsf ps aux

# 3. Check network connections inside container
docker exec mobsf netstat -tulnp

# 4. Install a tool on-the-fly (non-persistent)
docker exec -it mobsf bash -c "pip install androguard && python3 -c 'import androguard; print(\"OK\")'"

# 5. Connect Drozer to an Android device's agent
docker exec -it drozer drozer console connect --server 192.168.1.100:31415
```

---

### 44. Container Environment Variables
Environment variables (`--env` / `-e`) pass configuration into containers at runtime without hardcoding values in images — used for API keys, credentials, and feature flags.

```bash
# Pass a single environment variable
docker run -d --name mobsf \
  -e MOBSF_API_KEY=your_api_key_here \
  -p 8000:8000 \
  opensecurity/mobile-security-framework-mobsf:latest

# Pass multiple environment variables
docker run -d --name pentest-app \
  -e TARGET_APK=/uploads/target.apk \
  -e REPORT_FORMAT=json \
  -e DEBUG=false \
  -e SLACK_WEBHOOK=https://hooks.slack.com/services/xxx \
  my-android-scanner:1.0

# Use an env file (best practice — keep secrets out of command history)
# Create .env file:
cat > pentest.env << EOF
MOBSF_API_KEY=abc123xyz
VIRUSTOTAL_API_KEY=def456uvw
SLACK_WEBHOOK=https://hooks.slack.com/services/xxx
REPORT_OUTPUT=/reports
EOF

# Run using the env file
docker run -d --name pentest-scanner \
  --env-file pentest.env \
  -v $(pwd)/reports:/reports \
  my-android-scanner:1.0

# Inspect environment variables in a running container
docker inspect mobsf --format '{{json .Config.Env}}' | python3 -m json.tool

# View env vars from inside the container
docker exec mobsf env
docker exec mobsf printenv MOBSF_API_KEY
```

> **Security Warning:** Never pass secrets via `-e KEY=value` in production/shared environments — the value appears in `docker inspect` output and shell history. Always use `--env-file` with a properly permissioned file, or use Docker Secrets.

---

### 45. Container Exit Codes
Exit codes indicate why and how a container stopped — critical for debugging automation scripts and CI/CD pipelines for Android pentest tools.

| Exit Code | Meaning | Cause |
|---|---|---|
| `0` | Success | Process completed normally |
| `1` | Application error | Unhandled exception, generic error |
| `2` | Misuse of shell command | Invalid argument |
| `125` | Docker run itself failed | Invalid docker flag/option |
| `126` | Container command cannot be invoked | Permission denied on entrypoint |
| `127` | Container command not found | Binary doesn't exist in image |
| `137` | OOM killed / SIGKILL | Out of memory, or `docker kill` |
| `139` | Segmentation fault | Application crashed (SIGSEGV) |
| `143` | Graceful termination | SIGTERM received (`docker stop`) |

```bash
# Check exit code of last stopped container
docker inspect mobsf --format '{{.State.ExitCode}}'
# 137  ← Container was killed (OOM or docker kill)

# Check exit code after container exits
docker run --rm ubuntu:22.04 ls /nonexistent
echo $?
# 2

# Run and capture exit code in script
docker run --rm my-apk-scanner:1.0 /apks/target.apk
EXIT_CODE=$?
if [ $EXIT_CODE -eq 0 ]; then
  echo "Scan completed successfully"
elif [ $EXIT_CODE -eq 137 ]; then
  echo "Scanner was killed — increase memory limits"
else
  echo "Scan failed with code $EXIT_CODE"
fi

# View all recently exited containers and their exit codes
docker ps -a --format "table {{.Names}}\t{{.Status}}" | grep Exited
```

---

### 46. Container Resource Usage
`docker stats` provides live, streaming resource usage metrics for running containers — CPU, memory, network I/O, and disk I/O.

```bash
# Live stats for all running containers
docker stats
# CONTAINER ID  NAME        CPU %   MEM USAGE/LIMIT    MEM %   NET I/O       BLOCK I/O
# a1b2c3d4e5f6  mobsf       45.3%   1.2GiB / 8GiB      15%     125MB/50MB    2.1GB/500MB
# b2c3d4e5f6a7  drozer      0.1%    45MiB / 8GiB       0.5%    1.2MB/800KB   50MB/10MB

# Stats for specific container
docker stats mobsf

# One-time snapshot (no streaming)
docker stats --no-stream

# Custom format output
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# Limit container resources when running
docker run -d \
  --name mobsf \
  --memory 2g \          # Max 2GB RAM
  --cpus 2 \             # Max 2 CPU cores
  --memory-swap 2g \     # No swap
  -p 8000:8000 \
  opensecurity/mobile-security-framework-mobsf:latest

# Real-world: Monitor MobSF during intensive APK analysis
docker stats --format "{{.Name}}: CPU={{.CPUPerc}} MEM={{.MemPerc}}" mobsf
# mobsf: CPU=87.3% MEM=62.4%  ← Heavy analysis running
```

---

### 47. Container Filesystem
Each container gets a writable layer on top of the read-only image layers. `docker cp` transfers files between host and container without needing shared volumes.

**Container Filesystem Structure:**
```
Image Layers (read-only):
  ├── Layer 5: App code
  ├── Layer 4: pip install
  ├── Layer 3: apt-get install
  └── Layer 1: Ubuntu base

Container Layer (writable — lost on docker rm):
  └── /home/mobsf/.MobSF/  (uploads, reports, database)
```

```bash
# Copy a file FROM host TO container
docker cp target.apk mobsf:/home/mobsf/.MobSF/uploads/target.apk

# Copy a file FROM container TO host
docker cp mobsf:/home/mobsf/.MobSF/logs/debug.log ./debug.log

# Copy entire directory from container
docker cp mobsf:/home/mobsf/.MobSF/StaticAnalyzer/reports/ ./reports/

# Real-world workflows:
# 1. Upload APK directly to MobSF container
docker cp suspicious.apk mobsf:/home/mobsf/.MobSF/uploads/

# 2. Retrieve analysis reports for documentation
docker cp mobsf:/home/mobsf/.MobSF/PDF/ ./pentest-report-$(date +%Y%m%d)/

# 3. Copy Frida scripts into a running container
docker cp hooks.js frida-container:/opt/frida/scripts/

# Explore container filesystem interactively
docker exec -it mobsf bash
root@container:/# find /home/mobsf -name "*.json" -newer /tmp/baseline
root@container:/# du -sh /home/mobsf/.MobSF/*
```

> **Key Point:** Data written to the container's writable layer is **lost** when the container is removed. Use volumes (`-v`) for persistent data like APK uploads and reports.

---

### 48. Container Health Checks
Health checks define a command Docker periodically runs inside the container to determine if it's healthy, starting, or unhealthy — enabling automatic detection of broken services.

**Health Check States:**
```
starting → healthy → unhealthy
              ↑           ↓
              └─ (recovery if check passes again)
```

```bash
# Check health status of a running container
docker inspect mobsf --format '{{json .State.Health}}' | python3 -m json.tool
# {
#   "Status": "healthy",
#   "FailingStreak": 0,
#   "Log": [
#     {
#       "Start": "2024-01-15T10:00:00Z",
#       "End": "2024-01-15T10:00:01Z",
#       "ExitCode": 0,
#       "Output": "HTTP/1.1 200 OK"
#     }
#   ]
# }

# Quick health status check
docker inspect mobsf --format '{{.State.Health.Status}}'
# healthy

# Filter containers by health status
docker ps --filter health=healthy
docker ps --filter health=unhealthy

# HEALTHCHECK in Dockerfile (preview):
# HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
#   CMD curl -f http://localhost:8000/ || exit 1

# Run container with inline health check override
docker run -d \
  --name mobsf \
  --health-cmd "curl -f http://localhost:8000/ || exit 1" \
  --health-interval 30s \
  --health-timeout 10s \
  --health-start-period 60s \
  --health-retries 3 \
  -p 8000:8000 \
  opensecurity/mobile-security-framework-mobsf:latest

# Monitor health in real-time
watch -n5 'docker inspect mobsf --format "Health: {{.State.Health.Status}}"'
```

**Real-World Example:** Set a health check on your MobSF container so that if the web interface stops responding (e.g., crashes after a malformed APK), Docker automatically flags it as `unhealthy` and (combined with `--restart on-failure`) restarts it, keeping your analysis pipeline running unattended overnight.

---

## 5. Dockerfiles and Image Building

### 49. Dockerfile Structure

A **Dockerfile** is a plain-text script of instructions Docker reads top-to-bottom to build a container image. Each instruction creates a new read-only layer stacked on the previous one.

**Anatomy:**
```dockerfile
# Comment
INSTRUCTION argument(s)
```

**Typical order of instructions:**
```
FROM        → base image
LABEL       → metadata
ENV         → environment variables
ARG         → build-time arguments
RUN         → install dependencies / tools
COPY/ADD    → bring in files
WORKDIR     → set working directory
USER        → drop to non-root
EXPOSE      → document ports
VOLUME      → declare mount points
HEALTHCHECK → liveness probe
ENTRYPOINT  → fixed executable
CMD         → default arguments / command
```

---

### 50. `FROM`

Sets the **base image** all subsequent instructions build upon. Every Dockerfile must begin with `FROM` (comments excepted).

```dockerfile
FROM ubuntu:22.04
# or a minimal Alpine base:
FROM python:3.11-alpine
```

**Android pentesting example:** Start from a Kali Linux image that already bundles many security tools:
```dockerfile
FROM kalilinux/kali-rolling:latest
```

You can also use a **scratch** image for ultra-minimal static binaries:
```dockerfile
FROM scratch
COPY frida-server /frida-server
CMD ["/frida-server"]
```

---

### 51. `RUN`

**Executes a shell command during the image build** and commits the result as a new layer.

```dockerfile
# Shell form (default /bin/sh -c)
RUN apt-get update && apt-get install -y jadx adb wget

# Exec form (no shell, no variable expansion)
RUN ["apt-get", "install", "-y", "apktool"]
```

**Best practice:** chain commands with `&&` and clean package caches in the same `RUN` to keep the layer small:
```dockerfile
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        adb \
        apktool \
        jadx \
        python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
```

---

### 52. `CMD`

Defines the **default command** run when a container starts (can be overridden at `docker run`).

```dockerfile
# Exec form (preferred)
CMD ["python3", "apk_analyzer.py", "--help"]

# Shell form
CMD python3 apk_analyzer.py --help
```

**Android pentesting example:** Default to opening a Bash shell in a recon container:
```dockerfile
CMD ["/bin/bash"]
```

> **Note:** Only the last `CMD` instruction takes effect. Providing a command in `docker run` overrides `CMD` completely.

---

### 53. `ENTRYPOINT`

Sets a **fixed executable** that always runs; `CMD` (or `docker run` args) supply arguments to it.

```dockerfile
ENTRYPOINT ["apktool"]
CMD ["--help"]
```

Running `docker run apktool-img d target.apk` → executes `apktool d target.apk`.

**Difference from CMD:**

| | `ENTRYPOINT` | `CMD` |
|---|---|---|
| Overridden by `docker run` args? | No (use `--entrypoint`) | Yes |
| Purpose | Fixed executable | Default args / command |
| Combine? | Yes — CMD provides default args | Yes — fallback if no args given |

**Android pentesting example:**
```dockerfile
ENTRYPOINT ["frida", "--usb"]
CMD ["--help"]
```
Running `docker run frida-img -n com.target.app` → `frida --usb -n com.target.app`.

---

### 54. `COPY`

**Copies files or directories from the build context (host)** into the image filesystem.

```dockerfile
COPY <src> <dest>
```

```dockerfile
# Copy a single APK into the image for static analysis
COPY target.apk /workspace/target.apk

# Copy an entire scripts directory
COPY scripts/ /opt/pentest/scripts/

# Copy with ownership (BuildKit required)
COPY --chown=pentester:pentester scripts/ /opt/pentest/scripts/
```

**Android pentesting example — copy custom JADX config:**
```dockerfile
COPY jadx-config.properties /root/.jadx/jadx-config.properties
```

> `COPY` only works with local files; for remote URLs use `ADD` or `RUN curl`.

---

### 55. `ADD`

Like `COPY` but with **two extra superpowers**: automatic extraction of local `.tar` archives and support for **remote URLs**.

```dockerfile
# Download and extract a tool release directly during build
ADD https://github.com/skylot/jadx/releases/download/v1.4.7/jadx-1.4.7.zip /opt/jadx/

# Auto-extract a local tarball
ADD tools.tar.gz /opt/tools/
```

**Android pentesting example — fetch Frida server for a specific ABI:**
```dockerfile
ADD https://github.com/frida/frida/releases/download/16.0.19/frida-server-16.0.19-android-arm64.xz /opt/frida/
```

> **Prefer `COPY`** for local files. Reserve `ADD` for URL fetching and tar extraction.

---

### 56. `WORKDIR`

**Sets the current working directory** for subsequent `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, and `ADD` instructions.

```dockerfile
WORKDIR /workspace
```

If the directory does not exist Docker creates it automatically.

**Android pentesting example:**
```dockerfile
WORKDIR /workspace/apk-analysis
COPY target.apk .
RUN apktool d target.apk -o decoded/
```

You can chain multiple `WORKDIR` instructions and use relative paths:
```dockerfile
WORKDIR /opt
WORKDIR tools        # resolves to /opt/tools
WORKDIR frida        # resolves to /opt/tools/frida
```

---

### 57. `ENV`

**Sets environment variables** available both during the build and at container runtime.

```dockerfile
ENV KEY=value
# Multiple in one instruction (preferred):
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk \
    ANDROID_HOME=/opt/android-sdk \
    PATH="$PATH:/opt/android-sdk/platform-tools"
```

**Android pentesting example:**
```dockerfile
ENV MOBSF_API_KEY=changeme \
    MOBSF_PORT=8000 \
    FRIDA_VERSION=16.0.19
```

> Avoid embedding secrets directly in `ENV`; use `--env-file` or Docker Secrets at runtime.

---

### 58. `ARG`

Declares **build-time variables** passed with `--build-arg` in `docker build`. Unlike `ENV`, `ARG` values are not persisted in the final image or at runtime.

```dockerfile
ARG FRIDA_VERSION=16.0.19
ARG ANDROID_SDK_VERSION=11
RUN wget https://github.com/frida/frida/releases/download/${FRIDA_VERSION}/frida-server-${FRIDA_VERSION}-android-arm64.xz
```

**Build with a custom version:**
```bash
docker build --build-arg FRIDA_VERSION=16.1.0 -t frida-server:16.1.0 .
```

**Key differences:**

| | `ARG` | `ENV` |
|---|---|---|
| Available at build | ✅ | ✅ |
| Available at runtime | ❌ | ✅ |
| Visible in `docker inspect` | Sometimes | ✅ |

---

### 59. `EXPOSE`

**Documents** which ports the container intends to listen on. It does **not** actually publish them — it's metadata for humans and orchestrators.

```dockerfile
EXPOSE 8000        # MobSF web interface
EXPOSE 8080        # Burp Proxy
EXPOSE 27042       # Frida default port
```

Ports must still be published with `-p` at runtime:
```bash
docker run -p 8000:8000 mobsf-image
```

---

### 60. `USER`

**Switches the user** (and optionally group) for subsequent `RUN`, `CMD`, and `ENTRYPOINT` instructions.

```dockerfile
# Create a dedicated non-root user and switch to it
RUN groupadd -r pentester && useradd -r -g pentester pentester
USER pentester
```

**Security best practice:** Never run production containers as `root`. This limits the blast radius if a vulnerability in your tool is exploited.

```dockerfile
RUN adduser --disabled-password --gecos '' analyst
USER analyst
WORKDIR /home/analyst/workspace
```

---

### 61. `VOLUME`

**Declares a mount point** inside the container. Docker creates an anonymous volume at runtime if no explicit mount is provided.

```dockerfile
VOLUME /workspace/reports
VOLUME ["/data", "/logs"]
```

**Android pentesting example:** Persist JADX decompiled output and scan reports:
```dockerfile
VOLUME /workspace/decoded-apks
VOLUME /workspace/scan-reports
```

At runtime, map to a host path to keep results after the container stops:
```bash
docker run -v $(pwd)/reports:/workspace/scan-reports jadx-img
```

---

### 62. `SHELL`

**Overrides the default shell** used for the shell form of `RUN`, `CMD`, and `ENTRYPOINT` instructions.

```dockerfile
# Use bash instead of sh (gives access to bash-specific syntax)
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Or use PowerShell on Windows base images
SHELL ["powershell", "-Command"]
```

**Android pentesting example:**
```dockerfile
SHELL ["/bin/bash", "-c"]
RUN echo $BASH_VERSION && source /opt/tools/env.sh
```

---

### 63. `HEALTHCHECK`

**Defines a command Docker runs periodically** to check if the container is healthy. A failing healthcheck marks the container as `unhealthy`.

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8000/api/v1/health || exit 1
```

**Android pentesting example — verify MobSF is responding:**
```dockerfile
HEALTHCHECK --interval=60s --timeout=5s --start-period=120s --retries=5 \
    CMD wget -q --spider http://localhost:8000 || exit 1
```

To disable a healthcheck inherited from a base image:
```dockerfile
HEALTHCHECK NONE
```

---

### 64. `.dockerignore`

A file in the build context root that tells Docker **which files and directories to exclude** from being sent to the daemon. This shrinks the build context and avoids leaking sensitive files into the image.

**Example `.dockerignore`:**
```
# Source control
.git
.gitignore

# Local secrets and creds
*.pem
*.key
.env
secrets/

# Test artifacts
*.apk.bak
test-reports/

# IDE / OS files
.idea/
.DS_Store
__pycache__/
*.pyc

# Large unneeded files
node_modules/
target/
build/
```

**Android pentesting example:** Exclude sample APKs used only for local dev testing:
```
samples/*.apk
```

---

### 65. Image Build Context

The **build context** is the set of files Docker sends to the daemon when you run `docker build`. It defaults to the directory you specify (`.` for current).

```bash
docker build -t apk-analyzer:1.0 .
#                                ^ build context = current directory
```

The daemon reads the Dockerfile and uses the context to resolve `COPY` and `ADD` instructions. **Everything in the context is sent over the socket**, so a large context slows builds—use `.dockerignore` aggressively.

```bash
# Use a specific directory as context
docker build -t mobsf-custom -f Dockerfile.mobsf ./mobsf-context/

# Build from a remote Git repo (Docker fetches context from Git)
docker build https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
```

---

### 66. Build Cache

Docker caches each layer. If an instruction and its inputs are **unchanged from a previous build**, Docker reuses the cached layer — making subsequent builds much faster.

**Cache invalidation rules:**
- Any change to a `RUN` command string invalidates it and all layers below.
- `COPY`/`ADD`: if the copied files' checksums change, the layer is invalidated.
- `ARG` change → invalidates from that `ARG` instruction downward.

**Best practice — order from least to most frequently changing:**
```dockerfile
FROM ubuntu:22.04
# Rarely changes → near top
RUN apt-get update && apt-get install -y openjdk-17-jdk apktool

# More frequently changed → lower
COPY requirements.txt /app/
RUN pip install -r /app/requirements.txt

# Most frequently changed → near bottom
COPY . /app/
```

**Force no cache:**
```bash
docker build --no-cache -t apk-analyzer:fresh .
```

---

### 67. Multi-Stage Builds

**Use multiple `FROM` instructions** to separate build-time dependencies from the final runtime image, resulting in a much smaller, cleaner production image.

```dockerfile
# ── Stage 1: Build ──────────────────────────────────────────────
FROM gradle:8.4-jdk17 AS builder
WORKDIR /src
COPY . .
RUN gradle shadowJar --no-daemon

# ── Stage 2: Runtime ─────────────────────────────────────────────
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
# Copy ONLY the compiled JAR from the builder stage
COPY --from=builder /src/build/libs/apk-analyzer-all.jar /app/apk-analyzer.jar

RUN adduser -D analyst
USER analyst
ENTRYPOINT ["java", "-jar", "/app/apk-analyzer.jar"]
```

**Android pentesting example:** Build a custom JADX wrapper with Gradle in stage 1, ship only the JRE + JAR in stage 2. The final image contains no Gradle, no source, no build cache — just the runtime.

```bash
docker build -t apk-analyzer:slim .
docker images apk-analyzer
# REPOSITORY     TAG    SIZE
# apk-analyzer   slim   145MB   ← vs 800MB+ single-stage
```

---

### 68. BuildKit

**BuildKit** is Docker's modern, high-performance build engine replacing the legacy builder. It offers parallel stage execution, better caching, secret mounts, and SSH forwarding.

**Enable:**
```bash
# One-time
DOCKER_BUILDKIT=1 docker build -t myimage .

# Permanent (Docker Desktop enables it by default)
# In /etc/docker/daemon.json:
{ "features": { "buildkit": true } }
```

**Key BuildKit features:**

| Feature | Usage |
|---|---|
| Parallel stages | Concurrent independent `FROM` stages |
| `--mount=type=cache` | Persistent package cache across builds |
| `--mount=type=secret` | Inject secrets without baking into layers |
| `--mount=type=ssh` | Forward SSH agent into build |
| Inline cache | `--cache-from`, `--cache-to` |

**Android pentesting example — mount pip cache:**
```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.11-slim
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install frida-tools objection
```

**Inject an API key at build without embedding it:**
```bash
docker build --secret id=api_key,src=./api_key.txt -t secure-img .
```
```dockerfile
RUN --mount=type=secret,id=api_key \
    API_KEY=$(cat /run/secrets/api_key) ./configure --key=$API_KEY
```

---

### 69. Image Optimization

Strategies to produce **smaller, faster, more secure images:**

| Technique | Example |
|---|---|
| Use Alpine/slim base | `FROM python:3.11-alpine` |
| Minimize layers | Chain `RUN` with `&&` |
| Clean caches in same `RUN` | `rm -rf /var/lib/apt/lists/*` |
| Multi-stage builds | Separate build from runtime |
| `.dockerignore` | Exclude dev/test files |
| Remove unnecessary tools | Don't install `wget` if only needed during build |
| Use `--no-install-recommends` | `apt-get install -y --no-install-recommends` |

**Optimized Android pentesting tool image:**
```dockerfile
FROM python:3.11-alpine AS base

# Install build deps, use cache mount, then clean
RUN apk add --no-cache \
        openjdk17-jre \
        android-tools \
    && pip install --no-cache-dir frida-tools objection mobsfscan

WORKDIR /workspace
USER nobody
CMD ["bash"]
```

**Check image size:**
```bash
docker images --format "{{.Repository}}:{{.Tag}}\t{{.Size}}"
docker history apk-analyzer:latest   # see per-layer sizes
```

---

### 70. Non-Root Container Images

Running as `root` inside a container is dangerous: a container escape or tool vulnerability gives an attacker root on the host. **Always drop to a non-root user.**

```dockerfile
# Create user during build
RUN groupadd -r analyst && useradd -r -g analyst -d /home/analyst -s /bin/bash analyst \
    && mkdir -p /home/analyst/workspace \
    && chown -R analyst:analyst /home/analyst

# Switch to non-root user
USER analyst
WORKDIR /home/analyst/workspace
```

**At runtime, enforce non-root:**
```bash
docker run --user 1000:1000 apk-analyzer:latest
```

**Rootless Docker** (run the Docker daemon itself as non-root):
```bash
dockerd-rootless-setuptool.sh install
```

---

### Complete Example Dockerfile — Custom APK Analyzer / JADX Wrapper

```dockerfile
# syntax=docker/dockerfile:1
# ─────────────────────────────────────────────────────────────────
# Stage 1: Builder — download and prepare tools
# ─────────────────────────────────────────────────────────────────
FROM ubuntu:22.04 AS builder

ARG JADX_VERSION=1.4.7
ARG FRIDA_VERSION=16.0.19

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        wget \
        unzip \
        ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download JADX
RUN mkdir -p /opt/jadx \
    && wget -q https://github.com/skylot/jadx/releases/download/v${JADX_VERSION}/jadx-${JADX_VERSION}.zip \
        -O /tmp/jadx.zip \
    && unzip /tmp/jadx.zip -d /opt/jadx \
    && rm /tmp/jadx.zip

# ─────────────────────────────────────────────────────────────────
# Stage 2: Runtime — lean production image
# ─────────────────────────────────────────────────────────────────
FROM eclipse-temurin:17-jre-jammy

LABEL maintainer="your-team@example.com" \
      description="Android APK static analysis — JADX wrapper" \
      version="1.0"

# Environment configuration
ENV JADX_HOME=/opt/jadx \
    OUTPUT_DIR=/workspace/output \
    REPORT_DIR=/workspace/reports \
    PATH="$PATH:/opt/jadx/bin"

# Install runtime dependencies only
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        python3 \
        python3-pip \
        adb \
        curl \
    && pip3 install --no-cache-dir mobsfscan semgrep \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Copy JADX from builder stage
COPY --from=builder /opt/jadx /opt/jadx

# Copy custom analysis scripts
COPY --chown=analyst:analyst scripts/ /opt/apk-analyzer/scripts/
COPY --chown=analyst:analyst config/analyzer.conf /opt/apk-analyzer/config/

# Set working directory
WORKDIR /workspace

# Create non-root user
RUN groupadd -r analyst \
    && useradd -r -g analyst -d /home/analyst -s /bin/bash analyst \
    && mkdir -p /workspace/output /workspace/reports /home/analyst \
    && chown -R analyst:analyst /workspace /home/analyst

# Switch to non-root user
USER analyst

# Declare persistent storage mount points
VOLUME ["/workspace/output", "/workspace/reports"]

# Document port (e.g., if analyzer exposes a local web report)
EXPOSE 8080

# Health check — verify JADX is functional
HEALTHCHECK --interval=60s --timeout=10s --start-period=30s --retries=3 \
    CMD jadx --version || exit 1

# Default: show help
ENTRYPOINT ["python3", "/opt/apk-analyzer/scripts/analyze.py"]
CMD ["--help"]
```

**Build and run:**
```bash
# Build with default JADX version
docker build -t apk-analyzer:1.0 .

# Build with custom JADX version
docker build --build-arg JADX_VERSION=1.5.0 -t apk-analyzer:1.5 .

# Run analysis on a target APK (bind mount from host)
docker run --rm \
    -v $(pwd)/apks:/workspace/apks:ro \
    -v $(pwd)/reports:/workspace/reports \
    -p 8080:8080 \
    apk-analyzer:1.0 \
    --apk /workspace/apks/target.apk \
    --output /workspace/reports/
```

---

## 6. Docker Networking

### 71. Container Networking

Docker gives each container its own **virtual network namespace** — isolated network interfaces, routing tables, and iptables rules. Containers communicate via virtual ethernet pairs (`veth`) connected to a bridge or overlay network managed by Docker.

**Network stack overview:**
```
Host OS kernel
  └── Docker network namespace
        ├── docker0 (default bridge interface)
        │     ├── veth0 ←→ container1 eth0 (172.17.0.2)
        │     └── veth1 ←→ container2 eth0 (172.17.0.3)
        └── user-defined bridges (pentestlab, etc.)
```

```bash
# List all networks
docker network ls

# See Docker-managed interfaces on host
ip link show | grep -E "docker|veth|br-"
```

---

### 72. Bridge Network

The **default network mode**. Docker creates a virtual bridge interface `docker0` on the host; all containers without a `--network` flag attach to it.

```bash
# Run container on default bridge (implicit)
docker run -d --name mobsf opensecurity/mobile-security-framework-mobsf

# Inspect bridge subnet and connected containers
docker network inspect bridge
```

**Characteristics:**
- Containers get IPs in `172.17.0.0/16` by default
- Containers **cannot** resolve each other by name (use user-defined bridges for DNS)
- Port publishing (`-p`) required for host access

---

### 73. Host Network

Container **shares the host's network stack** — no network namespace isolation. The container's ports are the host's ports.

```bash
docker run --rm --network host nmap -sV 192.168.1.1
```

**Android pentesting use case:** Run `nmap` or `tcpdump` with direct access to host NICs (useful for scanning a LAN where the Android device resides):
```bash
docker run --rm --network host \
    --cap-add NET_RAW --cap-add NET_ADMIN \
    instrumentisto/nmap -sn 192.168.1.0/24
```

> **Security note:** Host networking bypasses container network isolation — use only when necessary.

---

### 74. None Network

**Completely disables networking** for the container. No loopback, no external connectivity.

```bash
docker run --rm --network none apk-analyzer:1.0 --apk /workspace/target.apk
```

**Android pentesting use case:** Run static analysis tools (JADX, apktool, MobSFScan) in a fully air-gapped container — no exfiltration risk, no outbound connections from the tool:
```bash
docker run --rm \
    --network none \
    -v $(pwd)/apks:/in:ro \
    -v $(pwd)/reports:/out \
    apk-analyzer:1.0 --apk /in/target.apk --output /out
```

---

### 75. User-Defined Bridge Networks

Custom networks created with `docker network create`. **Strongly preferred** over the default bridge.

```bash
docker network create pentestlab
docker run -d --network pentestlab --name mobsf opensecurity/mobile-security-framework-mobsf
docker run -d --network pentestlab --name postgres postgres:15
```

**Advantages over default bridge:**

| Feature | Default Bridge | User-Defined Bridge |
|---|---|---|
| DNS resolution by name | ❌ | ✅ |
| Container isolation | Shared with all containers | Per-network |
| Attach/detach live | ❌ | ✅ |
| Recommended for use | ❌ | ✅ |

---

### 76. Overlay Networks

**Span multiple Docker hosts** — used in Docker Swarm and Kubernetes for multi-node container communication. Each container on any node can reach any other by name.

```bash
# Initialize Swarm
docker swarm init

# Create overlay network
docker network create --driver overlay pentest-overlay

# Deploy service on overlay
docker service create --network pentest-overlay --name mobsf opensecurity/mobile-security-framework-mobsf
```

**Android pentesting use case:** Distribute a large-scale APK analysis pipeline across multiple machines — one node runs MobSF, another runs the database, another runs a Frida instrumentation bridge.

---

### 77. Network Creation and Removal

```bash
# Create a bridge network with custom subnet
docker network create \
    --driver bridge \
    --subnet 10.10.10.0/24 \
    --gateway 10.10.10.1 \
    pentestlab

# Create with custom label
docker network create --label project=android-pentest recon-net

# Remove a specific network
docker network rm pentestlab

# Remove all unused networks
docker network prune

# Remove multiple
docker network rm pentestlab recon-net
```

---

### 78. Network Inspection

```bash
# Inspect a network (shows connected containers, subnet, gateway)
docker network inspect pentestlab

# Pretty-print specific fields with Go template
docker network inspect pentestlab \
    --format '{{range .Containers}}{{.Name}}: {{.IPv4Address}}{{"\n"}}{{end}}'

# Inspect default bridge
docker network inspect bridge
```

**Sample output (trimmed):**
```json
{
  "Name": "pentestlab",
  "Driver": "bridge",
  "IPAM": { "Config": [{ "Subnet": "10.10.10.0/24" }] },
  "Containers": {
    "abc123": { "Name": "mobsf", "IPv4Address": "10.10.10.2/24" },
    "def456": { "Name": "postgres", "IPv4Address": "10.10.10.3/24" }
  }
}
```

---

### 79. Container-to-Container Communication

On a **user-defined bridge**, containers resolve each other by name automatically:

```bash
docker network create pentestlab
docker run -d --network pentestlab --name mobsf opensecurity/mobile-security-framework-mobsf
docker run -d --network pentestlab --name burp-proxy burpsuite-img

# From inside burp-proxy, reach mobsf by name:
docker exec burp-proxy curl http://mobsf:8000/api/v1/health
```

**Android pentesting example:** MobSF container calls a PostgreSQL container by service name `postgres` — no hardcoded IPs needed.

```bash
# Connect container to an additional network
docker network connect pentestlab another-container
```

---

### 80. DNS-Based Service Discovery

User-defined bridge networks have a **built-in DNS server** (at `127.0.0.11`) that resolves container names and network aliases to their IPs automatically.

```bash
# Set a network alias (multiple services behind one name)
docker run -d --network pentestlab --network-alias analyzer apk-analyzer:1.0
docker run -d --network pentestlab --network-alias analyzer apk-analyzer:2.0

# Both respond to "analyzer" — useful for load balancing experiments
docker exec some-container curl http://analyzer:8080
```

**Android pentesting example:** All containers in `pentestlab` resolve `mobsf` → `10.10.10.2` automatically; changing the MobSF container doesn't require updating other containers.

---

### 81. Port Publishing and Mapping

Exposes container ports to the **host** (and optionally to external machines).

```bash
# Syntax: -p <host_ip>:<host_port>:<container_port>/<proto>

# Map host 8000 → container 8000 (MobSF)
docker run -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Bind to localhost only (more secure — no external exposure)
docker run -p 127.0.0.1:8080:8080 burpsuite-img

# Random host port
docker run -p 8000 mobsf-img   # Docker picks an available host port

# Map UDP port (e.g., for a DNS listener)
docker run -p 53:53/udp dns-spoofer-img

# Check published ports
docker port container_name
```

---

### 82. Host-to-Container Communication

Once a port is published, access it from the host like any local service:

```bash
# MobSF is running with -p 8000:8000
curl http://localhost:8000/api/v1/health
# Open in browser: http://localhost:8000

# Upload APK via MobSF API from host
curl -F "file=@target.apk" http://localhost:8000/api/v1/upload
```

**Android pentesting example:** Point your Android device's proxy to `<host-machine-IP>:8080` to route traffic through Burp Suite running in a Docker container with `-p 8080:8080`.

---

### 83. Container-to-Host Communication

Containers need to reach **services running on the host machine** (e.g., ADB server on host, local API).

**On macOS/Windows:** Docker provides the magic hostname `host.docker.internal`:
```bash
docker exec mobsf curl http://host.docker.internal:5037   # reach ADB on host
```

**On Linux:** Use `--add-host` flag or the `host` gateway IP:
```bash
docker run --add-host=host.docker.internal:host-gateway apk-analyzer:1.0
```

**Android pentesting example:** Frida server runs on a connected Android device via ADB on the host. A container running Frida client connects to it:
```bash
docker run --rm \
    --add-host=host.docker.internal:host-gateway \
    -e ADB_SERVER=host.docker.internal:5037 \
    frida-client-img frida -H host.docker.internal:27042 -n com.target.app
```

---

### 84. Network Isolation

Use **separate networks** to control which containers can talk to each other — a critical security principle for a multi-tool pentesting lab.

```
┌─────────────────────────────────────────────────────┐
│  pentest-frontend (bridge)                           │
│   ├── burp-proxy     (access from host only)         │
│   └── mobsf          (access from host only)         │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  pentest-backend (bridge, NOT exposed to host)       │
│   ├── postgres       (DB for MobSF)                  │
│   └── redis          (cache)                         │
└─────────────────────────────────────────────────────┘

mobsf is connected to BOTH networks — acts as a gateway.
```

```bash
docker network create pentest-frontend
docker network create pentest-backend

docker run -d --network pentest-frontend --name burp-proxy ...
docker run -d --network pentest-frontend --network pentest-backend --name mobsf ...
docker run -d --network pentest-backend --name postgres ...
```

PostgreSQL is **unreachable** from `burp-proxy` — it's on a separate network.

---

### 85. Network Troubleshooting

```bash
# List all networks
docker network ls

# Inspect a network
docker network inspect pentestlab

# Check container's IP and network
docker inspect --format '{{json .NetworkSettings.Networks}}' container_name | jq

# Test connectivity from inside a container
docker exec mobsf curl -v http://postgres:5432
docker exec mobsf ping -c 3 postgres

# Install and use networking tools inside a container
docker exec -it recon-container bash
apt-get install -y iputils-ping netcat curl net-tools
netstat -tlnp
nc -zv postgres 5432

# Trace route from container
docker exec container_name traceroute 8.8.8.8

# Capture traffic on a container's veth interface (from host)
VETH=$(docker inspect --format '{{.NetworkSettings.SandboxKey}}' container_name)
nsenter --net=$VETH tcpdump -i eth0

# Check published ports
docker port container_name

# Restart network if broken
docker network disconnect pentestlab container_name
docker network connect pentestlab container_name
```

---

## 7. Docker Storage

### 86. Container Writable Layers

Every running container adds a thin **writable layer** (the "container layer") on top of the read-only image layers. All changes (files written, logs generated, tool output) go here.

```
Image layers (read-only):
  └── Layer 3: COPY scripts/
  └── Layer 2: RUN apt-get install
  └── Layer 1: FROM ubuntu:22.04

Container writable layer (ephemeral):
  └── /tmp/scan.log        ← written at runtime, LOST on container removal
  └── /workspace/output/   ← also lost unless volume-mounted
```

```bash
# Show writable layer location on host
docker inspect container_name | grep MergedDir
```

> **Key point:** Writable layer data is **destroyed** when the container is removed (`docker rm`). Use volumes or bind mounts for anything you want to keep.

---

### 87. Docker Volumes

**Managed by Docker**, stored in `/var/lib/docker/volumes/` on the host. Volumes persist independently of container lifecycle — they survive `docker rm`.

```bash
# Create a volume
docker volume create scan_results

# Mount it at run time
docker run -v scan_results:/workspace/reports apk-analyzer:1.0

# List volumes
docker volume ls

# Inspect a volume
docker volume inspect scan_results

# Remove a volume
docker volume rm scan_results
```

**Advantages of volumes over bind mounts:**

| | Volumes | Bind Mounts |
|---|---|---|
| Managed by Docker | ✅ | ❌ |
| Portable | ✅ | ❌ |
| Works on all OSes | ✅ | Mostly |
| Permissions controlled | ✅ | Host-dependent |
| Best for prod data | ✅ | Dev workflow |

---

### 88. Named Volumes

A **named volume** is a volume with an explicit name — easy to reference, reuse, and backup.

```bash
# Create named volume for APK scan results
docker volume create apk_scan_results

# Use in multiple containers (shared volume)
docker run -d -v apk_scan_results:/workspace/reports apk-analyzer:1.0 --apk /in/app1.apk
docker run -d -v apk_scan_results:/workspace/reports report-generator:latest

# Inspect path on host
docker volume inspect apk_scan_results
# "Mountpoint": "/var/lib/docker/volumes/apk_scan_results/_data"
```

**Android pentesting example:** Keep all MobSF scan reports in a named volume so they persist across MobSF container upgrades:
```bash
docker volume create mobsf_data
docker run -d \
    -p 8000:8000 \
    -v mobsf_data:/home/mobsf/.MobSF \
    opensecurity/mobile-security-framework-mobsf
```

---

### 89. Anonymous Volumes

Created **without an explicit name** — Docker assigns a random UUID. Lifecycle is loosely tied to the container (removed with `docker rm -v`).

```dockerfile
# Dockerfile declaring an anonymous volume
VOLUME /tmp/scan-cache
```

```bash
# At runtime — Docker creates an anonymous volume
docker run -d apk-analyzer:1.0

# See the anonymous volume (has a UUID name)
docker volume ls
# DRIVER    VOLUME NAME
# local     a3f2c1d4b5e6...

# Remove container AND its anonymous volumes
docker rm -v container_name
```

> **Best practice:** Prefer named volumes for anything important. Anonymous volumes are fine for true throw-away data.

---

### 90. Bind Mounts

**Mount a specific host filesystem path** into a container. Changes are immediately visible on both sides.

```bash
# Syntax: -v /host/absolute/path:/container/path[:options]
docker run --rm \
    -v /Users/analyst/apks:/workspace/apks:ro \
    -v /Users/analyst/reports:/workspace/reports \
    apk-analyzer:1.0 --apk /workspace/apks/target.apk

# Using --mount syntax (more explicit)
docker run --rm \
    --mount type=bind,source=/Users/analyst/apks,target=/workspace/apks,readonly \
    --mount type=bind,source=/Users/analyst/reports,target=/workspace/reports \
    apk-analyzer:1.0
```

**Android pentesting example:** Work on APKs from your host `~/pentest/apks/` folder while keeping the analysis environment isolated in the container:
```bash
docker run --rm \
    -v ~/pentest/apks:/in:ro \
    -v ~/pentest/reports:/out \
    -v ~/pentest/scripts:/opt/custom:ro \
    jadx-analyzer decompile /in/com.target.banking.apk -o /out/decompiled/
```

---

### 91. `tmpfs` Mounts

An **in-memory filesystem** — data is stored in RAM, never written to disk, and destroyed when the container stops.

```bash
docker run --rm \
    --tmpfs /tmp:size=100m,mode=1777 \
    apk-analyzer:1.0

# Using --mount
docker run --rm \
    --mount type=tmpfs,destination=/tmp,tmpfs-size=100m \
    apk-analyzer:1.0
```

**Android pentesting security use case:**
- Store decrypted payloads, private keys, or API tokens temporarily without leaving traces on disk.
- Run malware analysis where you don't want any artifacts persisted.

```bash
docker run --rm \
    --network none \
    --tmpfs /workspace/staging:size=500m \
    malware-sandbox:latest analyze --apk /in/suspicious.apk --workdir /workspace/staging
```

---

### 92. Persistent Storage

**Pattern for keeping scan reports across container restarts:**

```bash
# 1. Create a named volume once
docker volume create mobsf_reports

# 2. Run container with volume — reports persist even if container is replaced
docker run -d \
    --name mobsf \
    --restart unless-stopped \
    -p 8000:8000 \
    -v mobsf_reports:/home/mobsf/.MobSF \
    opensecurity/mobile-security-framework-mobsf

# 3. Stop and remove the container (simulate upgrade)
docker stop mobsf && docker rm mobsf

# 4. Start a new version — same data is available
docker run -d \
    --name mobsf \
    -p 8000:8000 \
    -v mobsf_reports:/home/mobsf/.MobSF \
    opensecurity/mobile-security-framework-mobsf:latest
```

**Android pentesting example:** APK scan history, decompiled code, and vulnerabilities found are never lost across MobSF version upgrades.

---

### 93. Read-Only Mounts

Mount a volume or bind mount as **read-only** (`:ro`) to prevent container from modifying host data — a key security hardening measure.

```bash
# Read-only bind mount of APK input directory
docker run --rm \
    -v /pentest/apks:/workspace/apks:ro \
    -v /pentest/reports:/workspace/reports \
    apk-analyzer:1.0

# Read-only volume mount
docker run -v shared_wordlists:/opt/wordlists:ro \
    --name scanner cracker-img
```

**Read-only root filesystem (most restrictive):**
```bash
docker run --read-only \
    --tmpfs /tmp \
    --tmpfs /run \
    apk-analyzer:1.0
```

**Android pentesting example:** Mount the APK under test as `:ro` so a potentially malicious APK analysis tool cannot overwrite the original file.

---

### 94. Volume Backup and Restoration

**Backup a named volume** by mounting it into a temporary container and archiving its contents:

```bash
# Backup: tar the volume contents to host
docker run --rm \
    -v mobsf_data:/source:ro \
    -v $(pwd)/backups:/backup \
    busybox tar czf /backup/mobsf_backup_$(date +%Y%m%d).tar.gz -C /source .

# Restore: extract archive back into volume
docker run --rm \
    -v mobsf_data:/dest \
    -v $(pwd)/backups:/backup:ro \
    busybox tar xzf /backup/mobsf_backup_20240101.tar.gz -C /dest
```

**Using `--volumes-from` (copy between containers):**
```bash
# Create a backup container that reads volumes from mobsf container
docker run --rm \
    --volumes-from mobsf \
    -v $(pwd)/backups:/backup \
    busybox tar czf /backup/mobsf_volumes.tar.gz /home/mobsf/.MobSF
```

---

### 95. Storage Cleanup

**Volumes persist even after containers are removed** — clean up regularly to reclaim disk space.

```bash
# Remove a specific volume
docker volume rm scan_results

# Remove ALL unused volumes (not referenced by any container)
docker volume prune

# Remove unused volumes with a label filter
docker volume prune --filter "label=project=old-pentest"

# Nuclear option — remove ALL unused Docker data:
docker system prune --volumes --all

# Check disk usage breakdown
docker system df
docker system df -v   # verbose, shows per-volume usage
```

**Safe cleanup pattern for a pentesting session:**
```bash
# Stop and remove all stopped containers, then prune dangling volumes
docker container prune -f
docker volume prune -f
docker image prune -f   # remove dangling images
```

---

### 96. File Permissions and Ownership

**UID/GID mismatches** are a common pain point: files written inside a container may be owned by `root` (UID 0) on the host if the container runs as root.

**The problem:**
```bash
docker run -v $(pwd)/reports:/workspace/reports apk-analyzer:1.0
# Reports are written as root inside container...
ls -la reports/   # -rw-r--r-- 1 root root  ← you can't delete these as regular user!
```

**Solutions:**

```bash
# 1. Run container as your UID
docker run --user $(id -u):$(id -g) -v $(pwd)/reports:/workspace/reports apk-analyzer:1.0

# 2. Fix ownership after the fact
docker run --rm -v $(pwd)/reports:/fix busybox chown -R 1000:1000 /fix

# 3. In Dockerfile: create user with matching UID
RUN groupadd -g 1000 analyst && useradd -u 1000 -g 1000 analyst
USER analyst
```

**In `compose.yaml`:**
```yaml
services:
  analyzer:
    image: apk-analyzer:1.0
    user: "1000:1000"
    volumes:
      - ./reports:/workspace/reports
```

---

## 8. Docker Compose

### 97. What is Docker Compose?

**Docker Compose** is a tool for defining and running **multi-container applications** from a single YAML file. Instead of juggling many `docker run` commands, you declare all services, networks, and volumes in one place and manage them with simple commands.

```bash
docker compose up -d      # start everything
docker compose down       # stop and remove everything
```

**Android pentesting use case:** Spin up a complete mobile pentesting lab — MobSF, PostgreSQL, Nginx proxy, and Burp Suite — with a single command.

---

### 98. `compose.yaml`

The default configuration file (also accepts `docker-compose.yml`). Contains top-level sections:

```yaml
# compose.yaml structure
name: pentest-lab          # optional project name

services:                  # container definitions
  mobsf: ...
  postgres: ...

networks:                  # network definitions
  frontend: ...
  backend: ...

volumes:                   # volume definitions
  mobsf_data: ...

configs:                   # config file injection (Swarm)
  nginx_conf: ...

secrets:                   # sensitive data (Swarm/standalone)
  db_password: ...
```

**Validate your compose file:**
```bash
docker compose config         # parse and print resolved config
docker compose config --quiet # just check for errors
```

---

### 99. Services

Each **service** is a container definition. Service name becomes the DNS hostname on shared networks.

```yaml
services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    container_name: mobsf

  burpsuite:
    image: ghcr.io/your-org/burpsuite:latest
    container_name: burp-proxy

  adb-server:
    image: sorccu/adb:latest
    container_name: adb-server
    privileged: true
    volumes:
      - /dev/bus/usb:/dev/bus/usb   # USB device pass-through for physical Android
```

Other services on the same network can reach `mobsf` at `http://mobsf:8000`, `burpsuite` at `http://burpsuite:8080`, etc.

---

### 100. Images and Builds

Two ways to specify a service's container image:

```yaml
services:
  # Option A: Pull from registry
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest

  # Option B: Build from local Dockerfile
  apk-analyzer:
    build:
      context: ./apk-analyzer          # build context directory
      dockerfile: Dockerfile.prod      # custom Dockerfile name
      args:
        JADX_VERSION: "1.4.7"
        FRIDA_VERSION: "16.0.19"
      target: runtime                  # multi-stage build target

  # Option C: Both (build locally, use image name for push)
  custom-frida:
    build: ./frida-client
    image: myregistry.io/frida-client:1.0
```

```bash
# Build all services
docker compose build

# Build and push to registry
docker compose build --push
```

---

### 101. Ports

Map **host ports to container ports** under the `ports:` key.

```yaml
services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    ports:
      - "8000:8000"            # host:container

  burpsuite:
    ports:
      - "127.0.0.1:8080:8080" # bind to localhost only — safer

  frida-server:
    ports:
      - "27042:27042"          # Frida default port

  adb-server:
    ports:
      - "5037:5037"            # ADB server port
```

```bash
# Check mapped ports for running services
docker compose port mobsf 8000
```

---

### 102. Volumes

Define volumes for **persistent data** across restarts and container replacements.

```yaml
services:
  mobsf:
    volumes:
      - mobsf_data:/home/mobsf/.MobSF        # named volume
      - ./apks:/workspace/apks:ro            # bind mount (read-only input)
      - ./reports:/workspace/reports          # bind mount (writable output)

  postgres:
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  mobsf_data:           # Docker-managed named volume
    driver: local
  postgres_data:
    driver: local
  # External volume (pre-existing, not managed by Compose)
  external_scans:
    external: true
    name: previous_scan_results
```

---

### 103. Networks

Define **custom networks** for isolation between service groups.

```yaml
services:
  mobsf:
    networks:
      - frontend
      - backend       # mobsf bridges both networks

  nginx:
    networks:
      - frontend      # only on frontend

  postgres:
    networks:
      - backend       # only on backend — not reachable from nginx

networks:
  frontend:
    driver: bridge
    ipam:
      config:
        - subnet: 10.10.1.0/24

  backend:
    driver: bridge
    internal: true    # no external internet access from backend network
```

---

### 104. Environment Variables

Pass configuration into containers via `environment:` or external `env_file:`.

```yaml
services:
  mobsf:
    environment:
      - MOBSF_API_KEY=changeme123
      - SECRET_KEY=super-secret-django-key
      - POSTGRES_DB=mobsf

  postgres:
    environment:
      POSTGRES_USER: mobsf
      POSTGRES_PASSWORD: ${DB_PASSWORD}   # from shell or .env file
      POSTGRES_DB: mobsf

  apk-analyzer:
    env_file:
      - .env                # load all vars from .env file
      - secrets.env         # additional secret vars
```

**`.env` file** (auto-loaded by Compose for variable substitution):
```bash
DB_PASSWORD=strongpassword123
MOBSF_API_KEY=abc123xyz
JADX_VERSION=1.4.7
```

---

### 105. Secrets

Compose supports **secrets** for sensitive values — mounted as files under `/run/secrets/` inside containers (avoid plaintext in env vars).

```yaml
services:
  mobsf:
    secrets:
      - db_password
      - api_key

secrets:
  db_password:
    file: ./secrets/db_password.txt    # read from file

  api_key:
    environment: MOBSF_API_KEY         # read from env var
```

Inside the container:
```bash
cat /run/secrets/db_password   # → strongpassword123
```

> For production Swarm deployments, use `docker secret create` and reference with `external: true`.

---

### 106. Service Dependencies

`depends_on:` ensures services start in the correct **order** and (with conditions) only when dependencies are healthy.

```yaml
services:
  mobsf:
    depends_on:
      postgres:
        condition: service_healthy    # wait for healthy postgres
      redis:
        condition: service_started   # just wait for start

  postgres:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U mobsf"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
```

**Available conditions:**

| Condition | Meaning |
|---|---|
| `service_started` | Container started (default) |
| `service_healthy` | Healthcheck reports healthy |
| `service_completed_successfully` | One-shot init container exited 0 |

---

### 107. Health Checks

Define **liveness probes** at the service level:

```yaml
services:
  mobsf:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/health"]
      interval: 30s       # time between checks
      timeout: 10s        # max time for check to complete
      retries: 5          # failures before marking unhealthy
      start_period: 120s  # grace period during startup

  postgres:
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-mobsf}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
```

```bash
# Check health status
docker compose ps           # shows (healthy)/(unhealthy)
docker inspect mobsf | jq '.[0].State.Health'
```

---

### 108. Restart Policies

Controls what Docker does when a container **exits or crashes**.

```yaml
services:
  mobsf:
    restart: unless-stopped      # restart unless manually stopped

  postgres:
    restart: always              # always restart, even after Docker daemon restart

  apk-scanner:
    restart: on-failure          # restart only on non-zero exit
    # or with max retries:
    deploy:
      restart_policy:
        condition: on-failure
        max_attempts: 3
        delay: 5s
```

| Policy | Behavior |
|---|---|
| `no` | Never restart (default) |
| `always` | Always restart, survives daemon restart |
| `unless-stopped` | Like `always` but respects manual stop |
| `on-failure` | Restart only on non-zero exit code |

---

### 109. Profiles

**Profiles** let you define optional services that only start when explicitly activated. Useful for dev-only tools or one-off utilities.

```yaml
services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    # No profile → always starts

  jadx-ui:
    image: ghcr.io/skylot/jadx:latest
    profiles: ["tools"]        # only starts with --profile tools

  apk-leaks:
    image: dwisiswant0/apkleaks:latest
    profiles: ["tools", "recon"]

  frida-trace:
    image: frida-trace:latest
    profiles: ["dynamic"]
```

```bash
# Start core services only
docker compose up -d

# Start core + tools profile
docker compose --profile tools up -d

# Start everything
docker compose --profile tools --profile dynamic up -d
```

---

### 110. Compose Lifecycle Commands

```bash
# Start all services (build if needed, detached)
docker compose up -d

# Start with rebuild
docker compose up -d --build

# Stop and remove containers, networks (preserves volumes)
docker compose down

# Stop and remove including volumes
docker compose down -v

# Stop without removing
docker compose stop

# Start stopped services
docker compose start

# List service status
docker compose ps

# View logs (all services)
docker compose logs -f

# View logs for specific service
docker compose logs -f mobsf

# Execute command in running service
docker compose exec mobsf bash
docker compose exec postgres psql -U mobsf

# Run a one-off command in a new container
docker compose run --rm apk-analyzer --apk /in/target.apk

# Build images
docker compose build

# Pull latest images
docker compose pull

# Scale a service (multiple instances)
docker compose up -d --scale apk-scanner=3

# Restart a single service
docker compose restart mobsf

# Validate compose file
docker compose config
```

---

### 111. Multi-Container Applications

**Real-world example:** MobSF + PostgreSQL + Nginx reverse proxy — a common mobile pentest lab setup.

**Architecture:**
```
Internet / Host
     │
     ▼
  Nginx :443/:80 (reverse proxy)
     │
     ▼
  MobSF :8000 (mobile security framework)
     │
     ▼
  PostgreSQL :5432 (database backend)
```

All components on isolated networks — only what needs to talk, talks.

---

### 112. Compose Troubleshooting

```bash
# 1. Validate your compose.yaml for syntax errors
docker compose config

# 2. View all service statuses
docker compose ps -a           # -a shows stopped containers too

# 3. Check logs (the first stop for any issue)
docker compose logs mobsf
docker compose logs --tail=50 --follow mobsf

# 4. Inspect a service's container
docker inspect pentest-lab-mobsf-1

# 5. Get a shell inside a service
docker compose exec mobsf bash

# 6. Check environment variables inside a container
docker compose exec mobsf env | sort

# 7. Check port bindings
docker compose port mobsf 8000

# 8. Check health status
docker compose ps   # (healthy)/(unhealthy) column

# 9. Restart a failing service
docker compose restart mobsf

# 10. Rebuild and restart after Dockerfile changes
docker compose up -d --build mobsf

# Common errors and fixes:
# "port is already allocated" → something else is using that host port
#   Fix: change host port or stop conflicting process
# "service x depends_on y which is unhealthy" → y's healthcheck is failing
#   Fix: check y's logs, increase start_period, fix healthcheck command
# "bind: no such file or directory" → bind mount source path doesn't exist
#   Fix: create the host directory: mkdir -p ./reports
# "permission denied" → UID mismatch on mounted volume
#   Fix: add `user: "1000:1000"` to service, or chown on host
```

---

### Complete Real-World `compose.yaml` — Mobile Pentesting Lab

```yaml
# compose.yaml — Android Mobile Pentesting Lab
# Usage:
#   docker compose up -d                          (core lab)
#   docker compose --profile tools up -d          (+ extra tools)
#   docker compose --profile dynamic up -d        (+ dynamic analysis tools)
#   docker compose down -v                         (destroy everything)

name: android-pentest-lab

# ─── SERVICES ────────────────────────────────────────────────────────────────
services:

  # ── Nginx reverse proxy ──────────────────────────────────────────────────
  nginx:
    image: nginx:1.25-alpine
    container_name: pentest-nginx
    restart: unless-stopped
    ports:
      - "127.0.0.1:80:80"
      - "127.0.0.1:443:443"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
      - nginx_logs:/var/log/nginx
    networks:
      - frontend
    depends_on:
      mobsf:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "nginx", "-t"]
      interval: 30s
      timeout: 5s
      retries: 3

  # ── MobSF — Mobile Security Framework ────────────────────────────────────
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    container_name: pentest-mobsf
    restart: unless-stopped
    networks:
      - frontend
      - backend
    expose:
      - "8000"    # only exposed internally; Nginx proxies it externally
    environment:
      - MOBSF_PLATFORM=docker
      - SECRET_KEY=${MOBSF_SECRET_KEY:-change-me-in-production}
      - POSTGRES_USER=mobsf
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - POSTGRES_DB=mobsf
      - POSTGRES_HOST=postgres
    env_file:
      - .env
    volumes:
      - mobsf_data:/home/mobsf/.MobSF
      - apk_uploads:/home/mobsf/.MobSF/uploads
    secrets:
      - db_password
    depends_on:
      postgres:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/health"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 120s

  # ── PostgreSQL database ───────────────────────────────────────────────────
  postgres:
    image: postgres:16-alpine
    container_name: pentest-postgres
    restart: unless-stopped
    networks:
      - backend
    environment:
      POSTGRES_USER: mobsf
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
      POSTGRES_DB: mobsf
    secrets:
      - db_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./postgres/init:/docker-entrypoint-initdb.d:ro
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U mobsf -d mobsf"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  # ── Burp Suite proxy ─────────────────────────────────────────────────────
  burpsuite:
    image: ghcr.io/your-org/burpsuite-headless:latest
    container_name: pentest-burp
    restart: unless-stopped
    networks:
      - frontend
    ports:
      - "127.0.0.1:8080:8080"   # HTTP proxy
      - "127.0.0.1:8443:8443"   # HTTPS proxy
    volumes:
      - burp_data:/home/burp/.BurpSuite
      - ./burp/config:/config:ro
    environment:
      - BURP_PROJECT=/home/burp/pentest.burp
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s

  # ── ADB server — USB Android device bridge ────────────────────────────────
  adb-server:
    image: sorccu/adb:latest
    container_name: pentest-adb
    restart: unless-stopped
    privileged: true
    networks:
      - frontend
    ports:
      - "127.0.0.1:5037:5037"
    volumes:
      - /dev/bus/usb:/dev/bus/usb   # pass USB devices through to container

  # ── JADX — APK decompiler (optional, profile=tools) ──────────────────────
  jadx:
    image: ghcr.io/skylot/jadx:latest
    container_name: pentest-jadx
    profiles: ["tools"]
    networks:
      - frontend
    ports:
      - "127.0.0.1:8888:8888"   # JADX GUI server (if applicable)
    volumes:
      - apk_uploads:/workspace/apks:ro
      - jadx_output:/workspace/decompiled
    command: ["--threads-count", "4", "-d", "/workspace/decompiled", "/workspace/apks/target.apk"]

  # ── APKLeaks — secret/endpoint scanner (optional, profile=tools) ──────────
  apkleaks:
    image: dwisiswant0/apkleaks:latest
    container_name: pentest-apkleaks
    profiles: ["tools"]
    networks:
      - none     # air-gapped — no network needed for static scan
    volumes:
      - apk_uploads:/apks:ro
      - ./reports/apkleaks:/reports

  # ── Frida server bridge (optional, profile=dynamic) ───────────────────────
  frida-bridge:
    build:
      context: ./frida-bridge
      args:
        FRIDA_VERSION: ${FRIDA_VERSION:-16.0.19}
    container_name: pentest-frida
    profiles: ["dynamic"]
    restart: on-failure
    networks:
      - frontend
    ports:
      - "127.0.0.1:27042:27042"   # Frida server port
    environment:
      - ADB_HOST=adb-server
      - ADB_PORT=5037
    depends_on:
      - adb-server

# ─── VOLUMES ─────────────────────────────────────────────────────────────────
volumes:
  mobsf_data:
    driver: local
    labels:
      project: android-pentest-lab
      component: mobsf

  postgres_data:
    driver: local
    labels:
      project: android-pentest-lab
      component: database

  apk_uploads:
    driver: local

  jadx_output:
    driver: local

  burp_data:
    driver: local

  nginx_logs:
    driver: local

# ─── NETWORKS ─────────────────────────────────────────────────────────────────
networks:
  frontend:
    driver: bridge
    ipam:
      config:
        - subnet: 10.20.1.0/24
    labels:
      project: android-pentest-lab

  backend:
    driver: bridge
    internal: true      # no internet access from backend network
    ipam:
      config:
        - subnet: 10.20.2.0/24
    labels:
      project: android-pentest-lab

# ─── SECRETS ─────────────────────────────────────────────────────────────────
secrets:
  db_password:
    file: ./secrets/db_password.txt   # create: echo "strongpass" > ./secrets/db_password.txt
```

**Quick start:**
```bash
# 1. Create secrets directory and password file
mkdir -p secrets
echo "$(openssl rand -base64 32)" > secrets/db_password.txt
chmod 600 secrets/db_password.txt

# 2. Create .env file
cat > .env <<EOF
MOBSF_SECRET_KEY=$(openssl rand -base64 50)
DB_PASSWORD=$(cat secrets/db_password.txt)
FRIDA_VERSION=16.0.19
EOF

# 3. Create report directories (avoid bind mount errors)
mkdir -p reports/apkleaks nginx/conf.d nginx/ssl postgres/init apks

# 4. Start the core lab
docker compose up -d

# 5. Start with optional tools
docker compose --profile tools up -d

# 6. Check everything is healthy
docker compose ps

# 7. Upload an APK to MobSF
curl -F "file=@target.apk" \
     -H "Authorization: dc9af1b63bff0063ade7f09f82bcc449bb64ef20d2d2e95cef02c57df4a41781" \
     http://localhost:80/api/v1/upload

# 8. Tear everything down (keep volumes)
docker compose down

# 9. Tear down including all data
docker compose down -v
```

**Lab network diagram:**
```
Host (macOS/Linux)
  │
  ├─ :80 / :443  ──→  nginx (frontend)
  ├─ :8080        ──→  burpsuite (frontend)
  ├─ :5037        ──→  adb-server (frontend)
  └─ :27042       ──→  frida-bridge (frontend) [--profile dynamic]

frontend network (10.20.1.0/24)
  ├── nginx       → 10.20.1.2
  ├── mobsf       → 10.20.1.3  (also on backend)
  ├── burpsuite   → 10.20.1.4
  ├── adb-server  → 10.20.1.5
  └── frida-bridge→ 10.20.1.6

backend network (10.20.2.0/24) — internal, no internet
  ├── mobsf       → 10.20.2.2
  └── postgres    → 10.20.2.3

Android Device (USB) ──USB──→ adb-server → frida-bridge
```

---

## 9. Docker Security Fundamentals

### 113. Container Isolation
Container isolation refers to the boundary separating containers from each other and the host OS using kernel-level features (namespaces, cgroups, LSMs), sharing the host kernel rather than virtualizing hardware.

**Real-World Example:** Isolating an untrusted, reverse-engineered malicious Android APK analysis tool inside a container prevents malicious scripts from inspecting or mutating your host operating system files.

```bash
# Verify container isolation: process tree inside container sees only its own PID namespace
docker run --rm alpine ps aux
# Output only displays PID 1 and ps aux command
```

---

### 114. Linux Namespaces
Linux namespaces partition kernel resources such that a group of processes sees a dedicated set of system resources (PID, NET, MNT, UTS, IPC, USER, CGROUP).

| Namespace | Isolates | Pentest Relevance |
|---|---|---|
| **PID** | Process IDs | Keeps Frida / gdb target processes hidden from host processes |
| **NET** | Network interfaces & routing | Routes APK traffic through custom mock gateways or VPNs |
| **MNT** | Filesystem mount points | Restricts decompiled APK malware from reaching host paths |
| **UTS** | Hostname & domain name | Allows assigning custom hostnames for internal testing |
| **IPC** | Inter-process communication | Prevents shared memory eavesdropping between analysis tools |
| **USER** | User and Group IDs | Maps root inside container to unprivileged user on host |

**Real-World Example:** Inspecting which namespaces are unshared for a MobSF container:

```bash
docker inspect --format '{{.State.Pid}}' mobsf
# Inspect namespaces using lsns on Linux host
sudo lsns -p <PID>
```

---

### 115. Control Groups (cgroups)
Control groups (cgroups) enforce resource allocation quotas and accounting for CPU, memory, disk I/O, and process limits across container instances.

**Real-World Example:** Preventing a "decompilation bomb" (a zip-bomb inside an obfuscated APK) from exhausting host RAM when processed by JADX or apktool:

```bash
docker run -d --name apk_analyzer   --memory="2g"   --memory-swap="2g"   --cpus="1.5"   --pids-limit=100   my-pentest-tool:latest
```

---

### 116. Linux Capabilities
Linux capabilities divide the monolithic root privileges into 40+ distinct privileges, enabling fine-grained control over what root-owned container processes can perform.

**Real-World Example:** An Android MITM proxy container needs `CAP_NET_ADMIN` to configure iptables redirection, but should NOT have `CAP_SYS_ADMIN` or `CAP_SYS_RAWIO`:

```bash
# Run proxy tool with only necessary network capabilities
docker run -d --name mitm_bridge   --cap-drop ALL   --cap-add NET_ADMIN   --cap-add NET_RAW   mitmproxy/mitmproxy
```

---

### 117. Rootless Docker
Rootless Docker allows running the Docker daemon (`dockerd`) and containers as an unprivileged user to mitigate container breakout vulnerabilities and host compromise.

**Real-World Example:** Running MobSF in rootless mode on a shared pentest workstation ensures that even if an exploit targets a parser bug in MobSF, the attacker obtains zero host-level root privileges.

```bash
# Check if rootless mode is active
docker info --format '{{.SecurityOptions}}'
# Displays: [name=rootless]
```

---

### 118. Non-Root Containers
Non-root containers run their entrypoint application processes under a dedicated unprivileged UID (e.g., UID 10001) rather than UID 0 (root).

**Real-World Example:** Creating an unprivileged user inside a custom APK scanner Dockerfile:

```dockerfile
FROM debian:bookworm-slim
RUN useradd -u 10001 -m pentester
USER pentester
WORKDIR /home/pentester
ENTRYPOINT ["jadx"]
```

---

### 119. Privileged Containers
A privileged container (`--privileged`) disables virtually all security isolation mechanisms, granting the container all Linux capabilities and direct access to host `/dev` devices.

**Real-World Example:** Connecting physical USB Android hardware to an ADB server running inside Docker on Linux requires access to USB device buses:

```bash
# HIGH RISK: Avoid unless strictly required for USB pass-through
docker run -d --name adb_server --privileged -v /dev/bus/usb:/dev/bus/usb sorccu/adb:latest

# SAFER ALTERNATIVE: Pass only the specific device node
docker run -d --name adb_server --device=/dev/bus/usb/001/002 sorccu/adb:latest
```

---

### 120. Seccomp Profiles
Secure Computing Mode (seccomp) restricts the system calls (syscalls) a container's processes are permitted to execute against the host Linux kernel.

**Real-World Example:** Docker's default seccomp profile blocks dangerous syscalls (e.g., `reboot`, `sys_ptrace` by default, `kexec_load`). For debugging Android binaries with GDB inside containers, selectively pass a tailored profile:

```bash
# Running analysis tool with custom seccomp profile enabling ptrace
docker run -it --security-opt seccomp=/path/to/custom-seccomp.json android-native-re:latest
```

---

### 121. AppArmor
AppArmor is a Linux Security Module (LSM) that enforces name-based mandatory access control policies on container file paths, network protocols, and capabilities.

**Real-World Example:** Loading an AppArmor profile to restrict a static APK scanner from reading host configuration files even if mounted:

```bash
# Apply a hardened AppArmor profile to the container
docker run -d --security-opt apparmor=docker-pentest-enforce mobsf-scanner:latest
```

---

### 122. SELinux
Security-Enhanced Linux (SELinux) is a kernel MAC mechanism that enforces label-based Type Enforcement on containers, host processes, and files.

**Real-World Example:** On Fedora/RHEL/CentOS systems, mounting host APK sample directories into MobSF requires the `:z` (shared) or `:Z` (private) volume flag so SELinux relabels the directory context (`container_file_t`):

```bash
docker run -d -p 8000:8000 -v /opt/apks:/home/mobsf/apks:z opensecurity/mobile-security-framework-mobsf:latest
```

---

### 123. Read-Only Root Filesystem
A read-only root filesystem (`--read-only`) mounts the container's root partition as immutable, preventing malware from persisting binaries or modifying system libraries.

**Real-World Example:** Analyzing a potentially malicious Android banking Trojan using a sandbox container with an immutable root filesystem and a RAM-backed `/tmp`:

```bash
docker run -it --rm   --read-only   --tmpfs /tmp:rw,noexec,nosuid,size=256m   apk-malware-analyzer:latest /tmp/trojan.apk
```

---

### 124. Resource Limits
Resource limits constrain the maximum amount of compute resources (CPU, RAM, block I/O, PID count) a container can consume to prevent Denial of Service on the host.

**Real-World Example:** Setting resource constraints on MobSF so that running intensive decompilation on multiple multi-hundred megabyte APKs does not freeze your macOS/Linux workstation.

```bash
docker run -d --name mobsf   --cpus="2.0"   --memory="4g"   --memory-reservation="2g"   --pids-limit=200   -p 8000:8000   opensecurity/mobile-security-framework-mobsf:latest
```

---

### 125. CPU and Memory Limits
Detailed configuration of specific CPU and RAM limits prevents rogue decompilers or infinite loops during static bytecode parsing from starving the host OS.

| Parameter | Function | Recommended for Pentest Lab |
|---|---|---|
| `--memory=4g` | Maximum RAM ceiling | Prevent OOM killing host IDEs |
| `--memory-swap=4g` | Total RAM + swap (equal = no swap) | Eliminates disk thrashing |
| `--cpus=2.0` | Maximum number of CPU cores | Leaves remaining cores for Burp/Emulator |
| `--cpu-shares=512` | Relative CPU priority weighting (default 1024) | Gives background scanners lower priority |

**Real-World Example:**
```bash
docker run -d --name jadx_worker   --cpus="1.5"   --memory="3g"   --memory-swap="3g"   --oom-kill-disable=false   jadx-cli:latest
```

---

### 126. Dropping Linux Capabilities
Dropping capabilities strips out all default privileges, selectively granting back only the atomic rights required for the specific pentest tool.

**Real-World Example:** Running an API testing stub (like an Android mock backend) with all capabilities dropped:

```bash
docker run -d --name mock_api   --cap-drop ALL   -p 8080:8080   mock-backend:v1
```

---

### 127. Docker Socket Security
The Docker UNIX socket (`/var/run/docker.sock`) is the direct IPC interface to the Docker daemon; granting a container access to this socket effectively yields host root control.

**Real-World Example:** Never mount `/var/run/docker.sock` into an untrusted APK scanning tool. An attacker with arbitrary code execution inside the container can spin up a sibling container with host root filesystem mounted:

```bash
# DANGEROUS ESCAPE VECTOR (Never allow in pentesting labs):
docker run -v /var/run/docker.sock:/var/run/docker.sock untrusted-scanner:latest

# Inside the compromised container, attacker escapes to host:
# docker run -v /:/host -it alpine chroot /host
```

---

### 128. Secrets Management
Secrets management protects sensitive data (API tokens, private signing keys, keystore passwords) from being baked into images or leaked via inspection.

**Real-World Example:** Passing an Android release keystore password to a CI/CD container using Docker Swarm/Compose secrets instead of plain environment variables:

```yaml
# compose.yaml
services:
  apk_signer:
    image: android-build-tools:latest
    secrets:
      - release_keystore_password

secrets:
  release_keystore_password:
    file: ./secrets/keystore_pass.txt
```

---

### 129. Environment Variable Security
Environment variables passed via `-e` or `ENV` instructions remain permanently visible in `docker inspect`, `/proc/<PID>/environ`, and image history.

**Real-World Example:** Avoid `docker run -e MOB_KEY="secret123"`. Use volume mounts, `.env` files with proper filesystem permissions, or Docker secrets:

```bash
# Inspecting container reveals exposed environment variables:
docker inspect --format='{{range .Config.Env}}{{println .}}{{end}}' mobsf
```

---

### 130. Container Image Vulnerabilities
Base images and package manager dependencies frequently inherit known Common Vulnerabilities and Exposures (CVEs) that require automated scanning.

**Real-World Example:** Scanning your custom Android pentest image for vulnerabilities using Trivy before deploying it into a client engagement environment:

```bash
# Scan image with Trivy for HIGH and CRITICAL vulnerabilities
trivy image --severity HIGH,CRITICAL android-pentest-suite:1.0
```

---

### 131. Image Provenance and Signatures
Image signing (Docker Content Trust / Cosign) guarantees cryptographic authenticity and integrity, ensuring images originate from trusted security teams.

**Real-World Example:** Verifying cryptographic signatures on images using Cosign:

```bash
# Enable Docker Content Trust for official pulls
export DOCKER_CONTENT_TRUST=1
docker pull opensecurity/mobile-security-framework-mobsf:latest

# Or verify with Cosign
cosign verify --key cosign.pub myregistry.internal/security/mobsf:latest
```

---

### 132. Software Bill of Materials (SBOM)
An SBOM is a structured machine-readable inventory of software components, libraries, and dependencies packaged inside a container image.

**Real-World Example:** Generating an SBOM for an Android build-farm container image using Docker CLI to ensure no malicious build-time dependencies exist:

```bash
docker sbom --format cyclonedx-json --output android_lab_sbom.json android-pentest:latest
```

---

### 133. Supply-Chain Security
Supply-chain security safeguards the entire lifecycle from upstream base images, build dependencies, and GitHub Actions through to registry storage.

**Real-World Example:** Pinning base images and dependencies by immutable SHA256 cryptographic digests rather than mutable tags (`:latest`):

```dockerfile
# INSECURE:
# FROM python:3.11-slim

# SECURE: Pinning immutable cryptographic digest
FROM python:3.11-slim@sha256:416b23b1458e0a2f8b5bb5a3da7d727b322a3621dfd4bebc7c6883e20e85a539
```

---

### 134. Container Escape Vulnerabilities
A container escape occurs when a bug in the container runtime (e.g., `runc`, `containerd`, or kernel) permits code execution outside the container boundary on the host kernel.

**Historical Examples:**
- **CVE-2019-5736 (runc overwrite):** Malicious container overwrote the host `runc` binary, executing arbitrary commands with host root privileges upon next execution.
- **CVE-2022-0492 (cgroups release_agent escape):** Escaped unconfined containers with `CAP_SYS_ADMIN` by manipulating `release_agent` cgroup scripts.

**Mitigation:** Run containers as non-root, use rootless Docker, drop capabilities, and keep container engines updated.

---

### 135. Container Hardening
Container hardening is the comprehensive application of defense-in-depth security configurations to minimize attack surfaces.

**Android Pentesting Container Hardening Checklist:**
- [x] Run as non-root user (`USER pentester`)
- [x] Read-only root filesystem (`--read-only`)
- [x] Drop all capabilities, add back only required (`--cap-drop ALL`)
- [x] Set explicit memory and CPU quotas (`--memory="2g" --cpus="1"`)
- [x] Enforce Seccomp and AppArmor profiles
- [x] Disallow privilege escalation (`--security-opt no-new-privileges:true`)
- [x] Avoid mounting `/var/run/docker.sock`

```bash
docker run -d --name hardened_scanner   --security-opt no-new-privileges:true   --cap-drop ALL   --read-only   --tmpfs /tmp:rw,noexec,nosuid,size=128m   --memory="2g"   --cpus="1.0"   custom-apk-scanner:v1
```

---

### 136. Docker Daemon Security
Docker daemon security involves securing `dockerd` configuration via `/etc/docker/daemon.json`, enforcing mTLS on network sockets, and enabling user namespaces.

**Real-World Example:** Hardened `/etc/docker/daemon.json` configuration for a mobile security analysis host:

```json
{
  "icc": false,
  "no-new-privileges": true,
  "userland-proxy": false,
  "userns-remap": "default",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "50m",
    "max-file": "3"
  }
}
```

---

## 10. Docker for Android Penetration Testing

### 137. Mobile Application Security Testing Environments
Dockerized mobile application security environments provide isolated, reproducible, on-demand platforms containing all SAST, DAST, decompilation, and proxy tooling.

**Real-World Example:** Instead of maintaining conflicting Python 2/3 virtual environments, conflicting Java 8/11/17 runtimes, and differing Android SDK tool versions on your primary host, spin up pre-configured containers for each specific assessment.

---

### 138. Mobile Security Framework (MobSF)
MobSF is an automated, all-in-one mobile application (Android/iOS) security assessment framework capable of performing static, dynamic, and web API security testing.

**Capabilities for Android Assessment:**
- AndroidManifest.xml vulnerability checks (exported components, debuggable flag, backup allowed)
- Hardcoded secrets and API keys scanner
- Code analysis (insecure crypto, weak PRNG, SQL injection, WebView vulnerabilities)
- OWASP Mobile Top 10 mapping and CVSS scoring

---

### 139. Running MobSF Using Docker
MobSF is deployed directly via official container images with port forwarding and persistent volumes.

```bash
docker run -it --rm   --name mobsf   -p 8000:8000   -v mobsf_data:/root/.MobSF   -e MOBSF_API_KEY="pentest_secret_key_2026"   opensecurity/mobile-security-framework-mobsf:latest
```

*Flags breakdown:*
- `-p 8000:8000`: Forwards host port 8000 to MobSF web interface and REST API.
- `-v mobsf_data:/root/.MobSF`: Persists scan history, uploaded APKs, and generated reports across container restarts.
- `-e MOBSF_API_KEY`: Configures a deterministic REST API key for programmatic scans.

---

### 140. APK and AAB Analysis Workflows
Uploading APK and Android App Bundle (AAB) binaries into containerized MobSF for automated triage.

**Workflow via REST API:**
```bash
# 1. Upload APK
UPLOAD_RESP=$(curl -s -F "file=@target_app.apk"   -H "Authorization: pentest_secret_key_2026"   http://localhost:8000/api/v1/upload)
HASH=$(echo $UPLOAD_RESP | jq -r '.hash')

# 2. Trigger Scan
curl -s -X POST --url http://localhost:8000/api/v1/scan   -H "Authorization: pentest_secret_key_2026"   --data "hash=$HASH"

# 3. Retrieve JSON report
curl -s -X POST --url http://localhost:8000/api/v1/report_json   -H "Authorization: pentest_secret_key_2026"   --data "hash=$HASH" -o report.json
```

---

### 141. Static Application Security Testing (SAST)
SAST analyzes the application source code or decompiled bytecode without execution to uncover security flaws and policy violations.

**Real-World Example:** Containerized SAST execution targeting an APK decompiled with `apktool`:

```bash
docker run --rm   -v $(pwd)/target_extracted:/src:ro   ghcr.io/returntocorp/semgrep:latest   semgrep --config "p/android" /src
```

---

### 142. Dynamic Application Security Testing (DAST)
DAST assesses the runtime behavior of the Android app, monitoring real-time network traffic, crypto operations, and inter-process communication while running on an active device or emulator.

**Real-World Example:** Configuring MobSF running in Docker to connect to an Android emulator hosted on macOS/Windows host:

```bash
# Linux/macOS Docker connecting to host emulator at port 5555
docker run -d --name mobsf   -p 8000:8000   -e ANALYZER_IDENTIFIER="host.docker.internal:5555"   opensecurity/mobile-security-framework-mobsf:latest
```

---

### 143. APK File Management Using Bind Mounts
Bind mounts map host directories containing collected APK samples into container analysis tools without duplicating large binary files.

```bash
docker run -it --rm   -v ~/Engagements/ClientA/APKs:/samples:ro   -v ~/Engagements/ClientA/Reports:/output:rw   custom-apk-scanner:latest /samples/vulnerable.apk -o /output
```

---

### 144. Exporting Security Scan Reports
Exporting structured vulnerability reports (PDF, JSON, SARIF) from containerized engines into client reporting formats.

```bash
# Export MobSF scan report as PDF
curl -s -X POST http://localhost:8000/api/v1/download_pdf   -H "Authorization: pentest_secret_key_2026"   --data "hash=$HASH" -o ClientA_Security_Report.pdf
```

---

### 145. JADX and APK Decompilation Workflows
Running JADX inside a container converts DEX bytecode back into readable Java source files without local Java SDK setup.

```bash
# Decompile APK directly to host directory using Docker
docker run --rm   -v $(pwd)/sample.apk:/app/sample.apk:ro   -v $(pwd)/decompiled:/out:rw   skylot/jadx:latest   -d /out /app/sample.apk
```

---

### 146. Android SDK and Platform Tools in Containers
A containerized Android SDK environment provides `adb`, `fastboot`, `apkanalyzer`, and `zipalign` in a consistent CLI toolchain.

**Dockerfile snippet:**
```dockerfile
FROM eclipse-temurin:17-jdk-jammy
RUN apt-get update && apt-get install -y wget unzip     && wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O cmdline.zip     && mkdir -p /opt/android-sdk/cmdline-tools     && unzip cmdline.zip -d /opt/android-sdk/cmdline-tools     && mv /opt/android-sdk/cmdline-tools/cmdline-tools /opt/android-sdk/cmdline-tools/latest
ENV ANDROID_HOME=/opt/android-sdk
ENV PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools
RUN yes | sdkmanager --licenses && sdkmanager "platform-tools" "build-tools;34.0.0"
```

---

### 147. ADB Connectivity with Containers
Connecting a Dockerized toolchain to the Android Debug Bridge (ADB) daemon running on the host machine or over Wi-Fi/TCP.

```bash
# Host starts ADB in network listening mode
adb kill-server
adb -a -P 5037 nodaemon server &

# Container connects to host ADB server
docker run -it --rm   --add-host=host.docker.internal:host-gateway   android-tools:latest   adb -H host.docker.internal -P 5037 devices
```

---

### 148. Android Emulator Connectivity
Linking dynamic analysis containers directly to Android Virtual Devices (AVD) running on host or Dockerized emulators (e.g., Redroid/Budtmo).

```bash
# Connect container to an emulator over TCP
docker exec -it pentest_tools adb connect host.docker.internal:5555
docker exec -it pentest_tools adb shell getprop ro.build.version.release
```

---

### 149. Frida and Frida Server Workflows
Frida is a dynamic instrumentation toolkit for injecting custom JavaScript hooks into native apps to bypass SSL pinning, root detection, and cryptography logic.

**Real-World Example:** Containerizing Frida client tools:

```bash
# Run Frida CLI in Docker targeting remote device/emulator
docker run -it --rm   --network host   frida-client:latest   frida -H 192.168.1.105:27042 -f com.vulnerable.bank --no-pause -l /scripts/bypass_pinning.js
```

---

### 150. Objection and Frida-Based Testing
Objection is a runtime mobile exploration toolkit powered by Frida that automates assessments without needing source code modification.

```bash
# Running Objection inside Docker pointing to host device
docker run -it --rm   --network host   objection-tool:latest   objection -N -h 127.0.0.1 -p 27042 -g "com.target.app" explore
```

---

### 151. Burp Suite Integration
Configuring Dockerized mobile testing pipelines to route API traffic through Burp Suite Professional/Community for manual inspection.

**Configuration:**
1. Configure containerized application or mock to set HTTP proxy:
```bash
docker run -d --name api_client   -e HTTP_PROXY="http://host.docker.internal:8080"   -e HTTPS_PROXY="http://host.docker.internal:8080"   target-app-backend:latest
```
2. In Burp Suite on Host: Proxy -> Options -> Proxy Listeners -> Bind to all interfaces `0.0.0.0:8080`.

---

### 152. OWASP ZAP Integration
OWASP Zed Attack Proxy (ZAP) in Docker enables automated active and passive vulnerability scanning of mobile app backend APIs.

```bash
# Run automated API scan against an Android app's REST backend
docker run -t --rm   -v $(pwd)/zap_reports:/zap/wrk/:rw   zaproxy/zap-stable   zap-api-scan.py -t http://host.docker.internal:5000/v1/swagger.json -f openapi -r zap_report.html
```

---

### 153. API Security Testing Environments
Spinning up containerized API security testing frameworks (e.g., Postman Newman, RESTler, or APIFuzzer) to test mobile backend endpoints.

```bash
# Run automated fuzzing against Android endpoints
docker run -it --rm   -v $(pwd)/endpoints.json:/spec.json:ro   apifuzzer:latest -s /spec.json -r http://backend-target.internal
```

---

### 154. Database and Backend Test Environments
Creating isolated local mock instances of databases (PostgreSQL, MongoDB, Redis) to replicate mobile application backend architectures for offline testing.

```bash
# Spin up test database mirror for mobile backend inspection
docker run -d --name test_pg   -e POSTGRES_DB=mobile_bank   -e POSTGRES_PASSWORD=insecure_test_password   -p 5432:5432   postgres:16-alpine
```

---

### 155. TLS and Certificate Testing Environments
Utilizing `mitmproxy` inside Docker to inspect certificate validation, TLS cipher negotiation, and SSL pinning resilience.

```bash
docker run -it --rm   --name mitmproxy   -p 8080:8080   -p 8081:8081   -v ~/.mitmproxy:/home/mitmproxy/.mitmproxy   mitmproxy/mitmproxy mitmweb --web-host 0.0.0.0
```

---

### 156. Test Account and Credential Management
Managing credentials and authentication tokens for multiple test personas (admin, user, auditor) via secure volume-mounted config files.

```bash
docker run --rm   --env-file ./credentials/staging_testers.env   mobile-auth-fuzzer:latest
```

---

### 157. Reproducible Security Testing Labs
Docker Compose packages the entire mobile testing infrastructure (MobSF, Postgres, Mitmproxy, ADB bridge) into a single declarative blueprint.

```yaml
# lab-compose.yaml
version: '3.8'
services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    ports:
      - "8000:8000"
    volumes:
      - mobsf_data:/root/.MobSF
  proxy:
    image: mitmproxy/mitmproxy
    ports:
      - "8080:8080"
volumes:
  mobsf_data:
```

---

### 158. CI/CD Security Testing for Android Apps
Integrating Dockerized APK security scans directly into CI/CD pipelines (e.g., GitHub Actions, GitLab CI) to catch security regressions before release.

```yaml
# .github/workflows/security.yml
jobs:
  mobsf-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Start MobSF Container
        run: |
          docker run -d -p 8000:8000 --name mobsf opensecurity/mobile-security-framework-mobsf:latest
          sleep 15
      - name: Scan APK
        run: |
          python3 ./scripts/scan_apk.py app/build/outputs/apk/release/app-release.apk
```

---

### 159. Automated APK Scanning Pipelines
Building automated continuous scanning pipelines that monitor directories for newly compiled APKs, run MobSF scans, and parse findings into Jira/Slack.

```bash
# Minimal pipeline trigger loop
for apk in ./inbox/*.apk; do
  echo "Processing $apk..."
  python3 mobsf_cli_scanner.py --file "$apk" --output "./reports/$(basename $apk).json"
  mv "$apk" ./archive/
done
```

---

### 160. Safe Malware-Analysis Lab Isolation
Safely detonating and analyzing malicious Android APKs by stripping all external network connectivity and enforcing container immutability.

```bash
# Air-gapped, immutable sandbox for suspicious APK extraction
docker run -it --rm   --network none   --read-only   --memory="1g"   --cpus="1.0"   --cap-drop ALL   -v $(pwd)/malware.apk:/tmp/malware.apk:ro   -v $(pwd)/artifacts:/output:rw   apk-malware-sandbox:latest
```

---

## 11. Advanced Docker Concepts

### 161. Docker Compose Advanced Configuration
Advanced Compose includes YAML anchors (`&`, `*`), extension fields (`x-custom`), service merging (`extends`), and environment file variable substitution.

**Real-World Example:** Defining shared security and logging templates across multiple pentest services without duplication:

```yaml
x-security-opts: &sec-opts
  security_opt:
    - no-new-privileges:true
  cap_drop:
    - ALL

services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    <<: *sec-opts
    ports:
      - "8000:8000"

  jadx_service:
    image: custom-jadx:latest
    <<: *sec-opts
```

---

### 162. Multi-Stage Security Builds
Multi-stage builds separate build dependencies (compilers, SDKs, private repos) from the lean final runtime image, drastically reducing attack surface and image size.

**Real-World Example:** Compiling a native C/C++ Android exploit or custom Frida gadget injector in stage 1, and copying only the compiled binary into an Alpine runtime:

```dockerfile
# Stage 1: Build environment
FROM gcc:12 AS builder
WORKDIR /src
COPY injector.c .
RUN gcc -static -o injector injector.c

# Stage 2: Minimal runtime
FROM alpine:3.19
COPY --from=builder /src/injector /usr/local/bin/injector
USER 1000:1000
ENTRYPOINT ["/usr/local/bin/injector"]
```

---

### 163. Build Secrets
BuildKit secrets (`--mount=type=secret`) pass sensitive data (SSH keys, tokens, license files) into the build process without persisting them in any intermediate or final image layers.

**Real-World Example:** Pulling private Android SDK licenses or client-specific commercial security tools (e.g., JEB decompiler) during build:

```dockerfile
# syntax=docker/dockerfile:1.4
FROM openjdk:17-slim
RUN --mount=type=secret,id=jeb_license \
    cat /run/secrets/jeb_license > /opt/jeb/license.key && \
    ./setup_jeb.sh && \
    rm /opt/jeb/license.key
```

```bash
docker build --secret id=jeb_license,src=./secrets/jeb.key -t pentest-decompiler .
```

---

### 164. Build Arguments and Environment Separation
Build arguments (`ARG`) customize images at build-time, whereas environment variables (`ENV`) persist into running containers.

**Real-World Example:** Selecting Android API levels and Frida versions dynamically at build time:

```dockerfile
ARG FRIDA_VERSION=16.2.1
ARG ANDROID_PLATFORM=android-34
RUN pip install frida-tools==${FRIDA_VERSION}
```

```bash
docker build --build-arg FRIDA_VERSION=16.3.0 -t frida-tester:16.3.0 .
```

---

### 165. Container Orchestration Fundamentals
Container orchestration automates the provisioning, networking, scaling, health tracking, and lifecycle management of multi-container environments.

**Real-World Example:** Orchestrating a fleet of headless Android emulators and parallelized MobSF scanning nodes to fuzz and audit 100+ internal APKs nightly.

---

### 166. Docker Swarm
Docker Swarm is Docker's native clustering and orchestration engine that groups multiple Docker hosts into a single virtual manager-worker cluster.

**Real-World Example:** Deploying MobSF across three worker nodes using Swarm stacks:

```bash
docker swarm init
docker stack deploy -c mobsf-stack.yaml pentest_fleet
docker service ls
```

---

### 167. Kubernetes Fundamentals
Kubernetes (K8s) is the industry-standard container orchestration platform using Declarative Pods, Deployments, and Services for planetary-scale container workloads.

| Concept | Docker Equivalent | Android Pentesting Analogy |
|---|---|---|
| **Pod** | One or co-located containers sharing localhost/volumes | MobSF worker + ADB sidecar proxy |
| **Deployment** | Scaled replicas of identical containers | 10 parallel APK static scanning workers |
| **Service** | Stable IP/DNS load balancer | Central API endpoint for report collection |
| **ConfigMap/Secret** | Environment variables & Docker secrets | Keystore passwords & target API keys |

---

### 168. Container Runtime Concepts
Container runtimes are software components that execute containers and manage container images, divided into high-level and low-level runtimes.

- **High-Level Runtimes (CRI):** containerd, CRI-O (handle image downloads, unpacking, API).
- **Low-Level Runtimes (OCI):** runc, crun, kata-containers (interface with Linux kernel cgroups/namespaces to spawn the processes).

---

### 169. Containerd
`containerd` is an industry-standard, CNCF-graduated core container runtime managing the complete container lifecycle of its host system from image transfer to execution.

**Real-World Example:** Docker daemon delegates low-level execution to `containerd`. You can inspect running containers via `ctr` or `crictl`:

```bash
ctr --namespace moby container ls
```

---

### 170. OCI (Open Container Initiative)
The OCI is an open governance structure that establishes vendor-neutral industry standards for container image formats (`image-spec`), container runtimes (`runtime-spec`), and registries (`distribution-spec`).

**Real-World Example:** Any OCI-compliant tool (Skopeo, Podman, Docker, Buildah) can inspect, build, and run your Android penetration-testing container images interchangeably.

---

### 171. Registries and Artifact Distribution
Registries store and distribute OCI container images and artifacts over HTTP APIs using content-addressable storage.

**Common Registries:**
- Docker Hub (`docker.io`)
- GitHub Packages (`ghcr.io`)
- Amazon ECR, Google Artifact Registry
- Self-hosted Harbor (enterprise registry with built-in Trivy vulnerability scanning)

---

### 172. Image Mirroring
Image mirroring involves replicating remote public images to a local or private registry to ensure offline availability, avoid Docker Hub rate limits, and prevent upstream tampering.

**Real-World Example:** Setting up an air-gapped pentest lab registry mirror:

```bash
# Pull, tag, and push MobSF to internal lab registry
docker pull opensecurity/mobile-security-framework-mobsf:latest
docker tag opensecurity/mobile-security-framework-mobsf:latest registry.internal:5000/sec/mobsf:latest
docker push registry.internal:5000/sec/mobsf:latest
```

---

### 173. Resource Quotas and Limits
Resource quotas restrict maximum compute utilization to prevent system crashes and ensure predictable performance across multiple testing tools.

```bash
# Strict limit: 2 CPU cores, 4GB RAM, 100 PIDs
docker run -d --name jadx_decompiler   --cpus="2.0"   --memory="4g"   --pids-limit=100   decompiler-tool:latest
```

---

### 174. Logging Drivers
Docker logging drivers control how container stdout/stderr output is captured, formatted, and forwarded to centralized log storage.

| Driver | Description | Use Case |
|---|---|---|
| `json-file` | Default; writes JSON logs to host disk | Local pentesting workstation |
| `syslog` | Sends logs to local syslog daemon | Centralized SIEM forwarding |
| `fluentd` / `loki` | Ships structured logs to Grafana/ELK | Enterprise mobile DevSecOps tracking |

```bash
docker run -d --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 mobsf
```

---

### 175. Docker Events
`docker events` streams real-time system events (container start, die, kill, OOM, mount) generated by the Docker daemon.

**Real-World Example:** Monitoring for container OOM (Out-Of-Memory) kills while batch-decompiling APKs:

```bash
docker events --filter 'event=oom' --filter 'event=die'
```

---

### 176. Container Monitoring
Container monitoring tracks real-time resource consumption (CPU, Memory, Network I/O, Disk I/O) to identify performance bottlenecks.

```bash
# Built-in live terminal monitor
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"
```

---

### 177. Docker Health Checks and Recovery
Pairing health checks with restart policies guarantees automated self-healing if a scanner hangs or exhausts memory.

```yaml
services:
  mobsf:
    image: opensecurity/mobile-security-framework-mobsf:latest
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

---

### 178. Backup and Disaster Recovery
Backing up container volumes and exporting container images ensures your entire mobile pentesting laboratory can be restored rapidly.

```bash
# 1. Backup persistent MobSF database and upload directory
docker run --rm -v mobsf_data:/data -v $(pwd):/backup alpine \
  tar -czvf /backup/mobsf_data_backup.tar.gz -C /data .

# 2. Save entire customized pentest image to tar
docker save -o android_pentest_suite.tar android-pentest:latest

# 3. Restore on new workstation
docker load -i android_pentest_suite.tar
```

---

## 12. Troubleshooting and Practical Operations

### 179. Container Startup Failures
Startup failures occur when the entrypoint fails, dependencies are missing, or exit code indicates an immediate fatal error.

**Troubleshooting Steps:**
```bash
# 1. Inspect recent exit code and status
docker inspect --format='{{.State.Status}} ExitCode={{.State.ExitCode}} Error={{.State.Error}}' <container>

# 2. View fatal startup crash logs
docker logs --tail 50 <container>

# 3. Override entrypoint to debug shell
docker run -it --entrypoint /bin/sh <image_name>
```

---

### 180. Image Pull Failures
Pull failures stem from network timeouts, missing credentials, repository rate limits (HTTP 429), or typos in the tag name.

**Resolution:**
```bash
# Authenticate to Docker registry
docker login

# Check rate limits (Docker Hub)
curl -s -D - -H "Authorization: Bearer <TOKEN>" "https://registry-1.docker.io/v2/ratelimitpreview/test/manifests/latest" | grep ratelimit

# Pull using explicit digest if tag fails
docker pull opensecurity/mobile-security-framework-mobsf@sha256:...
```

---

### 181. Port Conflicts
Occurs when host port requested via `-p <HOST_PORT>:<CONTAINER_PORT>` is already bound by another process (e.g., local Burp Suite on 8080).

**Resolution:**
```bash
# On macOS: find process occupying port 8000
lsof -i :8000
# On Linux:
ss -tulpn | grep 8000

# Remap to alternative host port
docker run -d -p 8081:8000 opensecurity/mobile-security-framework-mobsf:latest
```

---

### 182. DNS and Networking Failures
Containers cannot resolve external hosts or peer containers due to misconfigured DNS or using default bridge instead of user-defined bridge.

**Resolution:**
```bash
# Test internal DNS from within container
docker exec -it mobsf nslookup host.docker.internal
docker exec -it mobsf curl -Iv https://google.com

# Explicitly pass custom DNS server
docker run -d --dns 8.8.8.8 mobsf-tool:latest
```

---

### 183. Permission Errors
Arises when container UID lacks write permissions on host bind-mounted directories.

**Resolution:**
```bash
# Check host directory ownership
ls -ld ./scan_results

# Grant matching UID permission or pass host UID directly
docker run -it --rm --user $(id -u):$(id -g) -v $(pwd)/scan_results:/output my-scanner:latest
```

---

### 184. Volume Mount Errors
Path not found, invalid syntax, or SELinux denial preventing directory access.

**Resolution:**
```bash
# Always use absolute paths
docker run -v "$(pwd)/apks:/apks:ro" tool

# For SELinux systems (Fedora/RHEL), add :z flag
docker run -v "$(pwd)/apks:/apks:z" tool
```

---

### 185. Architecture Mismatch (ARM64 vs AMD64)
Occurs when running x86_64/AMD64 images on Apple Silicon (M1/M2/M3) Macs, leading to crashes or severe emulation slowdowns.

**Resolution:**
```bash
# Force Rosetta 2 emulation for AMD64 images on macOS Apple Silicon
docker run --platform linux/amd64 opensecurity/mobile-security-framework-mobsf:latest

# Or build multi-arch images with buildx
docker buildx build --platform linux/amd64,linux/arm64 -t my-tool:latest .
```

---

### 186. Docker Desktop Resource Allocation
MobSF and decompilation tools running slowly or crashing on macOS/Windows due to insufficient RAM/CPU allocated to the Docker VM.

**Recommendation:**
In Docker Desktop Settings -> Resources:
- **CPUs:** Allocate at least 4 cores
- **Memory:** Allocate at least 8 GB RAM (decompiling large DEX bytecode is memory intensive)
- **Swap:** 2 GB
- **Virtual disk limit:** 64 GB+

---

### 187. Disk-Space Management
Docker images, stopped containers, build caches, and dangling volumes consume tens of gigabytes of disk space over time.

```bash
# Check disk utilization breakdown
docker system df

# Check detailed space consumption
docker system df -v
```

---

### 188. Container Debugging
Techniques to inspect, debug, and diagnose failing or live containers.

```bash
# 1. Attach interactive shell to running container
docker exec -it mobsf /bin/bash

# 2. Execute one-off commands
docker exec -it mobsf netstat -tuln

# 3. Copy files out for forensic inspection
docker cp mobsf:/root/.MobSF/mobsf.log ./host_mobsf.log
```

---

### 189. Docker Logs and Events
Combining historical stdout/stderr logs with real-time daemon events provides a complete audit trail of container execution.

```bash
# Follow logs with timestamps
docker logs -f -t --tail 100 mobsf

# Monitor system events simultaneously in another terminal
docker events --filter 'container=mobsf'
```

---

### 190. Docker Cleanup Strategies
Pruning unused artifacts to reclaim disk space and maintain a pristine testing environment.

```bash
# Reclaim dangling images and stopped containers safely
docker system prune

# Comprehensive purge (removes stopped containers, all unused images, networks, and build cache)
docker system prune -a --volumes -f

# Specific cleanups
docker image prune -a
docker volume prune -f
docker network prune -f
```

---

## Recommended Learning Order for Android Penetration Testers

For an Android penetration tester, master Docker progressively in the following sequence:

```
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 1: Foundations & CLI (Topics 1–20)                              │
│  Understand container isolation, CLI commands, installation, contexts  │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 2: Images, Containers & Dockerfiles (Topics 21–70)              │
│  Build custom Android toolchains (JADX, APKTool, Frida), multi-stage   │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 3: Storage & Networking (Topics 71–96)                          │
│  Bind mounts for APKs/reports, connecting to host ADB and emulators   │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 4: Docker Compose Labs (Topics 97–112)                          │
│  Spin up full pentest stacks (MobSF + PostgreSQL + Mitmproxy) easily   │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 5: Mobile Security & MobSF Workflows (Topics 137–160)           │
│  Static/Dynamic APK analysis, REST API automation, CI/CD scanning      │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Phase 6: Container Hardening & Troubleshooting (Topics 113-136, 161-190)
│  Malware isolation, read-only roots, non-root users, ARM64 tuning      │
└────────────────────────────────────────────────────────────────────────┘
```

1. **Docker Fundamentals & CLI (Topics 1–20):** Learn basic container lifecycle management so you can pull and run existing mobile security tools effortlessly.
2. **Images, Containers & Dockerfiles (Topics 21–70):** Create custom images for APK decompilation, bytecode analysis, and Python instrumentation without polluting your host OS.
3. **Storage & Networking (Topics 71–96):** Master bind mounts to pass APK files in and extract vulnerability reports out; route container network traffic to host ADB and physical testing devices.
4. **Docker Compose (Topics 97–112):** Automate the orchestration of multi-container security labs with declarative YAML configuration files.
5. **Docker Security (Topics 113–136):** Understand container boundaries, Linux capabilities, and non-root execution to prevent malicious APKs from exploiting host systems.
6. **MobSF & Android Pentesting (Topics 137–160):** Deploy MobSF, JADX, and Frida to execute complete SAST and DAST workflows on Android apps.
7. **Advanced Topics & Troubleshooting (Topics 161–190):** Resolve architecture issues (ARM64 Apple Silicon), handle memory limits during deep decompilation, and automate cleanup routines.
