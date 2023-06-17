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

### 条件类型

条件类型可以帮助我们对类型的输入进行判断，从而输出合适的类型。

```ts
type num = 1;
type str = 'hello world';

type isNumber<T> = T extends number ? 'yes' : 'no';

type resultTrue = isNumber<num>;
// type resultTrue = "yes"
type resultFalse = isNumber<str>;
// type resultTrue = "yes"
```

条件类型类似于 JavaScript 当中的 `condition ? trueExpression : falseExpression` 三元运算符表达式。

我们可以通过 `extends` 关键词判断左侧类型与右侧类型时，是否可以满足分配条件，若满足，得获得 `true` 分支的类型，否则获得 `false` 分支上的类型。

#### 条件类型约束

同时**条件类型判断可以与泛型**一起使用，功能更加强大。

```ts
type Message<T extends number | string> = T extends number ? number : string;
function typeMessage<T extends number | string>(message: T): Message<T> {
    throw 'unimplemented';
}
const a = typeMessage('aaaaa');
// const a: string
const b = typeMessage(1);
// const b: number
const c = typeMessage(Math.random() ? 'hello' : 42);
// const c: string | number
```

条件类型也可以帮助我们**缩小类型判断**，使得类型判断更加精确可靠。

```ts
type MessageOf<T> = T['message'];
// Type '"message"' cannot be used to index type 'T'.
```

如上述代码的泛型，我们可以通过条件类型进行约束，帮助泛型 `T` 知道 `message`。

```ts
type MessageOf<T extends { message: unknown }> = T['message'];
interface Email {
    message: string;
}
type EmailMessageContent = MessageOf<Email>;
// type EmailMessageContent = string
```

我们如果需要对不存在的属性进行判断，可以将 `MessageOf` 修改为 `type MessageOf<T> = T extends { message: unknown } ? T['message'] : never;`

针对`数组`的情况，我们也可以通过条件类型得到数组的元素类型。

```ts
type Flatten<T> = T extends any[] ? T[number] : T;
type FlattenStr = Flatten<string[]>;
// type FlattenStr = string
```

#### 条件类型推断

我们可以通过条件类型进行类型约束，然后通过 `infer` 关键词进行类型推断，这是一种非常常见的操作。例如上述代码中我们可以通过条件类型在 `true` 分支上使用 `infer` 关键词得到具体类型，而不是通过索引提取相关元素。

```ts
type FlattenInfer<T> = T extends Array<infer P> ? P : T;
type FlattenInferStr = Flatten<Array<string | number>>;
// type FlattenInferStr = string | number
```

不得不感概 `infer` 关键词的强大，可以类似与一个类型变量参数，不需要关系内部逻辑的实现而可以直接提取类型。我们也可以通过条件类型和 `infer` 提取一个函数最终的返回结果类型。

```ts
type GetReturnType<T> = T extends (...args: unknown[]) => infer R ? R : never;
type Num = GetReturnType<() => number>;
// type Num = number
type Str = GetReturnType<(x: string) => string>;
// type Str = string
type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>;
// type Bools = boolean[]
```

#### 分布式条件类型

当条件类型作用于一个泛型类型，并且泛型类型是一个联合类型时，会进行分布式判断。

```ts
type ToArray<T> = T extends any ? T[] : never;
type StrArrOrNumArr = ToArray<string | number>;
// type StrArrOrNumArr = string[] | number[];
```

在执行联合类型的时候，我们会进行分配律进行判断，如果想要阻止这种行为，我们可以通过 `[]` 把 `extend` 一侧的所有关键词括起来。

```ts
type ToArrayNonDist<T> = [T] extends [any] ? T[] : never;
type StrArrOrNumArrNonDis = ToArrayNonDist<string | number>;
// type StrArrOrNumArrNonDis = (string | number)[]
```

### 映射类型

映射类型是基于索引类型的语法基础上的类型，用于声明没有提前声明的属性类型。

```ts
type OnlyBoolsAndHorses = {
    [key: string]: boolean;
};

const conforms: OnlyBoolsAndHorses = {
    del: true,
    rondeny: false,
};
```

映射类型是一种通用类型，使用 `PropertyKey` (通常使用 `keyof` 创建) 的联合来遍历键以创建类型。
如下面的代码示例， `OptionsFlags` 将 `Features` 类型的所有属性都变成 `boolean` 类型

