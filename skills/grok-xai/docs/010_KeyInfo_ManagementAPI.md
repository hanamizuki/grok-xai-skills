#### [Key Information](#key-information)

# [Using Management API](#using-management-api)

Some enterprise users may prefer to manage their account details programmatically rather than manually through the xAI Console. For this reason, we have developed a Management API to enable enterprise users to efficiently manage their team details.

You can read the endpoint specifications and descriptions at [Management API Reference](../management-api).

You need to get a management key, which is separate from your API key, to use the management API. The management key can be obtained at [xAI Console](https://console.x.ai) -> Settings -> Management Keys.

![Management API keys table in xAI Console settings showing available management keys](/_next/image?url=%2Fassets%2Fdocs%2Fkey-information%2Fusing-management-api%2Fmanagement-api-keys-table.png&w=3840&q=75)

The base URL is at `https://management-api.x.ai`, which is also different from the inference API.

* * *

## [Operations related to API Keys](#operations-related-to-api-keys)

You can create, list, update, and delete API keys via the management API.

You can also manage the access control lists (ACLs) associated with the API keys.

The available ACL types are:

*   `api-key:model`
*   `api-key:endpoint`

To enable all models and endpoints available to your team, use:

*   `api-key:model:*`
*   `api-key:endpoint:*`

Or if you need to specify the particular endpoint available to the API:

*   `api-key:endpoint:chat` for chat and vision models
*   `api-key:endpoint:image` for image generation models

And to specify models the API key has access to:

*   `api-key:model:<model name such as grok-4>`

* * *

### [Create an API key](#create-an-api-key)

An example to create an API key with all models and endpoints enabled, limiting requests to 5 queries per second and 100 queries per minute, without token number restrictions.

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/api-keys \
    -X POST \
    -H "Authorization: Bearer <Your Management API Key>" \
    -d '{
            "name": "My API key",
            "acls": ["api-key:model:*", "api-key:endpoint:*"],
            "qps": 5,
            "qpm": 100,
            "tpm": null
        }'
```

Specify `tpm` to any integer string to limit the number of tokens produced/consumed per minute. When the token rate limit is triggered, new requests will be rejected and in-flight requests will continue processing.

The newly-created API key will be returned in the `"apiKey"` field of the response object. The API Key ID is returned as `"apiKeyId"` in the response body as well, which is useful for updating and deleting operations.

* * *

### [List API keys](#list-api-keys)

To retrieve a list of API keys from a team, you can run the following:

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/api-keys?pageSize=10&paginationToken= \
    -H "Authorization: Bearer <Your Management API Key>"
```

You can customize the query parameters such as `pageSize` and `paginationToken`.

* * *

### [Update an API key](#update-an-api-key)

You can update an API key after it has been created. For example, to update the `qpm` of an API key:

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/api-keys \
    -X PUT \
    -d '{
            "apiKey": "<The apiKey Object with updated qpm>",
            "fieldMask": "qpm",
        }'
```

Or to update the `name` of an API key:

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/api-keys \
    -X PUT \
    -d '{
            "apiKey": "<The apiKey Object with updated name>",
            "fieldMask": "name",
        }'
```

* * *

### [Delete an API key](#delete-an-api-key)

You can also delete an API key with the following:

Bash

```
curl https://management-api.x.ai/auth/api-keys/{apiKeyId} \
    -X DELETE \
    -H "Authorization: Bearer <Your Management API Key>"
```

* * *

### [Check propagation status of API key across clusters](#check-propagation-status-of-api-key-across-clusters)

There could be a slight delay between creating an API key, and the API key being available for use across all clusters.

You can check the propagation status of the API key via API.

Bash

```
curl https://management-api.x.ai/auth/api-keys/{apiKeyId}/propagation \
    -H "Authorization: Bearer <Your Management API Key>"
```

* * *

### [List all models available for the team](#list-all-models-available-for-the-team)

You can list all the available models for a team with our management API as well.

The model names in the output can be used with setting ACL string on an API key as `api-key:model:<model-name>`

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/models \
    -H "Authorization: Bearer <Your Management API Key>"
```

* * *

## [Access Control List (ACL) management](#access-control-list-acl-management)

We also offer endpoint to list possible ACLs for a team. You can then apply the endpoint ACL strings to your API keys.

To view possible endpoint ACLs for a team's API keys:

Bash

```
curl https://management-api.x.ai/auth/teams/{teamId}/endpoints \
    -H "Authorization: Bearer <Your Management API Key>"
```

* * *

## [Validate a management key](#validate-a-management-key)

You can check if your key is a valid management key. If validation succeeds, the endpoint returns meta information about the management key. This endpoint does not require any Access Control List (ACL) permissions.

Bash

```
curl https://management-api.x.ai/auth/management-keys/validation \
    -H "Authorization: Bearer <Your Management API Key>"
```

* * *

## [Audit Logs](#audit-logs)

You can retrieve audit logs for your team. Audit events track changes to team settings, API keys, team membership, and other administrative actions.

### [List audit events](#list-audit-events)

To retrieve audit events for a team:

Bash

```
curl "https://management-api.x.ai/audit/teams/{teamId}/events?pageSize=10" \
    -H "Authorization: Bearer <Your Management API Key>"
```

You can customize the query parameters:

*   `pageSize` - Number of events per page
*   `pageToken` - Token for fetching the next page of results
*   `eventFilter.userId` - Filter events to a specific user
*   `eventFilter.query` - Full-text search in event descriptions
*   `eventTimeFrom` - Filter events from a specific time (ISO 8601 format)
*   `eventTimeTo` - Filter events up to a specific time (ISO 8601 format)

To fetch the next page of results, use the `nextPageToken` from the response:

Bash

```
curl "https://management-api.x.ai/audit/teams/{teamId}/events?pageSize=10&pageToken={nextPageToken}" \
    -H "Authorization: Bearer <Your Management API Key>"
```

Example with time filter:

Bash

```
curl "https://management-api.x.ai/audit/teams/{teamId}/events?pageSize=50&eventTimeFrom=2025-01-01T00:00:00Z" \
    -H "Authorization: Bearer <Your Management API Key>"
```