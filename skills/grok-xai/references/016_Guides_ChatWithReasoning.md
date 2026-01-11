#### [Guides](#guides)

# [Reasoning](#reasoning)

`grok-4-fast-non-reasoning` variant is based on `grok-4-fast-reasoning` with reasoning disabled.

`presencePenalty`, `frequencyPenalty` and `stop` parameters are not supported by reasoning models. Adding them in the request would result in an error.

* * *

## [Key Features](#key-features)

*   **Think Before Responding**: Thinks through problems step-by-step before delivering an answer.
*   **Math & Quantitative Strength**: Excels at numerical challenges and logic puzzles.
*   **Reasoning Trace**: The model's thoughts are available via the `reasoning_content` or `encrypted_content` field in the response completion object (see example below).

You can access the model's raw thinking trace via the `message.reasoning_content` of the chat completion response. Only `grok-3-mini` returns `reasoning_content`.

  

`grok-3`, `grok-4` and `grok-4-fast-reasoning` do not return `reasoning_content`. It may optionally return [encrypted reasoning content](#encrypted-reasoning-content) instead.

* * *

### [Encrypted Reasoning Content](#encrypted-reasoning-content)

For `grok-4`, the reasoning content is encrypted by us and sent back if `use_encrypted_content` is set to `true`. You can send the encrypted content back to provide more context to a previous conversation. See [Stateful Response with Responses API](responses-api) for more details on how to use the content.

* * *

## [Control how hard the model thinks](#control-how-hard-the-model-thinks)

`reasoning_effort` is not supported by `grok-3`, `grok-4` and `grok-4-fast-reasoning`. Specifying `reasoning_effort` parameter will get an error response. Only `grok-3-mini` supports `reasoning_effort`.

The `reasoning_effort` parameter controls how much time the model spends thinking before responding. It must be set to one of these values:

*   **`low`**: Minimal thinking time, using fewer tokens for quick responses.
*   **`high`**: Maximum thinking time, leveraging more tokens for complex problems.

Choosing the right level depends on your task: use `low` for simple queries that should complete quickly, and `high` for harder problems where response latency is less important.

* * *

## [Usage Example](#usage-example)

Here’s a simple example using `grok-3-mini` to multiply 101 by 3.

Python Javascript

Other

```
import os

from xai_sdk import Client
from xai_sdk.chat import system, user

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    timeout=3600, # Override default timeout with longer timeout for reasoning models
)

chat = client.chat.create(
    model="grok-3-mini",
    reasoning_effort="high",
    messages=[system("You are a highly intelligent AI assistant.")],
)
chat.append(user("What is 101*3?"))

response = chat.sample()

print("Final Response:")
print(response.content)

print("Number of completion tokens:")
print(response.usage.completion_tokens)

print("Number of reasoning tokens:")
print(response.usage.reasoning_tokens)
```

### [Sample Output](#sample-output)

Output

```

Final Response:
The result of 101 multiplied by 3 is 303.

Number of completion tokens:
14

Number of reasoning tokens:
310
```

* * *

## [Notes on Consumption](#notes-on-consumption)

When you use a reasoning model, the reasoning tokens are also added to your final consumption amount. The reasoning token consumption will likely increase when you use a higher `reasoning_effort` setting.