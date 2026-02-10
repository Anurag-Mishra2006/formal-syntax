---
title: "The Mathematical Syntax of Binary Search"
date: 2026-02-10
math: true
tags: ["Algorithms", "Math"]
showToc: true
draft : false
featured: true
---

## The Logic
Binary search is the quintessential example of logarithmic time complexity. Instead of searching linearly, we divide the problem space in half at each step.

The maximum number of comparisons required is defined by:
$$ T(n) = \log_2(n) + 1 $$

## The Implementation
In JavaScript, we express this formal logic as follows:

```javascript
function binarySearch(arr, target) {
    let left = 0;
    let right = arr.length - 1;
    // Implementation logic...
}