#### [Guides](#guides)

# [Grok Voice Agent API](#grok-voice-agent-api)

We're introducing a new API for voice interactions with Grok. We're initially launching with the Grok Voice Agent API and will soon be launching dedicated speech-to-text and text-to-speech APIs.

* * *

## [Grok Voice Agent API](#grok-voice-agent-api-1)

Build powerful real-time voice applications with the Grok Voice Agent API. Create interactive voice conversations with Grok models via WebSocket for voice assistants, phone agents, and interactive voice applications.

**WebSocket Endpoint:** `wss://api.x.ai/v1/realtime`

* * *

### [Enterprise Ready](#enterprise-ready)

Optimized for enterprise use cases across Customer Support, Medical, Legal, Finance, Insurance, and more. The Grok Voice Agent API delivers the reliability and precision that regulated industries demand.

*   **Telephony** - Connect to platforms like Twilio, Vonage, and other SIP providers
*   **Tool Calling** - CRMs, calendars, ticketing systems, databases, and custom APIs
*   **Multilingual** - Serve global customers in their native language with natural accents
*   **Low Latency** - Real-time responses for natural, human-like conversations
*   **Accuracy** - Precise transcription and understanding of critical information:
    *   Industry-specific terminology including medical, legal, and financial vocabulary
    *   Email addresses, dates, and alphanumeric codes
    *   Names, addresses, and phone numbers

* * *

### [Getting Started](#getting-started)

The Grok Voice Agent API enables interactive voice conversations with Grok models via WebSocket. Perfect for building voice assistants, phone agents, and interactive voice applications.

**Use Cases:**

*   Voice Assistants for web and mobile
*   AI-powered phone systems with Twilio
*   Real-time customer support
*   Interactive Voice Response (IVR) systems

[Documentation →](/docs/guides/voice/agent)

* * *

### [Low Latency](#low-latency)

Built for real-time conversations. The Grok Voice Agent API is optimized for minimal response times, enabling natural back-and-forth dialogue without awkward pauses. Stream audio bidirectionally over WebSocket for instant voice-to-voice interactions that feel like talking to a human.

* * *

### [Multilingual with Natural Accents](#multilingual-with-natural-accents)

The Grok Voice Agent API speaks over 100 languages with native-quality accents. The model automatically detects the input language and responds naturally in the same language-no configuration required.

* * *

### [Supported Languages](#supported-languages)

English, Spanish, French, German, Italian, Portuguese, Dutch, Russian, Chinese (Mandarin), Japanese, Korean, Arabic, Hindi, Turkish, Polish, Swedish, Danish, Norwegian, Finnish, Czech, and many more.

Each language features natural pronunciation, appropriate intonation patterns, and culturally-aware speech rhythms. You can also specify a preferred language or accent in your system instructions for consistent multilingual experiences.

* * *

### [Tool Calling](#tool-calling)

Extend your voice agent's capabilities with powerful built-in tools that execute during conversations:

*   **Web Search** - Real-time internet search for current information, news, and facts
*   **X Search** - Search posts, trends, and discussions from X
*   **Collections** - RAG-powered search over your uploaded documents and knowledge bases
*   **Custom Functions** - Define your own tools with JSON schemas for booking, lookups, calculations, and more

Tools are called automatically based on conversation context. Your voice agent can search the web, query your documents, and execute custom business logic-all while maintaining a natural conversation flow.

* * *

### [Voice Personalities](#voice-personalities)

Choose from 5 distinct voices, each with unique characteristics suited to different applications:

Voice

Type

Tone

Description

Sample

**`Ara`**

Female

Warm, friendly

Default voice, balanced and conversational

**`Rex`**

Male

Confident, clear

Professional and articulate, ideal for business applications

**`Sal`**

Neutral

Smooth, balanced

Versatile voice suitable for various contexts

**`Eve`**

Female

Energetic, upbeat

Engaging and enthusiastic, great for interactive experiences

**`Leo`**

Male

Authoritative, strong

Decisive and commanding, suitable for instructional content

* * *

### [Flexible Audio Formats](#flexible-audio-formats)

Support for multiple audio formats and sample rates to match your application's requirements:

*   **PCM (Linear16)** - High-quality audio with configurable sample rates (8kHz–48kHz)
*   **G.711 μ-law** - Optimized for telephony applications
*   **G.711 A-law** - Standard for international telephony

* * *

### [Example Applications](#example-applications)

Complete working examples are available demonstrating various voice integration patterns:

#### [Web Voice Agent](#web-voice-agent)

Real-time voice chat in the browser with React frontend and Python/Node.js backends.

**Architecture:**

Text

```
Browser (React) ←WebSocket→ Backend (FastAPI/Express) ←WebSocket→ xAI API
```

**Features:**

*   Real-time audio streaming
*   Visual transcript display
*   Debug console for development
*   Interchangeable backends

[View Web Example →](https://github.com/xai-org/xai-cookbook/tree/main/voice-examples/agent/web)

#### [Phone Voice Agent (Twilio)](#phone-voice-agent-twilio)

AI-powered phone system using Twilio integration.

**Architecture:**

Text

```
Phone Call ←SIP→ Twilio ←WebSocket→ Node.js Server ←WebSocket→ xAI API
```

**Features:**

*   Phone call integration
*   Real-time voice processing
*   Function/tool calling support
*   Production-ready architecture

[View Telephony Example →](https://github.com/xai-org/xai-cookbook/tree/main/voice-examples/agent/telephony)

#### [WebRTC Voice Agent](#webrtc-voice-agent)

The Grok Voice Agent API uses WebSocket connections. Direct WebRTC connections are not available currently.

You can use a WebRTC server to connect the client to a server that then connects to the Grok Voice Agent API.

**Architecture:**

Text

```
Browser (React) ←WebRTC→ Backend (Express) ←WebSocket→ xAI API
```

**Features:**

*   Real-time audio streaming
*   Visual transcript display
*   Debug console for development
*   WebRTC backend handles all WebSocket connections to xAI API

[View WebRTC Example →](https://github.com/xai-org/xai-cookbook/tree/main/voice-examples/agent/webrtc).