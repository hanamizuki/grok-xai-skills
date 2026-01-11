#### [Guides](https://docs.x.ai/docs/guides/voice/agent#guides)

# Grok Voice Agent API

Build interactive voice conversations with Grok models using WebSocket. The Grok Voice Agent API accepts audio and text inputs and creates text and audio responses in real-time.

**WebSocket Endpoint:** 
`wss://api.x.ai/v1/realtime`

---

## [Authentication](https://docs.x.ai/docs/guides/voice/agent#authentication)

You can authenticate WebSocket connections using the xAI API key or an ephemeral token.

> [!IMPORTANT]
> It is recommended to use an ephemeral token when authenticating from the client side (e.g., browser) to prevent API key exposure.

### [Fetching Ephemeral Tokens](https://docs.x.ai/docs/guides/voice/agent#fetching-ephemeral-tokens)
Set up a server to fetch the ephemeral token from xAI.

**Endpoint:** `POST https://api.x.ai/v1/realtime/client_secrets`

**Python (FastAPI) Example:**
```python
@app.post("/session")
async def get_ephemeral_token():
    async with httpx.AsyncClient() as client:
        response = await client.post(
            url="https://api.x.ai/v1/realtime/client_secrets",
            headers={"Authorization": f"Bearer {XAI_API_KEY}"},
            json={"expires_after": {"seconds": 300}},
        )
    return response.json()
```

### [Using API Key Directly](https://docs.x.ai/docs/guides/voice/agent#using-api-key-directly)
For server-side applications:
```python
async with websockets.connect(
    uri="wss://api.x.ai/v1/realtime",
    additional_headers={"Authorization": f"Bearer {XAI_API_KEY}"}
) as websocket:
    pass
```

---

## [Voice Options](https://docs.x.ai/docs/guides/voice/agent#voice-options)

| Voice | Type | Tone | Description |
| :--- | :--- | :--- | :--- |
| **Ara** | Female | Warm, friendly | Default voice, balanced |
| **Rex** | Male | Confident, clear | Professional and articulate |
| **Sal** | Neutral | Smooth, balanced | Versatile |
| **Eve** | Female | Energetic, upbeat | Engaging |
| **Leo** | Male | Authoritative, strong | Commanding |

---

## [Audio Format](https://docs.x.ai/docs/guides/voice/agent#audio-format)

Audio data must be encoded as **base64** strings.

| Format | Encoding | Sample Rate |
| :--- | :--- | :--- |
| `audio/pcm` | Linear16, LE | 8k - 48k (Default 24k) |
| `audio/pcmu` | G.711 μ-law | 8000 Hz |
| `audio/pcma` | G.711 A-law | 8000 Hz |

---

## [Message Types](https://docs.x.ai/docs/guides/voice/agent#message-types)

### [Client Events](https://docs.x.ai/docs/guides/voice/agent#client-events)
* `session.update`: Update configuration (prompt, voice, tools).
* `input_audio_buffer.append`: Send base64 audio chunks.
* `response.create`: Manually trigger a response.

### [Server Events](https://docs.x.ai/docs/guides/voice/agent#server-events)
* `input_audio_buffer.speech_started/stopped`: VAD events.
* `response.audio_transcript.delta`: Text stream.
* `response.audio.delta`: Audio stream chunks.
* `response.done`: Response complete.

---

## [Using Tools](https://docs.x.ai/docs/guides/voice/agent#using-tools)

Supports **Collections Search**, **Web Search**, **X Search**, and **Custom Functions**.

```json
{
    "type": "session.update",
    "session": {
        "tools": [
            {"type": "web_search"},
            {"type": "function", "name": "get_weather", "parameters": {...}}
        ]
    }
}
```

---

## [Complete Example (Python)](https://docs.x.ai/docs/guides/voice/agent#complete-example-python)

```python
import asyncio
import json
import websockets

async def main():
    async with websockets.connect(
        "wss://api.x.ai/v1/realtime",
        additional_headers={"Authorization": f"Bearer {XAI_API_KEY}"}
    ) as ws:
        # 1. Update Session
        await ws.send(json.dumps({
            "type": "session.update",
            "session": {"voice": "Ara", "turn_detection": {"type": "server_vad"}}
        }))

        # 2. Handle Messages
        async for message in ws:
            event = json.loads(message)
            if event["type"] == "response.audio_transcript.delta":
                print(event["delta"], end="", flush=True)
```
