# 🚀 Docker  Notes

---

<details open>
<summary><h1>📌 Docker Fundamentals</h1></summary>

<br>

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

Docker solves this problem by packaging:
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

## 📦 Docker Image

A Docker image is a blueprint/template used to create containers.

An image contains:
- Application code
- Runtime
- Dependencies
- Required libraries

Think of it like:

```text
Image = Blueprint
```

Examples:
- Node.js image
- MongoDB image
- Nginx image

---

## 📦 Docker Container

A container is a running instance of an image.

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

</details>

---

<details>
<summary><h1>🛠 Docker Commands </h1></summary>

<br>

# 🛠 docker build

## Command

```bash
docker build -t zomato:v1 .
```

This command:
- Reads the Dockerfile
- Executes instructions
- Creates a Docker image locally

---

## Understanding -t

```text
-t = tag
```

Example:

```bash
docker build -t zomato:v1 .
```

Here:
- zomato → image name
- v1 → version/tag

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

Purpose:
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

Purpose:
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

Meaning:

```text
-d = detached mode
```

This runs the container in the background.

Without `-d`:
- Terminal gets blocked
- Logs continuously appear

With `-d`:
- Container runs silently in background
- Terminal becomes free

Example:

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

Meaning:

```text
host_port:container_port
```

---

## Request Flow

```text
Browser
   ↓
localhost:3000
   ↓
container:3000
```

Another Example:

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

Purpose:
Pass runtime configuration into the container.

---

## Node.js Example

```js
const port = process.env.PORT;
```

---

## How Environment Variables Work

```text
docker run -e KEY=value
        ↓
Docker injects environment variable
        ↓
Node.js reads using process.env
```

Example:

```bash
docker run \
-e PORT=3000 \
-e DB_URL=mongodb://mongo:27017 \
zomato:v1
```

Inside application:

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

Purpose:
Shows application logs/output from container.

Example:

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

Purpose:
Provides detailed metadata/configuration of container.

Information shown:
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

Purpose:
Allows entering inside a running container interactively.

---

## Meaning

| Flag | Purpose |
|---|---|
| -i | Interactive mode |
| -t | Terminal |

Inside container you can run:

```bash
ls
pwd
env
```

</details>

---

<details>
<summary><h1>💾 Docker Volumes </h1></summary>

<br>

# 💾 Docker Volumes

Containers are temporary.

If a MongoDB container gets deleted:

```text
Database data also gets deleted
```

Docker volumes solve this problem by storing data outside container lifecycle.

---

# 📌 Example

```bash
docker run -v mongo-data:/data/db mongo
```

---

# 📖 Understanding Volume Mapping

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

# 💻 Development Example

```bash
docker run -v $(pwd):/app zomato:v1
```

Meaning:

```text
Local folder ↔ container folder sync
```

</details>

---

<details>
<summary><h1>🌐 Docker Network </h1></summary>

<br>

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

## Why mongo?

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

</details>

---

<details>
<summary><h1>🧩 Docker Compose </h1></summary>

<br>

# 🧩 Docker Compose

Docker Compose is used to manage multiple containers together using a single YAML file.

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

Docker Compose:
- Creates containers
- Builds images
- Creates networks automatically
- Connects containers automatically

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

## STEP 1 — Existing Node.js Project

Suppose your backend already exists:

```text
app.js
package.json
```

---

## STEP 2 — Create Dockerfile

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

## STEP 3 — Create docker-compose.yml

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

Why `mongo`?

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
- Builds Node.js image
- Pulls MongoDB image
- Creates containers
- Creates network
- Connects containers
- Starts application

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

</details>

---

<details>
<summary><h1>🚀 Docker Hub Flow</h1></summary>

<br>

# 🚀 Docker Hub Flow

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

</details>

---

<details>
<summary><h1>🐳 Dockerfile </h1></summary>

<br>

# 🧠 First Understand the Real Problem

Suppose you created a Node.js project in VS Code.

Your project structure looks like this:

