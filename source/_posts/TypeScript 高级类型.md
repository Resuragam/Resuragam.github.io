---
title: TypeScript 类型操作
author: Resuragam
top: true
hide: false
cover: true
toc: true
mathjax: false
tags:
    - TypeScript
    - 类型操作
date: 2023-06-14 23:00:15
summary: 总结 TypeScript 常用的类型操作，例如：keyof、typeof、索引类型等。
categories: TypeScript
---

## 前言

最近在学习 TypeScript 的时候，发现一个特别有意思的项目[type-challenges](https://github.com/type-challenges/type-challenges)。信心满满的我结果只会做第一个题，下定决心开始恶补一下 TypeScript 的相关知识，因此本文将介绍 TypeScript 一些常见的类型操作，方便后续学习。

> 本文将主要介绍 TypeScript 一些基本关键词的特性，帮助我们熟悉 TypeScript 的类型操作。如果你想获得更多关于 TypeScript 的相关知识，也请不要忘记 [官方文档](https://www.typescriptlang.org/)。

## Typescript 类型操作

Typescript 的类型系统十分强大，因为它允许通过类型去定义生成其他类型。TypeScript 向我们提供了各种各样的类型运算符去使用，从而生成新的类型。通过类型操作符，我们可以使用简洁的操作来表达复杂的类型与值。

### keyof

`keyof` 可以获得任意对象类型的键，并且返回根据键生成的字符串或者数字的组成**联合类型**：

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

### typeof

`typeof` 可以帮助你获取引用变量或者属性的类型

```ts
let str = 'hello';
let n: typeof str;
// let n: string
```

`typeof` 对基本类型的帮助不是很大，但是可以结合 `typeof` 获取生成其他类型模式，例如生成一个 `ReturnType` 类型：

```ts
type Predicate = (x: unknown) => boolean;
type K = ReturnType<Predicate>;
// type K = boolean
```

`ReturnType` 内置类型可以帮助我们返回一个函数类型的结果类型，但是如果我们不知道一个函数的具体类型时，可以借助 `typeof` 得到其类型。

```ts
function f() {
    return {
        x: 10,
        y: 10,
    };
}
type P = ReturnType<typeof f>;
// type P = {
//     x: number;
//     y: number;
// }
```

如果不是 `typeof` 的话，会产生 `'f' refers to a value, but is being used as a type here. Did you mean 'typeof f'?` 的错误提示。

> TypeScript 对于 `typeof` 的使用做出了严格的限制，只有作用与变量名和属性上才是合法的，从而减少语法错误。

### 索引类型

我们可以通过索引类型来获取某一个特定属性的类型。

例如：

```ts
type Person = {
    name: string;
    age: number;
};
type Age = Person['age'];
// type Age = number
```

注意上述 `'age'` 表示一个字面量类型，而索引类型接受的参数本身就是一个类型，因此我们可以使用联合类型，`keyof` ，等关键词来表示该类型。

```ts
type T1Person = Person['age' | 'name'];

type T2Person = Person[keyof Person];

type PersonKey = 'age' | 'name';
type T3Person = Person[PersonKey];
```

上述的类型都表示 `string | number`，因为传入的类型参数中，存在属性匹配。

如果索引一个不存在的属性，会产生 `Property 'alve' does not exist on type 'Person'.` 的错误。

```ts
type ErrorPerson = Person['son'];
```

除此之外，索引类型最重要的使用就是在数组元素中，通过 `number` 类型来获取数组元素的类型。我们通常会将 `typeof` 联合使用：

```ts
const MyArray = [
    { name: 'Alice', age: 15 },
    { name: 'Bob', age: 23 },
    { name: 'Eve', age: 38 },
];

type MyArrayT = (typeof MyArray)[number];
// type MyArrayT = {
//     name: string;
//     age: number;
// }

type Age1 = (typeof MyArray)[number]['age'];
// type Age = number
```

> 需要牢记的是，**索引类型传入的是一个类型参数，而不是具体的值**，否则会产生错误提示。`Type T cannot be used as an index type.T refers to a value, but is being used as a type here. Did you mean 'typeof T'?`
