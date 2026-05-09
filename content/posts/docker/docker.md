---
title: "Docker 101: The Gateway to DevOps"
date : 2026-05-07
featured: true
author : "Anurag Mishra"
tags : ['docker', "DevOps", "container"]
---
So, this post is coming after a long  time. Now the invisible man is back - with a post about Docker  where you will certainly learn something about Docker. Docker is often called the **gateway to DevOps**. 
_**Let's get into it.**_
## What is Docker?
Docker is a containerization platform that lets you package an application along with all its dependencies, libraries, and configuration into a single unit called a container. These containers can run consistently on any machine that has Docker installed — whether it's your laptop, a colleague's machine, or a cloud server.

 *Think of it like a **shipping container***
 _just as a shipping container can be loaded onto any ship, truck, or train without repacking its contents, a Docker container can run on any machine without reconfiguring the app inside._

## The Real Problem Before Docker - The old school method?
### One Application = One physical Server
In the old days, the rule was simple and very painful, i.e 
```
App A  →  Server 1  (dedicated)
App B  →  Server 2  (dedicated)
App C  →  Server 3  (dedicated)
```
- Each application got its own physical server
- You couldn't mix apps on the same server because of dependency conflicts, security concerns and stability risks.
This results in that server ran at maybe 5-10% CPU capacity - rest was just wasted.
#### What Happened when Traffic Increased?
>  **SOLUTION?** Buy another physical server!

### Then Came Virtual Machines - Better but still Heavy Solution
> It solved the problem of "one app per server" waste problem.

```
One Physical Server
┌────────────────────────────┐
│  VM 1      VM 2      VM 3  │
│  App A     App B     App C │
│  OS        OS        OS    │  ← Each VM carries full OS (GBs!)                       |
│       Hypervisor           │
│       Physical Server      │
└────────────────────────────┘
```

Now multiple apps on one server.

But, but, it is still heavy: 
- Each VM = full OS (required 10-20 GB)
- Boot time = taking minutes 
- Still slow to scale

### Then Docker Entered — The Container Revolution 

Docker introduced a lightweight alternative to VMs called containers. 
Instead of carrying a full OS, containers share the host machine's OS kernel and only package what the app actually needs: 
```

One Physical Server
┌────────────────────────────┐
│  C1        C2        C3    │
│  App A     App B     App C │
│  Libs      Libs      Libs  │  ← No full OS! Shares host kernel
│       Docker Engine        │
│       Host OS              │
│       Physical Server      │
└────────────────────────────┘

```


## Is Docker the Only Containerization Tool?
No! Docker is **not** the only tool — it is just the most **popular and widely adopted** one. Other tools exist like **Podman, rkt (Rocket), Buildah, LXC** and more.
### What made Docker win?
- Extremely **simple CLI** (`build`, `run`, `push`)
- **Dockerfile** — a clean, readable way to define your container
- **Docker Hub** — a massive public registry of ready-to-use images
- Huge **community and ecosystem**
## Installation : 
### Linux (Ubuntu/ Debian) 
#### Step 1 - Update your system
```
sudo apt update && sudo apt upgrade -y
```
#### Step 2 - Install required dependencies
```
sudo apt install -y ca-certificates curl gnupg lsb-release
```
#### Step 3 - Add Docker's official GPG key
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
#### Step 4 - Add Docker repository 
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```
#### Step 5 - Install Docker Engine 
```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
#### Step 6 - Start and Enable Docker
```
sudo systemctl start docker
sudo systemctl enable docker

```

#### Step 7 - Run docker without sudo (optional but recommended )
```
sudo usermod -aG docker $USER
newgrp docker
```

#### Step 8 - Verify Installation
```
docker --version
docker run hello-world
```

