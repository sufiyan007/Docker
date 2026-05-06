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

# Docker Compose — Complete Easy Understanding

---

# 1. What is Docker Compose?

Docker Compose is a tool used to manage and run:

```text
Multiple Docker containers together
```

using a single file:

```text
docker-compose.yml
```

---

# 2. Why Docker Compose is Needed

Without Docker Compose, suppose your project has:

- Node.js backend
- MongoDB database

You must manually run many commands:

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
- Too many commands
- Difficult in real projects

---

# 3. Docker Compose Solution

Instead of many commands:

👉 Put everything inside ONE file.

File name:

```text
docker-compose.yml
```

Then run:

```bash
docker-compose up
```

Docker Compose automatically:
- Builds images
- Pulls images
- Creates containers
- Creates network
- Connects containers
- Starts everything

---

# 4. Is Docker Compose a Network?

❌ NO

Docker Compose is NOT a network.

---

# Docker Compose does:

- Manage multiple containers
- Create network automatically
- Connect containers automatically

---

# Docker Network does:

```text
Communication between containers
```

---

# 5. Real-World Example

Suppose project:

```text
zomato/
 ├── app.js
 ├── package.json
 ├── Dockerfile
 └── docker-compose.yml
```

---

# 6. Step-by-Step Full Flow

---

# STEP 1 — Existing Node.js Project

Suppose your backend already exists:

```text
app.js
package.json
```

---

# STEP 2 — Create Dockerfile

Create file:

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

# What this Dockerfile does

| Instruction | Meaning |
|---|---|
| FROM node:18 | Use Node.js base image |
| WORKDIR /app | Create working directory |
| COPY | Copy files |
| RUN npm install | Install dependencies |
| EXPOSE 3000 | App runs on port 3000 |
| CMD | Start application |

---

# STEP 3 — Create docker-compose.yml

Create file:

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

# 7. Understanding docker-compose.yml

---

# version

```yaml
version: "3"
```

Docker Compose file format version.

---

# services

```yaml
services:
```

Defines all containers/services.

---

# app Service

```yaml
app:
```

Node.js container.

---

# build: .

```yaml
build: .
```

Meaning:

```text
Build image using Dockerfile in current folder
```

Equivalent to:

```bash
docker build -t app-image .
```

---

# container_name

```yaml
container_name: zomato-app
```

Container name becomes:

```text
zomato-app
```

---

# ports

```yaml
ports:
  - "3000:3000"
```

Meaning:

```text
Host port 3000 → container port 3000
```

---

# mongo Service

```yaml
mongo:
```

MongoDB container.

---

# image: mongo

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

# 8. VERY IMPORTANT — Network Understanding

You may notice:

```yaml
services:
  app:
  mongo:
```

No network written.

---

# Then how do containers communicate?

Because Docker Compose automatically creates:

```text
default network
```

Example:

```text
zomato_default
```

Both containers automatically join same network.

---

# Verify Network

Run:

```bash
docker network ls
```

You’ll see:

```text
zomato_default
```

---

# 9. How Node.js connects to MongoDB

Inside app.js:

```js
mongodb://mongo:27017
```

---

# Why "mongo"?

Because:

```text
mongo = service/container name
```

Docker Compose provides automatic DNS resolution.

---

# 10. Run Everything

Run:

```bash
docker-compose up
```

---

# What happens internally?

Docker Compose automatically:

✅ Builds Node.js image  
✅ Pulls MongoDB image  
✅ Creates containers  
✅ Creates network  
✅ Connects containers  
✅ Starts application  

---

# 11. Final Architecture

```text
Browser
   ↓
localhost:3000
   ↓
Node.js Container
   ↓
MongoDB Container
```

Connected using:

```text
Docker Network
```

Automatically created by Docker Compose.

---

# 12. Stop Everything

```bash
docker-compose down
```

Stops:
- Containers
- Network

---

# 13. Run in Background

```bash
docker-compose up -d
```

Equivalent to:
```text
Detached mode
```

---

# 14. View Logs

```bash
docker-compose logs
```

Live logs:

```bash
docker-compose logs -f
```

---

# 15. Why Companies Use Docker Compose

Because it:
- Simplifies multi-container setup
- Centralizes configuration
- Improves local development
- Reduces manual commands

Used heavily for:
- Backend development
- Microservices
- Local testing
- CI/CD pipelines

---

# 16. Compose vs Manual Docker

| Without Compose | With Compose |
|---|---|
| Many commands | One file |
| Manual network creation | Automatic |
| Manual container startup | Automatic |
| Hard to manage | Easy |
| Difficult scaling | Easier |

---

# 17. Final Mental Model

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

# 18. Most Important Understanding

```text
Docker Network
    ↓
Container communication

Docker Compose
    ↓
Container management/orchestration
```

---

# 19. One-Line Interview Answer

Docker Compose is a tool used to define and manage multi-container Docker applications using a YAML configuration file.

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
