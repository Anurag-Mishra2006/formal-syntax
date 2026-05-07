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

