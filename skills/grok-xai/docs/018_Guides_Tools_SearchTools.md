#### [Guides](https://docs.x.ai/docs/guides/tools/search-tools#guides)

# Search Tools

Agentic search represents one of the most compelling applications of agentic tool calling, with `grok-4-1-fast` specifically trained to excel in this domain. Leveraging its speed and reasoning capabilities, the model iteratively calls search tools—analyzing responses and making follow-up queries as needed—to seamlessly navigate web pages and X posts, uncovering difficult-to-find information or insights that would otherwise require extensive human analysis.

**xAI Python SDK Users**: Version 1.3.1 of the xai-sdk package is required to use the agentic tool calling API.

* * *

## [Available Search Tools](https://docs.x.ai/docs/guides/tools/search-tools#available-search-tools)

You can use the following server-side search tools in your request:

*   **Web Search** - allows the agent to search the web and browse pages
*   **X Search** - allows the agent to perform keyword search, semantic search, user search, and thread fetch on X

You can customize which tools are enabled in a given request by listing the needed tools in the `tools` parameter in the request.

| Tool | xAI SDK | OpenAI Responses API |
| --- | --- | --- |
| Web Search | `web_search` | `web_search` |
| X Search | `x_search` | `x_search` |

* * *

## [Retrieving Citations](https://docs.x.ai/docs/guides/tools/search-tools#retrieving-citations)

The search tools provide two types of citation information:

*   **All Citations** (`response.citations`): A list of all source URLs encountered during the search process. Always returned by default.
*   **Inline Citations** (`response.inline_citations`): Markdown-style links (e.g., `[[1]](https://x.ai/news)`) embedded directly into the response text at the points where the model references sources, with structured metadata available. Must be explicitly enabled.

### [All Citations](https://docs.x.ai/docs/guides/tools/search-tools#all-citations)
Access the complete list of encountered sources via `response.citations`. This is always returned by default.

Note that not every URL will necessarily be referenced in the final answer—the agent may examine sources and determine they aren't relevant.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[web_search()],
    include=["verbose_streaming"],
)

chat.append(user("What is xAI?"))

is_thinking = True
for response, chunk in chat.stream():
    # View the server-side tool calls as they are being made in real-time
    for tool_call in chunk.tool_calls:
        print(f"\nCalling tool: {tool_call.function.name} with arguments: {tool_call.function.arguments}")
    if response.usage.reasoning_tokens and is_thinking:
        print(f"\rThinking... ({response.usage.reasoning_tokens} tokens)", end="", flush=True)
    if chunk.content and is_thinking:
        print("\n\nFinal Response:")
        is_thinking = False
    if chunk.content and not is_thinking:
        print(chunk.content, end="", flush=True)

print("\n\nCitations:")
print(response.citations)
print("\n\nUsage:")
print(response.usage)
print(response.server_side_tool_usage)
print("\n\nServer Side Tool Calls:")
print(response.tool_calls)
```

### [Inline Citations](https://docs.x.ai/docs/guides/tools/search-tools#inline-citations)
To get citations embedded directly in the response text with position tracking, enable inline citations:

Python

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",
    include=["inline_citations"],  # Enable inline citations
    tools=[web_search()],
)

chat.append(user("What are the latest updates from xAI?"))
response = chat.sample()

# Response text contains markdown citations like [[1]](url)
print(response.content)

# Access structured citation data
for citation in response.inline_citations:
    if citation.HasField("web_citation"):
        print(f"[{citation.id}] {citation.web_citation.url}")
```

When enabled, the response text will contain citations like:

> The latest announcements from xAI date back to November 19, 2025.[[1]](https://x.ai/news/)[[2]](https://x.ai/)

Enabling inline citations does not guarantee the model will cite sources on every answer—the model decides when citations are appropriate based on the query.

For complete details on citations, including the `InlineCitation` object structure, position indices, and streaming behavior, see the [Citations section](https://docs.x.ai/docs/guides/tools/overview#citations) in the overview.

* * *

## [Applying Search Filters to Control Agentic Search](https://docs.x.ai/docs/guides/tools/search-tools#applying-search-filters-to-control-agentic-search)

Each search tool supports a set of optional search parameters to help you narrow down the search space and limit the sources/information the agent is exposed to during its search process.

| Tool | Supported Filter Parameters |
| --- | --- |
| Web Search | `allowed_domains`, `excluded_domains`, `enable_image_understanding` |
| X Search | `allowed_x_handles`, `excluded_x_handles`, `from_date`, `to_date`, `enable_image_understanding`, `enable_video_understanding` |

### [Web Search Parameters](https://docs.x.ai/docs/guides/tools/search-tools#web-search-parameters)

##### [Only Search in Specific Domains](https://docs.x.ai/docs/guides/tools/search-tools#only-search-in-specific-domains)

Use `allowed_domains` to make the web search **only** perform the search and web browsing on web pages that fall within the specified domains.

`allowed_domains` can include a maximum of five domains.

`allowed_domains` cannot be set together with `excluded_domains` in the same request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        web_search(allowed_domains=["wikipedia.org"]),
    ],
)

chat.append(user("What is xAI?"))

