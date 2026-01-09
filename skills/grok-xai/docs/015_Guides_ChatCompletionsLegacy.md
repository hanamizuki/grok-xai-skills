#### [Guides](#guides)

# [Chat Completions](#chat-completions)

Chat Completions is offered as a legacy endpoint. Most of our new features will come to [Chat Responses](chat) first.

Text in, text out. Chat is the most popular feature on the xAI API, and can be used for anything from summarizing articles, generating creative writing, answering questions, providing customer support, to assisting with coding tasks.

* * *

## [Prerequisites](#prerequisites)

*   xAI Account: You need an xAI account to access the API.
*   API Key: Ensure that your API key has access to the chat endpoint and the chat model is enabled.

If you don't have these and are unsure of how to create one, follow [the Hitchhiker's Guide to Grok](../tutorial).

You can create an API key on the [xAI Console API Keys Page](https://console.x.ai/team/default/api-keys).

Set your API key in your environment:

Bash

```
export XAI_API_KEY="your_api_key"
```

* * *

## [A basic chat completions example](#a-basic-chat-completions-example)

You can also stream the response, which is covered in [Streaming Response](streaming-response).

The user sends a request to the xAI API endpoint. The API processes this and returns a complete response.

Python Javascript

Other

```
import os

from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    timeout=3600, # Override default timeout with longer timeout for reasoning models
)

chat = client.chat.create(model="grok-4")
chat.append(system("You are a PhD-level mathematician."))
chat.append(user("What is 2 + 2?"))

response = chat.sample()
print(response.content)
```

Response:

Python Javascript

Other

```
'2 + 2 equals 4.'
```

* * *

## [Conversations](#conversations)

The xAI API is stateless and does not process new request with the context of your previous request history.

However, you can provide previous chat generation prompts and results to a new chat generation request to let the model process your new request with the context in mind.

An example message:

JSON

```
{
  "role": "system",
  "content": [{ "type": "text", "text": "You are a helpful and funny assistant."}]
}
{
  "role": "user",
  "content": [{ "type": "text", "text": "Why don't eggs tell jokes?" }]
},
{
  "role": "assistant",
  "content": [{ "type": "text", "text": "They'd crack up!" }]
},
{
  "role": "user",
  "content": [{"type": "text", "text": "Can you explain the joke?"}],
}
```

By specifying roles, you can change how the model ingests the content. The `system` role content should define, in an instructive tone, the way the model should respond to user request. The `user` role content is usually used for user requests or data sent to the model. The `assistant` role content is usually either in the model's response, or when sent within the prompt, indicates the model's response as part of conversation history.

The `developer` role is supported as an alias for `system`. Only a **single** system/developer message should be used, and it should always be the **first message** in your conversation.

* * *

## [Image understanding](#image-understanding)

Some models allow image in the input. The model will consider the image context, when generating the response.

### [Constructing the message body - difference from text-only prompt](#constructing-the-message-body---difference-from-text-only-prompt)

The request message to image understanding is similar to text-only prompt. The main difference is that instead of text input:

JSON

```
[
{
    "role": "user",
    "content": "What is in this image?"
}
]
```

We send in `content` as a list of objects:

JSON

```
[
{
    "role": "user",
    "content": [
{
    "type": "image_url",
    "image_url": {
    "url": "data:image/jpeg;base64,<base64_image_string>",
    "detail": "high"
}
},
{
    "type": "text",
    "text": "What is in this image?"
}
    ]
}
]
```

The `image_url.url` can also be the image's url on the Internet.

### [Image understanding example](#image-understanding-example)

Python Javascript

Other

```
import os

from xai_sdk import Client
from xai_sdk.chat import user, image

client = Client(api_key=os.getenv('XAI_API_KEY'))

image_url = "https://science.nasa.gov/wp-content/uploads/2023/09/web-first-images-release.png"

chat = client.chat.create(model="grok-4")
chat.append(
    user(
        "What's in this image?",
        image(image_url=image_url, detail="high"),
    )
)

response = chat.sample()
print(response.content)
```

### [Image input general limits](#image-input-general-limits)

*   Maximum image size: `20MiB`
*   Maximum number of images: No limit
*   Supported image file types: `jpg/jpeg` or `png`.
*   Any image/text input order is accepted (e.g. text prompt can precede image prompt)

### [Image detail levels](#image-detail-levels)

The `"detail"` field controls the level of pre-processing applied to the image that will be provided to the model. It is optional and determines the resolution at which the image is processed. The possible values for `"detail"` are:

*   **`"auto"`**: The system will automatically determine the image resolution to use. This is the default setting, balancing speed and detail based on the model's assessment.
*   **`"low"`**: The system will process a low-resolution version of the image. This option is faster and consumes fewer tokens, making it more cost-effective, though it may miss finer details.
*   **`"high"`**: The system will process a high-resolution version of the image. This option is slower and more expensive in terms of token usage, but it allows the model to attend to more nuanced details in the image.