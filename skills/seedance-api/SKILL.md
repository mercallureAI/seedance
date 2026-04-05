---
name: seedance-api
description: How to use the Seedance 2.0 and Seedance 2.0 fast video generation API (Volcengine Ark platform). Use this skill whenever the user wants to generate videos with Seedance, call the Seedance API, create video generation tasks, poll for video results, write code that uses Seedance/doubao-seedance models, or build anything involving AI video generation with the Ark API. Also trigger when the user mentions "seedance", "video generation API", "doubao-seedance", "ark video", "text to video API", or "image to video API".
---

# Seedance 2.0 API

Seedance 2.0 is an AI video generation model on the Volcengine Ark platform. It supports text-to-video, image-to-video, multimodal reference, video editing, video extension, and audio-synced video generation. This skill covers direct API usage (curl, Python SDK, etc.) without any custom CLI wrapper.

## Models

| Model | Model ID | Best for |
|---|---|---|
| Seedance 2.0 | `doubao-seedance-2-0-260128` | Highest quality |
| Seedance 2.0 fast | `doubao-seedance-2-0-fast-260128` | Faster/cheaper, same capabilities |

Both models share the same capabilities. Use 2.0 for maximum quality, 2.0 fast when speed and cost matter more.

## Authentication

The API uses an API Key via the `ARK_API_KEY` environment variable. Pass it as a Bearer token:

```
Authorization: Bearer $ARK_API_KEY
```

The user is responsible for obtaining and configuring their own API key from the Volcengine Ark console. Never hardcode or generate credentials.

## Core Workflow

Video generation is **asynchronous** — a two-step process:

1. **Create a task** via `POST /contents/generations/tasks` — returns a task ID
2. **Poll the task** via `GET /contents/generations/tasks/{id}` — until `status` is `succeeded` or `failed`
3. When succeeded, the response contains `content.video_url` with the MP4 download link (valid for 24h)

## API Base URL

```
https://seedance.infinitext.cn/api/v3
```

> **Note:** These skills are built for `https://seedance.infinitext.cn/`. They also work with the official Volcengine Ark API (`https://ark.cn-beijing.volces.com/api/v3`), but compatibility is not guaranteed.

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| POST | `/contents/generations/tasks` | Create a video generation task |
| GET | `/contents/generations/tasks/{id}` | Get task status and result |
| GET | `/contents/generations/tasks` | List tasks with filters |
| DELETE | `/contents/generations/tasks/{id}` | Cancel queued / delete completed task |

## Generation Modes

Seedance 2.0 supports these input combinations (all via the `content` array):

### Text-to-Video
Just a text prompt — the simplest mode.

### Image-to-Video (First Frame)
One image + optional text. The image becomes the video's first frame.
- Set image `role` to `first_frame` (or omit — it defaults to first frame with a single image)

### Image-to-Video (First + Last Frame)
Two images + optional text. Control both start and end frames.
- First image: `role: "first_frame"`
- Second image: `role: "last_frame"`

### Multimodal Reference
The most powerful mode — combine images (0-9), videos (0-3), audio (0-3), and text.
- Images use `role: "reference_image"`
- Videos use `role: "reference_video"`
- Audio uses `role: "reference_audio"`
- Audio cannot be the sole input; at least one image or video is required alongside it

### Video Editing
Provide a reference video + text describing the edits (e.g., "replace the perfume with a cream jar").

### Video Extension
Provide 1-3 reference videos + text describing how to extend or stitch them.

## Content Array Types

Each item in the `content` array has a `type` and corresponding payload:

**Text:**
```json
{ "type": "text", "text": "your prompt here" }
```

**Image:**
```json
{
  "type": "image_url",
  "image_url": { "url": "<URL, base64, or asset://ID>" },
  "role": "first_frame | last_frame | reference_image"
}
```

**Video:**
```json
{
  "type": "video_url",
  "video_url": { "url": "<URL or asset://ID>" },
  "role": "reference_video"
}
```

**Audio:**
```json
{
  "type": "audio_url",
  "audio_url": { "url": "<URL, base64, or asset://ID>" },
  "role": "reference_audio"
}
```

