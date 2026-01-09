#### [Guides](https://docs.x.ai/docs/guides/using-collections/api#guides)

# Using Collections via API

This guide walks you through managing collections programmatically using the xAI SDK and REST API.

* * *

## [Creating a Management Key](https://docs.x.ai/docs/guides/using-collections/api#creating-a-management-key)

To use the Collections API, you need to create a **Management API Key** with the `AddFileToCollection` permission.

1.  Navigate to **Management Keys** in the [xAI Console](https://console.x.ai/).
2.  Click **Create Management Key**.
3.  Select the `AddFileToCollection` permission and any other required permissions (e.g., from the **Collections Endpoint** group).
4.  Copy and securely store your key immediately; it will not be shown again.

* * *

## [Creating a collection](https://docs.x.ai/docs/guides/using-collections/api#creating-a-collection)

```python
import os
from xai_sdk import Client

client = Client(
    api_key=os.getenv("XAI_API_KEY"),
    management_api_key=os.getenv("XAI_MANAGEMENT_API_KEY"),
    timeout=3600,
)

collection = client.collections.create(
    name="SEC Filings",
)

print(collection)
```

* * *

## [Listing and Updating Collections](https://docs.x.ai/docs/guides/using-collections/api#listing-collections)

### [Listing collections](https://docs.x.ai/docs/guides/using-collections/api#listing-collections)
```python
collections = client.collections.list()
print(collections)
```

### [Updating collection configuration](https://docs.x.ai/docs/guides/using-collections/api#updating-collection-configuration)
```python
collection = client.collections.update(
    "collection_id",
    name="SEC Filings (Revised)"
)
```

* * *

## [Uploading documents](https://docs.x.ai/docs/guides/using-collections/api#uploading-documents)

Uploading a document is a two-step process:
1.  Upload the file to the xAI API.
2.  Add the file to your collection.

```python
with open("report.html", "rb") as file:
    file_data = file.read()

document = client.collections.upload_document(
    collection_id="collection_id",
    name="report.html",
    data=file_data,
    content_type="text/html",
)
print(document)
```

### [Uploading with metadata fields](https://docs.x.ai/docs/guides/using-collections/api#uploading-with-metadata-fields)
```python
document = client.collections.upload_document(
    collection_id="collection_id",
    name="paper.pdf",
    data=file_data,
    content_type="application/pdf",
    fields={
        "author": "Sandra Kim",
        "year": "2024",
        "title": "Q3 Revenue Analysis"
    },
)
```

* * *

## [Searching documents](https://docs.x.ai/docs/guides/using-collections/api#searching-documents)

### [Search modes](https://docs.x.ai/docs/guides/using-collections/api#search-modes)

| Mode | Description | Best for |
| --- | --- | --- |
| **Keyword** | Exact matches of words/numbers | Precise terms (IDs, dates) |
| **Semantic** | Conceptual relationships | Identifying intent and topics |
| **Hybrid** | Combines both (Default) | Most real-world use cases |

### [SDK Search Example](https://docs.x.ai/docs/guides/using-collections/api#searching-documents)
```python
response = client.collections.search(
    query="What were the key revenue drivers?",
    collection_ids=["collection_id"],
    retrieval_mode={"type": "hybrid"}
)
print(response)
```

* * *

## [Deleting Data](https://docs.x.ai/docs/guides/using-collections/api#deleting-a-document)

### [Deleting a document](https://docs.x.ai/docs/guides/using-collections/api#deleting-a-document)
```python
client.collections.remove_document(
    collection_id="collection_id",
    file_id="file_id",
)
```

### [Deleting a collection](https://docs.x.ai/docs/guides/using-collections/api#deleting-a-collection)
```python
client.collections.delete(collection_id="collection_id")
```
