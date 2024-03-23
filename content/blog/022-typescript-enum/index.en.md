+++
title = 'Using Enums in TypeScript'
date = 2024-03-23T13:11:13+09:00
draft = false
categories = ['Development']
tags = ['TypeScript', 'Node.js']
+++

## Overview
This article explains how to use Enums in TypeScript.

## What are Enums?

Enums (enumerated types) represent a collection of related values. While many languages implement Enums, JavaScript does not. However, TypeScript supports Enums, enriching the JavaScript experience.

## How to Use Enums

Enums can be defined as follows:

```typescript
enum Status {
    zero,
    one,
    two
}

console.log(Status.zero); // 0
```

By default, Enums are assigned numerical values, starting from 0. The compiled JavaScript code would look like this:

```javascript
var Status;
(function (Status) {
    Status[Status["zero"] = 0] = "zero";
    Status[Status["one"] = 1] = "one";
    Status[Status["two"] = 2] = "two";
})(Status || (Status = {}));
console.log(Status.zero); // 0
```

You can also assign string values to Enums:

```typescript
enum Status {
    zero = 'zero',
    one = 'one',
    two = 'two'
}

console.log(Status.zero); // 'zero'
```

When comparing string values, you can write as follows:

```typescript
const stringZero: String = 'zero';
const value = stringZero as StringStatus;

if (value === StringStatus.zero) {
    console.log('value is zero');
} else {
    console.log('value is not zero');
}
```

## Summary
This article explained how to use Enums in TypeScript. By utilizing Enums, you can improve the readability and maintainability of your code.