# stream or sample the response...
```

##### [Exclude Specific Domains](https://docs.x.ai/docs/guides/tools/search-tools#exclude-specific-domains)

Use `excluded_domains` to prevent the model from including the specified domains in any web search tool invocations and from browsing any pages on those domains.

`excluded_domains` can include a maximum of five domains.

`excluded_domains` cannot be set together with `allowed_domains` in the same request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        web_search(excluded_domains=["wikipedia.org"]),
    ],
)

chat.append(user("What is xAI?"))

# stream or sample the response...
```

##### [Enable Image Understanding](https://docs.x.ai/docs/guides/tools/search-tools#enable-image-understanding)

Setting `enable_image_understanding` to true equips the agent with access to the `view_image` tool, allowing it to invoke this tool on any image URLs encountered during the search process. The model can then interpret and analyze image contents, incorporating this visual information into its context to potentially influence the trajectory of follow-up tool calls.

When the model invokes this tool, you will see it as an entry in `chunk.tool_calls` and `response.tool_calls` with the `image_url` as a parameter. Additionally, `SERVER_SIDE_TOOL_VIEW_IMAGE` will appear in `response.server_side_tool_usage` along with the number of times it was called when using the xAI Python SDK.

Note that enabling this feature increases token usage, as images are processed and represented as image tokens in the model's context.

Enabling this parameter for Web Search will also enable the image understanding for X Search tool if it's also included in the request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        web_search(enable_image_understanding=True),
    ],
)

chat.append(user("What is included in the image in xAI's official website?"))

# stream or sample the response...
```

### [X Search Parameters](https://docs.x.ai/docs/guides/tools/search-tools#x-search-parameters)

##### [Only Consider X Posts from Specific Handles](https://docs.x.ai/docs/guides/tools/search-tools#only-consider-x-posts-from-specific-handles)

Use `allowed_x_handles` to consider X posts only from a given list of X handles. The maximum number of handles you can include is 10.

`allowed_x_handles` cannot be set together with `excluded_x_handles` in the same request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        x_search(allowed_x_handles=["elonmusk"]),
    ],
)

chat.append(user("What is the current status of xAI?"))

# stream or sample the response...
```

##### [Exclude X Posts from Specific Handles](https://docs.x.ai/docs/guides/tools/search-tools#exclude-x-posts-from-specific-handles)

Use `excluded_x_handles` to prevent the model from including X posts from the specified handles in any X search tool invocations. The maximum number of handles you can exclude is 10.

`excluded_x_handles` cannot be set together with `allowed_x_handles` in the same request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        x_search(excluded_x_handles=["elonmusk"]),
    ],
)

chat.append(user("What is the current status of xAI?"))

# stream or sample the response...
```

##### [Date Range](https://docs.x.ai/docs/guides/tools/search-tools#date-range)

You can restrict the date range of search data used by specifying `from_date` and `to_date`. This limits the data to the period from `from_date` to `to_date`, including both dates.

Both fields need to be in ISO8601 format, e.g., "YYYY-MM-DD". If you're using the xAI Python SDK, the `from_date` and `to_date` fields can be passed as `datetime.datetime` objects.

The fields can also be used independently. With only `from_date` specified, the data used will be from the `from_date` to today, and with only `to_date` specified, the data used will be all data until the `to_date`.

```python
import os
from datetime import datetime

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        x_search(
            from_date=datetime(2025, 10, 1),
            to_date=datetime(2025, 10, 10),
        ),
    ],
)

chat.append(user("What is the current status of xAI?"))

# stream or sample the response...
```

##### [Enable Image Understanding](https://docs.x.ai/docs/guides/tools/search-tools#enable-image-understanding-1)

Setting `enable_image_understanding` to true equips the agent with access to the `view_image` tool, allowing it to invoke this tool on any image URLs encountered during the search process. The model can then interpret and analyze image contents, incorporating this visual information into its context to potentially influence the trajectory of follow-up tool calls.

When the model invokes this tool, you will see it as an entry in `chunk.tool_calls` and `response.tool_calls` with the `image_url` as a parameter. Additionally, `SERVER_SIDE_TOOL_VIEW_IMAGE` will appear in `response.server_side_tool_usage` along with the number of times it was called when using the xAI Python SDK.

Note that enabling this feature increases token usage, as images are processed and represented as image tokens in the model's context.

Enabling this parameter for X Search will also enable the image understanding for Web Search tool if it's also included in the request.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        x_search(enable_image_understanding=True),
    ],
)

chat.append(user("What images are being shared in recent xAI posts?"))

# stream or sample the response...
```

##### [Enable Video Understanding](https://docs.x.ai/docs/guides/tools/search-tools#enable-video-understanding)

Setting `enable_video_understanding` to true equips the agent with access to the `view_x_video` tool, allowing it to invoke this tool on any video URLs encountered in X posts during the search process. The model can then analyze video content, incorporating this information into its context to potentially influence the trajectory of follow-up tool calls.

When the model invokes this tool, you will see it as an entry in `chunk.tool_calls` and `response.tool_calls` with the `video_url` as a parameter. Additionally, `SERVER_SIDE_TOOL_VIEW_X_VIDEO` will appear in `response.server_side_tool_usage` along with the number of times it was called when using the xAI Python SDK.

Note that enabling this feature increases token usage, as video content is processed and represented as tokens in the model's context.

```python
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        x_search(enable_video_understanding=True),
    ],
)

chat.append(user("What is the latest video talking about from the xAI official X account?"))

# stream or sample the response...
```
