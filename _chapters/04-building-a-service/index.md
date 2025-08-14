---
title: "Lesson 4: Building a Service"
description: "Learn how to build a service in C# and .NET, focusing on business logic and data handling."
chapter: 4
section: 1
order: 4
difficulty: "beginner"
duration: 20
type: "lesson"
tags:
  - "csharp"
  - "typescript"
  - "javascript"
  - "migration"
  - "programming"
  - "development"
  - "services"
category: "getting-started"
prerequisites: []
learning_objectives:
  - "Understand how to build a service in C#"
author: "James Marriott"
date: "2025-08-07"
status: "published"
featured: true
navigation:
  previous: "03-project-setup"
  next: null
layout: page
---

So far, we have a well-structured project and a set of data models. But models are just data containers; they can't perform actions on their own. We need to introduce a service layer to contain our application's business logic. In this lesson, we'll build a `PortfolioService` to handle the core operations of buying and selling stock, and we'll learn how to properly structure our solution for better maintainability.

## Structuring for Separation of Concerns

In software architecture, a service layer is a design pattern that separates the business logic of an application from its presentation (UI) and data access layers. It acts as a middleman, orchestrating operations and ensuring that business rules are followed.

This is similar to how you might organize a Node.js application with separate directories like `models/`, `services/`, and `controllers/`. The service layer concept is identical - it's where your business logic lives, separate from your API routes and database access.

To do this properly, we will create a new project dedicated to our business logic. Our solution will now have two projects:

- **Core:** Contains our domain models (the "nouns") and service interfaces (the contracts)
- **Application:** Contains our service implementations (the "verbs," or business logic)

In a TypeScript project, you might have separate directories like `src/types/` for interfaces and models, and `src/services/` for business logic. C#'s project structure is more formal - each project is essentially a separate package with its own dependencies.

### Create the Application Project

First, let's create a new class library for our services:

```bash
dotnet new classlib -n Application -o Application
```

This is like running `npm init` to create a new package, or creating a new directory in your monorepo for a specific service.

Next, add this new project to our solution file:

```bash
dotnet sln add Application/Application.csproj
```

If you look at the `ShareTradingPlatform.sln` file, you'll see that it now includes the Application project.

### Add a Project Reference

This is a critical step. Our new Application project needs to know about the models and interfaces in our Core project. We create a project reference to do this:

```bash
dotnet add Application/Application.csproj reference Core/Core.csproj
```

This is similar to importing types from another package in your monorepo, or adding a dependency in `package.json`. The project reference tells .NET that Application can import and use classes from Core, just like how you'd `import { User } from '../types'` in TypeScript.

If you look at the `Application.csproj` file, you'll see that it now has a reference to the `Core` project. This command tells the .NET build tools that Application depends on Core, allowing us to use any public class from Core inside the Application project.

## Defining the Service Interface

Before we write the actual logic, we'll define an interface. An interface in C# is like a contract. It defines what a class can do, but not how it does it. This allows for loose coupling between different parts of our application.

C# interfaces are very similar to TypeScript interfaces! They define the shape of an object or class without implementing the logic. The main difference is that C# interfaces can define method signatures, while TypeScript interfaces typically define data structures.

Create a new file in your Core project for our interface:

```csharp
// Core/IPortfolioService.cs
namespace Core;

public interface IPortfolioService
{
    Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity);
}
```

`Task` represents an asynchronous operation. It is a core component of the Task Parallel Library (TPL) and the Task-based Asynchronous Pattern (TAP), which are fundamental for writing responsive and scalable applications. The return type of `Task<T>` allows for asynchronous programming patterns, enabling methods to run in a non-blocking manner.

In the above interface, the `BuyStockAsync` method is defined as returning a `Task<Portfolio>`, indicating that it will perform its work asynchronously and return a `Portfolio` object which we previously defined in the `Core/Portfolio.cs` file.

This is similar to the TypeScript version:

```typescript
interface IPortfolioService {
  buyStock(
    userId: string,
    stockTicker: string,
    quantity: number
  ): Promise<Portfolio>;
}
```

Unlike JavaScript, which is single-threaded, C#'s async/await may be used on multiple threads concurrently allowing for more efficient use of resources and improved performance in I/O-bound applications.

**Key Points:**

- The interface lives in Core because it's part of the application's core definition, not its implementation. It defines the contract for the service without dictating how it should be implemented like TypeScript interfaces
- The method is async and returns a `Task<Portfolio>`. C#'s `Task<T>` is equivalent to TypeScript's `Promise<T>`. The `async/await` pattern works almost identically in both languages

## Updating Our Models

Our service logic will need to track the average price of a holding. Let's update our Holding model to include this. While we're at it, let's ensure our Portfolio has its own unique ID.

Update the following files in your Core project:

```csharp
// Core/Holding.cs
namespace Core;

// We add AveragePurchasePrice to track the cost basis of the holding.
public record Holding(
    Stock Stock,
    decimal Quantity,
    decimal AveragePurchasePrice
);
```

