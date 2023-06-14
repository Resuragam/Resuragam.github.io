---
title: TypeScript 高级类型
author: Resuragam
top: true
hide: false
cover: true
toc: true
mathjax: false
tags:
    - TypeScript
    - 高级类型
date: 2023-06-14 23:00:15
summary: 总结 TypeScript 常用的高级内置类型，例如：Pick、Record、Omit 等。
categories: TypeScript
---

## 前言

最近在学习 TypeScript 的时候，发现一个特别有意思的项目[type-challenges](https://github.com/type-challenges/type-challenges)。信心满满的我结果只会做第一个题，下定决心开始恶补一下 TypeScript 的相关知识，因此本文将介绍 TypeScript 一些常见的高级类型工具，方便后续开发（装杯）。

> 本文分为三个部分，第一个部分将介绍 TypeScript 一些基本关键词的特性，帮助我们熟悉 TypeScript 的类型操作，第二个部分介绍内置工具类型以及原理，第三部分将结合前面两个部分的知识，学习如何使用关键词和内置类型开发自定义工具类型。如果你想获得更多关于 TypeScript 的相关知识，也请不要忘记 [官方文档](https://www.typescriptlang.org/)。

## Typescript 类型操作

Typescript 的类型系统十分强大，因为它允许通过类型去定义生成其他类型。TypeScript 向我们提供了各种各样的类型运算符去使用，从而生成新的类型。通过类型操作符，我们可以使用简洁的操作来表达复杂的类型与值。

### keyof

keyof 可以获得任意对象类型的键，并且返回根据键生成的字符串或者数字的组成**联合类型**：

```ts
type Point = {
    x: number;
    y: number;
};
type P = keyof Point;
// P 与 type P = "x" | "y" 等价
```

如果该类型存在 `string` 或者 `number` 类型的键，那么 `keyof` 会将其返回：

```ts
type ArrayIs = {
    [N: number]: unknown;
};
type A = keyof ArrayIs;
// type A = number

type MapIs = {
    [K: string]: unknown;
};
type M = keyof MapIs;
// type M = string | number
```

> `M` 是 `string | number` 是**因为 JavaScript 的键总是会转化为一个 `string`** ,因此 `obj[0]` 和 `obj["0"]` 是相等的。

```ts
class EG {
    private name: string;
    public age: number;
}
type TEG = keyof EG;
// type TEG = 'age'
```

> `keyof` 只能查询该类型上的**公有属性**的 `key` 的联合。
