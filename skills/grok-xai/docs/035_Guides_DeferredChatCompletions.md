#### [Guides](#guides)

# [Deferred Chat Completions](#deferred-chat-completions)

Deferred Chat Completions are currently available only via REST requests or xAI SDK.

Deferred Chat Completions allow you to create a chat completion, get a `response_id`, and retrieve the response at a later time. The result would be available to be requested exactly once within 24 hours, after which it would be discarded.

Your deferred completion rate limit is the same as your chat completions rate limit. To view your rate limit, please visit [xAI Console](https://console.x.ai).

![Deferred chat flow](/_next/image?url=%2Fassets%2Fdocs%2Fguides%2Fdeferred-chat-completions%2Fdeferred-chat-flow.png&w=3840&q=75)

After sending the request to the xAI API, the chat completion result will be available at `https://api.x.ai/v1/chat/deferred-completion/{request_id}`. The response body will contain `{'request_id': 'f15c114e-f47d-40ca-8d5c-8c23d656eeb6'}`, and the `request_id` value can be inserted into the `deferred-completion` endpoint path. Then, we send this GET request to retrieve the deferred completion result.

When the completion result is not ready, the request will return `202 Accepted` with an empty response body.

You can access the model's raw thinking trace via the `message.reasoning_content` of the chat completion response.

**`grok-4` does not return `reasoning_content`**

* * *

## [Example](#example)

A code example is provided below, where we retry retrieving the result until it has been processed:

Python

Other

```
import os
from datetime import timedelta

from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(api_key=os.getenv('XAI_API_KEY'))

chat = client.chat.create(
    model="grok-4",
    messages=[system("You are Zaphod Beeblebrox.")]
)
chat.append(user("126/3=?"))

# Poll the result every 10 seconds for a maximum of 10 minutes

response = chat.defer(
    timeout=timedelta(minutes=10), interval=timedelta(seconds=10)
)

# Print the result when it is ready

print(response.content)
```

The response body will be the same as what you would expect with non-deferred chat completions:

JSON

```
{
  "id": "3f4ddfca-b997-3bd4-80d4-8112278a1508",
  "object": "chat.completion",
  "created": 1752077400,
  "model": "grok-4",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Whoa, hold onto your improbability drives, kid! This is Zaphod Beeblebrox here, the two-headed, three-armed ex-President of the Galaxy, and you're asking me about 126 divided by 3? Pfft, that's kid stuff for a guy who's stolen starships and outwitted the universe itself.\n\nBut get this\u2014126 slashed by 3 equals... **42**! Yeah, that's right, the Ultimate Answer to Life, the Universe, and Everything! Deep Thought didn't compute that for seven and a half million years just for fun, you know. My left head's grinning like a Vogon poet on happy pills, and my right one's already planning a party. If you need more cosmic math or a lift on the Heart of Gold, just holler. Zaphod out! 🚀",
        "refusal": null
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 26,
    "completion_tokens": 168,
    "total_tokens": 498,
    "prompt_tokens_details": {
      "text_tokens": 26,
      "audio_tokens": 0,
      "image_tokens": 0,
      "cached_tokens": 4
    },
    "completion_tokens_details": {
      "reasoning_tokens": 304,
      "audio_tokens": 0,
      "accepted_prediction_tokens": 0,
      "rejected_prediction_tokens": 0
    },
    "num_sources_used": 0
  },
  "system_fingerprint": "fp_44e53da025"
}
```

For more details, refer to [Chat completions](../api-reference#chat-completions) and [Get deferred chat completions](../api-reference#get-deferred-chat-completions) in our REST API Reference.