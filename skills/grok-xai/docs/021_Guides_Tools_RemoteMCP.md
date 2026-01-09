#### [Guides](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#guides)

# Remote MCP Tools

Remote MCP Tools allow Grok to connect to external MCP (Model Context Protocol) servers, extending its capabilities with custom tools from third parties or your own implementations. Simply specify a server URL and optional configuration - xAI manages the MCP server connection and interaction on your behalf.

**xAI Python SDK Users**: Version 1.4.0 of the xai-sdk package is required to use Remote MCP Tools.

* * *

## [SDK Support](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#sdk-support)

Remote MCP tools are supported in the xAI native SDK and the OpenAI compatible Responses API.

The `require_approval` and `connector_id` parameters in the OpenAI Responses API are not currently supported.

* * *

## [Configuration](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#configuration)

To use remote MCP tools, you need to configure the connection to your MCP server in the tools array of your request.

| Parameter | Required | Description |
| --- | --- | --- |
| `server_url` | Yes | The URL of the MCP server to connect to. Only Streaming HTTP and SSE transports are supported. |
| `server_label` | No | A label to identify the server (used for tool call prefixing) |
| `server_description` | No | A description of what the server provides |
| `allowed_tool_names` | No | List of specific tool names to allow (empty allows all) |
| `authorization` | No | A token that will be set in the Authorization header on requests to the MCP server |
| `extra_headers` | No | Additional headers to include in requests |

## [Basic MCP Tool Usage](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#basic-mcp-tool-usage)

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import mcp

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",
    tools=[
        mcp(server_url="https://mcp.deepwiki.com/mcp"),
    ],
    include=["verbose_streaming"],
)

chat.append(user("What can you do with https://github.com/xai-org/xai-sdk-python?"))

is_thinking = True
for response, chunk in chat.stream():
    for tool_call in chunk.tool_calls:
        print(f"\nCalling tool: {tool_call.function.name} with arguments: {tool_call.function.arguments}")
    if response.usage.reasoning_tokens and is_thinking:
        print(f"\rThinking... ({response.usage.reasoning_tokens} tokens)", end="", flush=True)
    if chunk.content and is_thinking:
        print("\n\nFinal Response:")
        is_thinking = False
    if chunk.content and not is_thinking:
        print(chunk.content, end="", flush=True)

print("\n\nUsage:")
print(response.usage)
print(response.server_side_tool_usage)
print("\n\nServer Side Tool Calls:")
print(response.tool_calls)
```

* * *

## [Tool Enablement and Access Control](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#tool-enablement-and-access-control)

When you configure a Remote MCP Tool without specifying `allowed_tool_names`, all tool definitions exposed by the MCP server are automatically injected into the model's context.

Use the `allowed_tool_names` parameter to selectively enable only specific tools from an MCP server:

*   **Better Performance**: Reduce context overhead.
*   **Reduced Risk**: Restrict access to read-only operations.

```python
# Enable only specific tools
mcp(
    server_url="https://comprehensive-tools.example.com/mcp",
    allowed_tool_names=["search_database", "format_data"]
)
```

* * *

## [Multi-Server Support](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#multi-server-support)

```python
chat = client.chat.create(
    model="grok-4-1-fast",
    tools=[
        mcp(server_url="https://mcp.deepwiki.com/mcp", server_label="deepwiki"),
        mcp(server_url="https://your-custom-tools.com/mcp", server_label="custom"),
        mcp(server_url="https://api.example.com/tools", server_label="api-tools"),
    ],
)
```

* * *

## [Best Practices](https://docs.x.ai/docs/guides/tools/remote-mcp-tools#best-practices)

*   **Provide clear server metadata**: Use `server_label` and `server_description`.
*   **Filter tools appropriately**: Use `allowed_tool_names`.
*   **Use secure connections**: Always use HTTPS.
*   **Provide Examples**: Multi-turn prompts can help the model understand tool usage.
