---
name: seedance
description: How to generate videos using Seedance 2.0 and 2.0 fast models via the meme-master CLI. Use this skill whenever the user wants to generate a video, create a video from text or images, use meme-master, run video generation commands, edit videos, extend videos, or work with Seedance models through the CLI. Also trigger when the user mentions "meme-master", "generate video", "text to video", "image to video", "seedance", or wants to produce any kind of AI-generated video clip, even if they don't name the tool explicitly.
---

# Seedance Video Generation via meme-master CLI

The `meme-master` CLI generates videos using Seedance 2.0 models on the Volcengine Ark platform. It handles the entire async workflow — submitting the task, polling for completion, and downloading the result — in a single command.

## Prerequisites

> **Note:** This skill is built for `https://seedance.infinitext.cn/`. It also works with the official Volcengine Ark API, but compatibility is not guaranteed.

The user needs these environment variables set:

```bash
export ARK_API_KEY="their-key-here"
export ARK_BASE_URL="https://seedance.infinitext.cn/api/v3"
```

Never hardcode or generate credentials. If the command fails with "Missing ARK_API_KEY", tell the user to set both variables above.

## Models

| Model | Model ID | Default? |
|---|---|---|
| Seedance 2.0 fast | `doubao-seedance-2-0-fast-260128` | Yes (default for all modes) |
| Seedance 2.0 | `doubao-seedance-2-0-260128` | No (use `--model` to select) |

Both have identical capabilities. The fast variant is the default — it's cheaper and quicker. Use the standard variant with `--model doubao-seedance-2-0-260128` when maximum quality matters.

## Commands

### Generate a video

```bash
meme-master video generate --prompt "<text>" [options]
```

### List previous generations

```bash
meme-master video list
```

### Check task status or download

```bash
meme-master video get --task-id <id>
meme-master video get --task-id <id> --download
```

## Generation Modes

### Text-to-Video

The simplest mode — just provide a prompt:

```bash
meme-master video generate --prompt "A cat yawning at the camera, soft lighting, close-up"
```

### Image-to-Video (First Frame)

Provide a local image as the video's starting frame:

```bash
meme-master video generate \
  --prompt "The girl opens her eyes and looks at the camera gently" \
  --image ./first-frame.png
```

### Image-to-Video (First + Last Frame)

Control both the start and end frame:

```bash
meme-master video generate \
  --prompt "360 degree rotation around the object" \
  --image ./start.png \
  --last-frame-image ./end.png
```

### Multimodal Reference

Combine reference images (up to 9), videos (up to 3), and audio (up to 3). These flags are repeatable:

```bash
meme-master video generate \
  --prompt "Use video 1's camera movement. The character from image 1 walks through image 2's scene. Background music from audio 1." \
  --reference-image ./character.png \
  --reference-image ./scene.jpg \
  --reference-video ./camera-move.mp4 \
  --reference-audio ./bgm.mp3
```

Reference inputs accept both local file paths and URLs.

### Video Editing

Pass a reference video and describe the edit:

```bash
meme-master video generate \
  --prompt "Replace the red car in video 1 with a blue truck, keep the same camera movement" \
  --reference-video ./original.mp4
```

### Video Extension

Extend or stitch videos together:

```bash
meme-master video generate \
  --prompt "Extend video 1 forward, the car drives into a desert oasis" \
  --reference-video ./clip.mp4 \
  --duration 10
```

Stitch multiple clips:

```bash
meme-master video generate \
  --prompt "Video 1 transitions into video 2, then into video 3 seamlessly" \
  --reference-video ./clip1.mp4 \
  --reference-video ./clip2.mp4 \
  --reference-video ./clip3.mp4 \
  --duration 12
```

## Key Options

