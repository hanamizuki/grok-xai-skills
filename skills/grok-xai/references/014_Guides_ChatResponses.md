#### [Guides](#guides)

# [Chat Responses](#chat-responses)

Chat Responses is the preferred way of interacting with our models via API. It allows an optional **stateful interaction** with our models, where **previous input prompts, reasoning content and model responses are saved by us**. A user can continue the interaction by appending new prompt messages, rather than sending all of the previous messages. This is by default turned on. If you would like to store your request/response locally, please see [Disable storing previous request/response on server](#disable-storing-previous-requestresponse-on-server).

Although you don't need to enter the conversation history in the request body, you will still be billed for the entire conversation history when using Responses API. The cost might be reduced as part of the conversation history are [automatically cached](../models#cached-prompt-tokens).

**The responses will be stored for 30 days, after which they will be removed. This means you can use the request ID to retrieve or continue a conversation within 30 days of sending the request.** If you want to continue a conversation after 30 days, please store your responses history and the encrypted thinking content locally, and pass them in a new request body.

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

## [Creating a new model response](#creating-a-new-model-response)

The first step in using Responses API is analogous to using Chat Completions API. You will create a new response with prompts. By default, your request/response history is stored on our server.

`instructions` parameter is currently not supported. The API will return an error if it is specified.

Python Javascript

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

chat = client.chat.create(model="grok-4")
chat.append(system("You are Grok, a chatbot inspired by the Hitchhiker's Guide to the Galaxy."))
chat.append(user("What is the meaning of life, the universe, and everything?"))
response = chat.sample()

print(response)

# The response id that can be used to continue the conversation later

print(response.id)
```

The `developer` role is supported as an alias for `system`. Only a **single** system/developer message should be used, and it should always be the **first message** in your conversation.

### [Disable storing previous request/response on server](#disable-storing-previous-requestresponse-on-server)

If you do not want to store your previous request/response on the server, you can set `store: false` on the request.

Python

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

chat = client.chat.create(model="grok-4", store_messages=False)
chat.append(system("You are Grok, a chatbot inspired by the Hitchhiker's Guide to the Galaxy."))
chat.append(user("What is the meaning of life, the universe, and everything?"))
response = chat.sample()

print(response)
```

### [Returning encrypted thinking content](#returning-encrypted-thinking-content)

If you want to return the encrypted thinking traces, you need to specify `use_encrypted_content=True` in xAI SDK or gRPC request message, or `include: ["reasoning.encrypted_content"]` in the request body.

Modify the steps to create a chat client (xAI SDK) or change the request body as following:

Python Javascript

Other

```
chat = client.chat.create(model="grok-4",
        use_encrypted_content=True)
```

See [Adding encrypted thinking content](#adding-encrypted-thinking-content) on how to use the returned encrypted thinking content when making a new request.

* * *

## [Image understanding](#image-understanding)

When sending images, it is advised to not store request/response history on the server. Otherwise the request may fail. See [Disable storing previous request/response on server](#disable-storing-previous-requestresponse-on-server).

Some models allow image in the input. The model will consider the image context, when generating the response.

## [Constructing the message body - difference from text-only prompt](#constructing-the-message-body---difference-from-text-only-prompt)

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
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

image_url = "https://science.nasa.gov/wp-content/uploads/2023/09/web-first-images-release.png"


chat = client.chat.create(model="grok-4")
chat.append(
    user(
        "What's in this image?",
        image(image_url=image_url, detail="high"),
    )
)

response = chat.sample()
print(response)

# The response id that can be used to continue the conversation later

print(response.id)
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

* * *

## [Chaining the conversation](#chaining-the-conversation)

We now have the `id` of the first response. With Chat Completions API, we typically send a stateless new request with all the previous messages.

With Responses API, we can send the `id` of the previous response, and the new messages to append to it.

Python Javascript

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

chat = client.chat.create(model="grok-4", store_messages=True)
chat.append(system("You are Grok, a chatbot inspired by the Hitchhiker's Guide to the Galaxy."))
chat.append(user("What is the meaning of life, the universe, and everything?"))
response = chat.sample()

print(response)

# The response id that can be used to continue the conversation later

print(response.id)

# New steps

chat = client.chat.create(
    model="grok-4",
    previous_response_id=response.id,
    store_messages=True,
)
chat.append(user("What is the meaning of 42?"))
second_response = chat.sample()

print(second_response)

# The response id that can be used to continue the conversation later

print(second_response.id)
```

### [Adding encrypted thinking content](#adding-encrypted-thinking-content)

After returning the encrypted thinking content, you can also add it to a new response's input:

Python Javascript

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

chat = client.chat.create(model="grok-4", store_messages=True, use_encrypted_content=True)
chat.append(system("You are Grok, a chatbot inspired by the Hitchhiker's Guide to the Galaxy."))
chat.append(user("What is the meaning of life, the universe, and everything?"))
response = chat.sample()

print(response)

# The response id that can be used to continue the conversation later

print(response.id)

# New steps

chat.append(response)  ## Append the response and the SDK will automatically add the outputs from response to message history

chat.append(user("What is the meaning of 42?"))
second_response = chat.sample()

print(second_response)

# The response id that can be used to continue the conversation later

print(second_response.id)
```

* * *

## [Retrieving a previous model response](#retrieving-a-previous-model-response)

If you have a previous response's ID, you can retrieve the content of the response.

Python Javascript

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

response = client.chat.get_stored_completion("<The previous response's id>")

print(response)
```

* * *

## [Delete a model response](#delete-a-model-response)

If you no longer want to store the previous model response, you can delete it.

Python Javascript

Other

```
import os
from xai_sdk import Client
from xai_sdk.chat import user, system

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

response = client.chat.delete_stored_completion("<The previous response's id>")
print(response)
```