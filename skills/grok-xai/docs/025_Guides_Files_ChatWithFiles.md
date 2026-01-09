#### [Guides](https://docs.x.ai/docs/guides/files/chat-with-files#guides)

# Chat with Files

Once you've uploaded files, you can reference them in conversations using the `file()` helper function in the xAI Python SDK. When files are attached, the system automatically enables document search capabilities, transforming your request into an agentic workflow.

**xAI Python SDK Users**: Version 1.4.0 of the xai-sdk package is required to use the Files API.

* * *

## [Basic Chat with a Single File](https://docs.x.ai/docs/guides/files/chat-with-files#basic-chat-with-a-single-file)

Reference an uploaded file in a conversation to let the model search through it for relevant information.

```python
import os
from xai_sdk import Client
from xai_sdk.chat import user, file

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Upload a document
document_content = b"""Quarterly Sales Report - Q4 2024

Revenue Summary:
- Total Revenue: $5.2M
- Year-over-Year Growth: +18%
- Quarter-over-Quarter Growth: +7%
"""

uploaded_file = client.files.upload(document_content, filename="sales_report.txt")

# Create a chat with the file attached
chat = client.chat.create(model="grok-4-fast")
chat.append(user("What was the total revenue in this report?", file(uploaded_file.id)))

# Get the response
response = chat.sample()

print(f"Answer: {response.content}")

# Clean up
client.files.delete(uploaded_file.id)
```

* * *

## [Streaming Chat with Files](https://docs.x.ai/docs/guides/files/chat-with-files#streaming-chat-with-files)

Get real-time responses while the model searches through your documents.

```python
import os
from xai_sdk import Client
from xai_sdk.chat import user, file

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Upload a document
document_content = b"""Product Specifications:
- Model: XR-2000
- Weight: 2.5 kg
"""

uploaded_file = client.files.upload(document_content, filename="specs.txt")

# Create chat with streaming
chat = client.chat.create(model="grok-4-fast")
chat.append(user("What is the weight of the XR-2000?", file(uploaded_file.id)))

# Stream the response
is_thinking = True
for response, chunk in chat.stream():
    if response.usage.reasoning_tokens and is_thinking:
        print(f"\rThinking... ({response.usage.reasoning_tokens} tokens)", end="", flush=True)
    
    if chunk.content and is_thinking:
        print("\n\nAnswer:")
        is_thinking = False
    
    if chunk.content:
        print(chunk.content, end="", flush=True)

# Clean up
client.files.delete(uploaded_file.id)
```

* * *

## [Multiple File Attachments](https://docs.x.ai/docs/guides/files/chat-with-files#multiple-file-attachments)

Query across multiple documents simultaneously.

```python
chat.append(
    user(
        "Compare the data in these three reports.",
        file(file1.id),
        file(file2.id),
        file(file3.id),
    )
)
```

* * *

## [Multi-Turn Conversations with Files](https://docs.x.ai/docs/guides/files/chat-with-files#multi-turn-conversations-with-files)

Maintain context across multiple questions about the same documents. Use encrypted content to preserve file context efficiently.

```python
# Create a multi-turn conversation with encrypted content
chat = client.chat.create(
    model="grok-4-fast",
    use_encrypted_content=True,
)

# First turn: Ask about the employee name
chat.append(user("What is the employee's name?", file(uploaded_file.id)))
response1 = chat.sample()

# Add the response to conversation history
chat.append(response1)

# Second turn: Ask about department (context is retained)
chat.append(user("What department does this employee work in?"))
response2 = chat.sample()
```

* * *

## [Combining Files with Code Execution](https://docs.x.ai/docs/guides/files/chat-with-files#combining-files-with-code-execution)

For data analysis tasks, attach data files and enable the code execution tool.

```python
from xai_sdk.tools import code_execution

# Create chat with both file attachment and code execution
chat = client.chat.create(
    model="grok-4-fast",
    tools=[code_execution()],
)

chat.append(
    user(
        "Analyze this sales data and calculate total revenue by product.",
        file(data_file.id)
    )
)
```

* * *

## [Limitations and Considerations](https://docs.x.ai/docs/guides/files/chat-with-files#limitations-and-considerations)

*   **Model Compatibility**: Use reasoning models for complex analysis.
*   **Request Constraints**: Total file size and token limits apply.
*   **Persistent Search**: Use Collections for large, permanent knowledge bases.
