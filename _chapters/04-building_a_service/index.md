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
  previous: "project-setup-and-domain-models"
  next: "unit-testing"
layout: page
---

So far, we have a well-structured project and a set of data models. But models are just data containers; they can't perform actions on their own. We need to introduce a service layer to contain our application's business logic. In this lesson, we'll build a PortfolioService to handle the core operations of buying and selling stock.

## 1. What is a Service Layer?

In software architecture, a service layer is a design pattern that separates the business logic of an application from its presentation (UI) and data access layers. It acts as a middleman, orchestrating operations and ensuring that business rules are followed.

For our trading platform, the PortfolioService will know how to:

- Calculate the total cost of a trade.
- Update a user's stock holdings after a purchase.
- Create a trade record for historical tracking.

This separation makes our code cleaner, easier to test, and more maintainable.

## 2. Defining a Service Interface

Before we write the actual logic, we'll define an interface. An interface in C# is like a contract. It defines what a class can do, but not how it does it. This is a powerful concept that allows for loose coupling between different parts of our application.

Create a new file in your Core project for our interface:

```csharp
// Core/IPortfolioService.cs
using System;
using System.Threading.Tasks;

namespace Core;

public interface IPortfolioService
{
                Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, int quantity);
}
```

**Key Points:**

- We've defined a single method, `BuyStockAsync`, that takes the `userId`, `stockTicker`, and `quantity` as input.
- The method is async and returns a `Task<Portfolio>`. This is standard practice for operations that might involve I/O (like database calls) in the future, ensuring our application remains responsive.

## 3. Implementing the Service

Now, let's create the concrete class that implements this interface. This class will contain the actual logic for buying a stock.

For now, we will hard-code some data to simulate having a database. We'll replace this with a real database connection in a later lesson.

Create a new file for the implementation:

```csharp
// Core/PortfolioService.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace Core;

public class PortfolioService : IPortfolioService
{
                // --- In-memory data for simulation ---
                // In a real app, this would come from a database.
                private readonly List<Portfolio> _portfolios = new();
                private readonly List<Stock> _stocks = new()
                {
                                new Stock("MSFT", "Microsoft Corp", 300.50m),
                                new Stock("AAPL", "Apple Inc", 175.25m)
                };
                // ------------------------------------

                public async Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, int quantity)
                {
                                // 1. Find the stock and its current price
                                var stock = _stocks.FirstOrDefault(s => s.TickerSymbol == stockTicker);
                                if (stock == null)
                                {
                                                throw new ArgumentException("Stock ticker not found.", nameof(stockTicker));
                                }

                                // 2. Find the user's portfolio (or create one if it doesn't exist)
                                var portfolio = _portfolios.FirstOrDefault(p => p.UserId == userId);
                                if (portfolio == null)
                                {
                                                portfolio = new Portfolio(Guid.NewGuid(), userId, new List<Holding>());
                                                _portfolios.Add(portfolio);
                                }

                                // 3. Find if the user already holds this stock
                                var holding = portfolio.Holdings.FirstOrDefault(h => h.StockTicker == stockTicker);

                                if (holding != null)
                                {
                                                // 4a. User already owns this stock: update the existing holding
                                                var existingQuantity = holding.Quantity;
                                                var existingValue = existingQuantity * holding.AveragePurchasePrice;
                                                var newValue = quantity * stock.CurrentPrice;

                                                var newTotalQuantity = existingQuantity + quantity;
                                                var newAveragePrice = (existingValue + newValue) / newTotalQuantity;

                                                var updatedHolding = holding with { Quantity = newTotalQuantity, AveragePurchasePrice = newAveragePrice };

                                                // Replace the old holding with the updated one
                                                portfolio.Holdings.Remove(holding);
                                                portfolio.Holdings.Add(updatedHolding);
                                }
                                else
                                {
                                                // 4b. User does not own this stock: create a new holding
                                                var newHolding = new Holding(stockTicker, quantity, stock.CurrentPrice);
                                                portfolio.Holdings.Add(newHolding);
                                }

                                // 5. Create a trade record for this transaction (for now, we don't store it)
                                var trade = new Trade(
                                                Guid.NewGuid(),
                                                userId,
                                                stockTicker,
                                                quantity,
                                                stock.CurrentPrice,
                                                DateTime.UtcNow,
                                                OrderType.Buy
                                );

                                // In a real app, you would save the trade to a database here.
                                Console.WriteLine($"Trade recorded: {trade}");

                                // Return the updated portfolio
                                return await Task.FromResult(portfolio);
                }
}
```

**Breakdown of the Logic:**

1. **Find Data**: We look up the stock and the user's portfolio from our simulated data.
2. **Handle Existing Holdings**: If the user already owns the stock, we recalculate the average purchase price and update the quantity. This is a common business rule in trading platforms. We use the `with` keyword, a feature of record types, to create a new Holding with updated values.
3. **Handle New Holdings**: If it's a new stock for the user, we simply create a new Holding and add it to their portfolio.
4. **Record the Trade**: We create a Trade object to log the transaction. For now, we just print it to the console.
5. **Return Result**: We return the modified Portfolio object. `Task.FromResult` is used to wrap our result in a completed Task, satisfying the async method signature.

## Summary

Congratulations! You've just built the heart of our application. We have:

- Learned about the service layer and its importance.
- Defined an interface (`IPortfolioService`) to create a contract for our service.
- Implemented the `PortfolioService` with the core business logic for buying stock.
- Used our data models together to perform a meaningful, real-world operation.

In the next lesson, we'll look at how to expose this service through a Web API so that a front-end application could interact with it.
