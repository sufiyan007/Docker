# 🚀 Docker Complete Notes 

---

# 1. What is Docker?

Docker is a platform used to package applications along with all dependencies into isolated environments called **containers**.

## Main Goal

```text
Build once, run anywhere
```

---

# 2. Why Docker is Needed

## Problem Without Docker

Application works:
- On developer machine
- But fails on teammate/server

Reasons:
- Different Node versions
- Missing dependencies
- Different OS configurations

Example:

```text
Developer → Node 18
Production → Node 16
```

Result:

```text
Application crash
```

---

## Docker Solution

Docker packages:
- Code
- Runtime
- Dependencies
- Configurations

inside one isolated environment.

Result:

```text
Same behavior everywhere
```

---

# 3. Core Docker Concepts

---

# IMAGE

An image is:
- Blueprint/template
- Read-only package
- Contains everything needed to run app

Example:
- Node.js runtime
- App code
- Dependencies

---

# CONTAINER

Container is:

```text
Running instance of image
```

Think:

```text
Image = Class
Container = Object
```

---

# DOCKERFILE

A file containing instructions to build image.

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

# FLOW

```text
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Container
```

---

# 4. docker build

Command:

```bash
docker build -t zomato:v1 .
```

## What It Does

- Reads Dockerfile
- Executes instructions
- Creates image locally

---

# Meaning of -t

```text
-t = tag
```

Example:

```bash
docker build -t zomato:v1 .
```

Here:

```text
zomato = image name
v1 = version/tag
```

---

# Important

`docker build` does NOT create container.

It only creates:

```text
IMAGE
```

---

# 5. Does docker build Download Images?

YES.

Example:

```dockerfile
FROM node:18
```

Docker checks:
- Is node:18 already available locally?

If NO:

```text
Automatically pulls image
```

---

# 6. docker pull

Command:

```bash
docker pull nginx
```

Purpose:

```text
Download existing image from Docker Hub
```

---

# Difference

| Command | Purpose |
|---|---|
| docker build | Create custom image |
| docker pull | Download existing image |

---

# Simple Analogy

```text
docker build = cooking food
docker pull = ordering food
```

---

# 7. docker run

Command:

```bash
docker run zomato:v1
```

Purpose:

```text
Create and start container
```

---

# Flow Again

```text
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Container
```

---

# 8. docker run -d

Command:

```bash
docker run -d zomato:v1
```

## Meaning

```text
-d = detached mode
```

Runs container in:

```text
BACKGROUND
```

---

# Without -d

Terminal gets blocked.

---

# With -d

Container runs silently in background.

---

# Example

```bash
docker run -d mongo
```

MongoDB runs in background.

---

# 9. Port Binding

Command:

```bash
docker run -p 3000:3000 zomato:v1
```

## Meaning

```text
host_port:container_port
```

---

# Flow

```text
Browser
   ↓
localhost:3000
   ↓
container:3000
```

---

# Example

```bash
docker run -p 8080:3000 zomato:v1
```

Access:

```text
localhost:8080
```

Inside container app still runs on:

```text
3000
```

---

# 10. Environment Variables (-e)

Command:

```bash
docker run -e PORT=3000 zomato:v1
```

Purpose:
Pass runtime configuration into container.

---

# Node.js Example

```js
const port = process.env.PORT;
```

---

# How It Works

```text
docker run -e KEY=value
        ↓
Docker injects env variable
        ↓
Node.js reads using process.env
```

---

# Example

```bash
docker run \
-e PORT=3000 \
-e DB_URL=mongodb://mongo:27017 \
zomato:v1
```

---

# Inside App

```js
process.env.PORT
process.env.DB_URL
```

---

# 11. docker ps

Command:

```bash
docker ps
```

Shows:

```text
Only running containers
```

---

# docker ps -a

Command:

```bash
docker ps -a
```

Shows:

```text
All containers
Running + stopped
```

---

# Difference

| Command | Shows |
|---|---|
| docker ps | Running containers |
| docker ps -a | All containers |

---

# 12. ps -aef vs docker ps

## ps -aef

Linux OS command.

Shows:

```text
All system processes
```

---

## docker ps

Docker command.

Shows:

```text
Docker containers
```

---

# 13. docker logs

Command:

```bash
docker logs zomato-app
```

Purpose:

```text
View application logs/output
```

---

# Example

```js
console.log("Server started");
```

Visible via:

```bash
docker logs zomato-app
```

---

# Live Logs

```bash
docker logs -f zomato-app
```

---

# 14. docker inspect

Command:

```bash
docker inspect zomato-app
```

Purpose:

```text
Detailed container metadata/configuration
```

---

# Shows

- IP address
- Environment variables
- Port mappings
- Network details
- Volumes

---

# 15. docker exec -it

Command:

