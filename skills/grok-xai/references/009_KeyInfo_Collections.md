#### [Key Information](#key-information)

# [Collections](#collections)

Collections offers xAI API users a robust set of tools and methods to seamlessly integrate their enterprise requirements and internal knowledge bases with the xAI API. This feature enables efficient management, retrieval, and utilization of documents to enhance AI-driven workflows and applications.

There are two entities that users can create within the Collections service:

*   `file`
    *   A `file` is a single entity of a user-uploaded file.
*   `collection`
    *   A `collection` is a group of `files` linked together, with an embedding index for efficient retrieval of each `file`.
    *   When you create a `collection` you have the option to automatically generate embeddings for any files uploaded to that `collection`. You can then perform semantic search across files in multiple `collections`.
    *   A single `file` can belong to multiple `collections` but must be part of at least one `collection`.

* * *

## [File storage and retrieval](#file-storage-and-retrieval)

Visit the **Collections** tab on the [xAI Console](https://console.x.ai) to create a new `collection`. Once created, you can add `files` to the `collection`. You can also add `files` without adding them to a `collection` using our [Files API](/docs/guides/files/managing-files).

All your `collections` and their associated `files` can be viewed in the **Collections** tab.

Your `files` and their embedding index are securely encrypted and stored on our servers. The index enables efficient retrieval of `files` during a relevance search.

* * *

## [Metadata Fields](#metadata-fields)

Collections support **metadata fields** — structured attributes you can attach to documents for enhanced retrieval and data integrity:

*   **Filtered retrieval** — Narrow search results to documents matching specific criteria (e.g., `author="Sandra Kim"`)
*   **Contextual embeddings** — Inject metadata into chunks to improve retrieval accuracy (e.g., prepending document title to each chunk)
*   **Data integrity constraints** — Enforce required fields or uniqueness across documents

When creating a collection, define metadata fields with options like `required`, `unique`, and `inject_into_chunk` to control how metadata is validated and used during search.

[Learn more about metadata fields →](/docs/guides/using-collections/metadata)

* * *

## [Usage limits](#usage-limits)

To be able to upload files and add to a collections you must have credits in your account.

**Maximum file size**: 100MB  
**Maximum number of files**: 100,000 files uploaded globally.  
**Maximum total size**: 100GB

Please [contact us](https://x.ai/contact) to increase any of these limits.

* * *

## [Data Privacy](#data-privacy)

We do not use user data stored on Collections for model training purposes by default, unless the user has given consent.

* * *

## [Supported MIME Types](#supported-mime-types)

While we support any `UTF-8` encoded text file, we also have special file conversion and chunking techniques for certain MIME types.

The following would be a non-exhaustive list for the MIME types that we support:

*   application/csv
*   application/dart
*   application/ecmascript
*   application/epub
*   application/epub+zip
*   application/json
*   application/ms-java
*   application/msword
*   application/pdf
*   application/typescript
*   application/vnd.adobe.pdf
*   application/vnd.curl
*   application/vnd.dart
*   application/vnd.jupyter
*   application/vnd.ms-excel
*   application/vnd.ms-outlook
*   application/vnd.oasis.opendocument.text
*   application/vnd.openxmlformats-officedocument.presentationml.presentation
*   application/vnd.openxmlformats-officedocument.presentationml.slide
*   application/vnd.openxmlformats-officedocument.presentationml.slideshow
*   application/vnd.openxmlformats-officedocument.presentationml.template
*   application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
*   application/vnd.openxmlformats-officedocument.spreadsheetml.template
*   application/vnd.openxmlformats-officedocument.wordprocessingml.document
*   application/x-csh
*   application/x-epub+zip
*   application/x-hwp
*   application/x-hwp-v5
*   application/x-latex
*   application/x-pdf
*   application/x-php
*   application/x-powershell
*   application/x-sh
*   application/x-shellscript
*   application/x-tex
*   application/x-zsh
*   application/xhtml
*   application/xml
*   application/zip
*   text/cache-manifest
*   text/calendar
*   text/css
*   text/csv
*   text/html
*   text/javascript
*   text/jsx
*   text/markdown
*   text/n3
*   text/php
*   text/plain
*   text/rtf
*   text/tab-separated-values
*   text/troff
*   text/tsv
*   text/tsx
*   text/turtle
*   text/uri-list
*   text/vcard
*   text/vtt
*   text/x-asm
*   text/x-bibtex
*   text/x-c
*   text/x-c++hdr
*   text/x-c++src
*   text/x-chdr
*   text/x-coffeescript
*   text/x-csh
*   text/x-csharp
*   text/x-csrc
*   text/x-d
*   text/x-diff
*   text/x-emacs-lisp
*   text/x-erlang
*   text/x-go
*   text/x-haskell
*   text/x-java
*   text/x-java-properties
*   text/x-java-source
*   text/x-kotlin
*   text/x-lisp
*   text/x-lua
*   text/x-objcsrc
*   text/x-pascal
*   text/x-perl
*   text/x-perl-script
*   text/x-python
*   text/x-python-script
*   text/x-r-markdown
*   text/x-rst
*   text/x-ruby-script
*   text/x-rust
*   text/x-sass
*   text/x-scala
*   text/x-scheme
*   text/x-script.python
*   text/x-scss
*   text/x-sh
*   text/x-sql
*   text/x-swift
*   text/x-tcl
*   text/x-tex
*   text/x-vbasic
*   text/x-vcalendar
*   text/xml
*   text/xml-dtd
*   text/yaml

* * *

## [Guides](#guides)

*   [Using Collections →](/docs/guides/using-collections) - Get started with creating collections and uploading documents
*   [Collections API →](/docs/guides/using-collections/api) - Programmatically manage collections, upload files, and search documents
*   [Metadata Fields →](/docs/guides/using-collections/metadata) - Attach structured metadata to documents for filtered retrieval
*   [Console Guide →](/docs/guides/using-collections/console) - Create and manage collections through the xAI Console interface