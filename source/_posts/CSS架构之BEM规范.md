---
title: CSS架构之BEM规范
author: Resuragam
top: true
hide: false
cover: true
toc: true
mathjax: false
tags:
    - CSS
date: 2023-07-05 17:03:46
summary: 学习CSS的BEM设计模式，了解BEM架构思想
categories: CSS
---

## 前言

最近在学习公司代码的时候，发现公司的代码会将统一风格样式的组件以及工具类进行抽离，放入一个独立的仓库进行维护。而这种现象不仅仅在公司代码当中，当我们阅读一些知名开源组件库时，都会进行此类操作。例如：[Tencent-design](https://github.com/Tencent/tdesign-mobile-vue)库将 PC 端，移动端，Vue ， React 组件库的样式进行抽离，保证风格统一。而对于大型组件库而言，对于样式的要求更高，因此各类组件库会采用 BEM 思想对样式命名进行统一规范。

> 本文主要对 CSS 的 BEM 架构思想进行学习，统一了解基本的 CSS 样式封装，对 CSS 的前端工程化进一步了解。

## 什么是 BEM 规范？

`BEM` 是指：块（`block`），元素(`element`)，修饰符（`modifier`）的简写，是一种组件化的 `CSS` 命名方法与规范，由俄罗斯 Yandex 团队所提出。

```css
.block {
}

.block__element {
}

.block__element--modifier {
}
```

## 为什么我们需要 BEM 规范？

> 在计算机科学领域，只有 2 个非常难解决的问题：一个是缓存失效，而另一个则是命名。-Phil Karlton

`BEM` 可以帮助开发将用户的界面划分为多个独立的模块，使开发更加便捷高效，利于团队开发。同时减少非常混乱的 CSS 命名，提高 CSS 代码的可读性。

-   无冲突性 & 复用性高

    针对团队开发中，我们可能会因为全局的 `CSS` 作用域而感到困扰，产生 `CSS` 命名冲突的问题。而对于项目开发而言，每一个组件其实都是独一无二的，我们可以根据组件的名字作为元素，确保选择器的唯一性，同时提高代码的复用率。

-   命名简单

-   风格统一

    `BEM` 可以保证团队开发当中风格的统一，减少沟通成本。

-   结构化

    保证 `CSS` 代码的结构化，命名具有逻辑性，更利用理解记忆。

## 如何使用 BEM 规范

### 块级 block

> 块级 block 是对一个组件名进行的抽象，多个单词采用 `-` 拼接

```html
<li>
    <a class="nav-li-a is-active"></a>
</li>
<header>
    <img class="header-image" />
</header>
```

### 元素 element

> 元素 element 在模块中可能表现为多个层级，通过 `__` 进行连接，也可以采用 `-` 进行演变。

```html
<li class="nav--main__item  js-nav--main__item"><a>...</a></li>
<li class="nav--main__item  js-nav--main__item"><a>...</a></li>
<li class="nav--main__item  js-nav--main__item"><a>...</a></li>
```

### 修饰符 modifier

> 针对某个元素特有的状态做修饰，例如：颜色，大小，用途。采用 `_` 进行拼接。

```html
<header>
    <img class="header-image_logo" />
</header>
```

## 总结

针对大型项目，我们可以采用 `BEM` 规范提高代码质量和开发效率，减少 `CSS` 命名烦恼，让我们拥抱更加模块化的开发。
