# 🛠️ Docker Commands Cheat Sheet

A categorized quick-reference guide for daily Docker operations, debugging, and interview preparation.

---

## 1. Container Lifecycle Management

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker run` | Create and start a container | `docker run -d --name web -p 80:80 nginx` |
| `docker ps` | List running containers | `docker ps` |
| `docker ps -a` | List all containers (running & stopped) | `docker ps -a` |
| `docker stop` | Gracefully stop running container(s) | `docker stop web` |
| `docker start` | Start a stopped container | `docker start web` |
| `docker restart` | Restart a running or stopped container | `docker restart web` |
| `docker rm` | Remove stopped container(s) | `docker rm web` |
| `docker rm -f` | Force remove a running container | `docker rm -f web` |

---

## 2. Image Management

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker images` | List locally stored images | `docker images` |
| `docker pull` | Download image from registry | `docker pull mongo:latest` |
| `docker rmi` | Remove an image from local store | `docker rmi nginx` |
| `docker image prune` | Remove unused dangling images | `docker image prune` |

---

## 3. Docker Networking

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker network ls` | List all networks | `docker network ls` |
| `docker network create` | Create a user-defined network | `docker network create app-network` |
| `docker network inspect` | Inspect subnet, gateway & attached containers | `docker network inspect app-network` |
| `docker network connect` | Connect a running container to a network | `docker network connect app-network web` |
| `docker network disconnect` | Disconnect a container from a network | `docker network disconnect app-network web` |
| `docker network rm` | Delete a custom network | `docker network rm app-network` |

---

## 4. Docker Volumes & Persistent Storage

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker volume ls` | List all volumes | `docker volume ls` |
| `docker volume create` | Create a named volume | `docker volume create mongo-data` |
| `docker volume inspect` | View volume metadata & host path | `docker volume inspect mongo-data` |
| `docker volume rm` | Delete a volume (not in use) | `docker volume rm mongo-data` |
| `docker volume prune` | Remove all unused local volumes | `docker volume prune` |

---

## 5. Inspection, Logs & Debugging

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker inspect` | Return low-level JSON details of container/image | `docker inspect mongo` |
| `docker inspect --format` | Query specific fields via Go template | `docker inspect mongo --format "{{json .Mounts}}"` |
| `docker logs` | Fetch logs of a container | `docker logs -f mongo` |
| `docker exec -it` | Run an interactive shell inside a container | `docker exec -it mongo mongosh` |
| `docker stats` | Live stream of container CPU and RAM usage | `docker stats` |
| `docker port` | List port mappings for a container | `docker port mongo` |

---

## 6. Common `docker run` Flags Explained

| Flag | Name | Purpose | Example |
| :--- | :--- | :--- | :--- |
| `-d` | Detached | Run container in background and print container ID | `docker run -d mongo` |
| `--name` | Name | Assign a custom human-readable name | `--name mongo-db` |
| `-p` | Publish | Map host port to container port (`host:container`) | `-p 27017:27017` |
| `-v` | Volume | Mount a volume or host path (`volume:container_path`) | `-v mongo-data:/data/db` |
| `--network` | Network | Connect container to a specific network | `--network app-network` |
| `-e` | Environment | Set environment variable | `-e MONGO_INITDB_ROOT_USERNAME=admin` |
| `-it` | Interactive/TTY | Allocate a pseudo-TTY with stdin open | `-it ubuntu bash` |
| `--rm` | Auto-remove | Automatically remove container when it exits | `docker run --rm alpine ping google.com` |
