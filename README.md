# 🚀 Docker Complete Notes (Beginner → 2+ Year SDE)

---

# 📌 What is Docker?

Docker is a containerization platform used to package applications along with all their dependencies into isolated environments called **containers**.

The main purpose of Docker is to ensure that applications behave consistently across:
- Developer machines
- Testing servers
- Production environments

In simple terms:

```text
Build once, run anywhere
```

---

# 🤔 Why Docker is Needed

## Problem Without Docker

In real-world projects, applications often work on a developer machine but fail on another system.

Common reasons include:
- Different Node.js versions
- Missing dependencies
- Different operating systems
- Different environment configurations

Example:

```text
Developer Machine → Node.js 18
Production Server → Node.js 16
```

Result:

```text
Application crash or unexpected behavior
```

---

## Docker Solution

Docker packages:
- Application code
- Runtime
- Dependencies
- Environment configuration

inside one isolated environment.

This ensures:

```text
Same behavior everywhere
```

---

# 🧱 Core Docker Concepts

---

## 📦 Docker Image

A Docker image is a **blueprint/template** used to create containers.

An image contains:
- Application code
- Runtime
- Dependencies
- Required libraries

Think of it like:

```text
Image = Blueprint
```

Example:
- Node.js image
- MongoDB image
- Nginx image

---

## 📦 Docker Container

A container is a **running instance** of an image.

Think:

```text
Image = Class
Container = Object
```

Containers are isolated environments where applications run.

---

## 📝 Dockerfile

A Dockerfile is a file containing instructions used to build a Docker image.

Example:

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

# 🔄 Docker Workflow

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Running Container
```

---

# 🛠 docker build

## Command

```bash
docker build -t zomato:v1 .
```

---

## What This Command Does

This command:
- Reads the Dockerfile
- Executes instructions
- Creates a Docker image locally

---

## Understanding `-t`

```text
-t = tag
```

Example:

```bash
docker build -t zomato:v1 .
```

Here:
- `zomato` → image name
- `v1` → version/tag

---

## Important Understanding

`docker build` does NOT create a container.

It only creates:

```text
Docker Image
```

---

# 📥 Does docker build Download Images?

YES.

Example:

```dockerfile
FROM node:18
```

Docker first checks:
- Is `node:18` already available locally?

If NOT:
- Docker automatically pulls the image from Docker Hub.

---

# 📥 docker pull

## Command

```bash
docker pull nginx
```

---

## Purpose

Downloads an existing image from Docker Hub.

---

## Difference Between build and pull

| Command | Purpose |
|---|---|
| docker build | Create custom image |
| docker pull | Download existing image |

---

## Simple Analogy

```text
docker build = cooking food
docker pull = ordering food
```

---

# ▶️ docker run

## Command

```bash
docker run zomato:v1
```

---

## Purpose

Creates and starts a Docker container.

---

# 🔄 Workflow Again

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Container
```

---

# 🔙 docker run -d

## Command

```bash
docker run -d zomato:v1
```

---

## Meaning

```text
-d = detached mode
```

This runs the container in the background.

---

## Without `-d`

- Terminal gets blocked
- Logs continuously appear

---

## With `-d`

- Container runs silently in background
- Terminal becomes free

---

## Example

```bash
docker run -d mongo
```

MongoDB now runs in the background.

---

# 🌐 Port Binding

## Command

```bash
docker run -p 3000:3000 zomato:v1
```

---

## Meaning

```text
host_port:container_port
```

---

# 🔄 Request Flow

```text
Browser
   ↓
localhost:3000
   ↓
container:3000
```

---

## Another Example

```bash
docker run -p 8080:3000 zomato:v1
```

Now:

```text
localhost:8080
```

maps to:

```text
container:3000
```

---

# 🔐 Environment Variables (-e)

## Command

```bash
docker run -e PORT=3000 zomato:v1
```

---

## Purpose

Pass runtime configuration into the container.

---

## Node.js Example

```js
const port = process.env.PORT;
```

---

# 🔄 How Environment Variables Work

```text
docker run -e KEY=value
        ↓
Docker injects environment variable
        ↓
Node.js reads using process.env
```

---

## Example

```bash
docker run \
-e PORT=3000 \
-e DB_URL=mongodb://mongo:27017 \
zomato:v1
```

---

## Inside Application

```js
process.env.PORT
process.env.DB_URL
```

---

# 📋 docker ps

## Command

```bash
docker ps
```

Shows:

```text
Only running containers
```

---

# 📋 docker ps -a

## Command

```bash
docker ps -a
```

Shows:
- Running containers
- Stopped containers

---

## Difference

| Command | Purpose |
|---|---|
| docker ps | Running containers only |
| docker ps -a | All containers |

