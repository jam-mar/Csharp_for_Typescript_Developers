---
title: "From JavaScript/TypeScript to C#: A Developer's Migration Guide"
description: "Complete guide for JS/TS developers transitioning to C# and .NET"
chapter: 1
section: 1
order: 1
difficulty: "beginner"
duration: 15
type: "lesson"
tags:
  - "csharp"
  - "typescript"
  - "javascript"
  - "migration"
  - "programming"
  - "development"
  - "getting-started"
  - "data-types"
  - "primitives"
category: "getting-started"
prerequisites: []
learning_objectives:
  - "Understand C-type language characteristics"
  - "Compare JS/TS with C# syntax and concepts"
  - "Identify key mental shifts needed"
author: "James Marriott"
date: "2025-08-07"
status: "published"
featured: true
navigation:
  previous: null
  next: "environment-setup"
layout: page
---

In this chapter, we will explore the fundamental data types in C# and how they compare to those in JavaScript and TypeScript. Understanding these differences is crucial for a smooth transition from JS/TS to C#.

## Overview of C# Data Types

C# is a statically typed language, meaning that variable types are known at compile time. This is in contrast to JavaScript, which is dynamically typed. Here’s a quick overview of the primary data types in C#:

- **Value Types**: These include simple types (e.g., `int`, `float`, `char`, `bool`) and structs. They hold their data directly.
- **Reference Types**: These include classes, arrays, and strings. They store references to their data (objects) in memory.
- **Nullable Types**: C# provides the ability to assign `null` to value types using `Nullable<T>` or the shorthand `T?`.

## Comparison with JavaScript/TypeScript

### Primitive Types

In JavaScript, primitive types include `number`, `string`, `boolean`, `null`, `undefined`, and `symbol`. TypeScript adds type annotations but does not change the underlying JavaScript types. Here’s how they map to C#:

| JavaScript/TypeScript | C# Type                  | Notes                                                                                                                                          |
| --------------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `number`              | `int`, `float`, `double` | C# has distinct numeric types. Use `int` for integers, `float` for single-precision, and `double` for double-precision floating-point numbers. |
| `string`              | `string`                 | C# has a `string` type, similar to JavaScript. It is immutable and supports various methods for manipulation.                                  |
| `boolean`             | `bool`                   | C# uses `bool` for boolean values, similar to JavaScript's `true` and `false`.                                                                 |
| `null`                | `null`                   | C# allows `null` for reference types and nullable value types. However, `null` cannot be assigned to non-nullable value types directly.        |
| `undefined`           | N/A                      | C# does not have an equivalent to `undefined`. Uninitialized variables will throw an error.                                                    |
| `symbol`              | N/A                      | C# does not have a direct equivalent to JavaScript's `symbol`.                                                                                 |
| `bigint`              | `long`, `BigInteger`     | C# has `long` for 64-bit integers and `BigInteger` for arbitrarily large integers (from `System.Numerics`).                                    |
| `object`              | `object`                 | C# has a base `object` type, similar to JavaScript's `Object`. All types in C# derive from `object`.                                           |

### Type Inference

In TypeScript, type inference allows the compiler to automatically determine the type of a variable based on its value. C# also supports type inference with the `var` keyword, but it is limited to local variables and cannot be used for fields or properties.

```JavaScript
let number = 42;
// TypeScript infers 'number' from the value
let userName = "Hello";
// TypeScript infers 'string'
```

```csharp
var number = 42;
// C# infers 'int'
var userName = "Hello";
// C# infers 'string'
```

### Explicit Type Declaration

In TypeScript, you can explicitly declare types using annotations. C# requires explicit type declarations for variables, fields, and properties.

```TypeScript
let age: number = 30;
// Explicit type declaration
let isActive: boolean = true;
 // Explicit type declaration
```

```csharp
int age = 30;
// Explicit type declaration
string userName = "John";
// Explicit type declaration
```

### Type Safety

C# is a strongly typed language, meaning that type mismatches are caught at compile time. This is different from JavaScript, where type coercion can lead to unexpected behavior at runtime. For example, in C#, trying to assign a string to an integer variable will result in a compile-time error.

```JavaScript
let num = 42;
num = "Hello";
// No error in javascript as the runtime will coerce the type from number to string. However, this can lead to bugs and is the whole point of TypeScript's type system.
```

