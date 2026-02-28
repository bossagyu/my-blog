+++
title = 'TyeScriptにおけるEnumの使い方'
description = 'TypeScriptのEnum（列挙型）の使い方を解説。数値型・文字列型Enumの定義方法から、文字列比較の実装例、JavaScriptへのコンパイル結果まで紹介します。'
date = 2024-03-23T13:11:13+09:00
lastmod = 2024-03-23T13:11:13+09:00
draft = false
categories = ['Engineering']
tags = ['TypeScript', 'Node.js']
+++

## 概要
この記事では、TypeScriptにおけるEnumの使い方について説明します。

## Enumとは

Enum（列挙型）は、特定の値の集合を表す型です。  
多くの言語に実装されていますが、JavaScriptには存在しません。しかし、TypeScriptではEnumがサポートされています。

## Enumの使い方

以下のようにEnumを定義します。

```typescript
enum Status {
    zero,
    one,
    two
}

console.log(Status.zero); // 0
```

Enumは、デフォルトで数値を割り当てられ、0から始まります。
生成されるJavaScriptコードは以下の通りです。

```javascript
var Status;
(function (Status) {
    Status[Status["zero"] = 0] = "zero";
    Status[Status["one"] = 1] = "one";
    Status[Status["two"] = 2] = "two";
})(Status || (Status = {}));
console.log(Status.zero); // 0
```

また、enumの値を文字列で指定することもできます。

```typescript
enum Status {
    zero = 'zero',
    one = 'one',
    two = 'two'
}

console.log(Status.zero); // zero
```

文字列比較を行う場合は、以下のように記述します。

```typescript
const stringZero :String = 'zero';
const value = stringZero as StringStatus;

if (value === StringStatus.zero) {
    console.log('value is zero');
} else {
    console.log('value is not zero');
}
```

## まとめ
この記事では、TypeScriptにおけるEnumの使い方について説明しました。  
enumを利用することで、コードの可読性、保守性を向上させることができます。