### Window
1.  Go to 👉 [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Download **Docker Desktop for Windows**
3. Run the installer `.exe`
4. Enable **WSL 2** when prompted (Windows Subsystem for Linux)
5. Restart your computer
6. Open Docker Desktop — wait for it to start
7. Open terminal and verify:
```
docker --version
docker run hello-world
```

### MacOs
1. Go to 👉 [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Download for **Mac (Apple Silicon or Intel)** — choose correctly!
3. Open the `.dmg` file → drag Docker to Applications
4. Launch Docker from Applications
5. Wait for Docker to fully start (whale icon in menu bar)
6. Verify in terminal:
```
docker --version
docker run hello-world

```
**Congratulations!** you have installed docker in your system. Before we write our first command, let's get comfortable with two words you'll hear constantly: **image** and **container**.
#### Docker Image
A Docker image is a **read-only blueprint/template** used to create containers.
Feels heavy? Think of it like a recipe - it contains everything needed to run your application:
- The OS layer
- Runtime
- Your app's code
- Dependencies (node_modules)
- Configuration
>You don't  run an image directly — you use it to create a container.

#### Docker Container 
A container is an **image brought to life**. When you run an image, Docker creates a container — an isolated, running instance of that blueprint. You can create **multiple containers from the same image**, all running independently.

> Image VS Container at a Glance 

| Feature    | Image             | Container            |
| ---------- | ----------------- | -------------------- |
| What it is | The blueprint     | The running instance |
| State      | Static, read-only | Live                 |
| Analogy    | A recipe          | The actual dish      |

## Warming Up — Your First Docker Commands
You've installed Docker, you understand images and containers. Now let's actually use it. These commands will cover 90% of your daily Docker usage.

 #### 1. docker run
 ```
 docker run node:19.6-bullseye-slim
 ```
>  This is the most fundamental  Docker commands. It does two things in one shot : 
	1. Pulls the image from Docker Hub(if not on your machine)
	2. Creates and starts a container from the image
#### 2. docker run -d
```
docker run -d node:19.6-bullseye-slim
```
The `-d` flag stands for **detached mode**. Without it, the container runs in your terminal — blocking it, printing logs, and dying when you close it. With `-d`, the container runs **in the background** and you get your terminal back.

```
# Without -d → terminal is stuck here
docker run node:19.6-bullseye-slim

# With -d → runs in background, prints container ID
docker run -d node:19.6-bullseye-slim
a3f92c1d8e45b...   ← container ID
```

> You'll use -d almost always for servers and databases.

#### 3. docker run -p 5432:5432
```
docker run -d -p 5432:5432 postgres
```
The `-p` flag stands for **publish** — it maps a port on your machine to a port inside the container.

```
Your Machine : Container
     5432    :   5432
```
> Containers are isolated by default — nothing from outside can reach them. `-p` punches a hole to allow traffic through.

The format is always **`-p host:container`**
#### 4. docker run --name
```
docker run -d --name myapp node:19.6-bullseye-slim
```
By default Docker assigns a random (and often hilarious) name to every container like `quirky_einstein` or `angry_torvalds`. The `--name` flag lets you give it a **meaningful name** you can actually remember and reference later.

```
docker stop quirky_einstein   # 😬 without --name
docker stop myapp             # 😌 with --name
```

#### 5. docker run --rm 
```
docker run --rm node:19.6-bullseye-slim
```
When a container stops, it doesn't disappear — it just sits there dead, taking up space. The `--rm` flag tells Docker to **automatically delete the container** the moment it stops.
This is perfect for:
- Running a one-off command
- Testing something quickly
- Scripts and CI pipelines
```
# Run, execute, vanish — no cleanup needed
docker run --rm node:19.6-bullseye-slim node --version
```

#### 6. docker ps

```
docker ps
```
Lists all **currently running** containers. Your go-to command to see what's alive.

```
CONTAINER ID   IMAGE      COMMAND                CREATED        STATUS        PORTS                    NAMES
a3f92c1d8e45   postgres   "docker-entrypoint.s…" 2 minutes ago  Up 2 minutes  0.0.0.0:5432->5432/tcp   mypostgres
```
The most useful columns:

- **CONTAINER ID** — short unique ID, use this to reference the container
- **STATUS** — is it running? crashed? paused?
- **PORTS** — which ports are exposed
- **NAMES** — the name (random or one you set with `--name`)

#### 7. docker ps -a
```
docker ps -a
```
The `-a` flag stands for **all**. Shows every container — running, stopped, and crashed. This is where you find containers that exited or failed.
```
CONTAINER ID   IMAGE    STATUS                      NAMES
a3f92c1d8e45   postgres Up 2 minutes                mypostgres
b7c12f9d3a11   node     Exited (1) 5 minutes ago    quirky_einstein
```

#### 8. docker stop <id/name>
```
docker stop myapp
# or
docker stop a3f92c1d8e45
```
**Gracefully stops** a running container. Docker sends a `SIGTERM` signal giving the app time to finish what it's doing and shut down cleanly. If the container doesn't stop within 10 seconds, Docker forces it.

#### 9. docker kill <id/name>
```
docker kill myapp
```
**Immediately and forcefully stops** a running container. Unlike `docker stop`, it gives the app **zero time** to clean up — it's an instant shutdown.

> ⚠️ Warning — Killing a container running a database can cause data corruption if it was in the middle of writing. 
> Always prefer docker stop for databases like PostgreSQL, MySQL etc. Use docker kill only when stop isn't working

#### 10. docker start <id/name>
```
docker start myapp
```
>**Restarts a stopped container.** Note this is different from `docker run` — `run` creates a brand new container from an image, while `start` brings back an **existing, already-created** container.
```
docker run    →  creates + starts  (new container)
docker start  →  just starts       (existing container)
```
#### 11. docker rm <id/name>
```
docker rm myapp
```
**Permanently deletes** a stopped container. Note: you can't remove a running container — stop it first, then remove it.
```
docker stop myapp && docker rm myapp

# Or force-remove a running container in one shot (not recommended)
docker rm -f myapp
```
#### 12. docker image ls
```
docker image ls
```
Lists all images downloaded on your machine.
```
REPOSITORY   TAG              IMAGE ID       CREATED        SIZE
postgres     16-alpine        a1b2c3d4e5f6   2 weeks ago    268MB
node         19.6-bullseye-slim  b2c3d4e5f6a7   3 weeks ago    245MB
```
Every time you do `docker run`, the image gets saved locally. This command shows everything that's sitting on your disk.

#### 13. docker container ls
```
docker container ls
```
This is just **another way to write `docker ps`** — identical output, different syntax style. Docker has two syntax styles:
```
docker ps              # shorthand (older style)
docker container ls    # explicit (newer style)
```
Same goes for images: 
```
docker images          # shorthand
docker image ls        # explicit
```
Both work - just pick one and stay consistent

#### 14. docker inspect <id/name>
```
docker inspect myapp
```
Returns **everything Docker knows** about a container in JSON format — its network settings, environment variables, mount points, restart policies, IP address, and much more.
```
[
  {
    "Id": "a3f92c1d8e45...",
    "Name": "/myapp",
    "NetworkSettings": {
      "IPAddress": "172.17.0.2",
      ...
    },
    "Config": {
      "Env": ["DATABASE_URL=postgres://..."],
      ...
    }
  }
]

```
You won't need this daily as a beginner, but it's invaluable for **debugging** — when something isn't connecting or behaving as expected, `docker inspect` tells you the truth.

#### Bonus: Common Issue - Port Already in Use

When you run : 
```
docker run -d -p 5432:5432 postgres
```
You might hit this error : 
```
Error: Bind for 0.0.0.0:5432 failed: port is already allocated
```
This means **something on your machine is already using port 5432** — most likely a locally installed PostgreSQL service running in the background.

**Step 1 - Find what's using the port**
```
sudo lsof -i :5432
```
You will see something like this : 
```
COMMAND   PID      USER  FD  TYPE DEVICE SIZE/OFF NODE NAME
postgres  1225  postgres   5u  IPv6   2678      0t0  TCP localhost:postgresql (LISTEN)
postgres  1225  postgres   6u  IPv4   2679      0t0  TCP localhost:postgresql (LISTEN)
```
This confirms a local PostgreSQL process is listening on 5432 - so Docker cannot bind to it.
**Step 2 - You have two options :**
_Option A_ - Stop the local service (recommended): 
```
sudo systemctl stop postgresql

# Verify the port is now free
sudo lsof -i :5432   # should return nothing
```
_Option B_ - Map to a different host port: 
```
docker run -d -p 5433:5432 postgres
```
This keeps your local PostgreSQL running and maps Docker's 5432 to your machine's **5433** instead. Just remember to update your `DATABASE_URL` accordingly:
```
postgres://postgres:foobarbaz@localhost:5433/postgres
#                                        ↑ changed
```

## Building Your Own Image - The Dockerfile
So far you've been running images that other people built - postgres, node, hello-world. But in real life, you need to package your own application. That's exactly what the dockerfile does.
> A Dockerfile is a plan text file containing the step-by-step instructions that tells Docker how to build your image.

Without Dockerfile: 
 > you have to manually install everything every time.

example:
1. Install OS packages
2. Install dependencies 
3. Copy project file
4. Set environment variables
5. Run the app
That's repetitive and error-prone.
But with Dockerfile: 
> You write all those steps once, and Docker automatically builds the environment whenever needed.

Think of it like a shopping list + cooking instructions combined - Docker reads it top to bottom and produces an image at the end

### Let's take an example 
Before writing the Dockerfile, we need an app. Let's keep  it simple Node.js server: 
`index.js`
```
const express = require("express")
const app = express()
app.get("/", (req, res) =>{
	return res.send("Hello from server!")
});
app.listen(3000, ()=>{
	console.log("Server running on port 3000")
})
```
`package.json`
```
{
  "name": "my-docker-app",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
Folder structure looks like : 
```
my-docker-app/
├── index.js
├── package.json
├── .dockerignore     ← we'll create this too
└── Dockerfile        ← we'll create this now
```
`dockerignore` - Create This First
Before writing the Dockerfile, create `.dockerignore` file in your project root: 
```
node_modules
.git
.env
*.log
```
This tells Docker to ignore these files and folders when copying. Without it:
```
COPY . .    
# ↑ copies YOUR local node_modules into the container
# Your local node_modules may be built for a different OS
# This overwrites what Docker installs and causes crashes
```
Think of it exactly like `.gitignore` - same idea, but different tool.

**The Dockerfile**
_Create a file named exactly `Dockerfile`(no extension) in your project root_

```dockerfile
# Pin specific version for stability
FROM node:19.6-bullseye-slim

# Set environment to production
ENV NODE_ENV production

# Set working directory inside the container
WORKDIR /usr/src/app

# Copy dependency files first (better layer caching)
COPY package*.json ./

# Install only production dependencies
RUN --mount=type=cache,target=/usr/src/app/.npm \
  npm set cache /usr/src/app/.npm && \
  npm ci --only=production

# Switch to non-root user
USER node

# Copy source code
COPY --chown=node:node ./src/ .

# Document the port
EXPOSE 3000

# Start the app
CMD [ "node", "index.js" ]
```
Now let's go through every single line.

####  FROM node:19.6-bullseye-slim
Every Dockerfile must start with `FROM` - it defines your base image. You're not starting from scratch, you're building on top of an image that already has Node.js installed.
```
node:19.6-bullseye-slim
 │      │       └── slim = stripped down OS, smaller image size
 │      └── bullseye = Debian version underneath
 └── 19.6 = exact Node.js version pinned
```
>⚠️ Always pin a **specific version** like `19.6` instead of `node:latest`. 
>Using `latest` means your image can silently change when a new Node version releases — potentially breaking your app in production with zero warning.
##### What using a base image actually give you
1. **Saves build time**
   If Docker had to install the OS and Node from the scratch every build, builds would be much slower.
2. **Reliability**
   Official base images are tested and maintained.
	If you install Node manually every time: 
		 - You might install wrong version
		 - Miss dependencies
		 - Break compatibility
#### ENV NODE_ENV production
`ENV` sets an **environment variable** inside the container. It's available at both build time and runtime — any process running inside the container can read it.
```
ENV NODE_ENV production
```

Setting `NODE_ENV=production` matters because:
- **Express.js** enables performance optimizations and disables detailed error messages
- **Many npm libraries** have different behaviour in development vs production
- It tells `npm` we only want production dependencies — no testing libraries, no bundlers, no dev tools
#### WORKDIR /usr/src/app
Sets the **working directory inside the container**. Every instruction after this runs from this path. If the folder doesn't exist, Docker creates it automatically.
```
# Without WORKDIR — files land all over /
COPY . .   ← dumps everything in root /

# With WORKDIR — clean and explicit
WORKDIR /usr/src/app
COPY . .   ← everything goes neatly to /usr/src/app

```
Think of it as doing `cd /usr/src/app` — but permanently, for the container.

#### COPY package*.json ./
Copies `package.json` and `package-lock.json` from your machine into the container's working directory.
```
package*.json  →  matches both package.json and package-lock.json
./             →  current working directory inside container (/usr/src/app)
```
>We copy **only** the package files here — not the entire source code. 
>This is intentional and critical for layer caching. We'll explain exactly why shortly.

#### RUN --mount=type=cache ... npm ci --only=production
This is the most interesting instruction in the file. Let's break it into two parts:
`Part 1` — `npm ci` vs `npm install`

```
npm ci --only=production
```
You might be used to `npm install` — but `npm ci` is strictly better for Docker:

| Feature                     | `npm install`                  | `npm ci`                     |
| --------------------------- | ------------------------------ | ---------------------------- |
| What it reads               | `package.json`                 | `package-lock.json`          |
| Versions installed          | Latest matching versions       | Exact locked versions        |
| Speed                       | Slower                         | Faster                       |
| `node_modules` behavior     | Updates existing dependencies  | Deletes and reinstalls fresh |
| Lock file changes           | Can update `package-lock.json` | Never modifies lock file     |
| Best suited for             | Development                    | Production / Docker builds   |
| Reliability                 | Less deterministic             | Fully deterministic          |
| Typical use                 | Adding/updating packages       | Clean reproducible installs  |
`--only=production` skips all `devDependencies` — testing libraries, bundlers, linters — things that have **no business being in a production image**. This keeps your image smaller and leaner.

`Part 2`— The BuildKit Cache Mount
```dockerfile
RUN --mount=type=cache,target=/usr/src/app/.npm \
  npm set cache /usr/src/app/.npm && \
  npm ci --only=production
```
This is a **BuildKit cache mount** — a powerful optimization. Here's the problem it solves:
```
Normal build (no cache mount):
Every build → npm downloads ALL packages from internet → slow ⏳

With cache mount:
First build  → downloads packages → stores them in cache
Second build → finds packages in cache → skips download → fast ⚡
```
**The  difference from regular Docker layer caching**
```
Docker layer cache   →  caches the entire node_modules layer
                     →  invalidated whenever package.json changes
                     →  forces full re-download on any dependency change

BuildKit cache mount →  caches npm's download cache (.npm folder)
                     →  even if package.json changes, already-downloaded
                        packages are NOT re-downloaded from internet
                     →  only truly new packages get fetched
```
Think of it this way:

- **Docker layer cache** = saves the cooked meal
- **BuildKit cache mount** = keeps the ingredients stocked in your fridge

>💡 BuildKit is enabled by default in Docker 23+. If you're on an older version, add `DOCKER_BUILDKIT=1` before your build command:

```bash
DOCKER_BUILDKIT=1 docker build -t my-docker-app .
```
#### User node
Switches from the default `root` user to a limited user called `node` for everything that follows.

The `node` base image comes with this user pre-created — you don't need to make it yourself. Running as root inside a container is dangerous: if an attacker exploits a vulnerability in your app, they get root access. The `node` user has only the permissions it needs and nothing more.
>⚠️ Notice that `RUN npm ci` runs **before** `USER node`. That's intentional — installing dependencies needs write access that the limited `node` user doesn't have.

#### COPY --chown=node:node ./src/ .
Copies your source code into the container **after** installing dependencies.
```dockerfile
COPY --chown=node:node ./src/ .
#    └── ownership     └── from  └── to (WORKDIR = /usr/src/app)
```
Two things happening here:

**`--chown=node:node`** — hands ownership of the copied files to the `node` user. Without this, the files would be owned by root and the `node` user couldn't read or execute them.

**Copying source code last** — your source code changes constantly. By copying it last, all the expensive layers above it (dependency installation, etc.) stay cached even when you edit a single line of code.

#### EXPOSE 3000
Documents that the container's app listens on port 3000. This is **purely informational** — it doesn't actually open any port
```dockerfile
EXPOSE 3000       ← tells humans and tooling "this app uses 3000"
```
Think of `EXPOSE` as a label on the outside of a box — it tells you what's inside, but you still need to open the box yourself.

#### CMD [ "node", "index.js" ]
The command that runs **when the container starts**. Unlike `RUN`, this doesn't execute at build time — it's baked in as the default startup instruction.
Always use the array format (called exec form):
```
CMD ["node", "index.js"]   ✅ exec form — signals handled correctly
CMD node index.js          ⚠️ shell form — app runs inside a shell,
                              signals like SIGTERM may not reach your app
```
This matters for graceful shutdown — when you run `docker stop`, it sends `SIGTERM` to your process. With shell form, the shell receives it but your Node app may not — causing forced kills instead of clean shutdowns.
#### Layer Caching — Why Order Matters
Every instruction creates a **layer**. Docker caches each layer — if nothing changed, it reuses the cached version and skips rebuilding. When a layer changes, **every layer after it rebuilds too**.
```dockerfile
FROM node:19.6-bullseye-slim     → Layer 1 ← rarely changes
ENV NODE_ENV production          → Layer 2 ← rarely changes
WORKDIR /usr/src/app             → Layer 3 ← rarely changes
COPY package*.json ./            → Layer 4 ← changes when deps change
RUN npm ci --only=production     → Layer 5 ← changes when Layer 4 changes
USER node                        → Layer 6 ← rarely changes
COPY --chown=node:node ./src/ .  → Layer 7 ← changes every code edit
CMD ["node", "index.js"]         → Layer 8 ← rarely changes
```
This ordering is deliberate:
```
You edit index.js
        ↓
Layer 7 changes (source code)
        ↓
Layers 1-6 are untouched → fully cached ✅
        ↓
npm ci is SKIPPED — uses cache ✅
        ↓
Build completes in seconds ⚡
```
If you made the classic mistake of copying everything first : 
```
# ❌ Wrong — npm ci reruns on every single code change
COPY --chown=node:node . .
RUN npm ci --only=production
```
Every time you fix a typo, Docker reinstalls all dependencies from scratch. On a large project that's minutes of waiting, every time.

#### Building Your Image
```
docker build -t my-docker-app .
#              │               └── build context (current folder)
#              └── tag — the name for your image
```
You'll see Docker executing each step:
```
[1/8] FROM node:19.6-bullseye-slim
[2/8] ENV NODE_ENV production
[3/8] WORKDIR /usr/src/app
[4/8] COPY package*.json ./
[5/8] RUN npm ci --only=production
[6/8] USER node
[7/8] COPY --chown=node:node ./src/ .
[8/8] CMD ["node", "index.js"]

Successfully built a1b2c3d4e5f6
Successfully tagged my-docker-app:latest
```
Run it second time - you'll see `CACHED` next to most steps:
```
[1/8] FROM node:19.6-bullseye-slim     → CACHED
[2/8] ENV NODE_ENV production          → CACHED
[3/8] WORKDIR /usr/src/app             → CACHED
[4/8] COPY package*.json ./            → CACHED
[5/8] RUN npm ci --only=production     → CACHED ⚡
[6/8] USER node                        → CACHED
[7/8] COPY --chown=node:node ./src/ .  → executing...
```
That's the cache working exactly as intended.
#### Running Your Image
```bash
docker run -d --name myapp -p 3000:3000 my-docker-app
```
Open  your browser at `http://localhost:3000`:
```
Hello from server!
```
Your app is now running inside a container, built from an image you created yourself.
#### Quick Reference 

| Instruction | Runs at            | What it does               |
| ----------- | ------------------ | -------------------------- |
| `FROM`      | Build time         | Sets the base image        |
| `ENV`       | Build + Runtime    | Sets environment variables |
| `WORKDIR`   | Build time         | Sets working directory     |
| `COPY`      | Build time         | Copies files into image    |
| `RUN`       | Build time         | Executes a command         |
| `USER`      | Build time         | Switches active user       |
| `EXPOSE`    | Documentation only | Documents the port         |
| `CMD`       | Start time         | Default startup command    |
**The Full Picture**
```
Your Code + Dockerfile
        ↓
  docker build
        ↓
    Image created (layered, cached, lean)
        ↓
  docker run
        ↓
  Container running → http://localhost:3000
```

### Container Registries - Storing and Sharing Your Images
Voila! You've build your own image with `docker build`. It lives on your machine right now. But what happens when : 
- Your teammate needs to run your app?
- You want to deploy it to a server?
- Your CI/CD pipeline needs to pull it?
You cannot just email a Docker image. You need registry.
#### What is a Container Registry?
A container registry is a storage catalog where you can push and pull container images from. Images are grouped into repositories - collections of related images with the same name.

Think of it like **GitHub, but for Docker images** instead of code:
```
GitHub       →  stores and shares your code
Registry     →  stores and shares your images

git push     →  sends code to GitHub
docker push  →  sends image to registry

git pull     →  gets code from GitHub
docker pull  →  gets image from registry
```
#### Registry vs Repository

These two words get confused constantly:

|Term|What it is|Example|
|---|---|---|
|**Registry**|The entire platform|Docker Hub|
|**Repository**|A collection of images inside a registry|`your-username/my-docker-app`|
|**Image**|A specific version inside a repository|`your-username/my-docker-app:1.0`|

Think of a repository as a folder where you organize your images based on projects. Each repository contains one or more container images.
```
Docker Hub (registry)
└── your-username/my-docker-app (repository)
    ├── :latest
    ├── :1.0
    └── :2.0
```
#### Anatomy of an Image URL
Every image has a full address that tells Docker exactly where to find it:
```
docker pull docker.io/your-username/my-docker-app:1.0
             │          │              │            └── tag (version)
             │          │              └── repository name
             │          └── your username/namespace
             └── registry (docker.io = Docker Hub)
```
When you just write `node` or `postgres`, Docker fills in the blanks:
```
postgres  →  docker.io/library/postgres:latest

```

#### Types of Registries 
##### 1. Public Registries
Open to everyone — anyone can push and pull images.

Docker Hub offers two categories of trusted, Docker-maintained images: Docker Official Images (DOI) — curated images for popular software, following best practices and regularly updated, and Docker Hardened Images (DHI) — minimal, secure, production-ready images with near-zero CVEs, designed to reduce attack surface and simplify compliance. 

Every image you've used so far came from here — `postgres`, `node`, `hello-world` — all Docker Official Images on Docker Hub.
##### 2. Private Registries
Restricted access — only authenticated users can push or pull. Used by companies to keep their proprietary application images internal.

There is a growing list of private registries available such as Amazon ECR, GCP Artifact Registry, GitHub Container Registry, and Docker Hub also offers a private repository feature. 
```
Public Registry   →  Docker Hub (free, open)
Private Registries →  AWS ECR, Google Artifact Registry,
                      GitHub Container Registry, Azure ACR
```
#### Hands On - Pushing to Docker Hub
Let's push the image we built in the  Dockerfile section to Docker Hub
##### step 1 - Create a Docker Hub account

Go to 👉 [https://hub.docker.com](https://hub.docker.com) and sign up for a free account.
##### Step 2 — Create a repository

1. Click **Create repository**
2. Enter a name — e.g. `my-docker-app`
3. Set visibility to **Public**
4. Click **Create**
##### Step 3 - Login from terminal 

```bash
docker login
```
Enter your Docker Hub username and password when prompted. This saves your credentials locally so future pushes don't require re-authentication.

##### Step 4 — Tag your image

Your image needs to be tagged with your Docker Hub username before pushing:
```bash
docker tag my-docker-app your-username/my-docker-app:1.0
#           │             │              │             └── version tag
#           │             │              └── repository name
#           │             └── your Docker Hub username
#           └── local image name
```
You can also tag it as `latest`:
```bash
docker tag my-docker-app your-username/my-docker-app:latest
```
>You can give one image multiple tags. `latest` is convention for the most recent stable version, while version numbers like `1.0` let you pull specific releases.

##### Step 5 — Push the image
```bash
docker push your-username/my-docker-app:1.0
```
You'll see Docker uploading each layer:
```
1.0: pushing to docker.io/your-username/my-docker-app
3a1b2c3d4e5f: Pushed    ← layer 1
6b7c8d9e0f1a: Pushed    ← layer 2
...
1.0: digest: sha256:abc123... size: 1234
```
> 💡 Notice Docker pushes **layers**, not the whole image at once. If you push a second image that shares layers with the first, those shared layers are skipped — already uploaded.

##### Step 6 — Verify on Docker Hub
Go to `hub.docker.com/r/your-username/my-docker-app` — your image is now publicly available for anyone to pull.
##### Step 7 — Pull it anywhere
On any machine with Docker installed:
```bash
docker pull your-username/my-docker-app:1.0
docker run -d -p 3000:3000 your-username/my-docker-app:1.0
```
That's the power of a registry — **build once, run anywhere**.
#### Other Registries Worth Knowing

| Registry                            | Provider  | Best for                      |
| ----------------------------------- | --------- | ----------------------------- |
| Docker Hub                          | Docker    | Open source, public images    |
| GitHub Container Registry (ghcr.io) | GitHub    | Projects already on GitHub    |
| Amazon ECR                          | AWS       | Apps deployed on AWS          |
| Google Artifact Registry            | GCP       | Apps deployed on Google Cloud |
| Azure Container Registry            | Microsoft | Apps deployed on Azure        |
For personal projects and learning — **Docker Hub is all you need**. In professional settings, we'll likely use whichever matches our cloud provider.
