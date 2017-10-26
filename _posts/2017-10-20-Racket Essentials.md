---
layout: post
title: Racket Essentials
categories: [The Racket Guide]
tags: Racket
---

## Simple Values
1. Numbers
所有的数值类型，包括分数和虚数。

2. Booleans
`#t` 代表 true，`#f` 代表 false。另外，所有的非 `#f` 值都被视为 true。

3. Strings
双引号包围的任意文本就是字符串，可以使用任何 Unicode 字符。

## Simple Definitions and Expressions
```racket
(define pie 3)   ; defines pie to be 3

(define (piece str)
  (substring str 0 pie))
```

## Lists, Iteration, and Recursion


## Pairs, Lists, and Racket Syntax
