---
title: "Lesson 5: Creating a Web API"
description: "Learn how to build a service in C# and .NET, focusing on business logic and data handling."
chapter: 5
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
  previous: "04-building-a-service"
  next: null
layout: page
---

Now that we have our service working, let's expose it through a Web API so front-end applications can interact with it. This is like creating Express.js routes, but in C#.

## What's a Web API?

A Web API provides HTTP endpoints that external applications can call. Instead of running console commands, clients can make HTTP requests like:

```
POST /api/portfolio/buy
{
  "userId": "123e4567-e89b-12d3-a456-426614174000",
  "stockTicker": "MSFT",
  "quantity": 10
}
```

## Setting Up the API Project

### Create the Web API Project

```bash
dotnet new webapi -n WebApi -o WebApi
dotnet sln add WebApi/WebApi.csproj
```

### Add Project References

Our API needs to use both Core and Application:

```bash
dotnet add WebApi/WebApi.csproj reference Core/Core.csproj
dotnet add WebApi/WebApi.csproj reference Application/Application.csproj
```

## Understanding the Generated Files

When you create a Web API project, .NET generates several files:

- **Program.cs** - The entry point (like `app.js` in Express)
- **appsettings.json** - Configuration file (like `.env` files)
- **Controllers/WeatherForecastController.cs** - Sample controller (like route handlers)

Let's clean up and add our own controller.

## Creating Our Portfolio Controller

### Remove the Sample Controller

```bash
rm WebApi/Controllers/WeatherForecastController.cs
rm WebApi/WeatherForecast.cs
```

### Create Our Portfolio Controller

```csharp
// WebApi/Controllers/PortfolioController.cs
using Microsoft.AspNetCore.Mvc;
using Core;
using Application;

namespace WebApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class PortfolioController : ControllerBase
{
    private readonly IPortfolioService _portfolioService;

    public PortfolioController(IPortfolioService portfolioService)
    {
        _portfolioService = portfolioService;
    }

    // We'll add endpoints here...
}
```

This is like creating a router in Express:

```typescript
const router = express.Router();
router.use("/api/portfolio", portfolioRoutes);
```

## Adding Our First Endpoint

Let's add an endpoint to buy stock:

```csharp
[HttpPost("buy")]
public async Task<ActionResult<Portfolio>> BuyStock([FromBody] BuyStockRequest request)
{
    try
    {
        var portfolio = await _portfolioService.BuyStockAsync(
            request.UserId,
            request.StockTicker,
            request.Quantity
        );

        return Ok(portfolio);
    }
    catch (ArgumentException ex)
    {
        return BadRequest(ex.Message);
    }
}
```

### Create the Request Model

We need a model for the incoming JSON:

```csharp
// WebApi/Models/BuyStockRequest.cs
namespace WebApi.Models;

public record BuyStockRequest(
    Guid UserId,
    string StockTicker,
    decimal Quantity
);
```

This is like defining a TypeScript interface for your request body:

```typescript
interface BuyStockRequest {
  userId: string;
  stockTicker: string;
  quantity: number;
}
```

## Setting Up Dependency Injection

We need to tell .NET how to create our service. Update `WebApi/Program.cs`:

```csharp
using Core;
using Application;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Register our service
builder.Services.AddScoped<IPortfolioService, PortfolioService>();

var app = builder.Build();

// Configure the HTTP request pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

This is like registering middleware in Express, but .NET handles the dependency injection automatically.

## Adding More Endpoints

Let's add a few more useful endpoints:

### Get User's Portfolio

```csharp
// Add this to PortfolioController.cs
[HttpGet("user/{userId}")]
public async Task<ActionResult<Portfolio?>> GetPortfolio(Guid userId)
{
    // We'll need to add this method to our service
    var portfolio = await _portfolioService.GetPortfolioAsync(userId);

    if (portfolio == null)
    {
        return NotFound("Portfolio not found");
    }

    return Ok(portfolio);
}
```

### Get Available Stocks

```csharp
[HttpGet("stocks")]
public async Task<ActionResult<List<Stock>>> GetAvailableStocks()
{
    var stocks = await _portfolioService.GetAvailableStocksAsync();
    return Ok(stocks);
}
```

## Updating Our Service

We need to add these new methods to our service interface and implementation:

### Update the Interface

```csharp
// Core/IPortfolioService.cs
namespace Core;

