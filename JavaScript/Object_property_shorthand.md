### JS object property shorthand

```js
// Traditional syntax
const assistantId = "asst_123";
const threadId = "thread_456";

const params = {
  assistantId: assistantId,
  threadId: threadId,
};

// Shorthand syntax
const params = {
  assistantId,
  threadId,
};
```

Both create the same object: `{ "assistantId": "asst_123", "threadId": "thread_456" }`