---

# ⚙️ ps -aef vs docker ps

---

## ps -aef

Linux OS command.

Shows:

```text
All operating system processes
```

---

## docker ps

Docker command.

Shows:

```text
Docker containers
```

---

# 📜 docker logs

## Command

```bash
docker logs zomato-app
```

---

## Purpose

Shows application logs/output from container.

---

## Example

```js
console.log("Server started");
```

Visible using:

```bash
docker logs zomato-app
```

---

## Live Logs

```bash
docker logs -f zomato-app
```

---

# 🔍 docker inspect

## Command

```bash
docker inspect zomato-app
```

---

## Purpose

Provides detailed metadata/configuration of container.

---

## Information Shown

- IP address
- Environment variables
- Port mappings
- Volumes
- Network configuration

---

# 🖥 docker exec -it

## Command

```bash
docker exec -it zomato-app /bin/bash
```

---

## Purpose

Allows entering inside a running container interactively.

---

## Meaning

| Flag | Purpose |
|---|---|
| -i | Interactive mode |
| -t | Terminal |

---

## Inside Container

You can run commands like:

```bash
ls
pwd
env
```

---

# 💾 Docker Volumes

---

## Problem

Containers are temporary.

If a MongoDB container gets deleted:

```text
Database data also gets deleted
```

---

## Solution

Docker volumes store data outside the container lifecycle.

---

## Example

```bash
docker run -v mongo-data:/data/db mongo
```

---

## Understanding Volume Mapping

| Part | Meaning |
|---|---|
| mongo-data | Volume name |
| /data/db | Path inside container |

---

# 🔄 Volume Flow

```text
Container writes data
        ↓
Docker Volume stores data
        ↓
Data survives container deletion
```

---

# 📌 Volume Use Cases

- Databases
- Logs
- Uploads
- Development sync

---

## Development Example

```bash
docker run -v $(pwd):/app zomato:v1
```

---

## Meaning

```text
Local folder ↔ container folder sync
```

---

# 🌐 Docker Network

Docker networks allow containers to communicate with each other securely.

Example:

```text
Node.js Container ↔ MongoDB Container
```

---

# 🛠 Create Network

```bash
docker network create zomato-network
```

---

# ▶️ Run MongoDB Container

```bash
docker run -d \
--name mongo \
--network zomato-network \
mongo
```

---

# ▶️ Run Node.js Container

```bash
docker run -d \
--name zomato-app \
--network zomato-network \
-p 3000:3000 \
zomato:v1
```

---

# 🔗 Database Connection URL

Inside Node.js app:

```js
mongodb://mongo:27017
```

---

## Why `mongo`?

Because:

```text
mongo = container name
```

Docker network automatically resolves container names.

---

# 📋 Network Commands

## List Networks

```bash
docker network ls
```

---

## Create Network

```bash
docker network create my-network
```

---

## Remove Network

```bash
docker network rm my-network
```

---

## Remove Unused Networks

```bash
docker network prune
```

---

# 🧩 Docker Compose

---

# 🤔 Why Docker Compose is Needed

Suppose your project contains:
- Node.js backend
- MongoDB database

Without Docker Compose, you must manually execute many commands:

```bash
docker network create zomato-network

docker run -d \
--name mongo \
--network zomato-network \
mongo

docker build -t zomato:v1 .

docker run -d \
--name zomato-app \
--network zomato-network \
-p 3000:3000 \
zomato:v1
```

This becomes:
- Hard to manage
- Repetitive
- Difficult in large projects

---

# ✅ Docker Compose Solution

Docker Compose allows managing multiple containers using a single YAML file:

```text
docker-compose.yml
```

Then everything runs using:

```bash
docker-compose up
```

---

# ⚠️ Is Docker Compose a Network?

NO.

Docker Compose is NOT a network.

---

# 📌 Docker Compose Responsibilities

Docker Compose:
- Creates containers
- Builds images
- Creates networks automatically
- Connects containers automatically

---

# 📌 Docker Network Responsibility

Docker Network handles:

```text
Communication between containers
```

---

# 📁 Real-World Project Structure

```text
zomato/
 ├── app.js
 ├── package.json
 ├── Dockerfile
 └── docker-compose.yml
```

---

# 🚀 Full Docker Compose Flow

---

# STEP 1 — Existing Node.js Project

Suppose your backend already exists:

```text
app.js
package.json
```

---

# STEP 2 — Create Dockerfile

Create:

```text
Dockerfile
```

Write:

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

# 📌 Dockerfile Explanation

| Instruction | Meaning |
|---|---|
| FROM node:18 | Use Node.js base image |
| WORKDIR /app | Create working directory |
| COPY | Copy files |
| RUN npm install | Install dependencies |
| EXPOSE 3000 | Application runs on port 3000 |
| CMD | Start application |

