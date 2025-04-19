```typescript
interface StartWebCrawlParams {
    assistantId: string;
    robotsTxtSettings: Record<string, boolean>;
}

export const useStartWebCrawl = (
    options?: UseMutationOptions<
        string, //Tdata the return data
        Error | ProblemDetailsError, //TError
        StartWebCrawlParams //TVarialbles
    >
): UseMutationResult<
//the use MutationResult generic is the same as useMutations
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
            options?.onSuccess?.(data, params, context);
        },
        //here we allow user to define more options when calling the hook
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
