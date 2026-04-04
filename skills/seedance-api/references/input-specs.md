# Input Specifications & Constraints

Detailed constraints for all input types accepted by Seedance 2.0 and 2.0 fast.

## Models

| Model | Model ID |
|---|---|
| Seedance 2.0 | `doubao-seedance-2-0-260128` |
| Seedance 2.0 fast | `doubao-seedance-2-0-fast-260128` |

## Image Inputs

| Property | Constraint |
|---|---|
| Formats | jpeg, png, webp, bmp, tiff, gif |
| Aspect ratio (w/h) | (0.4, 2.5) |
| Dimensions per side | 300 - 6000 px |
| Max file size | 30 MB per image |
| Max request body | 64 MB total |
| Max count (first frame) | 1 |
| Max count (first+last frame) | 2 |
| Max count (multimodal ref) | 9 |

### Image URL Formats

- **Public URL:** `https://example.com/image.png`
- **Base64:** `data:image/png;base64,{base64_data}` (format must be lowercase)
- **Asset ID:** `asset://<ASSET_ID>` (from virtual avatar library)

Large files should not use Base64 encoding.

## Video Inputs

| Property | Constraint |
|---|---|
| Formats | mp4, mov |
| Duration per clip | 2 - 15 seconds |
| Total duration (all clips) | <= 15 seconds |
| Max clip count | 3 |
| Resolution | 480p, 720p |
| Aspect ratio (w/h) | [0.4, 2.5] |
| Dimensions per side | 300 - 6000 px |
| Total pixels | [409600, 927408] (w * h) |
| Max file size | 50 MB per video |
| Frame rate | 24 - 60 FPS |

### Video URL Formats

- **Public URL:** `https://example.com/video.mp4`
- **Asset ID:** `asset://<ASSET_ID>`

## Audio Inputs

| Property | Constraint |
|---|---|
| Formats | wav, mp3 |
| Duration per clip | 2 - 15 seconds |
| Total duration (all clips) | <= 15 seconds |
| Max clip count | 3 |
| Max file size | 15 MB per audio |
| Max request body | 64 MB total |

### Audio URL Formats

- **Public URL:** `https://example.com/audio.mp3`
- **Base64:** `data:audio/wav;base64,{base64_data}` (format must be lowercase)
- **Asset ID:** `asset://<ASSET_ID>`

Audio cannot be the sole input — at least one image or video must accompany it.

## Text Prompts

| Property | Constraint |
|---|---|
| Languages | Chinese, English |
| Recommended length (Chinese) | < 500 characters |
| Recommended length (English) | < 1000 words |

Exceeding recommended length may cause the model to ignore details and focus only on key points.

## Output Video Specs

### Resolution Table (Seedance 2.0 / 2.0 fast)

| Resolution | Ratio | Width x Height |
|---|---|---|
| 480p | 16:9 | 864 x 496 |
| 480p | 4:3 | 752 x 560 |
| 480p | 1:1 | 640 x 640 |
| 480p | 3:4 | 560 x 752 |
| 480p | 9:16 | 496 x 864 |
| 480p | 21:9 | 992 x 432 |
| 720p | 16:9 | 1280 x 720 |
| 720p | 4:3 | 1112 x 834 |
| 720p | 1:1 | 960 x 960 |
| 720p | 3:4 | 834 x 1112 |
| 720p | 9:16 | 720 x 1280 |
| 720p | 21:9 | 1470 x 630 |

1080p is **not supported** for Seedance 2.0 / 2.0 fast.

## Parameter Constraints

| Parameter | Type | Range / Values | Default |
|---|---|---|---|
| duration | int | [4, 15] or -1 (auto) | 5 |
| seed | int | [-1, 2^32-1] | -1 (random) |
| execution_expires_after | int | [3600, 259200] seconds | 172800 (48h) |
| ratio | string | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9, adaptive | adaptive |
| resolution | string | 480p, 720p | 720p |

## Mutually Exclusive Modes

These input modes cannot be mixed:

1. **First-frame mode:** `role: "first_frame"` (with optional `last_frame`)
2. **Multimodal reference mode:** `role: "reference_image"`, `"reference_video"`, `"reference_audio"`

You cannot combine first-frame images with reference images/videos/audio in the same request.
