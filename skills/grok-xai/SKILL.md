---
name: grok-xai
description: xAI Grok API reference documentation. Use when working with xAI API, Grok models, agentic tools (x_search, web_search, code_execution), Live Search, Collections, Files API, Voice API, Function Calling, Structured Outputs, or any xAI/Grok development questions.
user-invocable: true
---

# xAI API Reference

This skill provides comprehensive documentation for the xAI API and Grok models.

## Quick Reference

| Topic | File |
|-------|------|
| **Getting Started** | `references/002_GettingStarted_Introduction.md` |
| **Models & Pricing** | `references/004_GettingStarted_ModelsAndPricing.md` |
| **Chat Responses API** | `references/014_Guides_ChatResponses.md` |
| **Reasoning (grok-4)** | `references/016_Guides_ChatWithReasoning.md` |

## Agentic Tools (Important)

| Tool | File |
|------|------|
| **Tools Overview** | `references/017_Guides_Tools_Overview.md` |
| **x_search / web_search** | `references/018_Guides_Tools_SearchTools.md` |
| **Code Execution** | `references/019_Guides_Tools_CodeExecution.md` |
| **Collections Search** | `references/020_Guides_Tools_CollectionsSearch.md` |
| **Remote MCP** | `references/021_Guides_Tools_RemoteMCP.md` |
| **Advanced Usage** | `references/022_Guides_Tools_AdvancedUsage.md` |

## Files & Collections

| Topic | File |
|-------|------|
| **Files Overview** | `references/023_Guides_Files_Overview.md` |
| **Managing Files** | `references/024_Guides_Files_ManagingFiles.md` |
| **Chat with Files** | `references/025_Guides_Files_ChatWithFiles.md` |
| **Collections Overview** | `references/009_KeyInfo_Collections.md` |
| **Using Collections** | `references/027_Guides_UsingCollections_Overview.md` |

## Other APIs

| Topic | File |
|-------|------|
| **Function Calling** | `references/037_Guides_FunctionCalling.md` |
| **Structured Outputs** | `references/038_Guides_StructuredOutputs.md` |
| **Image Generation** | `references/031_Guides_ImageGenerations.md` |
| **Voice API** | `references/032_Guides_Voice_Overview.md`, `references/033_Guides_Voice_GrokVoiceAgentAPI.md` |
| **Streaming** | `references/034_Guides_StreamResponse.md` |
| **Async Requests** | `references/036_Guides_AsyncRequests.md` |

## Reference

| Topic | File |
|-------|------|
| **Rate Limits** | `references/007_KeyInfo_RateLimits.md` |
| **Regional Endpoints** | `references/008_KeyInfo_RegionalEndpoints.md` |
| **Debugging Errors** | `references/013_KeyInfo_Debugging.md` |
| **Migration from OpenAI/Anthropic** | `references/040_Guides_Migration.md` |

## All Files

### Getting Started (001-005)
- `references/001_GettingStarted_Overview.md` - Overview
- `references/002_GettingStarted_Introduction.md` - Introduction
- `references/003_GettingStarted_GuideToGrok.md` - The Hitchhiker's Guide to Grok
- `references/004_GettingStarted_ModelsAndPricing.md` - Models and Pricing
- `references/005_GettingStarted_WhatsNew.md` - Release Notes

### Key Information (006-013)
- `references/006_KeyInfo_ManageBilling.md` - Manage Billing
- `references/007_KeyInfo_RateLimits.md` - Consumption and Rate Limits
- `references/008_KeyInfo_RegionalEndpoints.md` - Regional Endpoints
- `references/009_KeyInfo_Collections.md` - Collections
- `references/010_KeyInfo_ManagementAPI.md` - Management API
- `references/011_KeyInfo_UsageExplorer.md` - Usage Explorer
- `references/012_KeyInfo_MigratingModels.md` - Migrating to New Models
- `references/013_KeyInfo_Debugging.md` - Debugging Errors

### Guides - Chat (014-016)
- `references/014_Guides_ChatResponses.md` - Chat Responses (recommended)
- `references/015_Guides_ChatCompletionsLegacy.md` - Chat Completions (legacy)
- `references/016_Guides_ChatWithReasoning.md` - Reasoning

### Guides - Tools (017-022)
- `references/017_Guides_Tools_Overview.md` - Tools Overview
- `references/018_Guides_Tools_SearchTools.md` - Search Tools (x_search, web_search)
- `references/019_Guides_Tools_CodeExecution.md` - Code Execution
- `references/020_Guides_Tools_CollectionsSearch.md` - Collections Search
- `references/021_Guides_Tools_RemoteMCP.md` - Remote MCP Tools
- `references/022_Guides_Tools_AdvancedUsage.md` - Advanced Usage

### Guides - Files (023-025)
- `references/023_Guides_Files_Overview.md` - Files Overview
- `references/024_Guides_Files_ManagingFiles.md` - Managing Files
- `references/025_Guides_Files_ChatWithFiles.md` - Chat with Files

### Guides - Other (026-041)
- `references/026_Guides_LiveSearch.md` - Live Search (deprecated, use Tools)
- `references/027_Guides_UsingCollections_Overview.md` - Using Collections Overview
- `references/028_Guides_UsingCollections_Console.md` - Using Collections in Console
- `references/029_Guides_UsingCollections_API.md` - Using Collections via API
- `references/030_Guides_UsingCollections_Metadata.md` - Collection Metadata
- `references/031_Guides_ImageGenerations.md` - Image Generations
- `references/032_Guides_Voice_Overview.md` - Voice Overview
- `references/033_Guides_Voice_GrokVoiceAgentAPI.md` - Grok Voice Agent API
- `references/034_Guides_StreamResponse.md` - Streaming Response
- `references/035_Guides_DeferredChatCompletions.md` - Deferred Chat Completions
- `references/036_Guides_AsyncRequests.md` - Asynchronous Requests
- `references/037_Guides_FunctionCalling.md` - Function Calling
- `references/038_Guides_StructuredOutputs.md` - Structured Outputs
- `references/039_Guides_Fingerprint.md` - Fingerprint
- `references/040_Guides_Migration.md` - Migration from Other Providers
- `references/041_Guides_GrokCodePromptEngineering.md` - Prompt Engineering for Grok Code

### Grok Business (042-045)
- `references/042_GrokBusiness_UserGuide.md` - Grok.com User Guide
- `references/043_GrokBusiness_LicenseManagement.md` - License & User Management
- `references/044_GrokBusiness_Organization.md` - Organization Management
- `references/045_GrokBusiness_Apps_GoogleDrive.md` - Google Drive Integration

### Resources (046-049)
- `references/046_Resources_CommunityIntegrations.md` - Community Integrations
- `references/047_Resources_FAQ_General.md` - FAQ - General
- `references/048_Resources_FAQ_xAI_API.md` - FAQ - xAI Console
- `references/049_Resources_FAQ_Grok.md` - FAQ - Grok Website/Apps
