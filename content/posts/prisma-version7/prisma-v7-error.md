---

title: "Setting up Prisma 7 with Neon for Serverless Apps (Cloudflare Workers)"
date: 2026-03-12
featured: true
author: "Anurag Mishra"
tags: ['orms', 'prisma', 'serverless', 'neon', 'setup']
-------------------------------------------------------

# Prisma 7 Setup with Neon for Serverless Apps

## Hi There 👋

In this little blog I want to share how I set up **Prisma 7 with serverless platforms like Cloudflare Workers** and connected it with **Neon PostgreSQL**.

When I started learning Prisma a few weeks ago, most tutorials were still written for **Prisma 6**. Everything was going fine until I tried setting it up with **Cloudflare Workers**, and suddenly Prisma started throwing errors.

At first I thought I had messed something up. But after digging a bit deeper I realized something interesting.

**Prisma 7 had just changed a few important things.**

I’m not sure exactly when Prisma 7 was released (my guess was somewhere around the last months of 2025 😅), but one thing became clear quickly: many tutorials online were now outdated.

So after reading the docs, exploring **Prisma Accelerate**, and fixing a bunch of errors, I finally got everything working.

Instead of letting others repeat the same debugging journey, I thought:

> Why not write a small blog and save someone a few hours?

---

# Tech Stack Used

Just so we are all on the same ground, here’s what I used in this project.

* **Hono** — acts like Express but designed for edge runtimes
* **Prisma ORM**
* **Neon** — serverless PostgreSQL database
* **Zod** — for input validation (because… never trust user input 😅)
* **bcryptjs** — for hashing passwords
* **JWT** — for authentication

There are a few more libraries in the project, but these are the main ones.

---

# Step 1 — Install Required Dependencies

First install Prisma and the other packages.

```bash
npm install prisma @prisma/client
npm install @prisma/extension-accelerate
npm install hono zod bcryptjs dotenv
```

For development tools:

```bash
npm install -D prisma
```

---

# Step 2 — Initialize Prisma

Now initialize Prisma.

```bash
npx prisma init
```

This creates:

```
prisma/
   schema.prisma
.env
```

But if you're using **Prisma 7**, you will also notice something new.

```
prisma.config.ts
```

This is one of the biggest changes in Prisma 7.

---

# Step 3 — The New `prisma.config.ts`

In older Prisma versions the database URL was defined inside `schema.prisma`.

In **Prisma 7**, that configuration moved to a separate file called `prisma.config.ts`.

Example:

```ts
import "dotenv/config";
import { defineConfig, env } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: {
    path: "prisma/migrations",
  },
  datasource: {
    url: env("DIRECT_DATABASE_URL"),
  },
});
```

Here:

* `DIRECT_DATABASE_URL` is your **actual Neon database connection string**
* This URL is used mainly by **Prisma migrations**

---

# Step 4 — Schema Changes in Prisma 7

Another surprise you will encounter:

**The `url` field has been removed from `schema.prisma`.**

Older schema style:

```
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

New Prisma 7 style:

```prisma
generator client {
  provider = "prisma-client-js"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql"
}

model User {
  id        String @id @default(uuid())
  username  String
  useremail String @unique
  password  String
  posts     Post[]
}

model Post {
  id        String  @id @default(uuid())
  title     String
  content   String
  published Boolean @default(false)

  author    User @relation(fields: [authorId], references: [id])
  authorId  String
}
```

---

# Step 5 — Generate Prisma Client

After defining the schema run:

```bash
npx prisma generate
```

Then push schema to the database:

```bash
npx prisma db push
```

Or use migrations:

```bash
npx prisma migrate dev --name init
```

---

# Step 6 — The Big Serverless Problem

Serverless platforms like **Cloudflare Workers** cannot maintain persistent database connections.

If you connect directly to PostgreSQL, you might run into problems like:

* too many connections
* slow cold starts
* connection exhaustion

To solve this, Prisma introduced something called **Prisma Accelerate**.

Think of it as a **connection pooling layer optimized for serverless apps**.

---

# Step 7 — Creating an Accelerate Connection

Steps:

1. Go to the Prisma console
2. Create a new project
3. Connect your Neon database
4. Open the **Accelerate** section
5. Generate a connection string

It will look like this:

```
prisma://accelerate.prisma-data.net/?api_key=YOUR_KEY
```

Important:

This is **NOT your database URL**.

It is a **pooling proxy URL used by Prisma Client**.

---

# Step 8 — Create Prisma Client Wrapper

Create a `db.ts` file.

```ts
import { PrismaClient } from "../generated/prisma/edge"
import { withAccelerate } from "@prisma/extension-accelerate"

export const getPrisma = (databaseUrl: string) => {
  return new PrismaClient({
    accelerateUrl: databaseUrl
  }).$extends(withAccelerate())
}
```

---

# Step 9 — Use Prisma in Hono

Import the client.

```
import { getPrisma } from "./db"
```

Then inside your route:

```
const prisma = getPrisma(c.env.DATABASE_URL)
```

---

# Step 10 — Define Environment Bindings in Hono

For Cloudflare Workers, define environment bindings like this:

```ts
const app = new Hono<{
  Bindings: {
    DATABASE_URL: string
  }
}>()
```

Now you can access:

```
c.env.DATABASE_URL
```

---

# Step 11 — Add Secrets to Wrangler

Store your Accelerate URL securely:

```bash
wrangler secret put DATABASE_URL
```

And if using authentication:

```bash
wrangler secret put JWT_SECRET_KEY
```

---

# Example `.env`

For local development:

```
DIRECT_DATABASE_URL=postgresql://your-neon-db-url
DATABASE_URL=prisma://accelerate.prisma-data.net/?api_key=YOUR_KEY
```

Remember:

| Variable            | Purpose                   |
| -------------------  | ------------------------- |
| DIRECT_DATABASE_URL  | Used by Prisma migrations |
| DATABASE_URL        | Used by Prisma Client     |

---

# Common Errors You Might See

### Error

```
The datasource property `url` is no longer supported
```

Reason:
Using Prisma 6 schema format.

Fix:
Remove `url` from `schema.prisma`.

---

### Error

```
PrismaClientInitializationError
```

Reason:
Using direct database URL instead of Accelerate URL.

Fix:
Use the Accelerate connection string.

---

# Final Architecture

Your app will look something like this:

```
Cloudflare Worker
      ↓
Hono
      ↓
Prisma Client
      ↓
Prisma Accelerate
      ↓
Neon PostgreSQL
```

---

# Final Thoughts

Prisma 7 introduces some breaking changes, especially around configuration and serverless support.

But once everything is set up correctly, Prisma works really well with serverless platforms like Cloudflare Workers.

Hopefully this little guide saves you some debugging time.

If it did, feel free to share it with other developers who might be stuck with the same errors 😄
