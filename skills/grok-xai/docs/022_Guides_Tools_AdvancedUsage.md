#### [Guides](https://docs.x.ai/docs/guides/tools/advanced-usage#guides)

# Advanced Usage

This section covers mixing server-side tools and client-side tools, multi-turn conversations with agentic state preservation, and complex tool combinations to build sophisticated AI agents.

## [How It Works](https://docs.x.ai/docs/guides/tools/advanced-usage#how-it-works)
The key difference when mixing server-side and client-side tools is that **server-side tools are executed automatically by xAI**, while **client-side tools require developer intervention**:

1.   Define your client-side tools using standard function calling patterns.
2.   Include both types of tools in your request.
3.   **xAI automatically executes any server-side tools**.
4.   **When the model calls client-side tools, execution pauses** and control yields back to you.
5.   **Detect and execute client-side tool calls yourself**, then append the results to continue.

## [Understanding `max_turns` with Client-Side Tools](https://docs.x.ai/docs/guides/tools/advanced-usage#understanding-max_turns-with-client-side-tools)
`max_turns` only limits the assistant/server-side tool call turns within a single request. Client-side tool invocations act as "checkpoints" that reset the turn counter in follow-up requests.

## [Practical Example](https://docs.x.ai/docs/guides/tools/advanced-usage#practical-example)

### [Using the xAI SDK](https://docs.x.ai/docs/guides/tools/advanced-usage#using-the-xai-sdk)

```python
import os
import json
from xai_sdk import Client
from xai_sdk.chat import user, tool, tool_result
from xai_sdk.tools import web_search, get_tool_call_type

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Define client-side tool
def get_weather(city: str) -> str:
    """Get the weather for a given city."""
    return f"The weather in {city} is sunny."

# Tools array with both types
tools = [
    web_search(),
    tool(
        name="get_weather",
        description="Get the weather for a given city.",
        parameters={
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "The name of the city"}
            },
            "required": ["city"]
        },
    ),
]

model = "grok-4-1-fast"

# Create chat with state storage enabled
chat = client.chat.create(
    model=model,
    tools=tools,
    store_messages=True,
)
chat.append(user("What is the weather in the base city of the team that won the 2025 NBA championship?"))

while True:
    client_side_tool_calls = []
    for response, chunk in chat.stream():
        for tool_call in chunk.tool_calls:
            if get_tool_call_type(tool_call) == "client_side_tool":
                client_side_tool_calls.append(tool_call)
            else:
                print(f"Executing server-side tool: {tool_call.function.name}")

    if not client_side_tool_calls:
        break

    # Continue session using previous_response_id
    chat = client.chat.create(
        model=model,
        tools=tools,
        store_messages=True,
        previous_response_id=response.id,
    )

    for tool_call in client_side_tool_calls:
        args = json.loads(tool_call.function.arguments)
        result = get_weather(args["city"])
        chat.append(tool_result(result))

print(f"Final response: {response.content}")
```

### [Using the OpenAI SDK](https://docs.x.ai/docs/guides/tools/advanced-usage#using-the-openai-sdk)

```python
import os
import json
from openai import OpenAI

client = OpenAI(api_key=os.getenv("XAI_API_KEY"), base_url="https://api.x.ai/v1")

# ... Define tools and model ...

response = client.responses.create(
    model=model,
    input="What is the weather in the base city of the team that won the 2025 NBA championship?",
    tools=tools,
)

while True:
    tool_outputs = []
    for item in response.output:
        if item.type == "function_call":
            args = json.loads(item.arguments)
            weather = get_weather(args["city"])
            tool_outputs.append({
                "type": "function_call_output",
                "call_id": item.call_id,
                "output": weather,
            })

    if not tool_outputs:
        break

    response = client.responses.create(
        model=model,
        tools=tools,
        input=tool_outputs,
        previous_response_id=response.id,
    )
```

* * *

## [Multi-turn Conversations with State Preservation](https://docs.x.ai/docs/guides/tools/advanced-usage#multi-turn-conversations-with-preservation-of-agentic-state)

### [Store the Conversation History Remotely](https://docs.x.ai/docs/guides/tools/advanced-usage#store-the-conversation-history-remotely)
1. Add `store_messages=True` to the initial request.
2. Pass `previous_response_id=response.id` in follow-up requests.

### [Append the Encrypted Agentic States](https://docs.x.ai/docs/guides/tools/advanced-usage#append-the-encrypted-agentic-tool-calling-states)
For Zero Data Retention (ZDR) users:
1. Use `use_encrypted_content=True`.
2. Append the full `response` object back to the chat history.

* * *

## [Tool Combinations](https://docs.x.ai/docs/guides/tools/advanced-usage#tool-combinations)

| Use Case | Suggested Tools | Reason |
| --- | --- | --- |
| Research & Analysis | Web Search + Code Execution | Gather info then analyze/visualize |
| News & Social Media | Web Search + X Search | Comprehensive coverage |
| Insight Extraction | Search + Code Execution | Collect then compute trends |

```python
from xai_sdk.tools import web_search, x_search, code_execution
comprehensive_setup = [web_search(), x_search(), code_execution()]
```

## [Using Images in the Context](https://docs.x.ai/docs/guides/tools/advanced-usage#using-images-in-the-context)
You can include images in the initial prompt of an agentic request:
```python
chat.append(
    user(
        "Analyze the following image and find related info on the web.",
        image("https://example.com/image.jpg"),
    )
)
```
