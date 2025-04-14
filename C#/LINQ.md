### `ToDictionary`

```csharp
// If crawlRun.AssistantWebCrawlRobotsTxtSettings(a list) is not null, convert it to a dictionary
// where the key is the Domain property and the value is the RespectRobotsTxt property
var robotsTxtSettings = crawlRun.AssistantWebCrawlRobotsTxtSettings
    ?.ToDictionary(s => s.Domain, s => s.RespectRobotsTxt)
    // If crawlRun.AssistantWebCrawlRobotsTxtSettings is null, use an empty dictionary instead
    ?? new Dictionary<string, bool>();
```

A comprehensive exmaple:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main()
    {
        // Sample data
        var products = new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Category = "Electronics", Price = 1200 },
            new Product { Id = 2, Name = "Headphones", Category = "Electronics", Price = 250 },
            new Product { Id = 3, Name = "Coffee Mug", Category = "Kitchen", Price = 15 },
            new Product { Id = 4, Name = "Keyboard", Category = "Electronics", Price = 100 }
        };

        // Basic ToDictionary - using ID as key, whole object as value
        var productDictById = products.ToDictionary(
            keySelector: p => p.Id,
            elementSelector: p => p
        );

        Console.WriteLine("Dictionary with ID as key, product as value:");
        foreach (var kvp in productDictById)
        {
            Console.WriteLine($"Key: {kvp.Key}, Value: {kvp.Value.Name}, {kvp.Value.Price:C}");
        }

        // Using property as key, another property as value
        var productNamePriceDict = products.ToDictionary(
            p => p.Name,
            p => p.Price
        );

        Console.WriteLine("\nDictionary with Name as key, Price as value:");
        foreach (var kvp in productNamePriceDict)
        {
            Console.WriteLine($"Key: {kvp.Key}, Value: {kvp.Value:C}");
        }

        // Group by category then convert to dictionary
        var productsByCategory = products
            .GroupBy(p => p.Category)
            .ToDictionary(
                g => g.Key,
                g => g.ToList()
            );

        Console.WriteLine("\nDictionary with Category as key, List of products as value:");
        foreach (var category in productsByCategory)
        {
            Console.WriteLine($"Category: {category.Key}");
            foreach (var product in category.Value)
            {
                Console.WriteLine($"  - {product.Name}: {product.Price:C}");
            }
        }

        // Handling potential duplicate keys - using ToLookup instead
        var productLookupByCategory = products.ToLookup(p => p.Category);

        Console.WriteLine("\nLookup with Category as key (handles duplicates):");
        foreach (var category in productLookupByCategory)
        {
            Console.WriteLine($"Category: {category.Key}");
            foreach (var product in category)
            {
                Console.WriteLine($"  - {product.Name}: {product.Price:C}");
            }
        }

        // Null safe dictionary creation with empty fallback
        List<Product> nullProducts = null;
        var safeDictionary = nullProducts?.ToDictionary(p => p.Id, p => p.Name)
                           ?? new Dictionary<int, string>();

        Console.WriteLine($"\nSafe dictionary from null collection - Count: {safeDictionary.Count}");
    }
}

class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public decimal Price { get; set; }
}
```
