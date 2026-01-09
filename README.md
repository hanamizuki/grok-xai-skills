# xAI API (Grok) Skill for Claude Code

A [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code/skills) that provides comprehensive xAI Grok API documentation directly within your Claude Code sessions.

## What is this?

This skill gives Claude Code access to the complete xAI API reference documentation, enabling it to assist you with:

- **Grok Models** - grok-4, grok-4.1-fast, and other model variants
- **Agentic Tools** - x_search, web_search, code_execution, collections_search, remote MCP
- **Files API** - Upload and chat with documents
- **Collections** - Create and query document collections (RAG)
- **Voice API** - Real-time voice agent capabilities
- **Function Calling & Structured Outputs** - Build reliable integrations
- **Image Generation** - Aurora image generation model
- **Migration guides** - From OpenAI/Anthropic to xAI

## Installation

### Option 1: Clone the repository

```bash
# Clone to your Claude Code skills directory
git clone https://github.com/user/grok-xai-skills ~/.claude/skills/grok-xai-skills
```

### Option 2: Copy the skill folder

Copy the `skills/grok-xai` folder to your Claude Code skills directory:

```
~/.claude/skills/grok-xai/
```

## Usage

Once installed, Claude Code will automatically use this skill when you ask questions about xAI API or Grok development.

You can also explicitly invoke it:

```
/grok-xai
```

### Example prompts

- "How do I use x_search with the xAI API?"
- "Show me how to enable reasoning with grok-4"
- "What are the rate limits for xAI API?"
- "How do I upload files and chat with them using Grok?"
- "Help me migrate my OpenAI code to xAI"

## Documentation Coverage

| Category | Topics |
|----------|--------|
| Getting Started | Overview, Introduction, Models & Pricing, Release Notes |
| Key Information | Billing, Rate Limits, Regional Endpoints, Collections, Debugging |
| Chat | Chat Responses API, Legacy Completions, Reasoning |
| Tools | Overview, Search Tools, Code Execution, Collections Search, Remote MCP |
| Files | Overview, Managing Files, Chat with Files |
| Collections | Console, API, Metadata |
| Other APIs | Image Generation, Voice, Streaming, Async, Function Calling, Structured Outputs |
| Grok Business | User Guide, License Management, Organization, Apps |
| Resources | Community Integrations, FAQ |

## Source

Documentation sourced from [docs.x.ai](https://docs.x.ai/).

## License

MIT
