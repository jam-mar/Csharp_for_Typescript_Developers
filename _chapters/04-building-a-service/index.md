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
  next: "05-creating-a-web-api"
layout: page
---

We've got our data models, but they're just containers. Now we need to add the logic that actually _does_ things with our data. That's where services come in.

## What's a Service Layer?

Think of a service as the "brain" of your application. It contains all the business rules and logic, separate from your UI and database code.

In TypeScript, you might organize like this:

```
src/
  models/     # data structures
  services/   # business logic
  controllers/ # API endpoints
```

C# does the same thing, but with separate projects instead of folders.

## Setting Up Our Projects

Let's create a proper structure with two projects:

**Core** → The contracts and models  
**Application** → The actual business logic

### Create the Application Project

```bash
dotnet new classlib -n Application -o Application
dotnet sln add Application/Application.csproj
```

### Link the Projects

Our Application needs to use stuff from Core:

```bash
dotnet add Application/Application.csproj reference Core/Core.csproj
```

This is like adding `import { User } from '../types'` in TypeScript.

## Define What Our Service Does

Before writing code, let's define what we want our service to do. We'll use an interface:

```csharp
// Core/IPortfolioService.cs
namespace Core;

public interface IPortfolioService
{
    Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity);
}
```

This is just like a TypeScript interface:

```typescript
interface IPortfolioService {
  buyStock(
    userId: string,
    ticker: string,
    quantity: number
  ): Promise<Portfolio>;
}
```

## Build the Service (Step by Step)

Now for the actual logic. Let's break it down into small, understandable pieces:

### 1. Set Up the Class Structure

```csharp
// Application/PortfolioService.cs
using Core;

namespace Application;

public class PortfolioService : IPortfolioService
{
    // Mock data for now (normally this comes from a database)
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
}
```

### 2. Find the User and Stock

```csharp
public async Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity)
{
    // Find the user
    var user = _users.FirstOrDefault(u => u.Id == userId);
    if (user == null)
        throw new ArgumentException("User not found.");

    // Find the stock
    var stock = _stocks.FirstOrDefault(s => s.TickerSymbol == stockTicker);
    if (stock == null)
        throw new ArgumentException("Stock not found.");
}
```

### 3. Get or Create Portfolio

```csharp
// Find user's portfolio (or create new one)
var portfolio = _portfolios.FirstOrDefault(p => p.User.Id == userId);
if (portfolio == null)
{
    portfolio = new Portfolio(Guid.NewGuid(), user, new List<Holding>());
    _portfolios.Add(portfolio);
}
```

### 4. Handle the Stock Purchase

```csharp
// Check if user already owns this stock
var existingHolding = portfolio.Holdings.FirstOrDefault(h => h.Stock.TickerSymbol == stockTicker);

if (existingHolding != null)
{
    // Update existing holding with new average price
    var oldValue = existingHolding.Quantity * existingHolding.AveragePurchasePrice;
    var newValue = quantity * stock.CurrentPrice;
    var totalQuantity = existingHolding.Quantity + quantity;
    var newAveragePrice = (oldValue + newValue) / totalQuantity;

    var updatedHolding = existingHolding with {
        Quantity = totalQuantity,
        AveragePurchasePrice = newAveragePrice
    };

    portfolio.Holdings.Remove(existingHolding);
    portfolio.Holdings.Add(updatedHolding);
}
else
{
    // Create new holding
    var newHolding = new Holding(stock, quantity, stock.CurrentPrice);
    portfolio.Holdings.Add(newHolding);
}

return portfolio;
```

## The Complete Service

Here's everything together, but now you understand each piece:

<details>
<summary>Click to see the full implementation</summary>

```csharp
// Application/PortfolioService.cs
using Core;

namespace Application;

public class PortfolioService : IPortfolioService
{
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

    public async Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity)
    {
        var user = _users.FirstOrDefault(u => u.Id == userId);
        if (user == null)
            throw new ArgumentException("User not found.");

        var stock = _stocks.FirstOrDefault(s => s.TickerSymbol == stockTicker);
        if (stock == null)
            throw new ArgumentException("Stock not found.");

        var portfolio = _portfolios.FirstOrDefault(p => p.User.Id == userId);
        if (portfolio == null)
        {
            portfolio = new Portfolio(Guid.NewGuid(), user, new List<Holding>());
            _portfolios.Add(portfolio);
        }

        var existingHolding = portfolio.Holdings.FirstOrDefault(h => h.Stock.TickerSymbol == stockTicker);

        if (existingHolding != null)
        {
            var oldValue = existingHolding.Quantity * existingHolding.AveragePurchasePrice;
            var newValue = quantity * stock.CurrentPrice;
            var totalQuantity = existingHolding.Quantity + quantity;
            var newAveragePrice = (oldValue + newValue) / totalQuantity;

            var updatedHolding = existingHolding with {
                Quantity = totalQuantity,
                AveragePurchasePrice = newAveragePrice
            };

            portfolio.Holdings.Remove(existingHolding);
            portfolio.Holdings.Add(updatedHolding);
        }
        else
        {
            var newHolding = new Holding(stock, quantity, stock.CurrentPrice);
            portfolio.Holdings.Add(newHolding);
        }

        return await Task.FromResult(portfolio);
    }
}
```

