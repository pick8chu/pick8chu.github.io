---
title: langchain
last_updated: Dec 5, 2024
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: langchain.html

---

## [langchain with sqlite](https://python.langchain.com/docs/integrations/memory/sqlite/)
- This is to use the sqlite memory as a context db.
- It contain questions and answers, and put it as a prompt when requesting a new question. Thus, in terms of token size, it doesn't seem very smart way.

```python

# Initialize chat message history with a unique session ID
session_id = "session_id"
connection_string = "sqlite:///path_to_your_db.sqlite"  # Replace with your SQLite DB path
config = {"configurable": {"session_id": session_id}}

chat = ChatOpenAI(
    openai_api_key=OPENAI_API_KEY,
    openai_api_base="https://openrouter.ai/api/v1",
    model="gpt-4o-mini",
)

chain = prompt | chat

chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: SQLChatMessageHistory(session_id=session_id, connection_string=connection_string),
    input_messages_key="question",
    history_messages_key="history",
)

def print_message_history(session_id = session_id):
    text = f"SQLChatMessageHistory"
    print(SQLChatMessageHistory(
        session_id=session_id, connection_string=connection_string
    ))
    
def ask_with_memory(user_message):
    response = chain_with_history.invoke({"question": user_message}, config={"configurable": {"session_id": session_id}})

    # Logging the conversation history
    log_message("------------------------------------------------------------------")
    print_message_history()
    log_message("------------------------------------------------------------------")
    log_message(f"------------------------------------------------------------------")
    log_message(f"question: {user_message}")
    log_message(f"response: {response.content}")
    log_message(f"------------------------------------------------------------------")

    return response.content
```