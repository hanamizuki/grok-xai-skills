#### [Guides](https://docs.x.ai/docs/guides/files/managing-files#guides)

# Managing Files

The Files API provides a complete set of operations for managing your files. Before using files in chat conversations, you need to upload them using one of the methods described below.

**xAI Python SDK Users**: Version 1.4.0 of the xai-sdk package is required to use the Files API.

* * *

## [Uploading Files](https://docs.x.ai/docs/guides/files/managing-files#uploading-files)

You can upload files in several ways: from a file path, raw bytes, BytesIO object, or an open file handle.

### [Upload from File Path](https://docs.x.ai/docs/guides/files/managing-files#upload-from-file-path)
```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Upload a file from disk
file = client.files.upload("/path/to/your/document.pdf")

print(f"File ID: {file.id}")
print(f"Filename: {file.filename}")
print(f"Size: {file.size} bytes")
print(f"Created at: {file.created_at}")
```

* * *

### [Upload from Bytes](https://docs.x.ai/docs/guides/files/managing-files#upload-from-bytes)

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Upload file content directly from bytes
content = b"This is my document content.\nIt can span multiple lines."
file = client.files.upload(content, filename="document.txt")

print(f"File ID: {file.id}")
print(f"Filename: {file.filename}")
```

* * *

### [Upload from file object](https://docs.x.ai/docs/guides/files/managing-files#upload-from-file-object)

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Upload a file directly from disk
file = client.files.upload(open("document.pdf", "rb"), filename="document.pdf")

print(f"File ID: {file.id}")
print(f"Filename: {file.filename}")
```

* * *

## [Upload with Progress Tracking](https://docs.x.ai/docs/guides/files/managing-files#upload-with-progress-tracking)

Track upload progress for large files using callbacks or progress bars.

### [Custom Progress Callback](https://docs.x.ai/docs/guides/files/managing-files#custom-progress-callback)

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Define a custom progress callback
def progress_callback(bytes_uploaded: int, total_bytes: int):
    percentage = (bytes_uploaded / total_bytes) * 100 if total_bytes else 0
    mb_uploaded = bytes_uploaded / (1024 * 1024)
    mb_total = total_bytes / (1024 * 1024)
    print(f"Progress: {mb_uploaded:.2f}/{mb_total:.2f} MB ({percentage:.1f}%)")

# Upload with progress tracking
file = client.files.upload(
    "/path/to/large-file.pdf",
    on_progress=progress_callback
)

print(f"Successfully uploaded: {file.filename}")
```

### [Progress Bar with tqdm](https://docs.x.ai/docs/guides/files/managing-files#progress-bar-with-tqdm)

```python
import os
from xai_sdk import Client
from tqdm import tqdm

client = Client(api_key=os.getenv("XAI_API_KEY"))

file_path = "/path/to/large-file.pdf"
total_bytes = os.path.getsize(file_path)

# Upload with tqdm progress bar
with tqdm(total=total_bytes, unit="B", unit_scale=True, desc="Uploading") as pbar:
    file = client.files.upload(
        file_path,
        on_progress=pbar.update
    )

print(f"Successfully uploaded: {file.filename}")
```

* * *

## [Listing Files](https://docs.x.ai/docs/guides/files/managing-files#listing-files)

Retrieve a list of your uploaded files with pagination and sorting options.

### [Available Options](https://docs.x.ai/docs/guides/files/managing-files#available-options)
*   **`limit`**: Maximum number of files to return. Defaults to 100.
*   **`order`**: Sort order (`"asc"` or `"desc"`).
*   **`sort_by`**: Field to sort by (`"created_at"`, `"filename"`, or `"size"`).
*   **`pagination_token`**: Token for fetching the next page.

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# List files with pagination and sorting
response = client.files.list(
    limit=10,
    order="desc",
    sort_by="created_at"
)

for file in response.data:
    print(f"File: {file.filename} (ID: {file.id}, Size: {file.size} bytes)")
```

* * *

## [Getting File Metadata](https://docs.x.ai/docs/guides/files/managing-files#getting-file-metadata)

Retrieve detailed information about a specific file.

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Get file metadata by ID
file = client.files.get("file-abc123")

print(f"Filename: {file.filename}")
print(f"Size: {file.size} bytes")
print(f"Created: {file.created_at}")
print(f"Team ID: {file.team_id}")
```

* * *

## [Getting File Content](https://docs.x.ai/docs/guides/files/managing-files#getting-file-content)

Download the actual content of a file.

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Get file content
content = client.files.content("file-abc123")

# Content is returned as bytes
print(f"Content length: {len(content)} bytes")
print(f"Content preview: {content[:100]}")
```

* * *

## [Deleting Files](https://docs.x.ai/docs/guides/files/managing-files#deleting-files)

Remove files when they're no longer needed.

```python
import os
from xai_sdk import Client

client = Client(api_key=os.getenv("XAI_API_KEY"))

# Delete a file
delete_response = client.files.delete("file-abc123")

print(f"Deleted: {delete_response.deleted}")
print(f"File ID: {delete_response.id}")
```

* * *

## [Limitations and Considerations](https://docs.x.ai/docs/guides/files/managing-files#limitations-and-considerations)

### [File Size Limits](https://docs.x.ai/docs/guides/files/managing-files#file-size-limits)
*   **Maximum file size**: 48 MB per file

### [File Retention](https://docs.x.ai/docs/guides/files/managing-files#file-retention)
*   **Cleanup**: Delete files when no longer needed storage.
*   **Access**: Files are scoped to your team/organization.

### [Supported Formats](https://docs.x.ai/docs/guides/files/managing-files#supported-formats)
Supported file types include:
*   Plain text files (.txt)
*   Markdown files (.md)
*   Code files (.py, .js, .java, etc.)
*   CSV files (.csv)
*   JSON files (.json)
*   PDF documents (.pdf)