```text
zomato/
 ├── app.js
 ├── package.json
 └── node_modules
```

Now imagine you want:

```text
Anybody in the world should run this project
without installing anything manually
```

That means:
- They should NOT install Node.js manually
- They should NOT install npm manually
- They should NOT run npm install manually
- They should NOT configure environment manually

Everything should already be prepared.

This is where Dockerfile comes in.

---

# 📦 What Dockerfile ACTUALLY Does

A Dockerfile tells Docker:

```text
"Hey Docker,
take my project,
prepare environment,
install dependencies,
and make it runnable."
```

That’s the REAL purpose of Dockerfile.

---

# 🔄 Complete Mental Flow

```text
Dockerfile
    ↓
Docker reads instructions
    ↓
Creates Docker Image
    ↓
Docker Image becomes Container
    ↓
Application runs
```

---

# 📝 Example Dockerfile

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

Now let’s understand EXACTLY what happens internally.

---

# 1️⃣ FROM node:18

```dockerfile
FROM node:18
```

Imagine you bought a completely empty new laptop.

Can you run a Node.js application immediately?

NO.

First you need:
- Operating System
- Node.js
- npm

Correct?

This line solves that problem.

It tells Docker:

```text
"Start with a machine that already has Node.js 18 installed."
```

Docker downloads a ready-made Node.js environment from Docker Hub.

So now your container already contains:
- Linux OS
- Node.js
- npm

---

# ❌ What if we DON'T use FROM?

Then container would be empty.

No:
- Node.js
- npm
- Linux environment

Nothing exists.

Your application cannot run.

---

# 2️⃣ WORKDIR /app

```dockerfile
WORKDIR /app
```

Now Docker says:

```text
"Inside this container,
let's create a project folder called /app"
```

Equivalent Linux commands:

```bash
mkdir app
cd app
```

Why needed?

Because application files need proper organized location.

---

# 3️⃣ COPY package*.json ./

```dockerfile
COPY package*.json ./
```

Now Docker copies:
- package.json
- package-lock.json

from your laptop → inside container.

Why FIRST?

Because package.json contains dependency list.

Example:
- express
- mongoose
- dotenv

Docker needs these files before installing packages.

---

# 🚀 Docker Optimization

Docker copies package.json first because of:

```text
Docker Layer Caching
```

Suppose tomorrow:
- you only change app.js

Docker says:

```text
"Dependencies didn't change.
No need to run npm install again."
```

This makes builds MUCH faster.

---

# 4️⃣ RUN npm install

```dockerfile
RUN npm install
```

Now Docker runs:

```bash
npm install
```

inside container.

Docker installs:
- express
- mongoose
- dotenv
- all dependencies

Now container becomes fully ready.

Without this:
- node_modules won't exist
- application crashes

Example error:

```text
Cannot find module 'express'
```

---

# 5️⃣ COPY . .

```dockerfile
COPY . .
```

NOW Docker copies complete project.

That includes:
- app.js
- routes
- controllers
- everything

Why not earlier?

Because Docker should NOT reinstall dependencies whenever source code changes.

That is why:
- package.json copied first
- full code copied later

---

# Understanding COPY . .

```text
First dot  → local project folder
Second dot → current WORKDIR inside container
```

Since:

```dockerfile
WORKDIR /app
```

files get copied into:

```text
/app
```

inside container.

---

# 6️⃣ EXPOSE 3000

```dockerfile
EXPOSE 3000
```

This line tells Docker:

```text
"My application runs on port 3000"
```

VERY IMPORTANT:

EXPOSE does NOT actually open the port.

It only acts like:
- documentation
- declaration

Actual port mapping happens here:

```bash
docker run -p 3000:3000
```

---

# 7️⃣ CMD ["node", "app.js"]

```dockerfile
CMD ["node", "app.js"]
```

Docker now asks:

```text
"Everything is ready.
But what should I start?"
```

This line answers:

```text
"Run node app.js"
```

Internally equivalent to:

```bash
node app.js
```

VERY IMPORTANT:

