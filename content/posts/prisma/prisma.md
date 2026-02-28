---
title: "Introduction to Prisma ORM"
featured: true
---

# What are ORMs?
> ORM stands for Object-Relational Mapping, a programming technique used in software development to convert data between incompatible type systems in OOP languages.
This technique creates a "virtual object database" that can be used from within the programming language.<br>ORMs are used to abstract the complexities of the underlying databases into simpler,more easily managed objects within the code.


><b style="font-size: 30px;">😇 Let me tell you the digestiable one</b><br>
<b>ORMs</b> let you easily interact with your database without worring too much about the underlying syntax(ex. SQL)

# What is Prisma ORM?
![prisma orm](/images/prisma/intro.png)
## 1. Data Model
> In a single file, define your schema. What it looks like, what tables you have, what field each table has, how are rows related to each other.

## 2. Automated migration
> Prisma generates and runs database migrations based on changes to the Prisma schema.

## 3. Type Safety 
> Prisma generates a type-safe database client based on the Prisma schema
## 4. Auto-completion
> Gives suggestions in your respective code editor.


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

Now let's see how we define the relationships to relate the tables with each other.
# Types of Relationships: 
1. One to One
2. One to Many
3. Many to Many

## 1. One to One
```
model User {
  id      Int     @id @default(autoincrement())
  profile Profile
}
```
```
model Profile {
  id     Int  @id @default(autoincrement())
  userId Int  @unique
  user   User @relation(fields: [userId], references: [id])
}
```
One `User` can have one `Profile`, and one `Profile` must belongs to one `User`.
The `userId` in `Profile` links it to the `User`. The `@unique` constraint ensures each user has only one profile
## 2. One to Many 
> single record in one table can be associated with multiple records in another table
```
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}
```
```
model Post {
  id     Int  @id @default(autoincrement())
  userId Int
  user   User @relation(fields: [userId], references: [id])
}
```
A  `User` can have multiple  `Posts`, and each `Post` belongs to one `User`.  The `userId` in `Post` establishes this relationship, while the `posts` field in `User` is an array, allowing multiple posts per user.

## 3. Many to Many
> Multiple records in one table are associated with multiple records in another table.
```
model Post {
  id    Int    @id @default(autoincrement())
  tags  Tag[]
}
```
```
model Tag {
  id    Int    @id @default(autoincrement())
  posts Post[]
}
```
A `Post` can have multiple `tags` and a `tags` can belongs to multiple `posts`.

--- 