## Request Parameters

| Parameter | Type | Default | Notes |
|---|---|---|---|
| `model` | string | required | Model ID (see table above) |
| `content` | array | required | Array of text/image/video/audio objects |
| `ratio` | string | `"adaptive"` | `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9`, `adaptive` |
| `duration` | integer | `5` | Video length in seconds. Range: [4, 15]. Use `-1` for auto. |
| `resolution` | string | `"720p"` | `480p` or `720p` (1080p not supported for 2.0) |
| `generate_audio` | boolean | `true` | Whether to generate synced audio |
| `watermark` | boolean | `false` | Include watermark |
| `seed` | integer | `-1` | Reproducibility seed. [-1, 2^32-1] |
| `return_last_frame` | boolean | `false` | Return the last frame as PNG (useful for chaining) |
| `callback_url` | string | - | Webhook URL for status notifications |
| `tools` | array | - | `[{"type": "web_search"}]` to enable web search (text-only) |
| `execution_expires_after` | integer | `172800` | Task timeout in seconds [3600, 259200] |
| `safety_identifier` | string | - | End-user identifier for abuse monitoring |

## Task Statuses

| Status | Meaning |
|---|---|
| `queued` | Waiting in queue |
| `running` | Generation in progress |
| `succeeded` | Done — `content.video_url` available |
| `failed` | Error — check `error.code` and `error.message` |
| `cancelled` | Task was cancelled while queued |
| `expired` | Task exceeded timeout |

## Examples

### curl — Text-to-Video

```bash
# Step 1: Create task
curl -X POST https://seedance.infinitext.cn/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "doubao-seedance-2-0-260128",
    "content": [
        {
            "type": "text",
            "text": "A cat yawning at the camera, soft lighting, close-up shot"
        }
    ],
    "generate_audio": true,
    "ratio": "16:9",
    "duration": 5,
    "watermark": false
}'

# Step 2: Poll for result (replace TASK_ID)
curl -X GET https://seedance.infinitext.cn/api/v3/contents/generations/tasks/TASK_ID \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

### curl — Image-to-Video (First Frame)

```bash
curl -X POST https://seedance.infinitext.cn/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "doubao-seedance-2-0-260128",
    "content": [
        {
            "type": "text",
            "text": "The girl opens her eyes and looks at the camera gently"
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://example.com/first-frame.png"
            }
        }
    ],
    "generate_audio": true,
    "ratio": "adaptive",
    "duration": 5,
    "watermark": false
}'
```

### Python SDK — Full Workflow with Polling

```python
import os
import time
from volcenginesdkarkruntime import Ark

# pip install 'volcengine-python-sdk[ark]'
client = Ark(
    base_url="https://seedance.infinitext.cn/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)

# Create task
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "A cat yawning at the camera"},
        {
            "type": "image_url",
            "image_url": {"url": "https://example.com/cat.png"},
        },
    ],
    generate_audio=True,
    ratio="adaptive",
    duration=5,
    watermark=False,
)
task_id = result.id
print(f"Task created: {task_id}")

# Poll until done
while True:
    task = client.content_generation.tasks.get(task_id=task_id)
    if task.status == "succeeded":
        print(f"Video URL: {task.content.video_url}")
        break
    elif task.status == "failed":
        print(f"Failed: {task.error}")
        break
    else:
        print(f"Status: {task.status}, waiting...")
        time.sleep(30)
```

### Python SDK — Multimodal Reference (images + video + audio)

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {
            "type": "text",
            "text": "Use video 1's first-person perspective, use audio 1 as background music. A fruit tea commercial...",
        },
        {
            "type": "image_url",
            "image_url": {"url": "https://example.com/tea1.jpg"},
            "role": "reference_image",
        },
        {
            "type": "image_url",
            "image_url": {"url": "https://example.com/tea2.jpg"},
            "role": "reference_image",
        },
        {
            "type": "video_url",
            "video_url": {"url": "https://example.com/reference.mp4"},
            "role": "reference_video",
        },
        {
            "type": "audio_url",
            "audio_url": {"url": "https://example.com/bgm.mp3"},
            "role": "reference_audio",
        },
    ],
    generate_audio=True,
    ratio="16:9",
    duration=11,
    watermark=False,
)
```