```ts
type OptionsFlags<Type> = {
    [Property in keyof Type]: boolean;
};
type Features = {
    darkMode: () => void;
    newUserProfile: () => void;
};
type FeaturesOptions = OptionsFlags<Features>;
// type FeaturesOptions = {
//     darkMode: boolean;
//     newUserProfile: boolean;
// }
```

#### 映射修饰符

映射期间可以通过应用**两个额外的修饰符：`readonly` 和 `?` 来分别影响可变性和可读性**。
同时也可以**添加 `-` 和 `+` 来删除和添加这些修饰符**，默认为添加。

```ts
type CreateMutable<Type> = {
    -readonly [Property in keyof Type]: boolean;
};
type LockedAccount = {
    readonly id: string;
    readonly name: string;
};

type UnlockedAccount = CreateMutable<LockedAccount>;
// type UnlockedAccount = {
//     id: boolean;
//     name: boolean;
// }
```

```ts
type Concrete<Type> = {
    [Property in keyof Type]-?: Type[Property];
};

type MaybeUser = {
    id: string;
    name?: string;
    age?: number;
};

type User = Concrete<MaybeUser>;
// type User = {
//     id: string;
//     name: string;
//     age: number;
// }
```

#### 键重映射

键重映射可以通过 `as` 关键词取别名，同时也可以利用模板自变量的方式从先前的属性名称中创建新的属性名称。

```ts
type Getters<Type> = {
    [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property];
};
interface PersonMap {
    name: string;
    age: number;
}
type GettersPersonMap = Getters<PersonMap>;
// type GettersPersonMap = {
//     getName: () => string;
//     getAge: () => number;
// }
```

也可以通过条件类型来生成 `never` 来过滤掉键。

```ts
type RemoveKindFiled<Type> = {
    [Property in keyof Type as Exclude<Property, 'kind'>]: Type[Property];
};
interface Circle {
    kind: 'circle';
    radius: number;
}
type KindlessCircle = RemoveKindFiled<Circle>;
// type KindlessCircle = {
//     radius: number;
// }
```

映射类型可以联合任意类型，而不单单只是 `string | number | symbol`。

```ts
type EventConfig<Event extends { kind: string }> = {
    [E in Event as E['kind']]: (event: E) => void;
};
type SquareEvent = { kind: 'square'; x: number; y: number };
type CircleEvent = { kind: 'circle'; radius: number };
type Config = EventConfig<SquareEvent | CircleEvent>;
// type Config = {
//     square: (event: SquareEvent) => void;
//     circle: (event: CircleEvent) => void;
// }
```

### 模板字面量类型

模板字面量类型建立在字符串字面量类型之上，并且可以通过联合类型进行扩展。
通过类似于 JavaScript 当中的模板字符串语法格式，将类型进行拼接生成新的模板字面量类型。

```ts
type World = 'world';

type Greeting = `hello ${World}`;
// type Greeting = "hello world"
```

如果是联合类型，新生成的类型是每个字符串可能联合的成员，类似于交叉相乘。

```ts
type EmailLocaleIDs = 'welcome_email' | 'email_heading';
type FooterLocaleIDs = 'footer_title' | 'footer_sendoff';
type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs} _id`;
// type AllLocaleIDs = "welcome_email _id" | "email_heading _id" | "footer_title _id" | "footer_sendoff _id"
```

#### 内部字符串操作类型

##### `Uppercase<StringType>`

将字符串的每个字符转换为大写版本。

```ts
type ShoutyGreeting = Uppercase<'hello world'>;
// type ShoutyGreeting = "HELLO WORLD"
```

##### `Lowercase<StringType>`

将字符串的每个字符转换为小写版本。

```ts
type QuietGreeting = Lowercase<'HELLO WORLD'>;
// type QuietGreeting = "hello world"
```

##### `Capitalize<StringType>`

将字符串的开头字符转换为大写版本。

```ts
type CapitalizeLowercaseGreeting = Capitalize<'hello world'>;
// type CapitalizeLowercaseGreeting = "Hello, world"
```

##### `Uncapitalize<StringType>`

将字符串的开头字符转换为小写版本。

```ts
type UncomfortableGreeting = Uncapitalize<'HELLO WORLD'>;
// type UncomfortableGreeting = "hELLO WORLD"
```

### 推荐阅读

-   [TypeScript 内置类型](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html)
