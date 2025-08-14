---
title: "Lesson 3: Solution Setup & Domain Modeling"
description: "Complete guide for JS/TS developers transitioning to C# and .NET"
chapter: 3
section: 1
order: 2
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
  - "Set up a C# project structure"
  - "Create domain models using records"
  - "Apply appropriate data types for business logic"
author: "James Marriott"
date: "2025-08-07"
status: "published"
featured: true
navigation:
  previous: "02-data-types"
  next: "04-building-a-service"
layout: page
---

Welcome to the first practical lesson in our C# journey! Here, we'll apply the concepts from the data types chapter to build the foundational structure for our Share Trading Platform. The goal is to create a well-organized project and define the core data types that will represent stocks, trades, and portfolios. Follow along as we set up a C# project and define our domain models using C# records. You can find a complete example of this project in the [Share Trading Project Repository](https://github.com/jam-mar/c-_share_trading_project_files). Each branch in the repository corresponds to a lesson in this course, allowing you to see the code evolve as we progress through the material.

## 1. Project Setup

First, we need to create a new solution and a project to hold our core business logic. We'll use the command line for this, which is a common practice in .NET development.

````bash
### Create a Solution File

A solution (.sln) file is a container for one or more projects. It helps in managing multiple related projects together.

```bash
dotnet new sln -n ShareTradingPlatform
````

### Create a Class Library

Our domain models (the data types) don't need to be an executable application themselves. Instead, they will be part of a class library (.csproj) that other projects (like a web API or a user interface) can reference later.

```bash
dotnet new classlib -n Core -o Core
```

You might notice this is different from Lesson 1, where we used `dotnet new console`. The console template creates a runnable application with a `Program.cs` entry point. Here, we're using classlib to create a library—a non-executable package of code (our models) intended to be shared and used by other projects. It doesn't have a Main method because it isn't meant to be run on its own.

### Add Project to Solution

Now, let's add our new Core project to the solution.

```bash
dotnet sln add Core/Core.csproj
```

Now, let's add our new Core project to the solution.

```bash
dotnet sln add Core/Core.csproj
```

After these steps, you will have a `ShareTradingPlatform.sln` file and a `Core` directory containing the `Core.csproj` project. This is where we will define all our data models.

## 2. Defining the Domain Models

Now for the exciting part! Let's define the C# types for our application. We'll create a separate file for each model inside the Core project. This is a standard practice that keeps our code clean and organized.

### User.cs

Every trading platform has users. We'll start with a simple User model. We use a `Guid` for the Id to ensure a unique identifier for each user.

```csharp
// Core/User.cs
namespace Core;

public record User(
        Guid Id,
        string Username,
        string Email
);
```

### Stock.cs

A stock needs a ticker symbol and a name. For its price, we'll use the `decimal` type. As we learned in the data types lesson, `decimal` is perfect for financial calculations because it avoids the small precision errors that can occur with `float` or `double`.

```csharp
// Core/Stock.cs
namespace Core;

public record Stock(
    string TickerSymbol,
    string CompanyName,
    decimal CurrentPrice
);
```

### OrderType.cs

A trade can either be a "buy" or a "sell". An enum is the perfect data type for representing a fixed set of options like this. It makes the code more readable and prevents invalid values.

```csharp
// Core/OrderType.cs
namespace Core;

public enum OrderType
{
        Buy,
        Sell
}
```

### Trade.cs

A Trade represents a completed transaction. It brings together several data types we've discussed:

- `Guid` for the unique Id of the trade
- `User` to associate the trade with a specific user (as defined in the User model)
- `Stock` to specify which stock is being traded (as defined in the Stock model)
- `OrderType` to indicate whether the trade is a buy or sell order
- `decimal` for the Quantity of shares being traded
- `DateTimeOffset` for the exact time the trade was executed

```csharp
// Core/Trade.cs
namespace Core;

public record Trade(
    Guid Id,
    User User,
    Stock Stock,
    OrderType OrderType,
    decimal Quantity,
    DateTimeOffset Timestamp
);
```

### Holding.cs

A Holding represents the quantity of a specific stock a user owns in their portfolio.

- User: The user who owns the holding
- Stock: The stock being held
- Quantity: The number of shares held

```csharp
// Core/Holding.cs
public record Holding(
    User User,
    Stock Stock,
    decimal Quantity
);

```

### Portfolio.cs

Finally, the Portfolio ties everything together for a user. It has a unique Id and contains a list of all the stocks the user currently holds. We use a `List<Holding>` to represent a collection of Holding records.

```csharp
// Core/Portfolio.cs
namespace Core;

public record Portfolio(
    User User,
    List<Holding> Holdings
);
```

## Summary

Great work! We have now:

- Set up a clean project structure with a solution and a core class library
- Defined our primary data models (User, Stock, Trade, Portfolio, Holding) using C# record types for immutability
- Applied various data types from our previous lesson (`string`, `int`, `decimal`, `Guid`, `DateTime`, `enum`, `List<>`) to represent real-world data efficiently and safely

In the next lesson, we'll start building services that use these models to perform actions like buying and selling stocks.
