## 3. Fault-Tolerant Report Generator
A LangGraph workflow with a checkpointer (InMemorySaver) that saves state after
each completed step. Demonstrates recovery from a crash mid-execution.

To test the resume behavior:
1. Run the graph normally — it will crash intentionally partway through
   (a deliberate ZeroDivisionError was added to simulate failure).
2. Inspect `app.get_state(config)` to see which steps already completed
   and were saved before the crash.
3. Run `app.invoke(None, config=config)` with the same thread_id to resume
   from the last completed step, instead of restarting from scratch.

Note: `checkpointer = InMemorySaver()` should only be run ONCE per session —
re-running it wipes all saved history for every thread.