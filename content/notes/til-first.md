---
title: "TIL: PostgreSQL column names are case-sensitive"
date: 2026-02-10
draft: false
tags: ["postgres", "til"]
---

PostgreSQL treats quoted identifiers as case-sensitive.

`user_Id` ≠ `user_id`

This bit me today 😅
