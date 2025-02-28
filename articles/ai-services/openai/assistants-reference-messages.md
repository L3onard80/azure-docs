---
title: Azure OpenAI Service Assistants Python & REST API messages reference 
titleSuffix: Azure OpenAI
description: Learn how to use Azure OpenAI's Python & REST API messages with Assistants.
manager: nitinme
ms.service: azure-ai-openai
ms.topic: conceptual
ms.date: 02/01/2024
author: mrbullwinkle
ms.author: mbullwin
recommendations: false
ms.custom:
---

# Assistants API (Preview) messages reference

This article provides reference documentation for Python and REST for the new Assistants API (Preview). More in-depth step-by-step guidance is provided in the [getting started guide](./how-to/assistant.md).

## Create message

```http
POST https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages?api-version=2024-02-15-preview
```

Create a message.

**Path parameter**

|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread to create a message for. |

**Request body**

|Name | Type | Required | Description |
|---  |---   |---       |---          |
| `role` | string | Required | The role of the entity that is creating the message. Currently only user is supported.|
| `content` | string | Required | The content of the message. |
| `file_ids` | array | Optional | A list of File IDs that the message should use. There can be a maximum of 10 files attached to a message. Useful for tools like retrieval and code_interpreter that can access and use files. |
| `metadata` | map | Optional | Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format. Keys can be a maximum of 64 characters long and values can be a maximum of 512 characters long. |

### Returns

