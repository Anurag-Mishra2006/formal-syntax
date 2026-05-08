---
title: "Docker 101: The Gateway to DevOps"
date : 2026-05-07
featured: true
author : "Anurag Mishra"
tags : ['orms', 'prisma', 'relationship']
---
So, this post is coming after a long  time. Now the invisible man is back - with a post about Docker  where you will certainly learn something about Docker. Docker is often called the **gateway to DevOps**. 
_**Let's get into it.**_
## What is Docker?
Docker is a containerization platform that lets you package an application along with all it's dependencies, libraries, and configuration into a single unit called a container. These containers can run consistently on any machine that has Docker installed — whether it's your laptop, a colleague's machine, or a cloud server.

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
