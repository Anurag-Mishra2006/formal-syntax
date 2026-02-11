---
title: " Installation Guide"
date: 2026-02-11
type: "prisma"
---

![Prisma Image](/images/prisma/prisma.png)

# Getting Started with Prisma
## 1. Install Prisma
> First, install Prisma as a development dependency: 
```
npm install prisma --save-dev

```
## 2. Initailize Prisma
> Initialize the fresh prisma project.
```
npx prisma init 

```
![image](/images/prisma/initialise.png)
## 3. Set Up a PostgreSQL Database
> For a side project, you can get a free PostgreSQL database from Neon.
1. Sign up at neon.tech.
2. Create a new project and get the DATABASE_URL.
3. Replace the default DATABASE_URL in the .env file with your Neon database URL.

> You can use Docker, for getting Database url if you don't have psql locally. Here are the command for you 👇
### check for docker 
```
docker --version

```
if you don't have you can download it from [here](https://www.docker.com/products/docker-desktop/)

### Start a Database 
```
docker run -e POSTGRES_PASSWORD=mysecretpassword -d -p 5432:5432 postgres
```
> The connection string for this postgres would be: 
```
postgresql://postgres:mysecretpassword@localhost:5432/postgres
```
### You can see the tables and entities of the your local database 
1. Check for live docker container 
```
docker ps

```
2. Copy the ID of your postgres and use it to enter the container 

```
docker exec -it <ID> /bin/bash 
```
3. Connect to a PostgreSQL database from your host machine 
```
psql -U postgres -d postgres

```
4. Check the tables
```
\dt

```

> ℹ️ **Note**
>
> Replace DATABASE_URL with your database url.

## 4. Define a Model
> After initializing Prisma, define a table (model) inside the schema.prisma file.
Here's an example: 
```
model User {
  id       Int       @id @default(autoincrement())
  email    String    @unique
  name     String?
  profile  Profile?
}
```
## 5. Migrate the Schema
Once the schema is defined, the next step is to migrate it to the database. This converts the schema into SQL queries and executes them to create the actual tables in the database.
```
npx prisma migrate dev --name init
```
## 6. Generate the Prisma Client 
After migrating, you need to interact with the database in you application.
Prisma provides a client to connect with your database and perform queries.

Generate the Prisma Client by running:
```
npx prisma generate

```
## 7. Using the Prisma Client
Now, you can import and use the Prisma client in your application: 
```
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();
```
### Example: Inserting data in the User table.
```
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

async function insertUser(username: string, password: string, firstName: string, lastName: string) {
  const res = await prisma.user.create({
    data: {
        username,
        password,
        firstName,
        lastName
    }
  })
  console.log(res);
}

insertUser("admin1", "123456", "xyz", "abc")
```
---
👉 [Learn about Relationships]({{< relref "posts/prisma/relationship.md" >}})
--- 