# Seedance 2.0 API Examples & Recipes

Ready-to-use patterns for common video generation tasks.

## Table of Contents
1. [Text-to-Video](#text-to-video)
2. [First Frame Image-to-Video](#first-frame)
3. [First + Last Frame](#first-last-frame)
4. [Multimodal Reference](#multimodal-reference)
5. [Video Editing](#video-editing)
6. [Video Extension](#video-extension)
7. [Web Search Enhanced](#web-search)
8. [Video Chaining](#video-chaining)
9. [Virtual Avatar](#virtual-avatar)
10. [Polling Pattern](#polling-pattern)

---

## Text-to-Video

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "A cat yawning at the camera, soft lighting, close-up shot"}
    ],
    generate_audio=True,
    ratio="16:9",
    duration=5,
)
```

```bash
curl -X POST https://seedance.infinitext.cn/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{"model":"doubao-seedance-2-0-260128","content":[{"type":"text","text":"A cat yawning at the camera"}],"generate_audio":true,"ratio":"16:9","duration":5}'
```

---

## First Frame

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "The girl opens her eyes and looks at the camera gently"},
        {"type": "image_url", "image_url": {"url": "https://example.com/girl.png"}},
    ],
    generate_audio=True,
    ratio="adaptive",
    duration=5,
)
```

---

## First + Last Frame

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "360 degree rotation"},
        {"type": "image_url", "image_url": {"url": "https://example.com/start.png"}, "role": "first_frame"},
        {"type": "image_url", "image_url": {"url": "https://example.com/end.png"}, "role": "last_frame"},
    ],
    ratio="adaptive",
    duration=5,
)
```

---

## Multimodal Reference

Combine images, videos, and audio as references:

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Use video 1's camera movement. Character from image 1 walks through image 2's scene. Background music from audio 1."},
        {"type": "image_url", "image_url": {"url": "https://example.com/character.png"}, "role": "reference_image"},
        {"type": "image_url", "image_url": {"url": "https://example.com/scene.jpg"}, "role": "reference_image"},
        {"type": "video_url", "video_url": {"url": "https://example.com/camera.mp4"}, "role": "reference_video"},
        {"type": "audio_url", "audio_url": {"url": "https://example.com/bgm.mp3"}, "role": "reference_audio"},
    ],
    generate_audio=True,
    ratio="16:9",
    duration=10,
)
```

---

## Video Editing

Replace or modify elements in an existing video:

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Replace the red car in video 1 with image 1's blue truck, keep same camera movement"},
        {"type": "image_url", "image_url": {"url": "https://example.com/truck.jpg"}, "role": "reference_image"},
        {"type": "video_url", "video_url": {"url": "https://example.com/original.mp4"}, "role": "reference_video"},
    ],
    generate_audio=True,
    ratio="16:9",
    duration=5,
)
```

---

## Video Extension

Extend a single clip or stitch multiple clips:

```python
# Extend one clip
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Extend video 1 forward, the car drives into a desert oasis"},
        {"type": "video_url", "video_url": {"url": "https://example.com/clip.mp4"}, "role": "reference_video"},
    ],
    generate_audio=True,
    duration=10,
)

# Stitch three clips
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Video 1 transitions to video 2, then into video 3"},
        {"type": "video_url", "video_url": {"url": "https://example.com/a.mp4"}, "role": "reference_video"},
        {"type": "video_url", "video_url": {"url": "https://example.com/b.mp4"}, "role": "reference_video"},
        {"type": "video_url", "video_url": {"url": "https://example.com/c.mp4"}, "role": "reference_video"},
    ],
    generate_audio=True,
    duration=12,
)
```

---

## Web Search

Enable web search for up-to-date visual content (text-only input):

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "A macro shot of a glass frog on a leaf, showing its transparent belly with a beating red heart"},
    ],
    tools=[{"type": "web_search"}],
    generate_audio=True,
    ratio="16:9",
    duration=10,
)
```

---

## Video Chaining

Create continuous multi-segment videos by passing the last frame forward:

```python
# Segment 1
r1 = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[{"type": "text", "text": "Scene 1: A woman walks through a garden"}],
    return_last_frame=True,
    duration=5,
)
# ... poll until succeeded ...
t1 = client.content_generation.tasks.get(task_id=r1.id)

# Segment 2 — use last frame as first frame
r2 = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Scene 2: She sits on a bench and reads"},
        {"type": "image_url", "image_url": {"url": t1.content.last_frame_url}, "role": "first_frame"},
    ],
    return_last_frame=True,
    duration=5,
)
```

---

## Virtual Avatar

Use platform-provided virtual avatars (asset IDs) for human characters:

```python
result = client.content_generation.tasks.create(
    model="doubao-seedance-2-0-260128",
    content=[
        {"type": "text", "text": "Image 1's blogger introduces image 2's cream, smiling at the camera"},
        {"type": "image_url", "image_url": {"url": "asset://asset-20260224200602-qn7wr"}, "role": "reference_image"},
        {"type": "image_url", "image_url": {"url": "https://example.com/cream.jpg"}, "role": "reference_image"},
    ],
    generate_audio=True,
    ratio="adaptive",
    duration=11,
)
```

Asset IDs are for passing the resource only. In the prompt, reference them as "image 1", "image 2", etc.

---

## Polling Pattern

Standard polling loop for any task:

```python
import time

task_id = result.id
while True:
    task = client.content_generation.tasks.get(task_id=task_id)
    if task.status == "succeeded":
        print(f"Video: {task.content.video_url}")
        if hasattr(task.content, 'last_frame_url') and task.content.last_frame_url:
            print(f"Last frame: {task.content.last_frame_url}")
        break
    elif task.status in ("failed", "cancelled", "expired"):
        print(f"Terminal status: {task.status}")
        if task.error:
            print(f"Error: {task.error.message}")
        break
    else:
        print(f"Status: {task.status}...")
        time.sleep(30)
```

```bash
# Bash polling
while true; do
  STATUS=$(curl -s https://seedance.infinitext.cn/api/v3/contents/generations/tasks/$TASK_ID \
    -H "Authorization: Bearer $ARK_API_KEY" | jq -r '.status')
  echo "Status: $STATUS"
  [ "$STATUS" = "succeeded" ] || [ "$STATUS" = "failed" ] && break
  sleep 30
done
```
