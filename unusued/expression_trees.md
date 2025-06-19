**3. Expression Trees**

In C#, lambda expressions can be converted to expression trees, which represent code as data structures that can be analyzed and manipulated at runtime. This is particularly powerful for LINQ providers:

```csharp
// C# - Lambda as Expression Tree
Expression<Func<int, bool>> predicate = x => x > 5;
// This can be analyzed, modified, and translated (e.g., to SQL)

// JavaScript has no direct equivalent - functions are opaque
const predicate = x => x > 5; // Cannot inspect its internal logic
```

**4. Limitations and Capabilities**

JavaScript arrow functions have specific limitations:

- Cannot be used as constructors with the `new` keyword
- Do not have their own `arguments` object
- Cannot be used as generator functions

C# lambda expressions have different considerations:

- Can be converted to delegates or expression trees
- Support static modifier to prevent capturing local variables
- Can have attributes applied to them for analysis tools

#### Practical Usage Examples

**Array/Collection Processing:**

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((x) => x * 2);
const evens = numbers.filter((x) => x % 2 === 0);
const sum = numbers.reduce((acc, x) => acc + x, 0);
```

```csharp
// C# with LINQ
var numbers = new[] { 1, 2, 3, 4, 5 };
var doubled = numbers.Select(x => x * 2);
var evens = numbers.Where(x => x % 2 == 0);
var sum = numbers.Aggregate(0, (acc, x) => acc + x);
```

**Event Handling:**

```javascript
// JavaScript
button.addEventListener("click", () => {
  console.log("Button clicked!");
});
```

```csharp
// C#
button.Click += (sender, e) => {
    Console.WriteLine("Button clicked!");
};
```
