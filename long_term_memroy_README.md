## 4. Chatbot with Long-Term Memory (Store)
Extends the persistent chatbot with a second, separate memory system: an 
InMemoryStore, alongside the existing InMemorySaver checkpointer.

**Two memory systems, two different scopes:**
- Checkpointer (short-term memory): scoped to a single `thread_id`. Remembers 
  conversation turns within one ongoing chat, but knows nothing across 
  different threads.
- Store (long-term memory): scoped to a namespace (e.g., `("users", user_id)`), 
  independent of `thread_id`. Remembers facts across *any* thread for that user.

The chat node checks the store for a saved user name on every turn. If none 
exists yet, it does a simple string-match check ("my name is...") on the 
latest message and saves it to the store if found. On future turns — even in 
a brand-new thread — the saved name is injected into the LLM prompt so the 
model can recall it.

Note: name extraction here is a simple string match for demonstration purposes,
not LLM-based extraction. The `namespace` is also hardcoded to a single test 
user rather than pulled from a real authentication system — both are 
simplifications appropriate for a learning exercise, not production patterns.

### How to test
1. Run the chat loop with `thread_id: "1"`. Say "my name is Emre".
2. Stop the loop. Change `thread_id` to `"2"` (a completely new, unrelated 
   conversation as far as the checkpointer is concerned).
3. Run the loop again and ask "what's my name".
4. It should correctly answer "Emre" — proving the memory came from the 
   Store, not the Checkpointer, since thread "2" has zero shared message 
   history with thread "1".