```bash
docker exec -it zomato-app /bin/bash
```

Purpose:

```text
Enter inside running container
```

---

# Meaning

| Flag | Purpose |
|---|---|
| -i | Interactive |
| -t | Terminal |

---

# Inside Container

You can run:

```bash
ls
pwd
env
```

---

# 16. Docker Volumes

## Problem

Containers are temporary.

If MongoDB container gets deleted:

```text
Data also deleted
```

---

# Solution

Volumes store data outside container.

---

# Example

```bash
docker run -v mongo-data:/data/db mongo
```

---

# Meaning

| Part | Meaning |
|---|---|
| mongo-data | Volume name |
| /data/db | Path inside container |

---

# Flow

```text
Container writes data
        ↓
Volume stores data
        ↓
Data survives container deletion
```

---

# Volume Use Cases

- Databases
- Logs
- Uploads
- Development sync

---

# Development Example

```bash
docker run -v $(pwd):/app zomato:v1
```

---

# Meaning

```text
Local folder ↔ container folder sync
```

---

# 17. Docker Network

Purpose:

```text
Allow communication between containers
```

---

# Example

```text
Node.js Container ↔ MongoDB Container
```

---

# Create Network

```bash
docker network create zomato-network
```

---

# Run MongoDB

```bash
docker run -d \
--name mongo \
--network zomato-network \
mongo
```

---

# Run Node App

```bash
docker run -d \
--name zomato-app \
--network zomato-network \
-p 3000:3000 \
zomato:v1
```

---

# Database URL

Inside Node app:

```js
mongodb://mongo:27017
```

---

# Why mongo?

Because:

```text
mongo = container name
```

Docker network resolves it automatically.

---

# Network Commands

## List Networks

```bash
docker network ls
```

## Create Network

```bash
docker network create my-network
```

## Remove Network

```bash
docker network rm my-network
```

## Remove Unused Networks

```bash
docker network prune
```

---

# 18. Docker Compose

## Problem Without Compose

Too many commands:

```bash
docker build
docker network create
docker run
docker run
```

Messy for real projects.

---

# Solution

Docker Compose.

---

# What is Docker Compose?

Tool used to manage:

```text
Multiple containers together
```

using:

```text
docker-compose.yml
```

---

# Is Compose a Network?

NO.

Compose:
- Creates containers
- Creates networks automatically
- Connects containers

---

# 19. Why YAML File?

YAML:

```text
Configuration file format
```

Used heavily in:
- Docker Compose
- Kubernetes
- CI/CD

---

# Why YAML?

Because:
- Easy to read
- Structured
- Human friendly

---

# Example YAML

```yaml
name: Sufiyan
skills:
  - Docker
  - Node.js
```

---

# 20. docker-compose.yml Example

Create:

```text
docker-compose.yml
```

---

# Write

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

# Meaning

## app Service

```yaml
build: .
```

Meaning:

```text
Build image using Dockerfile in current folder
```

---

## mongo Service

```yaml
image: mongo
```

Meaning:

```text
Pull official MongoDB image
```

---

# 21. Run Compose

Command:

```bash
docker-compose up
```

---

# What Happens Internally

Compose automatically:

- Builds app image
- Pulls MongoDB image
- Creates network
- Starts containers
- Connects containers

---

# Important Network Understanding

Even though no network written:

```yaml
services:
  app:
  mongo:
```

Compose automatically creates:

```text
default network
```

Example:

```text
zomato_default
```

---

# Verify

```bash
docker network ls
```

---

# 22. Explicit Network in Compose (Advanced)

```yaml
version: "3"

services:

  app:
    build: .
    networks:
      - backend

  mongo:
    image: mongo
    networks:
      - backend

networks:
  backend:
```

---

# Meaning

Compose explicitly creates:

```text
backend network
```

---

# 23. Full Real-World Flow

## Step 1

Create:

```text
Dockerfile
```

---

## Step 2

Build image:

```bash
docker build -t zomato:v1 .
```

---

## Step 3

Create Compose file:

```text
docker-compose.yml
```

---

## Step 4

Run:

```bash
docker-compose up
```

---

# Final Architecture

```text
Browser
   ↓
Node.js Container
   ↓
MongoDB Container
```

Connected via:

```text
Docker Network
```

---

# 24. Docker Hub Flow

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

# 25. FINAL MOST IMPORTANT UNDERSTANDING

```text
Image = packaged app
Container = running app
Dockerfile = build instructions
Volume = persistent storage
Network = container communication
Compose = manage multiple containers together
```

---

# 26. FINAL INTERVIEW ONE-LINERS

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

Volumes provide persistent storage outside container lifecycle.

---

## Network

Docker networking enables communication between containers.

---

## Docker Compose

Docker Compose manages multi-container applications using a YAML configuration file.