```csharp
// Core/Portfolio.cs
namespace Core;

// We add a unique Id to the portfolio itself.
public record Portfolio(
    Guid Id,
    User User,
    List<Holding> Holdings
);
```

**TypeScript Comparison:** These records are similar to TypeScript interfaces or types:

```typescript
type Holding = {
  stock: Stock;
  quantity: number;
  averagePurchasePrice: number;
};

type Portfolio = {
  id: string;
  user: User;
  holdings: Holding[];
};
```

## Implementing the Service

Now, let's create the concrete class that implements our interface. This class will contain the actual logic for buying a stock. For now, we will hard-code some data to simulate having users and stocks available.

Create a new file for the implementation in your new Application project:

```csharp
// Application/PortfolioService.cs
using Core;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace Application;

public class PortfolioService : IPortfolioService
{
    // --- In-memory data for simulation ---
    // In a real app, this would come from a database.
    private readonly List<User> _users = new()
    {
        new User(Guid.NewGuid(), "testuser", "test@example.com")
    };

    private readonly List<Portfolio> _portfolios = new();

    private readonly List<Stock> _stocks = new()
    {
        new Stock("MSFT", "Microsoft Corp", 300.50m),
        new Stock("AAPL", "Apple Inc", 175.25m)
    };
    // ------------------------------------

    public async Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity)
    {
        // 1. Find the user and stock objects from our data source.
        var user = _users.FirstOrDefault(u => u.Id == userId);
        if (user == null)
        {
            throw new ArgumentException("User not found.", nameof(userId));
        }

        var stock = _stocks.FirstOrDefault(s => s.TickerSymbol == stockTicker);
        if (stock == null)
        {
            throw new ArgumentException("Stock ticker not found.", nameof(stockTicker));
        }

        // 2. Find the user's portfolio (or create one if it doesn't exist).
        var portfolio = _portfolios.FirstOrDefault(p => p.User.Id == userId);
        if (portfolio == null)
        {
            portfolio = new Portfolio(Guid.NewGuid(), user, new List<Holding>());
            _portfolios.Add(portfolio);
        }

        // 3. Find if the user already holds this stock.
        var holding = portfolio.Holdings.FirstOrDefault(h => h.Stock.TickerSymbol == stockTicker);

        if (holding != null)
        {
            // 4a. User already owns this stock: update the existing holding by recalculating the average price.
            var existingValue = holding.Quantity * holding.AveragePurchasePrice;
            var newValue = quantity * stock.CurrentPrice;
            var newTotalQuantity = holding.Quantity + quantity;
            var newAveragePrice = (existingValue + newValue) / newTotalQuantity;

            var updatedHolding = holding with {
                Quantity = newTotalQuantity,
                AveragePurchasePrice = newAveragePrice
            };

            // Replace the old holding with the updated one.
            portfolio.Holdings.Remove(holding);
            portfolio.Holdings.Add(updatedHolding);
        }
        else
        {
            // 4b. User does not own this stock: create a new holding.
            var newHolding = new Holding(stock, quantity, stock.CurrentPrice);
            portfolio.Holdings.Add(newHolding);
        }

        // 5. Create a trade record for this transaction.
        var trade = new Trade(
            Guid.NewGuid(),
            user,
            stock,
            OrderType.Buy,
            quantity,
            DateTimeOffset.UtcNow
        );

        // In a real app, you would save the trade to a database here.
        Console.WriteLine($"Trade recorded: {trade}");

        // Return the updated portfolio. Task.FromResult wraps our result in a completed Task.
        return await Task.FromResult(portfolio);
    }
}
```

**TypeScript Equivalent Structure:** This service implementation would look similar in TypeScript:

```typescript
class PortfolioService implements IPortfolioService {
  private users: User[] = [
    /* ... */
  ];
  private portfolios: Portfolio[] = [];
  private stocks: Stock[] = [
    /* ... */
  ];

  async buyStock(
    userId: string,
    stockTicker: string,
    quantity: number
  ): Promise<Portfolio> {
    const user = this.users.find((u) => u.id === userId);
    if (!user) {
      throw new Error("User not found");
    }
    // ... rest of the logic
  }
}
```

**Key Differences:**

- C# uses `FirstOrDefault()` while TypeScript uses `find()`
- C# has explicit type declarations (`List<User>`) while TypeScript can infer types
- C# uses `Task.FromResult()` to wrap synchronous results in async methods
- C# records use the `with` syntax for immutable updates, similar to the spread operator in TypeScript

## Summary

Congratulations! You've just built the heart of our application with a professional structure. We have:

- Learned about the service layer and its importance for separating concerns
- Created a new Application project for our business logic
- Added a project reference from Application to Core
- Defined an interface (`IPortfolioService`) in Core to create a contract for our service
- Implemented the `PortfolioService` in Application with the core business logic for buying stock

You'll notice that many concepts translate directly - interfaces, async/await, dependency management, and separation of concerns work similarly in both ecosystems. The main differences are in syntax and C#'s more formal project structure.

In the next lesson, we'll look at how to expose this service through a Web API so that a front-end application could interact with it.
