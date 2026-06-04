# Docker 🐳

Docker is a tool that lets you *package your application and everything it needs to run into a container* — so it works the same anywhere, regardless of the machine.

**Instead of dealing with "it works on my machine" problems, you define the environment once, and Docker makes it reproducible.**

---

## Index 👩🏽‍💻

1. [Core Concepts](#core-concepts)
2. [Container vs Virtual Machine](#container-vs-virtual-machine)
3. [Key Components](#key-components)
4. [Networking](#networking)
5. [Why it matters in Data Engineering](#why-it-matters-in-data-engineering)

---

## Core Concepts

- **Image** — a read-only blueprint for a container. Built from a `Dockerfile`.
- **Container** — a running instance of an image. Isolated, lightweight, disposable.
- **Dockerfile** — a script with instructions to build an image (`FROM`, `RUN`, `COPY`, `CMD`...).
- **Docker Compose** — a tool to define and run multiple containers together using a `.yml` file.
- **Volume** — a way to persist data outside the container lifecycle. Without it, data is lost when the container stops.
- **Port mapping** — connects a port on your local machine to a port inside the container (`-p local:container`).

---

## Container vs Virtual Machine

| | Container | Virtual Machine |
|---|---|---|
| Size | Lightweight (MBs) | Heavy (GBs) |
| Startup | Seconds | Minutes |
| Isolation | Process-level | Full OS |
| Overhead | Low | High |

Containers share the host OS kernel — VMs simulate an entire operating system. That's why containers are much faster and lighter.

---

## Key Components

### Image
The starting point. You either pull one from Docker Hub (`postgres:13`, `python:3.9`) or build your own with a `Dockerfile`.

### Container
What actually runs. Stateless by default — once it stops, anything written inside (without a volume) is gone.

### Dockerfile
Defines how to build a custom image:
```dockerfile
FROM python:3.9          # base image
RUN pip install pandas   # install dependencies
COPY . /app              # copy local files into the image
CMD ["python", "app.py"] # default command to run
```

### Docker Compose
Instead of running each container manually, Compose lets you define a full setup in one file:
```yaml
services:
  postgres:
    image: postgres:13
  pgadmin:
    image: dpage/pgadmin4
```
Run everything with `docker-compose up`.

---

## Networking

By default, containers are isolated — they can't talk to each other.

To connect them, you create a **network**:
- Containers on the same network can reference each other by name (e.g. `pg_database` instead of `localhost`).
- This is how Postgres and pgAdmin connect in development.

---

## Why it Matters in Data Engineering

Data pipelines depend on specific versions of databases, Python libraries, and tools. Docker makes that setup:
- **Reproducible** — same environment everywhere (local, staging, production)
- **Portable** — share a `Dockerfile` and anyone can run it
- **Isolated** — no conflicts between project dependencies
- **Disposable** — spin up and tear down databases in seconds for testing
