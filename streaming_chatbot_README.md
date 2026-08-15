## Streaming note
Initial attempt used stream_mode="messages" with a generator node (yield + 
consistent message id) — LangGraph docs confirm this mode is designed for 
LangChain-wrapped chat models, not raw SDK clients like google-genai used here.
Corrected version uses get_stream_writer() + stream_mode="custom", the 
documented pattern for raw/non-LangChain LLM clients.