public interface IPortfolioService
{
    Task<Portfolio> BuyStockAsync(Guid userId, string stockTicker, decimal quantity);
    Task<Portfolio?> GetPortfolioAsync(Guid userId);
    Task<List<Stock>> GetAvailableStocksAsync();
    User GetTestUser(); // Keep this for testing
}
```

### Update the Implementation

Add these methods to `Application/PortfolioService.cs`:

```csharp
public async Task<Portfolio?> GetPortfolioAsync(Guid userId)
{
    var portfolio = _portfolios.FirstOrDefault(p => p.User.Id == userId);
    return await Task.FromResult(portfolio);
}

public async Task<List<Stock>> GetAvailableStocksAsync()
{
    return await Task.FromResult(_stocks);
}
```

## Complete Controller Code

Here's your complete controller with all endpoints:

<details>
<summary>Click to see the full PortfolioController</summary>

```csharp
// WebApi/Controllers/PortfolioController.cs
using Microsoft.AspNetCore.Mvc;
using Core;
using Application;
using WebApi.Models;

namespace WebApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class PortfolioController : ControllerBase
{
    private readonly IPortfolioService _portfolioService;

    public PortfolioController(IPortfolioService portfolioService)
    {
        _portfolioService = portfolioService;
    }

    [HttpPost("buy")]
    public async Task<ActionResult<Portfolio>> BuyStock([FromBody] BuyStockRequest request)
    {
        try
        {
            var portfolio = await _portfolioService.BuyStockAsync(
                request.UserId,
                request.StockTicker,
                request.Quantity
            );

            return Ok(portfolio);
        }
        catch (ArgumentException ex)
        {
            return BadRequest(ex.Message);
        }
    }

    [HttpGet("user/{userId}")]
    public async Task<ActionResult<Portfolio?>> GetPortfolio(Guid userId)
    {
        var portfolio = await _portfolioService.GetPortfolioAsync(userId);

        if (portfolio == null)
        {
            return NotFound("Portfolio not found");
        }

        return Ok(portfolio);
    }

    [HttpGet("stocks")]
    public async Task<ActionResult<List<Stock>>> GetAvailableStocks()
    {
        var stocks = await _portfolioService.GetAvailableStocksAsync();
        return Ok(stocks);
    }

    [HttpGet("test-user")]
    public ActionResult<User> GetTestUser()
    {
        var user = _portfolioService.GetTestUser();
        return Ok(user);
    }
}
```

</details>

## Testing Your API

### Start the API

```bash
cd WebApi
dotnet run
```

You should see output like:

```
Now listening on: https://localhost:7001
Now listening on: http://localhost:5001
```

### Test with Swagger

Open your browser to `https://localhost:7001/swagger` (use your actual port). You'll see a nice UI where you can test your API endpoints!

### Test with curl

Get available stocks:

```bash
curl https://localhost:7001/api/portfolio/stocks
```

Get test user:

```bash
curl https://localhost:7001/api/portfolio/test-user
```

Buy some stock:

```bash
curl -X POST https://localhost:7001/api/portfolio/buy \
  -H "Content-Type: application/json" \
  -d '{"userId":"[USER-ID-FROM-TEST-USER]","stockTicker":"MSFT","quantity":10}'
```

## Key Takeaways

- **Controllers** handle HTTP requests (like Express route handlers)
- **Dependency Injection** automatically provides services to controllers
- **Action methods** map to HTTP endpoints with attributes like `[HttpPost]`
- **Model binding** automatically converts JSON to C# objects
- **Swagger** provides automatic API documentation and testing UI

The concepts are very similar to Express.js, but .NET provides more structure and automatic features like dependency injection and API documentation.

Next up: We'll add proper error handling and validation to make our API production-ready!