Container lives ONLY while process lives.

If process stops:
container also stops.

Without CMD:
container starts and immediately exits.

Because:
no running process exists.

---

# 🚀 COMPLETE STORY NOW

You wrote:

```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

Then you ran:

```bash
docker build -t zomato:v1 .
```

Docker internally:

1. Creates small Linux machine
2. Downloads Node.js environment from Docker Hub
3. Creates /app folder
4. Copies package.json
5. Runs npm install
6. Copies source code
7. Saves everything as Docker Image

---

# ▶️ Then You Run

```bash
docker run zomato:v1
```

Docker says:

```text
"Create container from this ready-made setup
and start node app.js"
```

---

# 🧠 FINAL SIMPLE UNDERSTANDING

Dockerfile is basically:

```text
Instructions to prepare another computer automatically
```

---

# 🏠 Real-Life Analogy

Suppose your friend buys new laptop.

Without Docker:
you tell him:

```text
Install Node.js
Install npm
Clone repository
Run npm install
Configure environment
Run application
```

Painful.

With Docker:

```text
docker run
```

Everything already comes packaged.

---

# 🎯 FINAL MENTAL MODEL

```text
Dockerfile
    ↓
Setup instructions

Docker Image
    ↓
Ready-made packaged environment

Container
    ↓
Running application
```

</details>

---


---

<details>
<summary><h1>🚀 Advanced Dockerfile Concepts</h1></summary>

<br>

<details>
<summary><h2>🏗 Multi-Stage Builds</h2></summary>

<br>

# 🚀 What Problem Does Multi-Stage Build Solve?

Suppose your Node.js project contains:

```text
Source code
TypeScript
npm
dev dependencies
build tools
temporary files
```

When you create Docker image normally:

```dockerfile
FROM node:18

WORKDIR /app

COPY . .

RUN npm install

CMD ["node", "app.js"]
```

your final image contains EVERYTHING:
- app code
- node_modules
- npm
- development tools
- temporary files

Result:

```text
Very large Docker image
```

Sometimes:
- 800MB
- 1GB

This is bad for:
- deployments
- cloud cost
- startup speed

---

# ✅ Multi-Stage Build Solution

Docker says:

```text
"Let's use one container for building
and another clean container for running."
```

---

# 🧠 Real-Life Analogy

Suppose you cook food.

Kitchen contains:
- utensils
- vegetables
- gas
- packets
- garbage

But when serving customer:
you only give:

```text
Final food
```

Correct?

Same idea in Docker.

---

# 📝 Example

```dockerfile
# Stage 1 → Build Stage
FROM node:18 AS builder

WORKDIR /app

COPY . .

RUN npm install

# Stage 2 → Production Stage
FROM node:18-alpine

WORKDIR /app

COPY --from=builder /app .

CMD ["node", "app.js"]
```

---

# ⚙️ What Happens Internally?

## Stage 1

Docker creates temporary container.

Used for:
- npm install
- build process
- compiling

---

## Stage 2

Docker creates NEW clean lightweight container.

Only copies:

```text
Final ready application
```

NOT:
- temporary files
- build cache
- extra tooling

---

# 🚀 Result

Much smaller image.

Example:

```text
Before → 900MB
After  → 150MB
```

---

# 🏢 Why Companies Use Multi-Stage Builds

Because it gives:
- faster deployments
- lower storage usage
- lower cloud cost
- better security

</details>

---

<details>
<summary><h2>⚡ Docker Layer Caching</h2></summary>

<br>

# 🚀 What is Layer Caching?

Docker builds images:

```text
Layer by layer
```

Think of Docker image like stack of layers.

---

# 📝 Example

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["node", "app.js"]
```

---

# ⚙️ Internally Docker Creates Layers

```text
Layer 1 → FROM
Layer 2 → WORKDIR
Layer 3 → COPY package.json
Layer 4 → RUN npm install
Layer 5 → COPY source code
```

Docker stores every layer separately.

---

# 🤔 Why is this Powerful?

