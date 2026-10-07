# 02 - Docker Architecture & Internal Mechanics

Understanding Docker's internal architecture is critical for debugging containers, network connectivity, and answering systems-level interview questions.

---

## 1. Client-Server Architecture Overview

Docker operates as a **client-server application** consisting of three primary components:

1. **Docker Client (`docker` CLI)**
2. **Docker Host (Docker Daemon / `dockerd`)**
3. **Docker Registry (e.g., Docker Hub)**

```mermaid
flowchart TD
    Developer["Developer"] -->|"Executes Commands"| Client["Docker Client (CLI)"]
    Client -->|"REST API (Unix Socket / Named Pipe)"| Daemon["Docker Daemon (dockerd)"]

    Registry[("Docker Registry<br/>(Docker Hub / Private Registry)")] <-->|"Pulls / Pushes Images"| Daemon

    subgraph HostSystem["Docker Host Machine"]
        Daemon -->|"Creates & Manages"| Containers["Containers"]
        Daemon -->|"Builds & Caches"| Images["Images"]
        Daemon -->|"Provisions"| Networks["Networks"]
        Daemon -->|"Attaches"| Volumes["Volumes"]
    end
```

---

## 2. Core Architectural Components

### 1. Docker Client
- The user-facing command-line tool (`docker`).
- Accepts commands such as `docker run`, `docker build`, `docker ps`, `docker network create`.
- Does **not** execute containers itself. Instead, it translates commands into HTTP REST API requests and sends them to the Docker Daemon.
- Can connect to a local Docker daemon (via Unix domain socket `/var/run/docker.sock` on Linux or named pipes on Windows) or a remote daemon over TCP/TLS.

### 2. Docker Host & Docker Daemon (`dockerd`)
- The long-running background engine running on the host system.
- Listens for Docker REST API requests.
- Responsible for:
  - Building images (`docker build`)
  - Running and managing container lifecycles (`docker run`, `docker stop`, `docker rm`)
  - Managing network interfaces, IP assignment, and DNS
  - Allocating storage drivers and persistent volumes
  - Communicating with container runtimes (`containerd` and `runc`).

### 3. Docker Registry
- A centralized or private repository that catalogs and stores Docker images.
- **Docker Hub** (`hub.docker.com`) is the public default registry.
- Organizations often use private registries like AWS ECR, Google Artifact Registry, Azure Container Registry, or self-hosted Harbor.

---

## 3. Deep Dive: What Happens During `docker run nginx`?

When you execute:
```bash
docker run -d --name web-server -p 8080:80 nginx
```

Here is the exact step-by-step sequence executed under the hood:

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Developer
    participant Client as Docker Client
    participant Daemon as Docker Daemon (dockerd)
    participant LocalStore as Local Image Cache
    participant Registry as Docker Hub Registry
    participant Container as Container Runtime

    Dev->>Client: docker run -d --name web-server -p 8080:80 nginx
    Client->>Daemon: POST /containers/create (REST API call)
    Daemon->>LocalStore: Check if 'nginx:latest' exists locally
    alt Image Not Found Locally
        LocalStore-->>Daemon: Image not found
        Daemon->>Registry: Pull 'nginx:latest' manifest & layers
        Registry-->>Daemon: Download layers & verify checksums
        Daemon->>LocalStore: Cache image layers
    else Image Exists Locally
        LocalStore-->>Daemon: Image found
    end
    Daemon->>Container: Create container filesystem (add thin read-write layer)
    Daemon->>Container: Allocate namespaces (PID, NET, MNT, IPC, UTS) & cgroups
    Daemon->>Container: Attach to default bridge network & assign IP address
    Daemon->>Container: Configure port forwarding (Host 8080 -> Container 80)
    Daemon->>Container: Execute entrypoint process (nginx in foreground)
    Daemon-->>Client: Return Container ID
    Client-->>Dev: Print Container ID
```

---

## 4. Key Takeaways & Interview Revision

1. **Does the Docker CLI directly start containers?**
   - No. The Docker CLI is merely a REST client. It delegates all operational tasks to the Docker Daemon (`dockerd`), which in turn interacts with low-level runtimes (`containerd` and `runc`).

2. **Where does Docker store images and volumes on Linux?**
   - Typically in `/var/lib/docker/` (under directories such as `overlay2/` for storage layers and `volumes/` for named volumes).

3. **What is the role of `containerd` and `runc` in modern Docker architecture?**
   - `dockerd` handles API, network, and storage orchestration.
   - `containerd` manages the container lifecycle (image transfer, execution, supervision).
   - `runc` is the OCI-compliant runtime that directly configures Linux namespaces and cgroups to spawn the actual container process.
