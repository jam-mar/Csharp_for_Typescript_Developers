---
title: "Lesson 1: Program Structure & Domain Models"
description: "Introduction to C# project structure and domain modeling for JS/TS developers"
chapter: 1
section: 1
order: 1
difficulty: "beginner"
duration: 10
type: "lesson"
tags:
  - "csharp"
  - "typescript"
  - "javascript"
  - "migration"
  - "programming"
  - "development"
  - "getting-started"
category: "getting-started"
prerequisites: []
learning_objectives:
  - "Understand C# project structure"
  - "Create a basic C# project"
  - "Define domain models using C# records"
author: "James Marriott"
date: "2025-08-07"
status: "published"
featured: true
navigation:
  previous: "introduction"
  next: "data-types"
layout: page
navigation:
  previous: "00-introduction"
  next: "02-data-types"
---

Let's start our journey into C# by understanding the basic structure of a C# project and how to define domain models. This lesson will set the foundation for our Share Trading Platform application.

## 1. C# Project Structure

C# projects consist of one or more files organized in a specific way. The main components include:

- **Solution**: A container for one or more projects, typically with a .sln file extension.
- **Project**: A set of related files, including source code, resources, and configuration files, usually with a .csproj file extension.
- **Source Files**: C# code files with a .cs extension, containing classes, interfaces, and other code elements.
- **Resources**: Non-code files used by the application, such as images, configuration files, and data files.
- **References**: External libraries and dependencies required by the project, managed via NuGet packages or project references.

Understanding this structure will help you navigate and manage C# projects more effectively.

## 2. C# Program Structure

A basic C# program consists of the following components:

- **Namespace**: A logical grouping of related classes and other types, similar to modules in JavaScript/TypeScript.
- **Class**: The fundamental building block of C# code, containing methods and properties.
- **Main Method**: The entry point of the application, where execution begins.
- **Using Directives**: Statements that import namespaces, allowing you to use types defined in those namespaces without fully qualifying them.

Here's a simple example of a C# program structure:

First let's take a look at JavaScript/TypeScript code that we will be migrating to C#:

```javascript
// JavaScript/TypeScript code
class Person {
  constructor(name) {
    this.name = name;
  }

  introduce() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const otto = new Person("Otto");

otto.introduce(); // Hello, my name is Otto
```

Let's remind ourselves of the key concepts from the JavaScript/TypeScript code:

- **Class**: Represents a blueprint for creating objects.
- **Constructor**: A special method for initializing new objects.
- **Method**: A function defined within a class that operates on the class's data.
  Now, let's see how we can translate this into C#:

```csharp
// C# code. System is a namespace that contains fundamental classes and base classes that define commonly-used types.
using System;

namespace Person
{
  class Person
  {
    public string Name { get; set; }
    // Constructor to initialize the name property
    public Person(string name)
    {
      Name = name;
    }
    // Method to introduce the person
    public void Introduce()
    {
      Console.WriteLine($"Hello, my name is {Name}");
    }
  }
}
class Program
{
  static void Main(string[] args)
  {
    // Create a new Person object
    Person otto = new Person("Otto");
    // Call the Introduce method
    otto.Introduce(); // Hello, my name is Otto
  }
}
```

The Main method is the entry point of the C# application, similar to the top-level code in JavaScript/TypeScript. There can only be one Main method in a C# program. Thi is where the program starts executing. For more [see](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/program-structure/main-command-line)

```csharp
// C# code demonstrating the Main method
class MainReturnValTest
{
    static int Main()
    {
        //...
        return 0;
    }
}
```
