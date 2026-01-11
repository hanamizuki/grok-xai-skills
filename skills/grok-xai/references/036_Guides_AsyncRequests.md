#### [Guides](#guides)

# [Asynchronous Requests](#asynchronous-requests)

When working with the xAI API, you may need to process hundreds or even thousands of requests. Sending these requests sequentially can be extremely time-consuming.

To improve efficiency, you can use `AsyncClient` from `xai_sdk` or `AsyncOpenAI` from `openai`, which allows you to send multiple requests concurrently. The example below is a Python script demonstrating how to use `AsyncClient` to batch and process requests asynchronously, significantly reducing the overall execution time:

The xAI API does not currently offer a batch API.

* * *

## [Rate Limits](#rate-limits)

Adjust the `max_concurrent` param to control the maximum number of parallel requests.

You are unable to concurrently run your requests beyond the rate limits shown in the API console.

Python

Other

```
import asyncio
import os

from xai_sdk import AsyncClient
from xai_sdk.chat import Response, user

async def main():
    client = AsyncClient(
        api_key=os.getenv("XAI_API_KEY"),
        timeout=3600, # Override default timeout with longer timeout for reasoning models
    )

    model = "grok-4"
    requests = [
        "Tell me a joke",
        "Write a funny haiku",
        "Generate a funny X post",
        "Say something unhinged",
    ]


    # Define a semaphore to limit concurrent requests (e.g., max 2 concurrent requests at a time)
    max_in_flight_requests = 2
    semaphore = asyncio.Semaphore(max_in_flight_requests)

    async def process_request(request) -> Response:
        async with semaphore:
            print(f"Processing request: {request}")
            chat = client.chat.create(model=model, max_tokens=100)
            chat.append(user(request))
            return await chat.sample()

    tasks = []
    for request in requests:
        tasks.append(process_request(request))

    responses = await asyncio.gather(*tasks)
    for i, response in enumerate(responses):
        print(f"Total tokens used for response {i}: {response.usage.total_tokens}")

if __name__ == "__main__":
    asyncio.run(main())
```