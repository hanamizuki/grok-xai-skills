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
| **Getting Started** | `docs/002_GettingStarted_Introduction.md` |
| **Models & Pricing** | `docs/004_GettingStarted_ModelsAndPricing.md` |
| **Chat Responses API** | `docs/014_Guides_ChatResponses.md` |
| **Reasoning (grok-4)** | `docs/016_Guides_ChatWithReasoning.md` |

## Agentic Tools (Important)

| Tool | File |
|------|------|
| **Tools Overview** | `docs/017_Guides_Tools_Overview.md` |
| **x_search / web_search** | `docs/018_Guides_Tools_SearchTools.md` |
| **Code Execution** | `docs/019_Guides_Tools_CodeExecution.md` |
| **Collections Search** | `docs/020_Guides_Tools_CollectionsSearch.md` |
| **Remote MCP** | `docs/021_Guides_Tools_RemoteMCP.md` |
| **Advanced Usage** | `docs/022_Guides_Tools_AdvancedUsage.md` |

## Files & Collections

| Topic | File |
|-------|------|
| **Files Overview** | `docs/023_Guides_Files_Overview.md` |
| **Managing Files** | `docs/024_Guides_Files_ManagingFiles.md` |
| **Chat with Files** | `docs/025_Guides_Files_ChatWithFiles.md` |
| **Collections Overview** | `docs/009_KeyInfo_Collections.md` |
| **Using Collections** | `docs/027_Guides_UsingCollections_Overview.md` |

## Other APIs

| Topic | File |
|-------|------|
| **Function Calling** | `docs/037_Guides_FunctionCalling.md` |
| **Structured Outputs** | `docs/038_Guides_StructuredOutputs.md` |
| **Image Generation** | `docs/031_Guides_ImageGenerations.md` |
| **Voice API** | `docs/032_Guides_Voice_Overview.md`, `docs/033_Guides_Voice_GrokVoiceAgentAPI.md` |
| **Streaming** | `docs/034_Guides_StreamResponse.md` |
| **Async Requests** | `docs/036_Guides_AsyncRequests.md` |

## Reference

| Topic | File |
|-------|------|
| **Rate Limits** | `docs/007_KeyInfo_RateLimits.md` |
| **Regional Endpoints** | `docs/008_KeyInfo_RegionalEndpoints.md` |
| **Debugging Errors** | `docs/013_KeyInfo_Debugging.md` |
| **Migration from OpenAI/Anthropic** | `docs/040_Guides_Migration.md` |

## All Files

### Getting Started (001-005)
- `docs/001_GettingStarted_Overview.md` - Overview
- `docs/002_GettingStarted_Introduction.md` - Introduction
- `docs/003_GettingStarted_GuideToGrok.md` - The Hitchhiker's Guide to Grok
- `docs/004_GettingStarted_ModelsAndPricing.md` - Models and Pricing
- `docs/005_GettingStarted_WhatsNew.md` - Release Notes

### Key Information (006-013)
- `docs/006_KeyInfo_ManageBilling.md` - Manage Billing
- `docs/007_KeyInfo_RateLimits.md` - Consumption and Rate Limits
- `docs/008_KeyInfo_RegionalEndpoints.md` - Regional Endpoints
- `docs/009_KeyInfo_Collections.md` - Collections
- `docs/010_KeyInfo_ManagementAPI.md` - Management API
- `docs/011_KeyInfo_UsageExplorer.md` - Usage Explorer
- `docs/012_KeyInfo_MigratingModels.md` - Migrating to New Models
- `docs/013_KeyInfo_Debugging.md` - Debugging Errors

### Guides - Chat (014-016)
- `docs/014_Guides_ChatResponses.md` - Chat Responses (recommended)
- `docs/015_Guides_ChatCompletionsLegacy.md` - Chat Completions (legacy)
- `docs/016_Guides_ChatWithReasoning.md` - Reasoning

### Guides - Tools (017-022)
- `docs/017_Guides_Tools_Overview.md` - Tools Overview
- `docs/018_Guides_Tools_SearchTools.md` - Search Tools (x_search, web_search)
- `docs/019_Guides_Tools_CodeExecution.md` - Code Execution
- `docs/020_Guides_Tools_CollectionsSearch.md` - Collections Search
- `docs/021_Guides_Tools_RemoteMCP.md` - Remote MCP Tools
- `docs/022_Guides_Tools_AdvancedUsage.md` - Advanced Usage

### Guides - Files (023-025)
- `docs/023_Guides_Files_Overview.md` - Files Overview
- `docs/024_Guides_Files_ManagingFiles.md` - Managing Files
- `docs/025_Guides_Files_ChatWithFiles.md` - Chat with Files

### Guides - Other (026-041)
- `docs/026_Guides_LiveSearch.md` - Live Search (deprecated, use Tools)
- `docs/027_Guides_UsingCollections_Overview.md` - Using Collections Overview
- `docs/028_Guides_UsingCollections_Console.md` - Using Collections in Console
- `docs/029_Guides_UsingCollections_API.md` - Using Collections via API
- `docs/030_Guides_UsingCollections_Metadata.md` - Collection Metadata
- `docs/031_Guides_ImageGenerations.md` - Image Generations
- `docs/032_Guides_Voice_Overview.md` - Voice Overview
- `docs/033_Guides_Voice_GrokVoiceAgentAPI.md` - Grok Voice Agent API
- `docs/034_Guides_StreamResponse.md` - Streaming Response
- `docs/035_Guides_DeferredChatCompletions.md` - Deferred Chat Completions
- `docs/036_Guides_AsyncRequests.md` - Asynchronous Requests
- `docs/037_Guides_FunctionCalling.md` - Function Calling
- `docs/038_Guides_StructuredOutputs.md` - Structured Outputs
- `docs/039_Guides_Fingerprint.md` - Fingerprint
- `docs/040_Guides_Migration.md` - Migration from Other Providers
- `docs/041_Guides_GrokCodePromptEngineering.md` - Prompt Engineering for Grok Code

### Grok Business (042-045)
- `docs/042_GrokBusiness_UserGuide.md` - Grok.com User Guide
- `docs/043_GrokBusiness_LicenseManagement.md` - License & User Management
- `docs/044_GrokBusiness_Organization.md` - Organization Management
- `docs/045_GrokBusiness_Apps_GoogleDrive.md` - Google Drive Integration

### Resources (046-049)
- `docs/046_Resources_CommunityIntegrations.md` - Community Integrations
- `docs/047_Resources_FAQ_General.md` - FAQ - General
- `docs/048_Resources_FAQ_xAI_API.md` - FAQ - xAI Console
- `docs/049_Resources_FAQ_Grok.md` - FAQ - Grok Website/Apps
