#### [Guides](https://docs.x.ai/docs/guides/tools/collections-search-tool#guides)

# Collections Search Tool

The collections search tool enables Grok to search through your uploaded knowledge bases (collections), allowing it to retrieve relevant information from your documents to provide more accurate and contextually relevant responses. This tool is particularly powerful for analyzing complex documents like financial reports, legal contracts, or technical documentation, where Grok can autonomously search through multiple documents and synthesize information to answer sophisticated analytical questions.

For an introduction to Collections, please check out the [Collections documentation](https://docs.x.ai/docs/key-information/collections).

**xAI Python SDK Users**: Version 1.4.0 of the xai-sdk package is required to use this collections-search tool in the agentic tool calling API.

## [Key Capabilities](https://docs.x.ai/docs/guides/tools/collections-search-tool#key-capabilities)

*   **Document Retrieval**: Search across uploaded files and collections to find relevant information
*   **Semantic Search**: Find documents based on meaning and context, not just keywords
*   **Knowledge Base Integration**: Seamlessly integrate your proprietary data with Grok's reasoning
*   **RAG Applications**: Power retrieval-augmented generation workflows
*   **Multi-format Support**: Search across PDFs, text files, CSVs, and other supported formats

## [When to Use Collections Search](https://docs.x.ai/docs/guides/tools/collections-search-tool#when-to-use-collections-search)

The collections search tool is particularly valuable for:

*   **Enterprise Knowledge Bases**: When you need Grok to reference internal documents and policies
*   **Financial Analysis**: Analyzing SEC filings, earnings reports, and financial statements across multiple documents
*   **Customer Support**: Building chatbots that can answer questions based on your product documentation
*   **Research & Due Diligence**: Synthesizing information from academic papers, technical reports, or industry analyses
*   **Compliance & Legal**: Ensuring responses are grounded in your official guidelines and regulations
*   **Personal Knowledge Management**: Organizing and querying your personal document collections

* * *

## [SDK Support](https://docs.x.ai/docs/guides/tools/collections-search-tool#sdk-support)

The collections search tool is available across multiple SDKs and APIs with different naming conventions:

| SDK/API | Tool Name | Description |
| --- | --- | --- |
| xAI SDK | `collections_search` | Native xAI SDK implementation |
| OpenAI Responses API | `file_search` | Compatible with OpenAI's API format |

## [Implementation Example](https://docs.x.ai/docs/guides/tools/collections-search-tool#implementation-example)

### [End-to-End Financial Analysis Example](https://docs.x.ai/docs/guides/tools/collections-search-tool#end-to-end-financial-analysis-example)

```python
import asyncio
import os

from xai_sdk import AsyncClient
from xai_sdk.chat import user
from xai_sdk.tools import collections_search

async def analyze_tesla_financials(client: AsyncClient, collection_id: str) -> None:
    # Enable collections search for specific collection
    chat = client.chat.create(
        model="grok-4-1-fast",
        tools=[
            collections_search(
                collection_ids=[collection_id],
            ),
        ],
        include=["verbose_streaming"],
    )

    # Ask a complex analytical question
    chat.append(
        user(
            "Based on the Tesla SEC filings in my collection, please analyze their vehicle "
            "production trends across the 2024 and 2025 fiscal years. What were the key "
            "drivers of production changes, and how did actual production compare to initial "
            "company guidance?"
        )
    )

    is_thinking = True
    async for response, chunk in chat.stream():
        # See the server-side tool calls as they happen
        for tool_call in chunk.tool_calls:
            print(f"\nCalling tool: {tool_call.function.name} with arguments: {tool_call.function.arguments}")
        
        if response.usage.reasoning_tokens and is_thinking:
            print(f"\rThinking... ({response.usage.reasoning_tokens} tokens)", end="", flush=True)
        
        if chunk.content and is_thinking:
            print("\n\nAnalysis Results:")
            is_thinking = False
        
        if chunk.content and not is_thinking:
            print(chunk.content, end="", flush=True)
        
        latest_response = response

    print("\n\nCitations:")
    print(latest_response.citations)
    print("\n\nTool Usage:")
    print(latest_response.server_side_tool_usage)
```

### [Understanding Collections Citations](https://docs.x.ai/docs/guides/tools/collections-search-tool#understanding-collections-citations)
When using the collections search tool, citations follow a special URI format that uniquely identifies the source documents:

```
collections://collection_id/files/file_id
```

For example:

```
collections://collection_3be0eec8-ee8e-4a18-a9d4-fb70a3150d64/files/file_d4d1a968-9037-4caa-8eca-47a1563f28ab
```

**Format Breakdown:**

*   **`collections://`**: Protocol identifier indicating this is a collection-based citation
*   **`collection_id`**: The unique identifier of the collection that was searched
*   **`files/`**: Path segment indicating file-level reference
*   **`file_id`**: The unique identifier of the specific document file that was referenced

These citations represent all the documents from your collections that Grok referenced during its search and analysis. Each citation points to a specific file within a collection, allowing you to trace back exactly which uploaded documents contributed to the final response.

### [Key Observations](https://docs.x.ai/docs/guides/tools/collections-search-tool#key-observations)
1.   **Autonomous Search Strategy**: Grok autonomously performs multiple searches across the documents, progressively refining queries.
2.   **Reasoning Process**: The model thinks through the problem before generating the final response.
3.   **Cited Sources**: All information is grounded in the uploaded documents with specific file citations.
4.   **Structured Analysis**: The final response breaks down the methodology and shows calculations.
5.   **Token Efficiency**: High cache utilization across multiple queries.

* * *

## [Combining Collections Search with Web Search/X-Search](https://docs.x.ai/docs/guides/tools/collections-search-tool#combining-collections-search-with-web-searchx-search)

One of the most powerful patterns is combining the collections search tool with web search/x-search to answer questions that require both your internal knowledge base and real-time external information.

### [Example: Internal Data + Market Intelligence](https://docs.x.ai/docs/guides/tools/collections-search-tool#example-internal-data--market-intelligence)

```python
import asyncio
from xai_sdk import AsyncClient
from xai_sdk.chat import user
from xai_sdk.tools import code_execution, collections_search, web_search, x_search

async def hybrid_analysis(client: AsyncClient, collection_id: str, model: str) -> None:
    # Enable multiple tools
    chat = client.chat.create(
        model=model,
        tools=[
            collections_search(collection_ids=[collection_id]),
            web_search(),
            x_search(),
            code_execution(),
        ],
        include=["verbose_streaming"],
    )

    chat.append(
        user(
            "Based on Tesla's actual production figures in my documents (collection), what is the "
            "current market and analyst sentiment on their 2024-2025 vehicle production performance?"
        )
    )

    # stream processing...
```

### [How It Works](https://docs.x.ai/docs/guides/tools/collections-search-tool#how-it-works)
1.   **Internal Analysis First**: Searches your uploaded Tesla SEC filings.
2.   **External Context Gathering**: Performs web/x-search searches.
3.   **Synthesis**: Combines both data sources.
4.   **Cited Sources**: Returns citations from both internal and external sources.

### [Use Cases for Hybrid Search](https://docs.x.ai/docs/guides/tools/collections-search-tool#use-cases-for-hybrid-search)
*   **Market Analysis**: Compare internal financials with market sentiment.
*   **Competitive Intelligence**: Analyze your performance against industry reports.
*   **Compliance Verification**: Cross-reference internal policies with regulatory requirements.
