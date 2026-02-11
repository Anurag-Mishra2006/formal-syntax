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
---
👉 [Go to Installation Guide]({{< relref "posts/prisma/prisma.md" >}})
--- 
