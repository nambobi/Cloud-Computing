# Project 05: Cloud Microservices and Containerization with Docker

## Project Overview

Containerization is a foundational pillar of modern cloud architecture. This project provides hands-on experience building custom Docker images, managing container lifecycles, orchestrating multi-container applications using Docker Compose, and persisting data with Docker volumes.

---

## Learning Objectives

Upon completion of this project, you will be able to:
1. Understand container virtualisation vs virtual machines.
2. Write production-ready Dockerfiles.
3. Build, tag, and publish Docker container images.
4. Orchestrate multi-service microservices using Docker Compose.
5. Manage container networking, port mapping, and environment variables.

---

## Prerequisites

- Workstation running Windows 10/11, macOS, or Linux.
- Docker Desktop or Docker Engine installed.
- Command Line Interface.

---

## Step-by-Step Implementation Guide

### Step 1: Create a Containerized Microservice

1. Create a directory named `cloud-service` containing `app.py`:

```python
from flask import Flask, jsonify
import os, socket

app = Flask(__name__)

@app.route("/")
def index():
    return jsonify({
        "service": "Cloud Computing API Microservice",
        "hostname": socket.gethostname(),
        "status": "Healthy"
    })

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

2. Create `requirements.txt`:
```text
flask==3.0.0
```

---

### Step 2: Write Dockerfile

Create a file named `Dockerfile` in the project directory:

```dockerfile
# Use official lightweight Python base image
FROM python:3.11-slim

# Set working directory inside container
WORKDIR /app

# Copy application dependencies
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application source code
COPY app.py .

# Expose microservice port
EXPOSE 5000

# Define container startup command
CMD ["python", "app.py"]
```

---

### Step 3: Build and Run Container Image

1. Build Docker image:
   ```cmd
   docker build -t cloud-microservice:v1.0 .
   ```
2. Verify image creation:
   ```cmd
   docker images
   ```
3. Run container in detached mode mapping host port 8080 to container port 5000:
   ```cmd
   docker run -d -p 8080:5000 --name cloud-api-container cloud-microservice:v1.0
   ```
4. Verify running container:
   ```cmd
   docker ps
   ```
5. Test microservice endpoint in browser or via curl:
   ```cmd
   curl http://localhost:8080
   ```

---

### Step 4: Multi-Container Orchestration with Docker Compose

Create a `docker-compose.yml` file to orchestrate the API microservice alongside a Redis database container:

```yaml
version: '3.8'

services:
  web-service:
    build: .
    ports:
      - "8080:5000"
    environment:
      - REDIS_HOST=redis-db
    depends_on:
      - redis-db

  redis-db:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data

volumes:
  redis-data:
```

Launch multi-container application stack:
```cmd
docker-compose up -d
```

Verify status of all stack services:
```cmd
docker-compose ps
```

Stop stack services:
```cmd
docker-compose down
```

---

## Troubleshooting Guide

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `Cannot connect to the Docker daemon` | Docker Desktop service is stopped. | Start Docker Desktop application on workstation. |
| `port is already allocated` | Another local process is using host port 8080. | Change host port mapping, e.g. `-p 8081:5000`. |