| Flag | Values | Default | Notes |
|---|---|---|---|
| `--prompt <text>` | any string | required | The video generation prompt |
| `--model <id>` | model ID | fast variant | Override the default model |
| `--image <path>` | local file path | - | First frame image (image-to-video mode) |
| `--last-frame-image <path>` | local file path | - | Last frame image (requires `--image`) |
| `--reference-image <path\|url>` | repeatable | - | Reference image for multimodal mode |
| `--reference-video <path\|url>` | repeatable | - | Reference video |
| `--reference-audio <path\|url>` | repeatable | - | Reference audio |
| `--aspect-ratio <w:h>` | `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9` | adaptive | Video aspect ratio |
| `--duration <seconds>` | 4-15 | 10 | Video length. Use `-1` for auto. |
| `--resolution <value>` | `480p`, `720p` | `720p` | Output resolution |
| `--generate-audio <on\|off>` | on/off | on | Synced audio generation |
| `--watermark <on\|off>` | on/off | off | Provider watermark |
| `--seed <number>` | integer | random | For reproducibility |
| `--return-last-frame <on\|off>` | on/off | off | Get last frame PNG (for chaining) |
| `--tool <type>` | `web_search` | - | Enable web search (text-only input) |
| `--draft <on\|off>` | on/off | off | Draft mode (Seedance 1.5 pro only) |
| `--out-dir <path>` | directory path | `~/.meme-master` | Output location |
| `--silent` | flag | - | Only print final result |
| `--nerd` | flag | - | Print extra operational detail |

## Validation Rules

The CLI enforces these constraints — be aware so you construct valid commands:

- `--last-frame-image` requires `--image` (can't have a last frame without a first frame)
- First-frame mode (`--image`) and reference mode (`--reference-image/video/audio`) are **mutually exclusive** — you can't mix them
- Reference audio requires at least one reference image or video alongside it
- Duration range for Seedance 2.0: 4-15 seconds
- Seedance 2.0 does not support 1080p — only 480p and 720p
- Seedance 2.0 does not support `--frames` — use `--duration` instead
- Seedance 2.0 does not support `--camera-fixed`

## Output

Generated files are saved to `~/.meme-master/` by default (or `--out-dir`), organized in timestamped run directories. The CLI prints the run directory path and saved file paths on completion.

The final output includes:
- The generated MP4 video file
- A metadata JSON file with settings, task ID, and provider info

## Chaining Videos

To create a multi-segment continuous video, use `--return-last-frame on` on each generation, then use the last frame URL as the next segment's first frame:

```bash
# Segment 1
meme-master video generate \
  --prompt "Scene 1: A woman walks through a garden" \
  --return-last-frame on \
  --duration 5

# Check the output for last_frame_url, then use it as reference for segment 2
meme-master video generate \
  --prompt "Scene 2: She sits on a bench and reads a book" \
  --image <last-frame-url-from-segment-1> \
  --duration 5
```

## Prompt Tips

Seedance 2.0 understands natural language deeply. Structure prompts with:

- **Subject + Action** (required): who is doing what
- **Environment + Aesthetics** (optional): background, lighting, visual style
- **Camera + Audio** (optional): camera movement, sound effects, music

When referencing multiple inputs, use ordinal references in the prompt: "image 1", "video 2", "audio 1" — numbered by order of same-type flags (1-indexed).

For better multi-image instruction following, use bracket notation in the prompt:
```
[image 1] the boy in blue and [image 2]'s corgi, sitting on [image 3]'s lawn
```

For speech, put dialogue in quotes: `The man says: "Hello, welcome!"`

## Input Constraints

**Images:** jpeg/png/webp/bmp/tiff/gif. Aspect ratio 0.4-2.5. Max 30MB. 300-6000px per side.

**Videos:** mp4/mov. Duration 2-15s per clip, total across all clips <= 15s. 480p-720p. Max 50MB. FPS 24-60.

**Audio:** wav/mp3. Duration 2-15s per clip, total <= 15s. Max 15MB.

**Text prompt:** Chinese < 500 chars, English < 1000 words recommended.

## Further Reference

This skill includes detailed reference files for deeper dives. Read them as needed:

- **`references/cli-reference.md`** — Complete flag and command reference with all options, toggle values, and environment variables
- **`references/input-specs.md`** — Exact constraints for images, videos, audio, text, output resolution tables, and validation rules
- **`references/examples.md`** — Ready-to-use CLI recipes for every generation mode (text-to-video, first frame, multimodal, editing, extension, chaining, web search, task management)
- **`references/prompt-tips.md`** — Advanced prompt techniques: text overlays, subtitles, speech bubbles, multi-angle character reference, camera/audio reference patterns

## Important Notes

- Generated video URLs from the Ark API expire after **24 hours** — download promptly (the CLI handles this for `video generate`, but for `video get --download` be timely)
- Seedance 2.0 does not support real human faces in uploaded reference images/videos. Use virtual avatars (asset IDs) or re-use model-generated videos for re-creation.
- Web search (`--tool web_search`) only works with text-only input (no images/videos/audio)
- The default duration is 10 seconds for Seedance models