---

# STEP 3 — Create docker-compose.yml

Create:

```text
docker-compose.yml
```

Write:

```yaml
version: "3"

services:

  app:
    build: .
    container_name: zomato-app
    ports:
      - "3000:3000"

  mongo:
    image: mongo
    container_name: mongo
    ports:
      - "27017:27017"
```

---

# 📖 Understanding docker-compose.yml

---

## version

```yaml
version: "3"
```

Docker Compose file format version.

---

## services

```yaml
services:
```

Defines all containers/services.

---

## app Service

Represents Node.js application container.

---

## build: .

```yaml
build: .
```

Meaning:

```text
Build image using Dockerfile from current folder
```

Equivalent to:

```bash
docker build -t app-image .
```

---

## container_name

```yaml
container_name: zomato-app
```

Container name becomes:

```text
zomato-app
```

---

## ports

```yaml
ports:
  - "3000:3000"
```

Meaning:

```text
Host port 3000 → container port 3000
```

---

## mongo Service

Represents MongoDB container.

---

## image: mongo

```yaml
image: mongo
```

Meaning:

```text
Pull official MongoDB image from Docker Hub
```

Equivalent to:

```bash
docker pull mongo
```

---

# 🌐 Automatic Network Creation

Even if you do not define a network manually:

```yaml
services:
  app:
  mongo:
```

Docker Compose automatically creates a default network.

Example:

```text
zomato_default
```

Both containers automatically join this network.

---

# 🔍 Verify Network

```bash
docker network ls
```

---

# 🔗 Node.js to MongoDB Connection

Inside app.js:

```js
mongodb://mongo:27017
```

---

## Why `mongo`?

Because:

```text
mongo = service/container name
```

Docker Compose automatically provides DNS resolution.

---

# ▶️ Run Everything

```bash
docker-compose up
```

---

# ⚙️ What Happens Internally?

Docker Compose automatically:

✅ Builds Node.js image  
✅ Pulls MongoDB image  
✅ Creates containers  
✅ Creates network  
✅ Connects containers  
✅ Starts application  

---

# 🏗 Final Architecture

```text
Browser
   ↓
localhost:3000
   ↓
Node.js Container
   ↓
MongoDB Container
```

Connected using Docker Network.

---

# ⛔ Stop Everything

```bash
docker-compose down
```

Stops:
- Containers
- Networks

---

# 🔙 Run in Background

```bash
docker-compose up -d
```

Equivalent to detached mode.

---

# 📜 View Logs

```bash
docker-compose logs
```

Live logs:

```bash
docker-compose logs -f
```

---

# 🏢 Why Companies Use Docker Compose

Docker Compose:
- Simplifies multi-container management
- Centralizes configuration
- Improves local development
- Reduces manual setup

Used heavily for:
- Backend development
- Microservices
- Local testing
- CI/CD pipelines

---

# ⚖️ Manual Docker vs Docker Compose

| Without Compose | With Compose |
|---|---|
| Many commands | Single file |
| Manual network creation | Automatic |
| Manual container startup | Automatic |
| Hard to manage | Easy |
| Difficult scaling | Easier |

---

# 🧠 Final Mental Model

```text
Docker Compose
      ↓
Reads docker-compose.yml
      ↓
Creates network automatically
      ↓
Starts all containers
      ↓
Connects containers together
```

---

# 📌 Most Important Understanding

```text
Docker Network
    ↓
Container communication

Docker Compose
    ↓
Container orchestration/management
```

---

# 🚀 Docker Hub Flow

---

## Build Image

```bash
docker build -t zomato:v1 .
```

---

## Login

```bash
docker login
```

---

## Tag

```bash
docker tag zomato:v1 username/zomato:v1
```

---

## Push

```bash
docker push username/zomato:v1
```

---

## Pull Anywhere

```bash
docker pull username/zomato:v1
```

---

## Run Anywhere

```bash
docker run username/zomato:v1
```

---

# 📌 Final Summary

| Concept | Meaning |
|---|---|
| Image | Packaged application blueprint |
| Container | Running application |
| Dockerfile | Instructions to build image |
| Volume | Persistent storage |
| Network | Communication layer |
| Docker Compose | Multi-container orchestration |

---

# 🎯 Interview One-Liners

## Docker

Docker packages applications and dependencies into containers for consistent execution across environments.

---

## Image

An image is a read-only blueprint used to create containers.

---

## Container

A container is a running instance of an image.

---

## Volume

Volumes provide persistent storage outside the container lifecycle.

---

## Network

Docker networking enables communication between containers.

---

## Docker Compose

Docker Compose manages multi-container applications using a YAML configuration file.