Suppose tomorrow:
you only change:

```text
app.js
```

Did package.json change?

NO.

So Docker says:

```text
"No need to rerun npm install"
```

Docker reuses old cached layer.

---

# 🚀 Huge Benefit

Without caching:

```text
npm install runs every build
```

Very slow.

---

# ✅ With Layer Caching

```text
Only changed layers rebuild
```

Very fast.

---

# 🎯 This is Why Industry Uses This Pattern

```dockerfile
COPY package*.json ./

RUN npm install

COPY . .
```

instead of:

```dockerfile
COPY . .

RUN npm install
```

---

# 🚀 Why This Optimization Matters

Suppose:
- npm install takes 3 minutes

Without caching:
every small code change triggers npm install again.

With caching:
Docker skips unnecessary work.

Huge productivity improvement.

</details>

---

<details>
<summary><h2>🚫 .dockerignore</h2></summary>

<br>

# 🚀 What Problem Does .dockerignore Solve?

Suppose your project contains:

```text
node_modules
.git
.env
logs
videos
```

When Docker runs:

```dockerfile
COPY . .
```

Docker copies EVERYTHING into image.

Including useless files.

---

# ❌ Problems Without .dockerignore

- huge Docker image
- slower builds
- security risks
- unnecessary files inside container

---

# ✅ Solution → .dockerignore

Create file:

```text
.dockerignore
```

Example:

```text
node_modules
.git
.env
logs
```

---

# ⚙️ What Happens Internally?

Docker says:

```text
"Ignore these files while building image"
```

---

# 🧠 Similar To

```text
.gitignore
```

Same concept.

---

# 🚨 VERY IMPORTANT

Never copy:

```text
.env
```

inside production Docker images.

Because:
- passwords may leak
- secrets may leak
- API keys may leak

Huge security risk.

---

# 🚀 Why Companies Use .dockerignore

Because it:
- reduces image size
- improves build speed
- improves security
- removes unnecessary files

</details>

---

<details>
<summary><h2>📦 Base Image Selection (alpine vs slim vs full)</h2></summary>

<br>

# 🚀 What is Base Image Selection?

This:

```dockerfile
FROM node:18
```

is NOT the only Node.js image.

Docker Hub provides multiple variants.

Examples:
- node:18
- node:18-slim
- node:18-alpine

---

# 🤔 Why Different Images Exist?

Because every project has different needs.

Some applications need:
- more tools
- debugging utilities
- full Linux packages

Some need:
- lightweight images
- smaller deployments
- faster startup

---

# 🧠 Real-Life Analogy

Think like mobile phones.

Some phones are:
- heavy but powerful

Some are:
- lightweight but minimal

Same idea.

---

# 📦 node:18

Full image.

Contains:
- many Linux packages
- debugging tools
- utilities

Good for:

```text
Beginners
Development
```

BUT:
- very large image size

---

# 📦 node:18-slim

Smaller version.

Removes unnecessary packages.

Good balance:

```text
Smaller + stable
```

Used heavily in production.

---

# 📦 node:18-alpine

Ultra lightweight version.

Uses Alpine Linux.

Very small image size.

Example:

```text
node:18        → 900MB
node:18-slim   → 250MB
node:18-alpine → 100MB
```

Huge difference.

---

# 🤔 Why Not Always Use Alpine?

Because Alpine sometimes causes:
- dependency issues
- package compatibility issues
- native module problems

Especially with:
- Python bindings
- binaries
- native Node modules

</details>
</details>

---

<details>
    
<summary><h1>🚀 Real Backend Integration Using Docker</h1></summary>

<br>

# 🚀 Moving From Docker Commands → Real Backend Development

Now we move from:

```text
Learning Docker commands
```

to:

```text
Using Docker in REAL backend projects
```

This is what companies actually expect from backend developers.

---

# 🏗 Real-World Example

Suppose you built:

```text
Zomato Backend
```

Tech stack:
- Node.js
- Express.js
- MongoDB

Project structure:

