#### [Guides](https://docs.x.ai/docs/guides/using-collections/metadata#guides)

# Collection Metadata

Metadata fields allow you to attach structured attributes (e.g., `author`, `year`, `status`) to documents in a collection.

## [Key Benefits](https://docs.x.ai/docs/guides/using-collections/metadata#guides)

*   **Filtered retrieval**: Narrow search results based on specific criteria.
*   **Contextual embeddings**: Inject metadata into chunks to improve retrieval accuracy.
*   **Data integrity**: Enforce required fields or uniqueness.

* * *

## [Defining Metadata Fields](https://docs.x.ai/docs/guides/using-collections/metadata#field-definition-options)

Define fields using `field_definitions` when creating a collection:

```json
{
  "collection_name": "research_papers",
  "field_definitions": [
    { "key": "author", "required": true },
    { "key": "year", "required": true, "unique": false },
    { "key": "title", "inject_into_chunk": true }
  ]
}
```

### [Field Definition Options](https://docs.x.ai/docs/guides/using-collections/metadata#field-definition-options)

| Option | Description |
| --- | --- |
| `required` | Uploads must include this field. |
| `unique` | Field value must be unique across the collection. |
| `inject_into_chunk` | Prepends field value to every embedding chunk for better context. |

* * *

## [Filtering Documents in Search](https://docs.x.ai/docs/guides/using-collections/metadata#filtering-documents-in-search)

Use the `filter` parameter with **AIP-160** syntax to restrict search results:

```python
response = client.collections.search(
    query="revenue growth",
    collection_ids=["collection_id"],
    filter='author="Sandra Kim" AND year>=2020'
)
```

### [Supported Filter Operators](https://docs.x.ai/docs/guides/using-collections/metadata#supported-filter-operators)

| Operator | Example | Description |
| --- | --- | --- |
| `=` | `author="Jane"` | Equals |
| `!=` | `status!="draft"` | Not equals |
| `<`, `>`, `<=`, `>=` | `year>=2020` | Numeric comparison |
| `AND` | `author="Jane" AND year=2024` | Both must match |
| `OR` | `status="active" OR status="pending"` | Either must match |

### [AIP-160 Syntax Notes](https://docs.x.ai/docs/guides/using-collections/metadata#aip-160-filter-string-examples)
*   String values require double (`"`) or single (`'`) quotes.
*   Parentheses are supported for complex logic: `dept="HR" AND (status="active" OR status="new")`.
*   Precedence: `OR` has higher precedence than `AND`.
*   All string comparisons are exact matches (no wildcards).

* * *

## [Quick Reference Examples](https://docs.x.ai/docs/guides/using-collections/metadata#quick-reference)

| Use Case | Filter String |
| --- | --- |
| Exact match | `author="Sandra Kim"` |
| Numeric range | `year>=2020 AND year<=2024` |
| Multiple condition | `author="Jane" AND status!="draft"` |
| Grouped logic | `(status="active" OR status="pending") AND year>=2020` |