A [message](#message-object) object.

### Example create message request

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

thread_message = client.beta.threads.messages.create(
  "thread_abc123",
  role="user",
  content="How does AI work? Explain it in simple terms.",
)
print(thread_message)
```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages?api-version=2024-02-15-preview \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
      "role": "user",
      "content": "How does AI work? Explain it in simple terms."
    }' 
```

---

## List messages

```http
GET https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages?api-version=2024-02-15-preview
```

Returns a list of messages for a given thread.

**Path Parameters**


|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread that messages belong to. |

**Query Parameters**

|Name | Type | Required | Description |
|---  |---   |---       |--- |
| `limit` | integer | Optional - Defaults to 20 |A limit on the number of objects to be returned. Limit can range between 1 and 100, and the default is 20.|
| `order` | string | Optional - Defaults to desc |Sort order by the created_at timestamp of the objects. asc for ascending order and desc for descending order.|
| `after` | string | Optional | A cursor for use in pagination. after is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with obj_foo, your subsequent call can include after=obj_foo in order to fetch the next page of the list.|
| `before` | string | Optional | A cursor for use in pagination. before is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with obj_foo, your subsequent call can include before=obj_foo in order to fetch the previous page of the list.|

### Returns

A list of [message](#message-object) objects.

### Example list messages request

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

thread_messages = client.beta.threads.messages.list("thread_abc123")
print(thread_messages.data)

```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages?api-version=2024-02-15-preview \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' 
```

---

## List message files

```http
GET https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}/files?api-version=2024-02-15-preview
```

Returns a list of message files.

|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread that the message and files belong to. |
|`message_id`| string | Required | The ID of the message that the files belongs to. |

**Query Parameters**

|Name | Type | Required | Description |
|---  |---   |---       |--- |
| `limit` | integer | Optional - Defaults to 20 |A limit on the number of objects to be returned. Limit can range between 1 and 100, and the default is 20.|
| `order` | string | Optional - Defaults to desc |Sort order by the created_at timestamp of the objects. asc for ascending order and desc for descending order.|
| `after` | string | Optional | A cursor for use in pagination. after is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with obj_foo, your subsequent call can include after=obj_foo in order to fetch the next page of the list.|
| `before` | string | Optional | A cursor for use in pagination. before is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with obj_foo, your subsequent call can include before=obj_foo in order to fetch the previous page of the list.|

### Returns

A list of [message file](#message-file-object) objects

### Example list message files request

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

message_files = client.beta.threads.messages.files.list(
  thread_id="thread_abc123",
  message_id="msg_abc123"
)
print(message_files)

```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/files?api-version=2024-02-15-preview \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' 
```

---

## Retrieve message

```http
GET https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}?api-version=2024-02-15-preview
```

Retrieves a message file.

**Path parameters**

|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread that the message belongs to. |
|`message_id`| string | Required | The ID of the message to retrieve. |


### Returns

The [message](#message-object) object matching the specified ID.

### Example retrieve message request

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

message = client.beta.threads.messages.retrieve(
  message_id="msg_abc123",
  thread_id="thread_abc123",
)
print(message)

```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}?api-version=2024-02-15-preview \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' 
```

---

## Retrieve message file

```http
GET https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}/files/{file_id}?api-version=2024-02-15-preview
```

Retrieves a message file.

**Path parameters**

|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread, which the message and file belongs to. |
|`message_id`| string | Required | The ID of the message that the file belongs to. |
|`file_id` | string | Required | The ID of the file being retrieved. |

**Returns**

The [message file](#message-file-object) object.

### Example retrieve message file request

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

message_files = client.beta.threads.messages.files.retrieve(
    thread_id="thread_abc123",
    message_id="msg_abc123",
    file_id="assistant-abc123"
)
print(message_files)
```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}/files/{file_id}?api-version=2024-02-15-preview
``` \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' 
```

---

## Modify message

```http
POST https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}?api-version=2024-02-15-preview
```

Modifies a message.

**Path parameters**

|Parameter| Type | Required | Description |
|---|---|---|---|
|`thread_id` | string | Required | The ID of the thread to which the message belongs. |
|`message_id`| string | Required | The ID of the message to modify. |

**Request body**

|Parameter| Type | Required | Description |
|---|---|---|---|
| metadata | map| Optional | Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format. Keys can be a maximum of 64 characters long and values can be a maximum of 512 characters long.|

### Returns

The modified [message](#message-object) object.

# [Python 1.x](#tab/python)

```python
from openai import AzureOpenAI
    
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-02-15-preview",
    azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
    )

message = client.beta.threads.messages.update(
  message_id="msg_abc12",
  thread_id="thread_abc123",
  metadata={
    "modified": "true",
    "user": "abc123",
  },
)
print(message)
```

# [REST](#tab/rest)

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/threads/{thread_id}/messages/{message_id}?api-version=2024-02-15-preview
``` \
  -H "api-key: $AZURE_OPENAI_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
      "metadata": {
        "modified": "true",
        "user": "abc123"
      }
    }'  
   
```

---

## Message object

Represents a message within a thread.

|Name | Type | Description |
|---  |---   |---         |
| `id` | string  |The identifier, which can be referenced in API endpoints.|
| `object` | string  |The object type, which is always thread.message.|
| `created_at` | integer  |The Unix timestamp (in seconds) for when the message was created.|
| `thread_id` | string  |The thread ID that this message belongs to.|
| `role` | string  |The entity that produced the message. One of user or assistant.|
| `content` | array  |The content of the message in array of text and/or images.|
| `assistant_id` | string or null  |If applicable, the ID of the assistant that authored this message.|
| `run_id` | string or null  |If applicable, the ID of the run associated with the authoring of this message.|
| `file_ids` | array  |A list of file IDs that the assistant should use. Useful for tools like retrieval and code_interpreter that can access files. A maximum of 10 files can be attached to a message.|
| `metadata` | map  |Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format. Keys can be a maximum of 64 characters long and values can be a maximum of 512 characters long.|

## Message file object

A list of files attached to a message.

|Name | Type | Description |
|---  |---   |---         |
| `id`| string | The identifier, which can be referenced in API endpoints.|
|`object`|string| The object type, which is always `thread.message.file`.|
|`created_at`|integer | The Unix timestamp (in seconds) for when the message file was created.|
|`message_id`| string | The ID of the message that the File is attached to.|