```text
zomato/
 ├── app.js
 ├── package.json
 ├── .env
 ├── Dockerfile
 └── docker-compose.yml
```

Goal:

```text
Run complete backend using Docker
```

INCLUDING:
- backend app
- database
- networking
- environment variables

---

<details>
<summary><h2>📝 STEP 1 — Existing Backend Application</h2></summary>

<br>

Suppose your `app.js` looks like this:

```js
const express = require("express");
const mongoose = require("mongoose");

const app = express();

const PORT = process.env.PORT;
const DB_URL = process.env.DB_URL;

mongoose.connect(DB_URL)
.then(() => console.log("MongoDB Connected"));

app.get("/", (req, res) => {
  res.send("Zomato Backend Running");
});

app.listen(PORT, () => {
  console.log(`Server running on ${PORT}`);
});
```

---

# 🧠 Important Understanding

Notice:

```js
process.env.PORT
process.env.DB_URL
```

This means:

```text
Application expects values from environment variables
```

NOT hardcoded values.

This is VERY important in real-world backend systems.

</details>

---

<details>
<summary><h2>🐳 STEP 2 — Create Dockerfile</h2></summary>

<br>

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

# 🧠 What This Dockerfile Does

Docker internally:

1. Downloads Node.js environment from Docker Hub
2. Creates `/app` folder
3. Copies package.json
4. Installs dependencies
5. Copies full source code
6. Declares port 3000
7. Starts Node.js application

</details>

---

<details>
<summary><h2>🧩 STEP 3 — Why Docker Compose is Needed</h2></summary>

<br>

Now think carefully.

Your backend needs:
- Node.js container
- MongoDB container

That means:

```text
Multiple containers
```

Managing manually becomes messy.

Without Docker Compose:

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

Too many commands.

Hard to maintain.

So we use:

```text
docker-compose.yml
```

</details>

---

<details>
<summary><h2>📄 STEP 4 — Create docker-compose.yml</h2></summary>

<br>

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

    environment:
      PORT: 3000
      DB_URL: mongodb://mongo:27017/zomato

    depends_on:
      - mongo

  mongo:
    image: mongo
    container_name: mongo

    ports:
      - "27017:27017"
```

</details>

---

<details>
<summary><h2>⚙️ Understanding docker-compose.yml Internally</h2></summary>

<br>

# 🔹 app Service

```yaml
app:
```

This creates:

```text
Node.js container
```

---

# 🔹 build: .

```yaml
build: .
```

Means:

```text
Use Dockerfile from current folder
```

Equivalent to:

```bash
docker build -t app-image .
```

---

# 🔹 ports

```yaml
ports:
  - "3000:3000"
```

Meaning:

```text
Laptop port 3000
        ↓
Container port 3000
```

Now browser can access:

```text
localhost:3000
```

---

# 🔹 environment

```yaml
environment:
  PORT: 3000
  DB_URL: mongodb://mongo:27017/zomato
```

Docker injects environment variables inside container.

Equivalent to:

```bash
docker run -e PORT=3000
```

---

# 🧠 VERY IMPORTANT

Inside Node.js:

```js
process.env.PORT
process.env.DB_URL
```

reads these values.

---

# 🔹 DB_URL Understanding

```text
mongodb://mongo:27017/zomato
```

Break it:

| Part | Meaning |
|---|---|
| mongo | MongoDB container name |
| 27017 | MongoDB port |
| zomato | database name |

---

# 🚨 MOST IMPORTANT UNDERSTANDING

Why:

```text
mongo
```

instead of:

```text
localhost
```

Because:

```text
Inside container,
localhost means SAME container itself
```

NOT another container.

---

# 🌐 Docker Compose Network Understanding

Docker Compose automatically creates network.

Both containers join same network.

So:

```text
app container
      ↓
can talk to
      ↓
mongo container
```

using:

```text
container/service name
```

---

# 🔹 depends_on

```yaml
depends_on:
  - mongo