```TypeScript
let num: number = 42;
num = "Hello";
// TypeScript error: Type 'string' is not assignable to type 'number'
```

```csharp
int num = 42;
num = "Hello";
// C# error: Cannot implicitly convert type 'string' to 'int'
```

### Compartive Examples

#### String

In JavaScript/TypeScript, strings are immutable and can be manipulated using various methods. C# strings are also immutable and provide a rich set of methods for manipulation. While variables holding these primitive types can be reassigned to new values, the underlying values themselves cannot be altered.

```JavaScript
let greeting = "Hello, World!";
greeting = greeting.toUpperCase();
// "HELLO, WORLD!" greeting is reassigned to a new string value while the actual string value remains unchanged the greeting variable now points to a new string object. The original "Hello, World!" string still exists in memory (until garbage collection if no longer referenced).
```

```TypeScript
let greeting: string = "Hello, World!";
greeting = greeting.toUpperCase();
// "HELLO, WORLD!"
```

```csharp
string greeting = "Hello, World!";
greeting = greeting.ToUpper();
// "HELLO, WORLD!"
```

#### Boolean

In JavaScript/TypeScript, booleans are straightforward. C# also uses `bool` for boolean values, but it is more strict about type usage.

```JavaScript
let isActive = true;
isActive = false;
// Valid
```

```TypeScript
let isActive: boolean = true;
isActive = false;
// Valid
```

```csharp
bool isActive = true;
isActive = false;
// Valid
```

#### Number

In JavaScript/TypeScript, numbers can represent both integers and floating-point values. C# distinguishes between different numeric types, such as `int`, `float`, and `double`.

```JavaScript
let count = 42;
// Number type
let price = 19.99;
// Number type
let hex = 0xFF;
// Hexadecimal number
```

```TypeScript
let count: number = 42;
// Number type
let price: number = 19.99;
// Number type
let hex: number = 0xFF;
// Hexadecimal number
```

```csharp
int count = 42;
// Integer type
double price = 19.99;
// Double type
int hex = 0xFF;
// Hexadecimal integer
```

Here we can see that C# requires explicit type declarations for different numeric types, which helps prevent errors related to type coercion.

#### Numeric Types

In JavaScript, all numbers are represented as double-precision floating-point values (64-bit). There is no distinction between integers and floating-point numbers. In TypeScript, you can specify types, but they still map to JavaScript's number type. The only exception is the `bigint` type, which allows for arbitrarily large integers.

In C#, however, numeric types are more granular, allowing developers to choose the appropriate type based on the size and range of the values they need to store. This can lead to more efficient memory usage and better performance in applications.

Compared to JavaScript/TypeScript, C# has a more [extensive set of numeric types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/built-in-types), each with specific sizes and ranges. This allows for better memory management and performance optimization.

#### Integral Numeric Types

C# has several [integral numeric types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types), each with a specific size and range:

| C# Type  | Size (bits) | Range                                                   |
| -------- | ----------- | ------------------------------------------------------- |
| `sbyte`  | 8           | -128 to 127                                             |
| `byte`   | 8           | 0 to 255                                                |
| `short`  | 16          | -32,768 to 32,767                                       |
| `ushort` | 16          | 0 to 65,535                                             |
| `int`    | 32          | -2,147,483,648 to 2,147,483,647                         |
| `uint`   | 32          | 0 to 4,294,967,295                                      |
| `long`   | 64          | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| `ulong`  | 64          | 0 to 18,446,744,073,709,551,615                         |

In this example byte size is used to store a small number, to save memory. Attempting to store a larger number in a smaller type will result in a compile-time error.

```csharp
sbyte smallNumber = 100;
// Valid
smallNumber = 200;
// Compile-time error: Cannot implicitly convert type 'int' to 'sbyte'
```

#### Floating-Point Types

C# has two primary floating-point types: `float` and `double`. The `float` type is a single-precision 32-bit floating-point number, while `double` is a double-precision 64-bit floating-point number. The `decimal` type is also available for high-precision decimal numbers, often used in financial calculations.

```csharp
float pi = 3.14f;
// Single-precision
double e = 2.718281828459045;
// Double-precision
decimal price = 19.99m;
// High-precision decimal
```
