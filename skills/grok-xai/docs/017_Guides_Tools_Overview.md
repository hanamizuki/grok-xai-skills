#### [Guides](#guides)

# [Overview](#overview)

The xAI API supports **agentic server-side tool calling** which enables the model to autonomously explore, search, and execute code to solve complex queries. Unlike traditional tool-calling where clients must handle each tool invocation themselves, xAI's agentic API manages the entire reasoning and tool-execution loop on the server side.

**xAI Python SDK Users**: Version 1.3.1 of the xai-sdk package is required to use the agentic tool calling API.

## [Tools Pricing](#tools-pricing)

Agentic requests are priced based on two components: **token usage** and **tool invocations**. Since the agent autonomously decides how many tools to call, costs scale with query complexity.

For more details on Tools pricing, please check out [the pricing page](/docs/models#tools-pricing).

* * *

## [Agentic Tool Calling](#agentic-tool-calling)

When you provide server-side tools to a request, the xAI server orchestrates an autonomous reasoning loop rather than returning tool calls for you to execute. This creates a seamless experience where the model acts as an intelligent agent that researches, analyzes, and responds automatically.

Behind the scenes, the model follows an iterative reasoning process:

1.  **Analyzes the query** and current context to determine what information is needed
2.  **Decides what to do next**: Either make a tool call to gather more information or provide a final answer
3.  **If making a tool call**: Selects the appropriate tool and parameters based on the reasoning
4.  **Executes the tool** in real-time on the server and receives the results
5.  **Processes the tool response** and integrates it with previous context and reasoning
6.  **Repeats the loop**: Uses the new information to decide whether more research is needed or if a final answer can be provided
7.  **Returns the final response** once the agent determines it has sufficient information to answer comprehensively

This autonomous orchestration enables complex multi-step research and analysis to happen automatically, with clients seeing the final result as well as optional real-time progress indicators like tool call notifications during streaming.

## [Core Capabilities](#core-capabilities)

*   **[Web Search](/docs/guides/tools/search-tools)**: Real-time search across the internet with the ability to both search the web and browse web pages.
*   **[X Search](/docs/guides/tools/search-tools)**: Semantic and keyword search across X posts, users, and threads.
*   **[Code Execution](/docs/guides/tools/code-execution-tool)**: The model can write and execute Python code for calculations, data analysis, and complex computations.
*   **[Image/Video Understanding](/docs/guides/tools/search-tools#parameter-enable_image_understanding-supported-by-web-search-and-x-search)**: Optional visual content understanding and analysis for search results encountered (video understanding is only available for X posts).
*   **[Collections Search](/docs/guides/tools/collections-search-tool)**: The model can search through your uploaded knowledge bases and collections to retrieve relevant information.
*   **[Remote MCP Tools](/docs/guides/tools/remote-mcp-tools)**: Connect to external MCP servers to access custom tools.
*   **[Document Search](/docs/guides/files)**: Upload files and chat with them using intelligent document search. This tool is automatically enabled when you attach files to a chat message.

## [Quick Start](#quick-start)

We strongly recommend using the xAI Python SDK in streaming mode when using agentic tool calling. Doing so grants you the full feature set of the API, including the ability to get real-time observability and immediate feedback during potentially long-running requests.

Here is a quick start example of using the agentic tool calling API.

Python Javascript

```
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search, x_search, code_execution

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    # All server-side tools active
    tools=[
        web_search(),
        x_search(),
        code_execution(),
    ],
    include=["verbose_streaming"],
)

# Feel free to change the query here to a question of your liking
chat.append(user("What are the latest updates from xAI?"))

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

You will be able to see output like:

Output

```
Thinking... (270 tokens)
Calling tool: x_user_search with arguments: {"query":"xAI official","count":1}
Thinking... (348 tokens)
Calling tool: x_user_search with arguments: {"query":"xAI","count":5}
Thinking... (410 tokens)
Calling tool: x_keyword_search with arguments: {"query":"from:xai","limit":10,"mode":"Latest"}
Thinking... (667 tokens)
Calling tool: web_search with arguments: {"query":"xAI latest updates site:x.ai","num_results":5}
Thinking... (850 tokens)
Calling tool: browse_page with arguments: {"url": "https://x.ai/news"}
Thinking... (1215 tokens)

Final Response:
### Latest Updates from xAI (as of October 12, 2025)

xAI primarily shares real-time updates via their official X (Twitter) account (@xai), with more formal announcements on their website (x.ai). Below is a summary of the most recent developments...

... full response omitted for brevity

Citations:
[
'https://x.com/i/user/1912644073896206336',
'https://x.com/i/user/1019237602585645057',
'https://x.com/i/status/1975607901571199086',
'https://x.com/i/status/1975608122845896765',
'https://x.com/i/status/1975608070245175592',
'https://x.com/i/user/1603826710016819209',
'https://x.com/i/status/1975608007250829383',
'https://status.x.ai/',
'https://x.com/i/user/150543432',
'https://x.com/i/status/1975608184711880816',
'https://x.com/i/status/1971245659660718431',
'https://x.com/i/status/1975608132530544900',
'https://x.com/i/user/1661523610111193088',
'https://x.com/i/status/1977121515587223679',
'https://x.ai/news/grok-4-fast',
'https://x.com/i/status/1975608017396867282',
'https://x.ai/',
'https://x.com/i/status/1975607953391755740',
'https://x.com/i/user/1875560944044273665',
'https://x.ai/news',
'https://docs.x.ai/docs/release-notes'
]


Usage:
completion_tokens: 1216
prompt_tokens: 29137
total_tokens: 31568
prompt_text_tokens: 29137
reasoning_tokens: 1215
cached_prompt_text_tokens: 22565
server_side_tools_used: SERVER_SIDE_TOOL_X_SEARCH
server_side_tools_used: SERVER_SIDE_TOOL_X_SEARCH
server_side_tools_used: SERVER_SIDE_TOOL_X_SEARCH
server_side_tools_used: SERVER_SIDE_TOOL_WEB_SEARCH
server_side_tools_used: SERVER_SIDE_TOOL_WEB_SEARCH

{'SERVER_SIDE_TOOL_X_SEARCH': 3, 'SERVER_SIDE_TOOL_WEB_SEARCH': 2}


Server Side Tool Calls:
[id: "call_51132959"
function {
  name: "x_user_search"
  arguments: "{"query":"xAI official","count":1}"
}
, id: "call_00956753"
function {
  name: "x_user_search"
  arguments: "{"query":"xAI","count":5}"
}
, id: "call_07881908"
function {
  name: "x_keyword_search"
  arguments: "{"query":"from:xai","limit":10,"mode":"Latest"}"
}
, id: "call_43296276"
function {
  name: "web_search"
  arguments: "{"query":"xAI latest updates site:x.ai","num_results":5}"
}
, id: "call_70310550"
function {
  name: "browse_page"
  arguments: "{"url": "https://x.ai/news"}"
}
]
```

## [Understanding the Agentic Tool Calling Response](#understanding-the-agentic-tool-calling-response)

The agentic tool calling API provides rich observability into the autonomous research process. This section dives deep into the original code snippet above, covering key ways to effectively use the API and understand both real-time streaming responses and final results:

### [Real-time server-side tool calls](#real-time-server-side-tool-calls)

When executing agentic requests using streaming, you can observe **every tool call decision** the model makes in real-time via the `tool_calls` attribute on the `chunk` object. This shows the exact parameters the agent chose for each tool invocation, giving you visibility into its search strategy. Occasionally the model may decide to invoke multiple tools in parallel during a single turn, in which case each entry in the list of `tool_calls` would represent one of those parallel tool calls; otherwise, only a single entry would be present in `tool_calls`.

**Note**: Only the tool call invocations themselves are shown - **server-side tool call outputs are not returned** in the API response. The agent uses these outputs internally to formulate its final response, but they are not exposed to the user.

When using the xAI Python SDK in streaming mode, it will automatically accumulate the `tool_calls` into the `response` object for you, letting you access a final list of all the server-side tool calls made during the agentic loop. This is demonstrated in the [section below](#server-side-tool-calls-vs-tool-usage).

Python

```
for tool_call in chunk.tool_calls:
    print(f"\nCalling tool: {tool_call.function.name} with arguments: {tool_call.function.arguments}")
```
Output

```
Calling tool: x_user_search with arguments: {"query":"xAI official","count":1}
Calling tool: x_user_search with arguments: {"query":"xAI","count":5}
Calling tool: x_keyword_search with arguments: {"query":"from:xai","limit":10,"mode":"Latest"}
Calling tool: web_search with arguments: {"query":"xAI latest updates site:x.ai","num_results":5}
Calling tool: browse_page with arguments: {"url": "https://x.ai/news"}
```

### [Citations](#citations)

The agent tools API provides two types of citation information: **All Citations** (a complete list of all sources encountered) and **Inline Citations** (markdown-style links embedded directly in the response text, with structured metadata also available on the response object).

##### [All Citations](#all-citations)

The `citations` attribute on the `response` object provides a comprehensive list of URLs for all sources the agent encountered during its search process. This list is **always returned by default** — no additional configuration is required.

Citations are automatically collected from successful tool executions and provide full traceability of the agent's information sources. They are returned when the agentic request completes and are not available in real-time during streaming.

Note that not every URL in this list will necessarily be directly referenced in the final answer. The agent may examine a source during its research process and determine it is not sufficiently relevant to the user's query, but the URL will still appear in this list for transparency.

Python

```
response.citations
```

Output

```
[
'https://x.com/i/user/1912644073896206336',
'https://x.com/i/status/1975607901571199086',
'https://x.ai/news',
'https://docs.x.ai/docs/release-notes',
...
]
```

##### [Inline Citations](#inline-citations)

**xAI Python SDK Users**: Version 1.5.0 or later of the xai-sdk package is required to use the inline citations feature.

Inline citations are **markdown-style links** (e.g., `[[1]](https://x.ai/news)`) inserted directly into the response text at the points where the model references sources. In addition to these visible links, **structured metadata** is available on the response object with precise positional information. Unlike the `citations` list which contains all encountered URLs, inline citations only include sources that the model explicitly cited in its response.

**Important**: Enabling inline citations does not guarantee that the model will cite sources on every answer. The model decides when and where to include citations based on the context and nature of the query. Some responses may have no inline citations if the model determines citations are not necessary for that particular answer.

##### [Enabling Inline Citations](#enabling-inline-citations)

Inline citations are **opt-in** and must be explicitly enabled by passing `include=["inline_citations"]` when creating your chat:

Python

```
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search, x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",
    tools=[
        web_search(),
        x_search(),
    ],
    include=["inline_citations"],  # Enable inline citations
)
```

##### [Markdown Citation Format](#markdown-citation-format)

When inline citations are enabled, the model will insert markdown-style citation links directly into the response text at the points where sources are referenced:

Output

```
The latest announcements from xAI, primarily from their official X account (@xai) and website (x.ai/news), date back to November 19, 2025.[[1]](https://x.ai/news/)[[2]](https://x.ai/)[[3]](https://x.com/i/status/1991284813727474073)
```

When rendered as markdown, this displays as clickable links:

> The latest announcements from xAI, primarily from their official X account (@xai) and website (x.ai/news), date back to November 19, 2025.[\[1\]](https://x.ai/news/)[\[2\]](https://x.ai/)[\[3\]](https://x.com/i/status/1991284813727474073)

The format is `[[N]](url)` where:

*   `N` is the sequential display number for the citation **starting from 1**.
*   `url` is the source URL

**Citation placement**: Citations are typically placed at the end of a sentence, with multiple citations for the same statement appearing adjacent to one another as shown in the example above.

**Citation numbering**: Citation numbers always start from 1 and increment sequentially. If the same source is cited again later in the response, the original citation number will be reused to maintain consistency.

##### [Accessing Structured Inline Citation Data](#accessing-structured-inline-citation-data)

In addition to the markdown links in the response text, structured inline citation data is available via the `inline_citations` property. Each [`InlineCitation`](https://github.com/xai-org/xai-proto/blob/f6d4d709515fd666ebeb7dfcb89bd50e58c859d9/proto/xai/api/v1/chat.proto#L329) object contains:

Field

Type

Description

`id`

string

The display number as a string (e.g., "1", "2")

`start_index`

int

Character position where the citation starts in the response text

`end_index`

int

Character position where the citation ends (exclusive, following Python slice convention)

`web_citation`

[**WebCitation**](https://github.com/xai-org/xai-proto/blob/f6d4d709515fd666ebeb7dfcb89bd50e58c859d9/proto/xai/api/v1/chat.proto#L362)

Present if the citation is from a web source (contains `url`)

`x_citation`

[**XCitation**](https://github.com/xai-org/xai-proto/blob/f6d4d709515fd666ebeb7dfcb89bd50e58c859d9/proto/xai/api/v1/chat.proto#L367)

Present if the citation is from an X/Twitter source (contains `url`)

Python

```
# After streaming or sampling completes, access the structured inline citations from the response object:
for citation in response.inline_citations:
    print(f"Citation [{citation.id}]:")
    print(f"  Position: {citation.start_index} to {citation.end_index}")
    
    # Check citation type
    if citation.HasField("web_citation"):
        print(f"  Web URL: {citation.web_citation.url}")
    elif citation.HasField("x_citation"):
        print(f"  X URL: {citation.x_citation.url}")
```

Output

```
Citation [1]:
  Position: 37 to 76
  Web URL: https://x.ai/news/grok-4-fast
Citation [2]:
  Position: 124 to 171
  X URL: https://x.com/xai/status/1234567890
```

##### [Using Position Indices](#using-position-indices)

The `start_index` and `end_index` values follow Python slice convention:

*   **`start_index`**: The character position of the first opening square bracket `[` of the citation (e.g., the position of the first `[` in `[[1]](url)`)
*   **`end_index`**: The character position immediately _after_ the closing parenthesis `)` (exclusive, so the citation text is `content[start_index:end_index]`)

This means you can extract the exact citation markdown from the response text using a simple slice:

Python

```
content = response.content

for citation in response.inline_citations:
    # Extract the markdown link from the response text
    citation_text = content[citation.start_index:citation.end_index]
    print(f"Citation text: {citation_text}")
```

Output

```
Citation text: [[1]](https://x.ai/news/grok-4-fast)
Citation text: [[2]](https://x.com/xai/status/1234567890)
```

##### [Streaming Inline Citations](#streaming-inline-citations)

During streaming, inline citations are accumulated and available on the final response. The markdown links appear in real-time in the `chunk.content` as the model generates text, while the structured `InlineCitation` objects are populated as citations are detected:

Python

```
for response, chunk in chat.stream():
    # Markdown links appear in chunk.content in real-time
    if chunk.content:
        print(chunk.content, end="", flush=True)
    
    # Inline citations can also be accessed per-chunk during streaming
    for citation in chunk.inline_citations:
        print(f"\nNew citation: [{citation.id}]")

# After streaming, access all accumulated inline citations
print("\n\nAll inline citations:")
for citation in response.inline_citations:
    url = ""
    if citation.HasField("web_citation"):
        url = citation.web_citation.url
    elif citation.HasField("x_citation"):
        url = citation.x_citation.url
    print(f"  [{citation.id}] {url}")
```

### [Server-side Tool Calls vs Tool Usage](#server-side-tool-calls-vs-tool-usage)

The API provides two related but distinct metrics for server-side tool executions:

`tool_calls` - All Attempted Calls

Python

```
response.tool_calls
```

Returns a list of all **attempted** tool calls made during the agentic process. Each entry is a [ToolCall](https://github.com/xai-org/xai-proto/blob/736b835b0c0dd93698664732daad49f87a2fbc6f/proto/xai/api/v1/chat.proto#L474) object containing:

*   `id`: Unique identifier for the tool call
*   `function.name`: The name of the specific server-side tool called
*   `function.arguments`: The parameters passed to the server-side tool

This includes **every tool call attempt**, even if some fail.

Output

```
[id: "call_51132959"
function {
  name: "x_user_search"
  arguments: "{"query":"xAI official","count":1}"
}
, id: "call_07881908"
function {
  name: "x_keyword_search"
  arguments: "{"query":"from:xai","limit":10,"mode":"Latest"}"
}
, id: "call_43296276"
function {
  name: "web_search"
  arguments: "{"query":"xAI latest updates site:x.ai","num_results":5}"
}
]
```

`server_side_tool_usage` - Successful Calls (Billable)

Python

```
response.server_side_tool_usage
```

Returns a map of successfully executed tools and their invocation counts. This represents only the tool calls that returned meaningful responses and is what determines your billing.

Output

```
{'SERVER_SIDE_TOOL_X_SEARCH': 3, 'SERVER_SIDE_TOOL_WEB_SEARCH': 2}
```

### [Tool Call Function Names vs Usage Categories](#tool-call-function-names-vs-usage-categories)

The function names in `tool_calls` represent the precise/exact name of the tool invoked by the model, while the entries in `server_side_tool_usage` provide a more high-level categorization that aligns with the original tool passed in the `tools` array of the request.

**Function Name to Usage Category Mapping:**

Usage Category

Function Name(s)

`SERVER_SIDE_TOOL_WEB_SEARCH`

`web_search`, `web_search_with_snippets`, `browse_page`

`SERVER_SIDE_TOOL_X_SEARCH`

`x_user_search`, `x_keyword_search`, `x_semantic_search`, `x_thread_fetch`

`SERVER_SIDE_TOOL_CODE_EXECUTION`

`code_execution`

`SERVER_SIDE_TOOL_VIEW_X_VIDEO`

`view_x_video`

`SERVER_SIDE_TOOL_VIEW_IMAGE`

`view_image`

`SERVER_SIDE_TOOL_COLLECTIONS_SEARCH`

`collections_search`

`SERVER_SIDE_TOOL_MCP`

`{server_label}.{tool_name}` if `server_label` provided, otherwise `{tool_name}`

### [When Tool Calls and Usage Differ](#when-tool-calls-and-usage-differ)

In most cases, `tool_calls` and `server_side_tool_usage` will show the same tools. However, they can differ when:

*   **Failed tool executions**: The model attempts to browse a non-existent webpage, fetch a deleted X post, or encounters other execution errors
*   **Invalid parameters**: Tool calls with malformed arguments that can't be processed
*   **Network or service issues**: Temporary failures in the tool execution pipeline

The agentic system is robust enough to handle these failures gracefully, updating its trajectory and continuing with alternative approaches when needed.

**Billing Note**: Only successful tool executions (`server_side_tool_usage`) are billed. Failed attempts are not charged.

### [Server-side Tool Call and Client-side Tool Call](#server-side-tool-call-and-client-side-tool-call)

Agentic tool calling supports mixing server-side tools and client-side tools, which enables more use cases when some private tools and data are needed during the agentic tool calling process.

To determine whether the received tool calls need to be executed by the client side, you can simply check the type of the tool call.

For xAI Python SDK users, you can use the provided `get_tool_call_type` function to get the type of the tool calls.

For a full guide into requests that mix server-side and client-side tools, please check out the [advanced usage](/docs/guides/tools/advanced-usage) page.

**xAI Python SDK Users**: Version 1.4.0 of the xai-sdk package is the minimum requirement to use the `get_tool_call_type` function.

Python

```
# ...
response = chat.sample()

from xai_sdk.tools import get_tool_call_type

for tool_call in response.tool_calls:
    print(get_tool_call_type(tool_call))
```

The available tool call types are listed below:

Tool call types

Description

`"client_side_tool"`

Indicates this tool call is a **client-side tool** call, and an invocation to this function on the client side is required and the tool output needs to be appended to the chat

`"web_search_tool"`

Indicates this tool call is a **web-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"x_search_tool"`

Indicates this tool call is an **x-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"code_execution_tool"`

Indicates this tool call is a **code-execution tool** call, which is performed by xAI server, **NO** action needed from the client side

`"collections_search_tool"`

Indicates this tool call is a **collections-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"mcp_tool"`

Indicates this tool call is an **MCP tool** call, which is performed by xAI server, **NO** action needed from the client side

### [Understanding Token Usage](#understanding-token-usage)

Agentic requests have unique token usage patterns compared to standard chat completions. Here's how each token field in the usage object is calculated:

#### [`completion_tokens`](#completion_tokens)

Represents **only the final text output** of the model - the comprehensive answer returned to the user. This is typically much smaller than you might expect for such rich, research-driven responses, as the agent performs all its intermediate reasoning and tool orchestration internally.

#### [`prompt_tokens`](#prompt_tokens)

Represents the **cumulative input tokens** across all inference requests made during the agentic process. Since agentic workflows involve multiple reasoning steps with tool calls, the model makes several inference requests throughout the research process. Each request includes the full conversation history up to that point, which grows as the agent progresses through its research.

While this can result in higher `prompt_tokens` counts, agentic requests benefit significantly from **prompt caching**. The majority of the prompt (the conversation prefix) remains unchanged between inference steps, allowing for efficient caching of the shared context. This means that while the total `prompt_tokens` may appear high, much of the computation is optimized through intelligent caching of the stable conversation history, leading to better cost efficiency overall.

#### [`reasoning_tokens`](#reasoning_tokens)

Represents the tokens used for the model's internal reasoning process during agentic workflows. This includes the computational work the agent performs to plan tool calls, analyze results, and formulate responses, but excludes the final output tokens.

#### [`cached_prompt_text_tokens`](#cached_prompt_text_tokens)

Indicates how many prompt tokens were served from cache rather than recomputed. This shows the efficiency gains from prompt caching - higher values indicate better cache utilization and lower costs.

#### [`prompt_image_tokens`](#prompt_image_tokens)

Represents the tokens derived from visual content that the agent processes during the request. These tokens are produced when visual understanding is enabled and the agent views images (e.g., via web browsing) or analyzes video frames on X. They are counted separately from text tokens and reflect the cost of ingesting visual features alongside the textual context. If no images or videos are processed, this value will be zero.

#### [`prompt_text_tokens` and `total_tokens`](#prompt_text_tokens-and-total_tokens)

`prompt_text_tokens` reflects the actual text tokens in prompts (excluding any special tokens), while `total_tokens` is the sum of all token types used in the request.

### [Limiting Tool Call Turns in Agentic Requests](#limiting-tool-call-turns-in-agentic-requests)

The `max_turns` parameter allows you to control the maximum number of assistant/tool-call turns the agent can perform during a single agentic request. This provides a powerful mechanism to balance response latency, cost, and research depth.

##### [Understanding Turns vs Tool Calls](#understanding-turns-vs-tool-calls)

**Important**: The `max_turns` value does **not** directly limit the number of individual tool calls. Instead, it limits the number of _assistant turns_ in the agentic loop. During a single turn, the model may decide to invoke multiple tools in parallel. For example, with `max_turns=1`, the agent could still execute 3 or more tool calls if it chooses to run them in parallel within that single turn.

A "turn" represents one iteration of the agentic reasoning loop:

1.  The model analyzes the current context
2.  The model decides to call one or more tools (potentially in parallel)
3.  Tools execute and return results
4.  The model processes the results

This completes one turn. If more research is needed and `max_turns` allows, the loop continues. Below is an example of how to use `max_turns` in an agentic request.

Python

```
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search, x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",
    tools=[
        web_search(),
        x_search(),
    ],
    max_turns=3,  # Limit to 3 assistant/tool-call turns
)

chat.append(user("What is the latest news from xAI?"))
response = chat.sample()
print(response.content)
```

##### [When to Use `max_turns`](#when-to-use-max_turns)

Use Case

Recommended `max_turns`

Tradeoff

**Quick lookups**

1-2

Fastest response, may miss deeper insights

**Balanced research**

3-5

Good balance of speed and thoroughness

**Deep research**

10+ or unset

Most comprehensive, longer latency and higher cost

**Common scenarios for limiting turns:**

*   **Latency-sensitive applications**: When you need faster responses and can accept less exhaustive research
*   **Cost control**: To cap the maximum number of tool invocations and limit per-request spend
*   **Simple queries**: When the question likely only needs one or two searches to answer
*   **Predictable behavior**: When you want more consistent response times across requests

To allow the agent to make as many tool calls as needed, and determine by itself when to stop, you should leave `max_turns` unset.

##### [Default Behavior](#default-behavior)

If `max_turns` is not specified, the server applies a global default cap. The effective limit is always the **minimum** of your requested `max_turns` and the server's global cap—this ensures requests cannot exceed system-defined limits.

**Note**: The `max_turns` parameter is ignored for non-agentic requests (requests without server-side tools).

##### [Behavior When Limit is Reached](#behavior-when-limit-is-reached)

When the agent reaches the `max_turns` limit, it will stop making additional server-side tool calls and proceed directly to generating a final response based on the information gathered so far. The agent will do its best to provide a helpful answer with the available context, though it may note that additional research could provide more complete information.

## [Synchronous Agentic Requests (Non-streaming)](#synchronous-agentic-requests-non-streaming)

Although not typically recommended, for simpler use cases or when you want to wait for the complete agentic workflow to finish before processing the response, you can use synchronous requests:

Python Javascript

```
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import code_execution, web_search, x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        web_search(),
        x_search(),
        code_execution(),
    ],
)

chat.append(user("What is the latest update from xAI?"))

# Get the final response in one go once it's ready
response = chat.sample()

print("\n\nFinal Response:")
print(response.content)

# Access the citations of the final response
print("\n\nCitations:")
print(response.citations)

# Access the usage details from the entire search process
print("\n\nUsage:")
print(response.usage)
print(response.server_side_tool_usage)

# Access the server side tool calls of the final response
print("\n\nServer Side Tool Calls:")
print(response.tool_calls)
```

Synchronous requests will wait for the entire agentic process to complete before returning the response. This is simpler for basic use cases but provides less visibility into the intermediate steps compared to streaming.

* * *

## [Using Tools with Responses API](#using-tools-with-responses-api)

We also support using the Responses API in both streaming and non-streaming modes.

Python Javascript

Other

```
import os

from xai_sdk import Client
from xai_sdk.chat import user
from xai_sdk.tools import web_search, x_search

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-fast",
    store_messages=True,  # Enable Responses API
    tools=[
        web_search(),
        x_search(),
    ],
)

chat.append(user("What is the latest update from xAI?"))
response = chat.sample()

print(response.content)
print(response.citations)

# The response id can be used to continue the conversation
print(response.id)
```

### [Identifying the Client-side Tool Call](#identifying-the-client-side-tool-call)

A critical step in mixing server-side tools and client-side tools is to identify whether a returned tool call is a client-side tool that needs to be executed locally on the client side.

Similar to the way in xAI Python SDK, you can identify the client-side tool call by checking the `type` of the output entries (`response.output[].type`) in the response of OpenAI Responses API.

Types

Description

`"function_call"`

Indicates this tool call is a **client-side tool** call, and an invocation to this function on the client side is required and the tool output needs to be appended to the chat

`"web_search_call"`

Indicates this tool call is a **web-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"x_search_call"`

Indicates this tool call is an **x-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"code_interpreter_call"`

Indicates this tool call is a **code-execution tool** call, which is performed by xAI server, **NO** action needed from the client side

`"file_search_call"`

Indicates this tool call is a **collections-search tool** call, which is performed by xAI server, **NO** action needed from the client side

`"mcp_call"`

Indicates this tool call is an **MCP tool** call, which is performed by xAI server, **NO** action needed from the client side

* * *

## [Accessing Outputs from Server Side Tool Calls](#accessing-outputs-from-server-side-tool-calls)

By default, the output of server-side tool calls will not be returned to the caller since the outputs can be large and require extra compute to process on the client side.

However, the output of server-side tool calls can be useful in some cases, for example providing a good user experience on the client side. We introduce a new parameter `include`which allows you to selectively specify which server-side tool outputs should be included in the responses.

### [Specify Tool Outputs to Return](#specify-tool-outputs-to-return)

In order to include the tool output in the response, you will need to specify which server-side tool outputs you wish to include in the response.

For the xAI-SDK, the mapping below shows the tool name and corresponding values that should be passed to the `include` array:

Tool

Value for `include` field in xAI-SDK

Included Outputs

`"web_search"`

`"web_search_call_output"`

Encrypted web search content

`"x_search"`

`"x_search_call_output"`

Encrypted X search content

`"code_execution"`

`"code_execution_call_output"`

Full plaintext execution output

`"collections_search"`

`"collections_search_call_output"`

Plaintext content chunks matching the search query

`"document_search"`

`"document_search_call_output"`

Plaintext extracted document content

`"mcp"`

`"mcp_call_output"`

Full plaintext MCP tool output

You can also enable the tool output for the server-side tool calls when using Responses AI, the structures of the tool output are compatible with the equivalent standard server-side tools in Responses API.

Tool

Responses API tool name

Value for `include` field in Responses API

`"web_search"`

`"web_search"`

`"web_search_call.action.sources"`

`"code_execution"`

`"code_interpreter"`

`"code_interpreter_call.outputs"`

`"collections_search"`

`"file_search"`

`"file_search_call.results"`

`"mcp"`

`"mcp"`

No need to include since it will be always returned in Responses API

Python

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, CompletionOutputChunk
from xai_sdk.proto import chat_pb2
from xai_sdk.tools import code_execution

client = Client(api_key=os.getenv("XAI_API_KEY"))
chat = client.chat.create(
    model="grok-4-1-fast",  # reasoning model
    tools=[
        code_execution(),
    ],
    include=["code_execution_call_output"],
)
chat.append(user("What is the 100th Fibonacci number?"))

# stream or sample the response...
```

### [Find the Tool Output from Response](#find-the-tool-output-from-response)

The the tool outputs from the specified tools will be included in the response from the server. And different APIs or SDKs will require different ways to process the tool output.

#### [Using xAI Python SDK](#using-xai-python-sdk)

The tool output content will be included in the output entries with the `role` is set to `tool`.

A complete tool call is also attached to each of the output entries that represents the tool output content, you can use that to check the details of the corresponding tool call.

Below is a sample of print the tool output during streaming.

Python

```
# previous steps to set up `chat`...

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

    # View the tool output as they are being completed in real-time
    for tool_output in chunk.tool_outputs:
        if tool_output.content:
            tool_call = tool_output.tool_calls[0]
            print(f"\nTool '{tool_call.function.name}' completed with output: {tool_output.content}")
```

#### [Using Responses API](#using-responses-api)

In Responses API, different server-side tools will have different dedicated structures to host the tool arguments and the tool outputs, e.g. `web_search_call`, `code_interpreter_call`, `file_search_call`, `mcp_call`. For more details, you will need to check the official Responses API specifications.

Here we use the `code_interpreter` as the example: scan through the `response.output` array and find the entries with the `type` of `code_interpreter_call`.

Python (OpenAI)

```
# previous step to generate the `response` ...

print(next(
    output
    for output in response.output
    if output.type == "code_interpreter_call"
))
```

* * *

### [Structured Outputs with Tools](#structured-outputs-with-tools)

You can also combine agent tool calling with [structured outputs](/docs/guides/structured-outputs) to get type-safe responses from tool-augmented queries.

For more details, consult the [Structured Outputs with Tools](/docs/guides/structured-outputs#structured-outputs-with-tools) section in the Structured Outputs guide.

* * *

## [Agentic Tool Calling Requirements and Limitations](#agentic-tool-calling-requirements-and-limitations)

### [Model Compatibility](#model-compatibility)

*   **Supported Models**: `grok-4`, `grok-4-fast`, `grok-4-fast-non-reasoning`, `grok-4-1-fast`, `grok-4-1-fast-non-reasoning`
*   **Strongly Recommended**: `grok-4-1-fast` - specifically trained to excel at agentic tool calling

### [Request Constraints](#request-constraints)

*   **No batch requests**: `n > 1` not supported
*   **Limited sampling params**: Only `temperature` and `top_p` are respected

* * *

## [FAQ and Troubleshooting](#faq-and-troubleshooting)

### [I'm seeing empty or incorrect content when using agentic tool calling with the xAI Python SDK](#im-seeing-empty-or-incorrect-content-when-using-agentic-tool-calling-with-the-xai-python-sdk)

Please make sure to upgrade to the latest version of the xAI SDK. Agentic tool calling requires version `1.3.1` or above.