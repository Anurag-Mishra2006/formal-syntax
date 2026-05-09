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
>[! You don't  run an image directly — you use it to create a container.]

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
