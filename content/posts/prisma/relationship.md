---
title : "Relationships"
author : "Anurag Mishra"
date: 2026-02-16
---
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