</details>

## Key Takeaways

- **Services contain business logic** - they're the "verbs" of your application
- **Interfaces define contracts** - what the service can do, not how it does it
- **Project references** work like imports in TypeScript
- **C# async/await** is very similar to JavaScript promises

The main difference from TypeScript is C#'s formal project structure, but the concepts are identical.

## Testing Our Service

Let's create a simple console app to test our service and see it in action.

### Create a Console App

```bash
dotnet new console -n TestApp -o TestApp
dotnet sln add TestApp/TestApp.csproj
```

### Add References

Our test app needs to reference both Core and Application:

```bash
dotnet add TestApp/TestApp.csproj reference Core/Core.csproj
dotnet add TestApp/TestApp.csproj reference Application/Application.csproj
```

### Write a Simple Test

Replace the contents of `TestApp/Program.cs`:

```csharp
using Core;
using Application;

// Create our service
var portfolioService = new PortfolioService();

// Test buying some stock
try
{
    // This will fail because we need a real user ID
    // Let's get the actual user ID from our mock data first
    Console.WriteLine("Testing our Portfolio Service...\n");

    // For now, let's create a simple test
    var userId = Guid.NewGuid(); // This won't work with our current setup
    var portfolio = await portfolioService.BuyStockAsync(userId, "MSFT", 10);

    Console.WriteLine($"Portfolio ID: {portfolio.Id}");
    Console.WriteLine($"User: {portfolio.User.Username}");
    Console.WriteLine($"Holdings: {portfolio.Holdings.Count}");
}
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
}
```

### Run the Test

```bash
cd TestApp
dotnet run
```

You'll get an error because we're using a random user ID, but our service only has one hardcoded user.

### Fix the Test

Let's make our service more testable by exposing the user data:

```csharp
// In Application/PortfolioService.cs, add this method:
public User GetTestUser()
{
    return _users.First(); // Return the first (and only) user
}
```

Now update your `Program.cs`:

```csharp
using Core;
using Application;

var portfolioService = new PortfolioService();

try
{
    Console.WriteLine("=== Testing Portfolio Service ===\n");

    // Get our test user
    var testUser = portfolioService.GetTestUser();
    Console.WriteLine($"Using test user: {testUser.Username}");

    // Buy some Microsoft stock
    Console.WriteLine("\n1. Buying 10 shares of MSFT...");
    var portfolio = await portfolioService.BuyStockAsync(testUser.Id, "MSFT", 10);

    Console.WriteLine($"✓ Portfolio created with ID: {portfolio.Id}");
    Console.WriteLine($"✓ Holdings count: {portfolio.Holdings.Count}");

    var holding = portfolio.Holdings.First();
    Console.WriteLine($"✓ Stock: {holding.Stock.Name} ({holding.Stock.TickerSymbol})");
    Console.WriteLine($"✓ Quantity: {holding.Quantity}");
    Console.WriteLine($"✓ Average Price: ${holding.AveragePurchasePrice}");

    // Buy more of the same stock
    Console.WriteLine("\n2. Buying 5 more shares of MSFT...");
    portfolio = await portfolioService.BuyStockAsync(testUser.Id, "MSFT", 5);

    holding = portfolio.Holdings.First();
    Console.WriteLine($"✓ Updated Quantity: {holding.Quantity}");
    Console.WriteLine($"✓ New Average Price: ${holding.AveragePurchasePrice:F2}");

    // Buy a different stock
    Console.WriteLine("\n3. Buying 20 shares of AAPL...");
    portfolio = await portfolioService.BuyStockAsync(testUser.Id, "AAPL", 20);

    Console.WriteLine($"✓ Total holdings: {portfolio.Holdings.Count}");
    foreach (var h in portfolio.Holdings)
    {
        Console.WriteLine($"  - {h.Stock.TickerSymbol}: {h.Quantity} shares @ ${h.AveragePurchasePrice:F2}");
    }
}
catch (Exception ex)
{
    Console.WriteLine($"❌ Error: {ex.Message}");
}

Console.WriteLine("\nPress any key to exit...");
Console.ReadKey();
```

### Run It Again

```bash
dotnet run
```

You should see output like:

```
=== Testing Portfolio Service ===

Using test user: testuser

1. Buying 10 shares of MSFT...
✓ Portfolio created with ID: 12345678-1234-1234-1234-123456789012
✓ Holdings count: 1
✓ Stock: Microsoft Corp (MSFT)
✓ Quantity: 10
✓ Average Price: $300.50

2. Buying 5 more shares of MSFT...
✓ Updated Quantity: 15
✓ New Average Price: $300.50

3. Buying 20 shares of AAPL...
✓ Total holdings: 2
  - MSFT: 15 shares @ $300.50
  - AAPL: 20 shares @ $175.25
```

## What We've Built

You now have a working service that:

- Creates portfolios for users
- Handles stock purchases
- Calculates average purchase prices
- Manages multiple holdings per portfolio

Next up: We'll expose this service through a Web API so front-end apps can use it!
