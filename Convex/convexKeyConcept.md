## Convex key Concepts
1. Convex is a backend-as-a-service that provides:
- Database tables (defined in schema.ts)
- Server functions (queries, mutations, actions)
- Real-time data syncing
2. Main function types:
    ```
    // Read data
    query({
      // Define expected arguments
      args: {
        worldId: v.id('worlds') 
      },
      // Function implementation
      handler: async (ctx, args) => {
        // Use ctx.db to access database
      }
    });

    // Write data
    mutation({
      args: {...},
      handler: async (ctx, args) => {
        // Modify database
      }
    });
    ```