```

Means:

```text
Start mongo container before app container
```

Very common production practice.

---

# 🔹 mongo Service

```yaml
mongo:
```

Creates MongoDB container.

---

# 🔹 image: mongo

```yaml
image: mongo
```

Docker downloads official MongoDB image from Docker Hub.

Equivalent to:

```bash
docker pull mongo
```

</details>

---

<details>
<summary><h2>▶️ STEP 5 — Run Everything</h2></summary>

<br>

Run:

```bash
docker-compose up
```

---

# ⚙️ What Happens Internally?

Docker Compose:

---

## 1️⃣ Creates Network Automatically

Example:

```text
zomato_default
```

---

## 2️⃣ Pulls MongoDB Image

Downloads official MongoDB image from Docker Hub.

---

## 3️⃣ Builds Node.js Image

Uses Dockerfile to build application image.

---

## 4️⃣ Creates MongoDB Container

MongoDB container starts.

---

## 5️⃣ Creates Node.js Container

Backend container starts.

---

## 6️⃣ Injects Environment Variables

PORT and DB_URL become available inside container.

---

## 7️⃣ Connects Containers To Same Network

Now both containers can communicate.

---

## 8️⃣ Starts Application

Node.js backend starts successfully.

</details>

</details>

---



<details>
<summary><h1>🛠 Docker Debugging Mindset </h1></summary>

<br>

# 🚀 Real Industry Understanding

Anyone can memorize Docker commands.

But companies care about something else:

```text
Can you debug failures when system breaks?
```

That is what separates:
- beginner developers
from
- strong backend engineers

---

# 🧠 Real Production Reality

In real projects:
Docker problems happen DAILY.

Examples:
- container crashes
- app not starting
- DB connection fails
- ports already used
- environment variables missing
- permission problems

Good engineers know:

```text
HOW TO DEBUG STEP-BY-STEP
```

---

<details>
<summary><h2>❌ Problem 1 — Container Not Starting</h2></summary>

<br>

# 🚨 Situation

You run:

```bash
docker run zomato:v1
```

But container immediately exits.

---

# 🧠 What Beginners Do

Panic.

---

# ✅ What Good Engineers Do

First check:

```bash
docker ps -a
```

This shows:
- stopped containers
- exited containers

Example:

```text
STATUS: Exited (1)
```

Meaning:
container crashed.

---

# 🚀 Next Step → Check Logs

Run:

```bash
docker logs <container_name>
```

Example:

```bash
docker logs zomato-app
```

---

# 🔍 Why Logs Matter

Logs tell:
```text
WHAT ACTUALLY FAILED
```

Example errors:

```text
Cannot find module 'express'
```

OR:

```text
MongoDB connection failed
```

OR:

```text
Port already in use
```

---

# 🧠 MOST IMPORTANT DEBUGGING MINDSET

Never randomly guess problem.

Always:
```text
Check logs first
```

This is one of the most important interview expectations.

---

# 🚀 Common Reasons Container Stops

| Problem | Meaning |
|---|---|
| App crash | Node.js application failed |
| Missing dependency | npm install issue |
| Wrong CMD | startup command wrong |
| DB connection failure | Mongo not reachable |
| Missing env variables | process.env undefined |

</details>

---

<details>
<summary><h2>🌐 Problem 2 — Port Conflict Issues</h2></summary>

<br>

# 🚨 Situation

You run:

```bash
docker run -p 3000:3000 zomato:v1
```

Error:

```text
Port 3000 already allocated
```

---

# 🧠 What Does This Mean?

Docker is trying to use:

```text
Laptop port 3000
```

BUT:
some other application already uses it.

Example:
- another Node.js app
- another Docker container
- VS Code dev server

---

# 🧠 Visual Understanding

```text
Your Laptop
   ↓
Port 3000 already busy
   ↓
Docker cannot bind container to same port
```

---

# ✅ Solution 1 — Change Host Port

Run:

```bash
docker run -p 5000:3000 zomato:v1
```

Meaning:

```text
Laptop port 5000
        ↓
