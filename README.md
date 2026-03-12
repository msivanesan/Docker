# The Complete Docker Masterclass
> A comprehensive guide from basic CLI commands to multi-container architecture and production workflows.

---

## Table of Contents
1. [What is Docker?](#1-what-is-docker)
   - [Containers vs Virtual Machines](#11-containers-vs-virtual-machines)
   - [Core Architecture](#12-core-architecture)
2. [Installation & Setup](#2-installation--setup)
   - [Windows & macOS (Docker Desktop)](#21-windows--macos-docker-desktop)
   - [Linux (Ubuntu/Debian)](#22-linux-ubuntudebian)
   - [Post-Installation (Linux Only)](#23-post-installation-linux-only)
   - [WSL2 Integration](#24-wsl2-windows-backend)
   - [Verification](#25-verification-the-sanity-check)
3. [The Docker CLI Master Reference](#3-the-docker-cli-master-reference)
   - [Docker Core & Information](#31-docker-core--information)
   - [Image Management](#32-image-management-the-blueprints)
   - [Container Lifecycle](#33-container-lifecycle-the-running-instances)
   - [Interaction & Debugging](#34-container-interaction--debugging)
   - [Volumes & Persistence](#35-volumes--persistence-the-hard-drive)
   - [Networking](#36-networking-the-connectors)
   - [Registry & Authentication](#37-registry--authentication)
   - [System Cleanup](#38-system-cleanup-the-janitor)
4. [Deep Dive into Dockerfiles](#4-deep-dive-into-dockerfiles)
   - [Layers & Caching](#41-the-architecture-of-layers--caching)
   - [Instruction Encyclopedia](#42-core-instruction-encyclopedia)
   - [.dockerignore Strategy](#43-the-dockerignore-strategy)
   - [Multi-stage Build Masterclass](#44-multi-stage-build-masterclass)
   - [Best Practices Checklist](#45-best-practices-checklist)
   - [Dockerfile Cheat Sheet](#46-dockerfile-instruction-cheat-sheet)
5. [Docker Networking & Databases](#5-docker-networking--databases)
   - [Networking Fundamentals](#51-networking-fundamentals)
   - [The Localhost Trap](#52-service-discovery--the-localhost-trap)
   - [Database Persistence (Volumes)](#53-database-persistence-volumes)
   - [Advanced Database Patterns](#54-advanced-database-patterns)
   - [Networking & Database Cheat Sheet](#55-networking--database-cheat-sheet)
6. [Multi-Container Orchestration (Docker Compose)](#6-multi-container-orchestration-docker-compose)
   - [Anatomy of a docker-compose.yml](#62-anatomy-of-a-docker-composeyml)
   - [Professional Multi-Tier Example](#63-professional-multi-tier-example)
   - [Master Command Reference](#64-master-command-reference)
   - [Orchestration Lifecycle Patterns](#65-orchestration-lifecycle-patterns)
   - [Docker Compose Cheat Sheet](#66-docker-compose-cheat-sheet)
7. [Nginx in Docker](#7-nginx-in-docker)
   - [Nginx Reverse Proxy](#71-nginx-reverse-proxy)
8. [Debugging & Monitoring](#8-debugging--monitoring)
9. [Typical Backend Workflow](#9-typical-backend-workflow)
10. [Advanced Docker Compose Patterns](#10-advanced-docker-compose-patterns)
   - [Environment Overrides](#101-environment-overrides-docker-composeoverrideyml)
   - [Healthchecks](#102-healthchecks)
11. [Performance Tuning & Optimization](#11-performance-tuning--optimization)
12. [Container Security & Hardening](#12-container-security--hardening)
13. [The Polyglot Guide (Language-Specific Dockerfiles)](#13-the-polyglot-guide-language-specific-dockerfiles)
14. [System Design Masterclass (Architectural Blueprints)](#14-system-design-masterclass-architectural-blueprints)
15. [Comprehensive Case Study: Microservices Orchestration](#15-comprehensive-case-study-microservices-orchestration)
16. [Advanced Tooling & Modern Docker](#16-advanced-tooling--modern-docker)

---

## 1. What is Docker?

**Docker** is a platform used to bundle applications and their dependencies into lightweight, portable **containers**.

### 1.1 Containers vs Virtual Machines

| Feature | Virtual Machine | Docker Container |
| :--- | :--- | :--- |
| **OS** | Full OS per VM (Heavy) | Shares Host OS Kernel (Lightweight) |
| **Size** | GBs | MBs |
| **Startup** | Minutes | Seconds |
| **Performance** | Significant overhead | Near-native speed |

### 1.2 Core Architecture
Docker operates in a Client-Server model:
*   **Docker CLI**: The interface humans interact with.
*   **Docker Daemon (dockerd)**: The background process managing objects.
*   **Images**: Read-only templates (Class).
*   **Containers**: Running instances of images (Object).
*   **Registry**: Where images are stored (e.g., Docker Hub).

---

## 2. Installation & Setup

Docker installation varies by platform. Follow the guide for your specific OS to get the engine running properly.

### 2.1 Windows & macOS (Docker Desktop)
The most convenient way to run Docker on personal machines. It includes the Docker Engine, CLI, Docker Compose, and a GUI (Docker Dashboard).

*   **Download**: Get the installer from the [Official Docker Website](https://www.docker.com/products/docker-desktop/).
*   **Requirements**: 
    *   **Windows**: Requires WSL2 or Hyper-V enabled in BIOS.
    *   **macOS**: Supports Intel and Apple Silicon (M1/M2/M3) chips.
*   **Key Settings**:
    1.  **Resources**: Adjust CPU and RAM limits in *Settings > Resources*.
    2.  **WSL2 Integration**: On Windows, ensure "Use the WSL 2 based engine" is checked.

### 2.2 Linux (Ubuntu/Debian)
On Linux, Docker runs natively. It is recommended to use the official Docker repository rather than default package managers for the latest versions.

```bash
# 1. Update and install prerequisites
sudo apt update
sudo apt install ca-certificates curl gnupg

# 2. Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 3. Add the repository to Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine & Compose
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 2.3 Post-Installation (Linux Only)
By default, Docker requires `sudo`. To run it as a regular user:

```bash
# 1. Create the docker group
sudo groupadd docker

# 2. Add your user to the group
sudo usermod -aG docker $USER

# 3. Apply the changes (or log out and back in)
newgrp docker 

# 4. Verify you can run without sudo
docker ps
```

### 2.4 WSL2 (Windows Backend)
WSL2 is the high-performance backend for Docker on Windows.
1.  **Install WSL**: Run `wsl --install` in PowerShell (as Admin).
2.  **Integration**: Open Docker Desktop Dashboard > *Settings* > *Resources* > *WSL Integration*.
3.  **Enable**: Toggle your specific Linux distro (e.g., Ubuntu) to "On".

### 2.5 Verification (The Sanity Check)
Once installed, run these commands to ensure everything is working:

```bash
docker --version         # Check installed version
docker info              # Verify the daemon is running
docker run hello-world   # Pulls a tiny image and runs it to confirm setup
```

---

## 3. The Docker CLI Master Reference

This section serves as a comprehensive encyclopedia of the Docker CLI. Commands are grouped by functionality, providing both the syntax and the "why" behind their use.

### 3.1 Docker Core & Information
Basic commands to check the health and version of your Docker environment.

| Command | Explanation |
| :--- | :--- |
| `docker version` | Shows detailed version info for both Client and Server (Daemon). |
| `docker info` | Displays system-wide information (Containers running/paused/stopped, Images, Storage Driver, etc.). |
| `docker help <command>` | Access the manual for any specific command (e.g., `docker run --help`). |

---

### 3.2 Image Management (The Blueprints)
Images are read-only templates. These commands allow you to acquire, inspect, and clean them.

```bash
# 1. Listing and Finding
docker images                        # List all top-level images, their tags, and sizes
docker images -a                     # List all images, including intermediate layers
docker search nginx                  # Search Docker Hub for for "nginx" images

# 2. Acquisition and Removal
docker pull python:3.10-slim         # Download a specific version of an image
docker rmi <image_id>                # Remove a specific image from local storage
docker rmi -f <image_id>             # Force remove an image (useful if it has multiple tags)
docker image prune                   # Remove all dangling (untagged) images to save space

# 3. Inspection and Metadata
docker image inspect <name>          # Return low-level information on an image (JSON format)
docker image history <name>          # Show the "layers" and commands used to build the image
docker tag app:v1 myrepo/app:v1      # Create a new tag (alias) for an existing image

# 4. Import/Export
docker save -o my-app.tar my-app:v1  # Save an image to a tar archive (for offline transfer)
docker load -i my-app.tar            # Load an image from a tar archive
```

---

### 3.3 Container Lifecycle (The Running Instances)
Commands to manage the birth, life, and death of containers.

```bash
# 1. Creation and Execution
# -d: Detached (background) | -p: Port mapping | --name: Custom name | -e: Env variable
docker run -d -p 8080:80 --name my-web nginx

# Run and enter an interactive shell inside a new container
# --rm: Automatically remove the container when it exits
docker run -it --rm ubuntu bash

# 2. Status and Listing
docker ps                            # List only currently running containers
docker ps -a                         # List ALL containers (Exited, Created, Running)
docker ps -q                         # List only the numeric IDs of running containers
docker ps -s                         # Show the actual disk size used by each container

# 3. Control Operations
docker stop <id/name>                # Send a SIGTERM to stop gracefully
docker kill <id/name>                # Send a SIGKILL to stop immediately
docker start <id/name>               # Start a stopped container
docker restart <id/name>             # Stop and start a container
docker pause <id/name>               # Suspend all processes in a container
docker unpause <id/name>             # Resume a suspended container

# 4. Resource Updates
docker update --cpus 2 <name>        # Dynamically change CPU limits of a running container
docker rename old_name new_name      # Change the name of an existing container
```

---

### 3.4 Container Interaction & Debugging
The "surgical tools" for looking inside and modifying running containers.

```bash
# 1. Executing Commands
docker exec -it <name> sh            # Open a new shell inside a RUNNING container
docker exec <name> env               # View the environment variables of a runner

# 2. Logs and Monitoring
docker logs <name>                   # Fetch the logs of a container
docker logs -f <name>                # Stream (follow) the logs in real-time
docker logs --since 30m <name>       # View logs from the last 30 minutes
docker top <name>                    # Display the running processes of a container
docker stats                         # Live stream of CPU, Memory, and Network usage

# 3. File Transfers
docker cp <name>:/app/logs ./logs    # Copy files from container to host
docker cp ./config.yml <name>:/app/  # Copy files from host into container

# 4. Inspection
docker inspect <name>                # Detailed JSON configuration (IPs, Volumes, Env, Status)
docker port <name>                   # List the port mappings for the container
docker diff <name>                   # Inspect changes to files or directories on a container's FS
```

---

### 3.5 Volumes & Persistence (The Hard Drive)
Managing data that lives outside the container's temporary filesystem.

| Command | Action |
| :--- | :--- |
| `docker volume ls` | List all local volumes managed by Docker. |
| `docker volume create <name>` | Manually create a named volume. |
| `docker volume inspect <name>` | See the mount point on the host machine. |
| `docker volume rm <name>` | Delete a specific volume (must not be in use). |
| `docker volume prune` | Delete all unused volumes. |

---

### 3.6 Networking (The Connectors)
Managing communication between containers and the outside world.

```bash
docker network ls                    # List all available networks (bridge, host, none)
docker network create my-net         # Create a new user-defined bridge network
docker network connect my-net <app>  # Attach a running container to a network
docker network disconnect my-net <app> # Remove a container from a network
docker network inspect <net_name>    # See which containers are connected to the network
docker network prune                 # Remove all unused networks
```

---

### 3.7 Registry & Authentication
Interacting with Docker Hub or private registries.

```bash
docker login                         # Authenticate with the configured registry (interactive)
docker logout                        # Remove local authentication credentials
docker search <term>                 # Search the registry for an image
```

---

### 3.8 System Cleanup (The Janitor)
Docker accumulates a lot of "trash" (stopped containers, unused images). Use these to free up disk space.

```bash
docker system df                     # Show how much space Docker is consuming
docker container prune               # Delete all stopped containers
docker image prune                   # Delete all dangling images
docker volume prune                  # Delete all unused volumes
docker network prune                 # Delete all unused networks

# THE NUCLEAR OPTION
docker system prune -a --volumes     # Delete EVERYTHING that is not actively being used
```

---

## 4. Deep Dive into Dockerfiles

A **Dockerfile** is the definitive "recipe" for your container environment. Understanding how it builds images is the difference between a 10MB production image and a 1GB broken mess.

---

### 4.1 The Architecture of Layers & Caching
Docker images are **stacked**. Each instruction in your Dockerfile (especially `RUN`, `COPY`, and `ADD`) creates a new **immutable layer**.

*   **Layer Stacking**: If you have 10 lines in your Dockerfile, you have 10 layers. The final image is the union of all these layers.
*   **The Cache Mechanism**: When you rebuild an image, Docker checks if the instruction and the files involved have changed. If not, it reuses the layer from the **cache**.
*   **The "Invalidation" Rule**: If layer #4 changes, **all subsequent layers (#5 through #10) are invalidated** and must be rebuilt from scratch. 
    *   *Pro Tip*: Always put instructions that change frequently (like `COPY . .`) at the bottom of the file.

---

### 4.2 Core Instruction Encyclopedia

#### **1. FROM (The Foundation)**
Defines the base image. Every Dockerfile **must** start with this.
*   **Best Practice**: Never use `latest`. Use specific versions and lightweight variants like `alpine` or `slim`.
*   *Example*: `FROM python:3.11-slim`

#### **2. WORKDIR (The Home Base)**
Sets the working directory for all subsequent instructions (`RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `ADD`).
*   **Why?**: It creates the directory if it doesn't exist and avoids polluting the root `/` folder.
*   *Example*: `WORKDIR /usr/src/app`

#### **3. COPY vs. ADD (The Ingestion)**
*   **COPY**: Moves local files from your host into the image. Use this 99% of the time.
    *   *Example*: `COPY package.json .`
*   **ADD**: Similar to COPY, but can also download files from URLs or automatically extract `.tar.gz` archives. Use only when specifically needed.

#### **4. RUN (The Builder)**
Executes commands inside the container during the build process (e.g., installing packages).
*   **Optimization**: Always chain commands with `&&` and clean up caches in the same instruction to keep the layer small.
*   *Example*:
    ```dockerfile
    RUN apt-get update && \
        apt-get install -y --no-install-recommends curl && \
        rm -rf /var/lib/apt/lists/*
    ```

#### **5. ENV vs. ARG (The Variables)**
| Type | Visibility | Use Case |
| :--- | :--- | :--- |
| **ARG** | Build-time only | Passing a version number or API token during `docker build`. |
| **ENV** | Build & Runtime | Setting `NODE_ENV=production` or `PORT=80`. Accessible inside the app. |

#### **6. CMD vs. ENTRYPOINT (The Execution)**
*   **ENTRYPOINT**: Defines the fixed command that "is" the container. 
*   **CMD**: Defines the default arguments for the ENTRYPOINT.
*   *Example (The "Binary" Pattern)*:
    ```dockerfile
    ENTRYPOINT ["python", "app.py"]
    CMD ["--port", "8080"] 
    # Result: Runs "python app.py --port 8080"
    # Overridable: "docker run myapp --port 9000" runs "python app.py --port 9000"
    ```

#### **7. USER (The Security Guard)**
By default, Docker runs as `root`. This is a massive security risk.
*   **Best Practice**: Create a dedicated user and swap to it before finishing the Dockerfile.
*   *Example*:
    ```dockerfile
    RUN groupadd -r appuser && useradd -r -g appuser appuser
    USER appuser
    ```

---

### 4.3 The `.dockerignore` Strategy
Before Docker builds an image, it sends the "Context" (your project folder) to the Docker Daemon. If you have a 2GB `node_modules` folder, your builds will be incredibly slow.

**Create a `.dockerignore` file in your root:**
```text
# Exclude heavy dependencies
node_modules/
venv/
.venv/

# Exclude git and sensitive files
.git/
.env
.dockerignore

# Exclude build artifacts
dist/
build/
*.log
```

---

### 4.4 Multi-stage Build Masterclass
This is how professionals build images. You use a heavy image with all the tools to compile your code, then copy ONLY the compiled binary into a tiny "Distroless" or Alpine image.

**Example: A Production-Grade Go/React Application**
```dockerfile
# STAGE 1: Compilation (The "Heavy" Builder)
FROM golang:1.21-alpine AS builder
WORKDIR /src
COPY . .
RUN go build -o /bin/app main.go

# STAGE 2: Deployment (The "Tiny" Runtime)
FROM alpine:latest
WORKDIR /app
# We ONLY copy the binary, nothing else!
COPY --from=builder /bin/app .
EXPOSE 8080
# Run as non-root for security
RUN adduser -D appuser
USER appuser
CMD ["./app"]
```
*   **The Result**: Image size drops from ~800MB down to ~20MB.

---

### 4.5 Best Practices Checklist
1.  **Order Matters**: Place instructions that don't change often (like `FROM` and `WORKDIR`) at the top.
2.  **Combine RUNs**: Every `RUN` creates a layer. Combine related commands to keep the stack shallow.
3.  **No Secrets**: Never hardcode API keys or passwords in the Dockerfile. Use `ENV` or secret managers.
### 4.6 Dockerfile Instruction Cheat Sheet

| Instruction | Action | Best Practice / Context |
| :--- | :--- | :--- |
| **FROM** | Sets base image | Use specific tags (e.g., `alpine`, `slim`). |
| **WORKDIR** | Sets working directory | Always use this to avoid root directory pollution. |
| **COPY** | Copies local files | Use this for 99% of file transfers. |
| **ADD** | Advanced copying | Only use for URLs or auto-extracting `.tar`. |
| **RUN** | Builds image layers | Chain commands with `&&` to reduce layers. |
| **ENV** | Sets runtime variables | Use for ports, keys, or app settings. |
| **ARG** | Sets build-time variables | Ideal for version numbers or build-only flags. |
| **CMD** | Default start command | Overridable at runtime with `docker run`. |
| **ENTRYPOINT** | Fixed start command | Makes the container behave like a binary. |
| **USER** | Sets execution user | Always swap from `root` to a user for security. |
| **EXPOSE** | Documents ports | Purely informational; doesn't open ports. |
| **VOLUME** | Defines mount points | Ensures data is stored outside the container. |
| **LABEL** | Adds metadata | Useful for versioning, maintainers, or CI/CD. |

---

## 5. Docker Networking & Databases

Efficient networking and persistent storage are the pillars of a production-ready containerized backend. Without a proper strategy, your database will lose data on restart, and your services won't be able to find each other.

---

### 5.1 Networking Fundamentals
Docker uses a sandboxed networking stack. Understanding these drivers is essential for security and performance.

#### **Core Network Drivers**
1.  **Bridge (Default)**: Creates a private virtual network on the host. Containers can talk to each other via internal IPs or Service Names. This is the standard for 99% of web apps.
2.  **Host**: Removes network isolation. The container shares the host's IP address and port space directly. 
    *   *Pro*: Near-zero networking overhead.
    *   *Con*: High security risk; port conflicts are common.
3.  **Overlay**: Connects multiple Docker daemons together (across different physical servers). This is how Docker Swarm and Kubernetes manage distributed systems.
4.  **None**: Disables all networking for the container. Used for batch processing sensitive data where security is the absolute priority.

---

### 5.2 Service Discovery & "The Localhost Trap"
This is the most common mistake for beginners. **Inside a container, `localhost` refers to that container, not your computer.**

*   **The Failure**: If your Backend container tries to connect to `localhost:5432` for a Database, it is looking for a DB *inside* the Backend container. It will fail.
*   **The Orchestration Solution**: 
    1.  Place both containers on the same **custom network**.
    2.  Docker provides a built-in **Internal DNS**.
    3.  Connect using the **Container Name** or **Service Name**.
    *   *Correct Database URL*: `db:5432` or `postgres-service:5432`.

---

### 5.3 Database Persistence (Volumes)
Docker containers are **ephemeral** by design. If the container process stops or the container is deleted, the filesystem is wiped. To make data survive, you MUST use Volumes.

| Database | Internal Data Path | Why it matters? |
| :--- | :--- | :--- |
| **PostgreSQL**| `/var/lib/postgresql/data` | Standard path for Postgres data files. |
| **MySQL / MariaDB** | `/var/lib/mysql` | Storage for all schemas and tables. |
| **MongoDB** | `/data/db` | NoSQL document storage path. |
| **Redis** | `/data` | Path for `dump.rdb` or `appendonly.aof` files. |

**Volume Command Examples:**
```bash
# Named Volume (Production Recommended)
docker run -v db_data:/var/lib/postgresql/data postgres

# Bind Mount (Development Only - Live file sync)
docker run -v $(pwd)/config:/etc/app/config my-app
```

---

### 5.4 Advanced Database Patterns

#### **1. The Auto-Initialization Pattern**
Almost all official DB images (Postgres, MySQL, Mongo) scan the `/docker-entrypoint-initdb.d/` folder on the very first startup.
*   **How to use**: Mount a folder containing your `.sql` or `.sh` schema seeds to this path. 
*   **Result**: Your tables and initial admin users are created automatically the second the container starts.

#### **2. Network Isolation (DMZ Architecture)**
Professionals use **Dual-Network Isolation**.
*   **Frontend Network**: Nginx and the API-Server live here.
*   **Backend Network**: The API-Server and Database live here.
*   **Result**: The Database is physically invisible to the public internet because it has no path to the Frontend network.

#### **3. Healthchecks for DB Readiness**
Databases take a few seconds to boot up. Use Docker Healthchecks to ensure your API doesn't crash while waiting for the DB to be "Ready for connections." (See Section 10 for configuration).

---

### 5.5 Networking & Database Cheat Sheet

| Task | Command / Path | Note |
| :--- | :--- | :--- |
| **Create Network** | `docker network create <name>` | Use `--driver bridge` for most apps. |
| **Inspect Network** | `docker network inspect <name>` | Useful to find container IP addresses. |
| **Connect App** | `docker network connect <net> <app>` | Add a running container to another network. |
| **Create Volume** | `docker volume create <name>` | Creates a managed named volume. |
| **Volume Path** | `/var/lib/docker/volumes/` | Actual host path (on native Linux). |
| **Postgres Path** | `/var/lib/postgresql/data` | Map this to a volume for Postgres. |
| **MySQL Path** | `/var/lib/mysql` | Map this to a volume for MySQL. |
| **Redis Path** | `/data` | Map this to a volume for Redis persistence. |
| **Init Scripts** | `/docker-entrypoint-initdb.d/` | Path for `.sql` or `.sh` startup scripts. |

---

## 6. Multi-Container Orchestration (Docker Compose)

**Docker Compose** is a tool for defining and running multi-container Docker applications. Instead of running 10 separate `docker run` commands with complex flags, you define your entire infrastructure in a single YAML file.

---

### 6.1 The Logic of Orchestration
Compose treats your application as a collection of **Services**. 
*   **Infrastructure as Code**: Your network, volumes, and container settings are version-controlled in `docker-compose.yml`.
*   **Single Command Lifecycle**: Start the entire stack with one command (`up`) and tear it down with another (`down`).
*   **Isolated Environments**: You can run multiple isolated copies of the same stack on a single host (e.g., specific branches or dev/test environments).

---

### 6.2 Anatomy of a `docker-compose.yml`

| Section | Role | Example |
| :--- | :--- | :--- |
| **services** | Defines individual containers. | `web:`, `api:`, `db:` |
| **build** | Instructions to build an image from a local Dockerfile. | `build: ./backend` |
| **image** | Uses an existing image from a registry. | `image: postgres:15-alpine` |
| **ports** | Maps host ports to container ports. | `"8080:80"` (Host:Container) |
| **environment** | Sets environment variables inside the container. | `DEBUG: "true"` |
| **depends_on** | Expresses startup order (does NOT wait for readiness). | `depends_on: [db]` |
| **volumes** | Mounts persistent or shared data. | `pg_data:/var/lib/postgresql/data` |
| **networks** | Defines virtual isolated networks for services. | `frontend:`, `backend:` |

---

### 6.3 Professional Multi-Tier Example
This blueprint shows a production-ready setup with a Reverse Proxy, Node.js API, and a PostgreSQL database.

```yaml
version: "3.8"

services:
  # 1. Reverse Proxy
  proxy:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    networks:
      - public

  # 2. Application Logic
  backend:
    build: 
      context: ./api
      args:
        - NODE_ENV=production
    environment:
      - DATABASE_URL=postgres://user:pass@database:5432/myapp
    depends_on:
      - database
    networks:
      - public
      - private

  # 3. Persistent Data
  database:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    volumes:
      - db_volume:/var/lib/postgresql/data
    networks:
      - private

# Dedicated Networking for Security
networks:
  public:   # Accessible by proxy and backend
  private:  # ONLY accessible by backend and database

# Persistent Storage
volumes:
  db_volume:
```

---

### 6.4 Master Command Reference

| Command | Action | Professional Use Case |
| :--- | :--- | :--- |
| `docker compose up` | Creates and starts the stack. | Add `-d` to run in background (Detached). |
| `docker compose down` | Stops and removes containers + networks. | Use `-v` to also delete volumes (Data Loss!). |
| `docker compose build` | Rebuilds images defined with a `build:` key. | Use `--no-cache` for a clean fresh build. |
| `docker compose ps` | Lists status of all services in the stack. | Shows which containers are running or exited. |
| `docker compose logs` | Shows output from all containers. | Use `-f` to follow logs and `--tail 100`. |
| `docker compose exec` | Runs a command in a running service. | `docker compose exec db psql` |
| `docker compose pull` | Pulls images for services with `image:` keys. | Useful for updating to the latest base images. |
| `docker compose restart` | Restarts all or specific services. | `docker compose restart backend` |

---

### 6.5 Orchestration Lifecycle Patterns

#### **The "Build vs. Run" Trap**
If you change your code, `docker compose up` will **NOT** rebuild your image automatically. It only checks if the image exists.
*   **Fix**: Always run `docker compose up --build` if you have modified your source code or Dockerfile.

#### **Startup Order vs. Readiness**
`depends_on` makes sure the Database container *starts* before the App container. However, the database might still be "booting" when the App tries to connect.
*   **Pro Solution**: Use **Healthchecks** in your compose file or a "wait-for-it" script in your container entrypoint. (See Section 10).

---

### 6.6 Docker Compose Cheat Sheet

| Task | Command |
| :--- | :--- |
| **Start everything** | `docker compose up -d` |
| **Rebuild & Start** | `docker compose up -d --build` |
| **Stop but keep data** | `docker compose down` |
| **Nuke everything** | `docker compose down -v` |
| **Check logs for one service** | `docker compose logs -f <service_name>` |
| **Scale a service** | `docker compose up -d --scale worker=3` |

---

---

## 7. Nginx in Docker

Mapping Nginx in Docker differs from traditional Linux installs.

*   **Linux Path**: `/etc/nginx/sites-enabled/`
*   **Docker Path**: `/etc/nginx/conf.d/`

### 7.1 Nginx Reverse Proxy
Commonly used to route traffic based on URL paths.

```nginx
# inside default.conf
server {
    listen 80;
    
    location / {
        proxy_pass http://frontend:3000; # Routes to frontend service
    }

    location /api {
        proxy_pass http://backend:5000;  # Routes to backend service
    }
}
```

---

## 8. Debugging & Monitoring

1.  **Check Logs**: `docker logs <container_id>`
2.  **Internal Shell**: `docker exec -it <container_id> sh` (Check files inside).
3.  **Inspect Metadata**: `docker inspect <container_id>` (Check IPs, Mounts, Env).
4.  **Resource Usage**: `docker stats` (Check if container is OOM - Out of Memory).
5.  **Event Stream**: `docker events` (See exactly when containers restart).

---

## 9. Typical Backend Workflow

1.  **Develop**: Write code locally.
2.  **Dockerfile**: Define the environment.
3.  **Build**: `docker build -t app:v1 .`
4.  **Orchestrate**: Use `docker-compose.yml` for local testing with DBs.
5.  **Test**: Ensure networking and volumes work.
6.  **Push**: `docker push registry/app:v1`
7.  **Deploy**: Pull and run on the server.

---

## 10. Advanced Docker Compose Patterns

### 10.1 Environment Overrides (`docker-compose.override.yml`)
Docker Compose automatically merges `docker-compose.yml` and `docker-compose.override.yml`. Use the override file for local development (e.g., mounting local code) without changing the base production file.

### 10.2 Healthchecks
Prevent a service from starting until its database is truly ready.
```yaml
services:
  db:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
  app:
    build: .
    depends_on:
      db:
        condition: service_healthy
```

---

## 11. Performance Tuning & Optimization

### 11.1 The "BuildKit" Engine
Modern Docker uses BuildKit. Enable it to get faster parallel builds and better caching.
`export DOCKER_BUILDKIT=1`

### 11.2 Layer Caching Strategy
Always put the most stable instructions at the top and the most volatile (like `COPY . .`) at the bottom.
```dockerfile
# GOOD: Dependencies cached separately
COPY package.json .
RUN npm install
COPY src ./src  # This only runs if src changes
```

### 11.3 Minimizing Image Size
*   **Use `.dockerignore`**: Prevents `node_modules` or `.git` from being sent to the Docker daemon.
*   **Multi-stage builds**: As shown in Section 4.2.
*   **Alpine base images**: Reduces image size from ~900MB (Ubuntu) to ~5MB.

---

## 12. Container Security & Hardening

### 12.1 Rootless Containers
By default, Docker containers run as `root`. If an attacker escapes the container, they have root access to your host. 
**Fix**: Use the `USER` instruction in your Dockerfile to run as a non-privileged user.

### 12.2 Read-Only Filesystem
For high-security APIs, make the container filesystem read-only to prevent unauthorized file modifications.
`docker run --read-only my-app`

### 12.3 Secret Management
Never put API keys or passwords in a Dockerfile or `docker-compose.yml`. Use environment variables or **Docker Secrets**.
```bash
# Set secrets via CLI or .env file (add .env to .gitignore!)
docker run -e DB_PASSWORD=$DB_PASSWORD my-app
```

---
> **Final Summary**: Docker is about **Reproducibility**. By mastering CLI lifecycle, Dockerfile optimization, and Compose orchestration, you ensure that "It works on my machine" means "It works in Production."

---

## 13. The Polyglot Guide (Language-Specific Dockerfiles)

Every language has unique needs (compilation, runtimes, package managers). Use these templates as high-fidelity starting points.

### 13.1 Python (Django/FastAPI)
Focuses on virtualenv-like behavior and caching layers.
```dockerfile
FROM python:3.11-slim
WORKDIR /app
# Install system dependencies (e.g., for psycopg2)
RUN apt-get update && apt-get install -y libpq-dev gcc
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 13.2 Node.js (Express/NestJS)
Uses Multi-stage builds to keep production images tiny.
```dockerfile
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/package*.json ./
RUN npm install --omit=dev
CMD ["node", "dist/main.js"]
```

### 13.3 Java (Spring Boot)
Separates the heavy Maven/Gradle build from the tiny JRE runtime.
```dockerfile
FROM maven:3.8-openjdk-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:17-jdk-slim
COPY --from=build /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

---

## 14. System Design Masterclass (Architectural Blueprints)

### 14.1 The Sidecar Pattern (Logging/Proxy)
Adding functionality (like log shipping or monitoring) without modifying the main application code.
```text
[Main App Container] <───► [Shared Volume] <───► [Logging Sidecar]
       │                                               │
       ▼                                               ▼
   API Logic                                     Ships logs to ELK
```

### 14.2 The Ambassador Pattern (Database Proxy)
Decoupling the application from the database connection details. The app always talks to `localhost`, and the Ambassador routes it to the real DB.
```text
[App Container] ───► [Ambassador (Proxy)] ───► [Cloud Managed DB]
 (Connects to        (Handles Retries,          (External State)
  localhost:5432)     Auth, Pooling)
```

---

## 15. Comprehensive Case Study: Microservices Orchestration

This `docker-compose.yml` demonstrates a real-world stack with a Reverse Proxy, API, Database, and Cache.

```yaml
version: '3.8'

services:
  # 1. Edge Proxy (Entrypoint)
  gateway:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - api-server
    networks:
      - frontend

  # 2. Application Logic
  api-server:
    build: 
      context: ./backend
      dockerfile: Dockerfile
    environment:
      - DB_URL=postgres://db:5432/myapp
      - REDIS_URL=redis:6379
    depends_on:
      db:
        condition: service_healthy
    networks:
      - frontend
      - backend

  # 3. Persistent State
  db:
    image: postgres:15-alpine
    volumes:
      - pg_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
    networks:
      - backend

  # 4. Ephemeral State
  redis:
    image: redis:alpine
    networks:
      - backend

networks:
  frontend:
  backend:

volumes:
  pg_data:
```

---

## 16. Advanced Tooling & Modern Docker

### 16.1 Docker Buildx (Cross-Platform)
Build images for multiple architectures (Intel x86 vs Apple M1/M2) simultaneously.
```bash
docker buildx build --platform linux/amd64,linux/arm64 -t myuser/app:latest .
```

### 16.2 Docker Scout (Security)
Automatically scan your images for vulnerabilities and get remediation advice.
```bash
docker scout quickview my-image:latest
```

### 16.3 Dev Containers (The Future of IDEs)
Using a container as your **entire development environment** (including VS Code extensions and SDKs). This eliminates "works on my machine" bugs for developers too.

---
> **Mastery Recap**: Docker is more than just "packaging." It is an orchestration ecosystem. By combining **Multi-stage builds**, **Sidecar patterns**, and **Compose Healthchecks**, you create production environments that are resilient, scalable, and secure.