### Python SDK — Video Extension

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Extend video 1 forward, the car drives into a desert oasis"},
        {
            "type": "video_url",
            "video_url": {"url": "https://example.com/clip.mp4"},
            "role": "reference_video",
        },
    ],
    generate_audio=True,
    ratio="16:9",
    duration=8,
    watermark=False,
)
```

### Chaining Videos with Last Frame

To create continuous multi-segment videos, use `return_last_frame=True` on each task, then feed the last frame as the next task's first frame:

```python
# Task 1: generate first segment
result1 = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[{"type": "text", "text": "Scene 1 description..."}],
    return_last_frame=True,
    duration=5,
)

# ... poll until succeeded ...
task1 = client.content_generation.tasks.get(task_id=result1.id)
last_frame_url = task1.content.last_frame_url

# Task 2: use last frame as first frame
result2 = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Scene 2 continues from where scene 1 left off..."},
        {
            "type": "image_url",
            "image_url": {"url": last_frame_url},
            "role": "first_frame",
        },
    ],
    return_last_frame=True,
    duration=5,
)
```

## Prompt Tips

Seedance 2.0 understands natural language deeply. Structure prompts with:

- **Subject + Action** (required): who is doing what
- **Environment + Aesthetics** (optional): background, lighting, style
- **Camera + Audio** (optional): camera movement, sound effects

When referencing multiple inputs, use ordinal references: "image 1", "video 2", "audio 1" — these map to the order of same-type items in the `content` array (1-indexed).

For better instruction following with multiple reference images, use the bracket format: `[image 1] description of first image's role, [image 2] description of second image's role`.

To generate speech, put dialogue in quotes: `The man says: "Hello, welcome!"`

## Input Constraints

**Images:** jpeg/png/webp/bmp/tiff/gif. Aspect ratio 0.4-2.5. Size < 30MB. Dimensions 300-6000px per side.

**Videos:** mp4/mov. Duration 2-15s per clip, total across all clips <= 15s. Resolution 480p-720p. Size < 50MB. FPS 24-60.

**Audio:** wav/mp3. Duration 2-15s per clip, total <= 15s. Size < 15MB.

**Text prompt:** Chinese < 500 chars, English < 1000 words recommended.

## Important Notes

- Generated video URLs expire after **24 hours** — download or transfer promptly
- Task records are kept for **7 days** only
- Seedance 2.0 does not support real human faces in uploaded reference images/videos (use virtual avatars via `asset://` IDs or re-use model-generated videos for re-creation)
- Offline inference (`service_tier: "flex"`) is not supported for 2.0 models
- The `frames` parameter is not supported for 2.0 models — use `duration` instead
- `camera_fixed` is not supported for 2.0 models
- Web search (`tools: [{"type": "web_search"}]`) only works with text-only input

## Listing and Managing Tasks

```bash
# List recent tasks
curl -X GET "https://seedance.infinitext.cn/api/v3/contents/generations/tasks?page_num=1&page_size=10" \
  -H "Authorization: Bearer $ARK_API_KEY"

# Filter by status
curl -X GET "https://seedance.infinitext.cn/api/v3/contents/generations/tasks?filter.status=succeeded" \
  -H "Authorization: Bearer $ARK_API_KEY"

# Delete a task
curl -X DELETE "https://seedance.infinitext.cn/api/v3/contents/generations/tasks/TASK_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

## Further Reference

This skill includes detailed reference files for deeper dives. Read them as needed:

- **`references/api-reference.md`** — Condensed endpoint specs, request/response schemas, and status codes
- **`references/input-specs.md`** — Exact constraints for images, videos, audio, text, and output resolution tables
- **`references/examples.md`** — Ready-to-use code recipes for every generation mode (text-to-video, first frame, multimodal, editing, extension, chaining, virtual avatars, polling)
- **`references/prompt-tips.md`** — Advanced prompt techniques: text overlays, subtitles, speech bubbles, multi-angle character reference, camera/audio reference patterns
