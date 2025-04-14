### Entity Framework Basics

#### Setting up the model

```csharp
// Pet.cs
public class Pet
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Species { get; set; }

    // Foreign key property
    public int OwnerId { get; set; }

    // Navigation property
    public Owner Owner { get; set; }
}

// Owner.cs
public class Owner
{
    public int Id { get; set; }
    public string Name { get; set; }

    // Navigation property (collection)
    // Represents a one-to-many relationship between Owner and Pet.
    //When working with DTOs (Data Transfer Objects), you'd typically represent this using a concrete collection type like List<PetDto>.
    public ICollection<Pet> Pets { get; set; }
}

public class PetShopContext : DbContext
{
    public PetShopContext(DbContextOptions<PetShopContext> options)
        : base(options)
    { }

    public DbSet<Pet> Pets { get; set; }
    public DbSet<Owner> Owners { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Define relationships
        modelBuilder.Entity<Pet>()
            .HasOne(p => p.Owner)
            .WithMany(o => o.Pets)
            .HasForeignKey(p => p.OwnerId);
    }
}
```

#### Retrieving data with entity vs SQL

1. ##### Find all pets

```sql
SELECT * FROM Pets;
```

```
var allPets = context.Pets.ToList();
```

The ToList() executes the query and loads results into memory.

2. ##### Finding a specific pet

```sql
SELECT * FROM Pets WHERE Id = 1;
```

```csharp
var pet=context.Pets.FirstOrDefault(id=>id==1)
```

3. ##### Joining tables to get pets with their owners

```sql
SELECT p.Id, p.Name, p.Species, o.Name as OwnerName
FROM Pets p
JOIN Owners o ON p.OwnerId = o.Id;
```

```csharp
var petsWithOwners = context.Pets
    .Include(p => p.Owner)
    .ToList();
```

#### Inserting Data

1. ##### Adding a new owner

```sql
INSERT INTO Owners (Name) VALUES ('John Doe');
```

```csharp
var newOwner = new Owner { Name = "John Doe" };
context.Owners.Add(newOwner);
context.SaveChanges(); // This commits the change to the database
```

2. ##### Adding a pet with a relationship

```sql
INSERT INTO Pets (Name, Species, OwnerId) VALUES ('Rex', 'Dog', 1);
```

```
var owner = context.Owners.FirstOrDefault(o => o.Id == 1);
var newPet = new Pet { Name = "Rex", Species = "Dog", Owner = owner };
context.Pets.Add(newPet);
context.SaveChanges();
```

### eager loading vs lazy loading

```csharp
// With lazy loading enabled:
var pet = context.Pets.FirstOrDefault(p => p.Id == 1);
// First database query: SELECT * FROM Pets WHERE Id = 1

Console.WriteLine(pet.Name); // No database query, just accessing loaded data

Console.WriteLine(pet.Owner.Name);
// Second database query: SELECT * FROM Owners WHERE Id = pet.OwnerId
```

This creates the "N+1 problem" - if you were looping through 100 pets and accessing their owners, you'd make 101 database queries (1 for the pets + 100 for each owner).
In contrast, with eager loading:

```csharp
// With eager loading:
var pet = context.Pets
    .Include(p => p.Owner)
    .FirstOrDefault(p => p.Id == 1);
// One database query:
// SELECT p.*, o.*
// FROM Pets p
// LEFT JOIN Owners o ON p.OwnerId = o.Id
// WHERE p.Id = 1

Console.WriteLine(pet.Name); // No database query
Console.WriteLine(pet.Owner.Name); // No database query - data already loaded
```

Eager loading with Include() makes a single, more complex query, but it's typically more efficient than multiple round trips to the database. This is especially important in web applications where database connections are a limited resource and each query adds latency.
The main advantage of lazy loading is developer convenience and potentially loading less data when you don't need related entities. But the performance trade-offs often make eager loading the better choice for most scenarios.

### Commonly used standard LINQ method

1. ##### in memory operations

```csharp
// First use EF-specific methods to get data from the database
var pets = context.Pets.Include(p => p.Owner).ToList();

// Then use standard LINQ for in-memory operations
var groupedBySpecies = pets
    .GroupBy(p => p.Species)
    .Select(g => new {
        Species = g.Key,
        Count = g.Count(),
        Pets = g.ToList()
    })
    .ToList();
```

2. ##### Projection to Non-Entity Types

```csharp
var petSummaries = context.Pets
    .Select(p => new PetSummary {
        Id = p.Id,
        Name = p.Name,
        OwnerName = p.Owner.Name  // Can still use navigation properties in projections
    })
    .ToList();
```

3. ##### working with results

```csharp
var petsWithOwners = context.Pets.Include(p => p.Owner).ToList();

// Standard LINQ on the in-memory collection
var dogsOnly = petsWithOwners.Where(p => p.Species == "Dog");
var sortedByName = dogsOnly.OrderBy(p => p.Name);
```

#### `DbSet<T>`

Why DbContext Uses DbSet<T>

When you register your entity in the DbContext, it becomes a `DbSet<T>` because:

- DbSet is a collection type: A DbSet represents a collection of entities that can be queried from the database. It provides the basic functionality for reading, inserting, updating, and deleting entities.
- Type safety: By making it a DbSet<T> where T is your entity type, the compiler ensures you can only add entities of that specific type to that collection. This provides type safety throughout your application.
- Query capability: The DbSet<T> implements IQueryable<T> which allows you to build and execute LINQ queries against your database.
- ORM mapping: Entity Framework uses this DbSet to determine what tables to create or map to in your database schema. Each DbSet typically corresponds to a database table.

The `DbContext`acts as a unit of work and manages the connections and operations between your code and the database. By using DbSet<T>, it can track changes to your entities and save them to the database when you call SaveChanges().
