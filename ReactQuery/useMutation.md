```typescript
interface StartWebCrawlParams {
    assistantId: string;
    robotsTxtSettings: Record<string, boolean>;
}

export const useStartWebCrawl = (
    options?: UseMutationOptions<
        string, //Tdata the return data - whatever the mutationFn returns
        Error | ProblemDetailsError, //TError - the error throw by the mutationFn
        StartWebCrawlParams //TVarialbles - the variable we pass to mutationFn
    >
): UseMutationResult<
//the use MutationResult generic is the same as useMutation options
    string,
    Error | ProblemDetailsError,
    StartWebCrawlParams
> => {
    const authenticatedFetch = useCreateAuthenticatedFetch();
    const queryClient = useQueryClient();

    return useMutation({
        mutationFn: async params => {
            const {assistantId, robotsTxtSettings} = params;
            const response = await authenticatedFetch(
                ${baseUrl}/api/assistants/${assistantId}/webcrawl/start,
                {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify({robotsTxtSettings})
                }
            );
            return handleResponse(response);
        },
        onSuccess: (data, params, context) => {
            queryClient.invalidateQueries({
                queryKey: ['crawlRuns', params.assistantId]
            });
            options?.onSuccess?.(data, params, context);//user can provide his own onSuccess, and both will run
        },
        //here we allow user to define more options when calling the hook,such as onError...
        ...options
    });
};
```

### `useMutation` and `useMutationOptions`

React Query provides the useMutation hook to handle asynchronous “mutations” like posting data to a server or performing any side-effect operations that change data.

```typescript
useMutation<TData, TError, TVariables, TContext>(
  mutationFn,
  options?: UseMutationOptions<TData, TError, TVariables, TContext>
)
```
`useMuation(options)`returns:
```typescript
{
  mutate,         // function to trigger the mutation
  mutateAsync,    // Promise-based version of mutate
  isLoading,
  isError,
  error,
  data,
  ...more
}
```

the`options` we pass in is an object like:
```typescript
{
  mutationFn: (variables: TVariables) => Promise<TData>, // required
  onSuccess?: (data: TData, variables: TVariables, context: TContext) => void,
  onError?: (error: TError, variables: TVariables, context: TContext) => void,
  onSettled?: (data: TData | undefined, error: TError | null, variables: TVariables, context: TContext) => void,
  // other optional configs like retry, mutationKey...
}
```

### Example: what happends when we call `useStartWebCrawl`

`useStartWebCrawl`is a custom hook when we call it, it calls `useMutation` and returns the full `useMutaionResult` object.The object looks like somehthing like this:
```typescript
{
  mutate: (variables: StartWebCrawlParams) => string,
  mutateAsync: (variables: StartWebCrawlParams) => Promise<void>,
  isLoading: boolean,
  isError: boolean,
  error: Error | ProblemDetailsError | null,
  // ... more
}
```

### In Plain English:
- useMutation() needs to know how to perform the mutation (mutationFn)
- Optionally, you give it lifecycle callbacks like onSuccess, onError, etc.
- It gives you back an object with .mutate() and status flags so you can use it in your UI.

### About the `options?.onSuccess()`and`...options` Spread Operator
- This merges any additional configuration that the caller of your custom hook provided with the default configuration you’ve set in your hook.

- It provides flexibility: if a component using useStartWebCrawl wants to customize something (for example, provide a different onError or additional mutation options), they can pass those in as options. Your hook will then include these options along with its custom logic.

- Thanks to TypeScript and the generic signature of useMutation, TypeScript enforces that the options passed match the expected type of UseMutationOptions`<string, Error | ProblemDetailsError, StartWebCrawlParams>`.

```ts
//custom hook
import { useMutation, UseMutationOptions } from '@tanstack/react-query';

const useMyMutation = (
  options?: UseMutationOptions<string, Error, { name: string }>
) => {
  return useMutation({
    mutationFn: async ({ name }) => {
      console.log(`Sending ${name} to API...`);
      return `Hello, ${name}`;
    },

    // Custom onSuccess logic inside the hook
    onSuccess: (data, variables, context) => {
      console.log('✅ Custom hook: onSuccess');
      
      // Manually call the user-defined onSuccess, if provided
      options?.onSuccess?.(data, variables, context);
    },

    ...options // Spread the rest of the user's options (like onError, retry, etc.)
  });
};

//use in component:
const mutation = useMyMutation({
  onSuccess: (data) => {
    console.log('🙋 User: onSuccess', data);
  }
});

mutation.mutate({ name: 'Alice' });
```
output in the console:
```nginx
Sending Alice to API...
✅ Custom hook: onSuccess
🙋 User: onSuccess Hello, Alice
```

📌 Key Points:
-`options?.onSuccess?.(...)` ensures both your logic and the user's logic run.

`...options` allows users to override or extend other options (onError, retry, etc).

If you don’t call `options?.onSuccess()`, the user's callback won’t run.