Container port 3000
```

Now app runs on:

```text
localhost:5000
```

---

# ✅ Solution 2 — Stop Existing Process

Check running containers:

```bash
docker ps
```

Stop old container:

```bash
docker stop <container_id>
```

---

# 🧠 VERY IMPORTANT UNDERSTANDING

Only ONE process can use:
```text
same host port
```

at a time.

---

# 🚀 Common Interview Question

```text
Why does port conflict happen?
```

Answer:

```text
Because another process/container is already using the host machine port.
```

</details>

---

<details>
<summary><h2>🔐 Problem 3 — Environment Variable Misconfiguration</h2></summary>

<br>

# 🚨 Situation

Application crashes with:

```text
MongoDB connection failed
```

OR:

```text
PORT undefined
```

---

# 🧠 Real Root Cause

Environment variables missing or incorrect.

Example:

```js
process.env.DB_URL
```

returns:

```text
undefined
```

because Docker never received env variable.

---

# 🚀 Example Mistake

Running:

```bash
docker run zomato:v1
```

without:

```bash
-e DB_URL=...
```

---

# ✅ Correct Command

```bash
docker run \
-e PORT=3000 \
-e DB_URL=mongodb://mongo:27017 \
zomato:v1
```

---

# 🧠 How To Debug Env Problems

Enter container:

```bash
docker exec -it zomato-app /bin/bash
```

Then check:

```bash
env
```

This shows all environment variables.

---

# 🚀 Important Understanding

If variable missing inside container:

```text
Application configuration breaks
```

---

# 🏢 Real Production Reality

Most production failures happen because of:
- wrong environment variables
- wrong secrets
- wrong database URLs

VERY common issue.

</details>

---

<details>
<summary><h2>💾 Problem 4 — Volume Permission Issues</h2></summary>

<br>

# 🚨 Situation

Application says:

```text
Permission denied
```

OR:

```text
Cannot write file
```

when using Docker volumes.

---

# 🧠 Why This Happens

Container user may NOT have permission to:
- write files
- create folders
- access mounted volume

---

# 🧠 Visual Understanding

```text
Laptop Folder
      ↓
Mounted into container
      ↓
Container tries writing
      ↓
OS blocks permission
```

---

# 🚀 Example

```bash
docker run -v $(pwd):/app zomato:v1
```

Container tries:

```text
Write logs
Create uploads
Store files
```

But host OS permissions block access.

---

# ✅ Common Fixes

---

## Fix 1 — Proper Permissions

Linux:

```bash
chmod -R 777 folder_name
```

---

## Fix 2 — Run Correct User

Sometimes containers run as:
```text
non-root user
```

Need proper ownership.

---

## Fix 3 — Verify Mounted Path

Wrong path also causes issues.

Example:

```bash
-v wrong_path:/app
```

---

# 🧠 MOST IMPORTANT UNDERSTANDING

Volumes connect:
```text
Host machine filesystem
        ↔
Container filesystem
```

So OS permissions matter.

---

# 🚀 Real Backend Examples

Volume issues commonly happen with:
- uploads folder
- MongoDB data
- PostgreSQL data
- logs
- generated files

</details>

---

<details>
<summary><h2>🎯 Final Debugging Mindset</h2></summary>

<br>

# 🚀 Strong Engineers Think Like This

When Docker fails:

❌ NOT:
```text
Randomly changing commands
```

✅ Instead:

```text
1. Check container status
2. Check logs
3. Verify ports
4. Verify env variables
5. Verify network
6. Verify volumes
```

---

# 🧠 Real Interview Expectation

Interviewers are NOT checking:

```text
Can you memorize Docker commands?
```

They are checking:

```text
Can you debug production-like failures?
```

That is the REAL engineering mindset.

---

# 📌 Final Summary

| Problem | Debugging Step |
|---|---|
| Container exits | docker logs |
| Port conflict | check running processes |
| Env issue | check process.env / env |
| Volume issue | check permissions |
| DB connection issue | verify network + DB URL |

</details>

</details>

---
