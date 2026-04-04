# meme-master CLI Reference

Complete flag and command reference for quick lookup.

## Commands

```
meme-master video generate --prompt <text> [options]
meme-master video list
meme-master video get --task-id <id> [--download]
```

---

## video generate

Generate a video from text, images, video, and/or audio inputs.

### Required

| Flag | Description |
|---|---|
| `--prompt <text>` | Text prompt describing the video to generate |

### Model Selection

| Flag | Description | Default |
|---|---|---|
| `--model <id>` | Model ID to use | `doubao-seedance-2-0-fast-260128` |

Available models:
- `doubao-seedance-2-0-fast-260128` (default, faster/cheaper)
- `doubao-seedance-2-0-260128` (highest quality)

### Input Modes

| Flag | Description | Repeatable? |
|---|---|---|
| `--image <path>` | First-frame image (local path) | No |
| `--last-frame-image <path>` | Last-frame image (requires `--image`) | No |
| `--reference-image <path\|url>` | Reference image for multimodal mode | Yes |
| `--reference-video <path\|url>` | Reference video | Yes |
| `--reference-audio <path\|url>` | Reference audio | Yes |

### Video Settings

| Flag | Values | Default |
|---|---|---|
| `--aspect-ratio <w:h>` | `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9` | adaptive |
| `--duration <seconds>` | 4-15, or -1 for auto | 10 |
| `--resolution <value>` | `480p`, `720p` | `720p` |
| `--seed <number>` | integer, -1 for random | random |
| `--frames <count>` | integer (not supported for 2.0) | - |

### Audio & Watermark

| Flag | Values | Default |
|---|---|---|
| `--generate-audio <on\|off>` | on/off | on |
| `--watermark <on\|off>` | on/off | off |

### Output Control

| Flag | Values | Default |
|---|---|---|
| `--return-last-frame <on\|off>` | on/off | off |
| `--out-dir <path>` | directory path | `~/.meme-master` |

### Advanced / Seedance-Specific

| Flag | Values | Default |
|---|---|---|
| `--tool <type>` | `web_search` (repeatable) | - |
| `--service-tier <value>` | `default`, `flex` | `default` |
| `--camera-fixed <on\|off>` | on/off (not supported for 2.0) | off |
| `--execution-expires-after <sec>` | seconds | 172800 |
| `--callback-url <url>` | webhook URL | - |
| `--safety-identifier <value>` | string | - |
| `--draft <on\|off>` | on/off (1.5 pro only) | off |
| `--poll-timeout-ms <ms>` | milliseconds | - |

### Display Modes

| Flag | Description |
|---|---|
| `--silent` | Only print the final result |
| `--nerd` | Print extra operational detail |

---

## video list

List all previously generated videos stored locally.

```bash
meme-master video list
```

Output format: `runId | model | provider | status | taskId | firstFilePath`

No flags required.

---

## video get

Retrieve task status or download a completed video.

| Flag | Description | Required? |
|---|---|---|
| `--task-id <id>` | Seedance task ID | Yes |
| `--download` | Download the video if succeeded | No |

```bash
# Check status
meme-master video get --task-id cgt-20260403112501-xxxxx

# Download
meme-master video get --task-id cgt-20260403112501-xxxxx --download
```

---

## Environment Variables

| Variable | Required For | Description |
|---|---|---|
| `ARK_API_KEY` | Seedance models | Volcengine Ark API key |
| `AI_GATEWAY_API_KEY` | Non-Seedance models | Gateway key for Kling, Grok |

---

## Toggle Values

All toggle flags accept these values:

| Truthy | Falsy |
|---|---|
| `on`, `true`, `yes`, `1` | `off`, `false`, `no`, `0` |
