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

## 3.Top-Level Statements

C# 9.0 introduced top-level statements, allowing you to write simple programs without explicitly defining a class or Main method. This feature is particularly useful for small scripts and quick prototypes.

Here's a Program.cs file that is a complete C# program:

```csharp
Console.WriteLine("Hello World");
```

In this example, we directly use `Console.WriteLine` without wrapping it in a class or method.

## 4. Creating a project

To create a new C# project, you can use the .NET CLI. This is the primary tool for creating, building, and running .NET applications from the command line.

Open your terminal and run the following command:

```bash
dotnet new console -o MyFirstApp
```

This command tells the .NET CLI to create a new project using the console application template, and to place it in an output directory named MyFirstApp.

After running the command, you will see a new MyFirstApp folder. Let's explore the key files and folders it creates:

MyFirstApp/
├── MyFirstApp.csproj
├── Program.cs
├── obj/
└── bin/

Understanding the Generated Files
Here’s what each of these files and folders is for, with comparisons to the JavaScript/TypeScript ecosystem.

MyFirstApp.csproj
This is the project file and the heart of your C# project. Think of it as the equivalent of package.json. It's an XML file that defines crucial project settings:

Project Type: It specifies that this is a console application.

Target Framework: It declares which version of .NET the project is built for (e.g., net9.0).

Dependencies: Any external libraries (called NuGet packages) you add to your project will be listed here, similar to the dependencies section in package.json.

Program.cs

### MyFirstApp.csproj

This XML file is a .NET project file (.csproj) that defines the build configuration for your application. The root <Project> element specifies that the project uses the Microsoft.NET.Sdk, which provides the necessary tools and targets for building .NET applications.

Inside the <PropertyGroup>, several key properties are set:

<OutputType>Exe</OutputType> tells the build system to produce a console or desktop application (an executable), rather than a library.
<TargetFramework>net9.0</TargetFramework> specifies that the project targets .NET 9.0, meaning it will use the APIs and runtime features available in that version.
<ImplicitUsings>enable</ImplicitUsings> automatically includes commonly used namespaces (like System, System.Collections.Generic, etc.) in your code files, reducing the need for repetitive using statements.
<Nullable>enable</Nullable> enables nullable reference type annotations and warnings, helping you write safer code by catching potential null reference errors at compile time.
Overall, this configuration sets up a modern .NET executable project with features that improve code safety and reduce boilerplate.

### Program.cs

This is the main source code file for your application. By default, it contains the "Hello, World!" code. It's the equivalent of index.js or main.ts—the entry point where your program starts executing.

### obj/ folder

This is the "object" or intermediate build folder. When you build your project, the C# compiler uses this folder as a temporary staging area to store intermediate files it needs during the compilation process.

**Analogy**: You can think of this like temporary files generated by a bundler like Webpack or Vite before the final output is created.

**Rule of Thumb**: You can safely ignore this folder. It's managed entirely by the build tools and should be added to your .gitignore file.

### bin/ folder

This is the "binaries" or final output folder. After a successful build, the final, runnable program is placed here. It's the direct equivalent of a dist or build folder in a JavaScript project.

Inside bin/Debug/net9.0/ you'll find:

- **MyFirstApp.dll**: This is the compiled code of your application as a Dynamic Link Library. It contains all the logic from your .cs files in an intermediate language that the .NET runtime can execute.
- **MyFirstApp.exe** (on Windows): A small launcher that runs your app's .dll file.
- **MyFirstApp.deps.json**: Lists all the runtime dependencies your project needs, similar to a package-lock.json file.
- **MyFirstApp.runtimeconfig.json**: Specifies the .NET runtime version and other configuration options needed to run your application.
  Now that you understand the structure, let's run the app. Navigate into the new directory and use the dotnet run command:
