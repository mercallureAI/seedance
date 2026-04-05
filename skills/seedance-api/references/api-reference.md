# Seedance 2.0 API Reference

Condensed endpoint and parameter reference for quick lookup.

## Base URL

```
https://seedance.infinitext.cn/api/v3
```

## Authentication

All requests require:
```
Authorization: Bearer $ARK_API_KEY
```

---

## POST /contents/generations/tasks

Create a video generation task.

### Request Body

```json
{
  "model": "doubao-seedance-2-0-260128",
  "content": [],
  "ratio": "adaptive",
  "duration": 5,
  "resolution": "720p",
  "generate_audio": true,
  "watermark": false,
  "seed": -1,
  "return_last_frame": false,
  "callback_url": null,
  "tools": [],
  "execution_expires_after": 172800,
  "safety_identifier": null,
  "service_tier": "default"
}
```

### Content Array Item Types

**Text:**
```json
{ "type": "text", "text": "prompt" }
```

**Image:**
```json
{
  "type": "image_url",
  "image_url": { "url": "<URL | base64 | asset://ID>" },
  "role": "first_frame | last_frame | reference_image"
}
```

**Video:**
```json
{
  "type": "video_url",
  "video_url": { "url": "<URL | asset://ID>" },
  "role": "reference_video"
}
```

**Audio:**
```json
{
  "type": "audio_url",
  "audio_url": { "url": "<URL | base64 | asset://ID>" },
  "role": "reference_audio"
}
```

### Response

```json
{
  "id": "cgt-20260403112501-xxxxx"
}
```

---

## GET /contents/generations/tasks/{id}

Query task status.

### Response (succeeded)

```json
{
  "id": "cgt-...",
  "model": "doubao-seedance-2-0-260128",
  "status": "succeeded",
  "created_at": 1712345678,
  "updated_at": 1712345700,
  "content": {
    "video_url": "https://...",
    "last_frame_url": "https://..."
  },
  "seed": 42,
  "resolution": "720p",
  "ratio": "16:9",
  "duration": 5,
  "generate_audio": true,
  "usage": {
    "completion_tokens": 1000,
    "total_tokens": 1000
  }
}
```

### Task Statuses

| Status | Meaning | Terminal? |
|---|---|---|
| `queued` | Waiting in queue | No |
| `running` | Generation in progress | No |
| `succeeded` | Done, video_url available | Yes |
| `failed` | Error occurred | Yes |
| `cancelled` | Cancelled while queued | Yes |
| `expired` | Exceeded timeout | Yes |

---

## GET /contents/generations/tasks

List tasks with optional filters.

### Query Parameters

| Param | Type | Description |
|---|---|---|
| `page_num` | int [1,500] | Page number |
| `page_size` | int [1,500] | Results per page |
| `filter.status` | string | Filter by status |
| `filter.task_ids` | string[] | Filter by task IDs (use `&` to join) |
| `filter.model` | string | Filter by endpoint ID |
| `filter.service_tier` | string | `default` or `flex` |

### Response

```json
{
  "items": [ /* task objects */ ],
  "total": 42
}
```

---

## DELETE /contents/generations/tasks/{id}

Cancel a queued task or delete a completed task record.

| Current Status | Supported? | Effect |
|---|---|---|
| queued | Yes | Cancels, status becomes `cancelled` |
| running | No | - |
| succeeded | Yes | Deletes record |
| failed | Yes | Deletes record |
| cancelled | No | - |
| expired | Yes | Deletes record |

### Response

No body. HTTP 200 on success.
