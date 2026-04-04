# Input Specifications & Constraints

Detailed constraints for all input types accepted by the meme-master CLI with Seedance 2.0 models.

## Image Inputs (--image, --last-frame-image)

| Property | Constraint |
|---|---|
| Formats | jpeg, png, webp, bmp, tiff, gif |
| Aspect ratio (w/h) | 0.4 - 2.5 |
| Dimensions per side | 300 - 6000 px |
| Max file size | 30 MB |
| Input type | Local file path only |

## Reference Images (--reference-image)

| Property | Constraint |
|---|---|
| Formats | jpeg, png, webp, bmp, tiff, gif |
| Aspect ratio (w/h) | 0.4 - 2.5 |
| Dimensions per side | 300 - 6000 px |
| Max file size | 30 MB |
| Max count | 9 |
| Input type | Local path or URL |

## Reference Videos (--reference-video)

| Property | Constraint |
|---|---|
| Formats | mp4, mov |
| Duration per clip | 2 - 15 seconds |
| Total duration (all clips) | <= 15 seconds |
| Max count | 3 |
| Resolution | 480p - 720p |
| Aspect ratio (w/h) | 0.4 - 2.5 |
| Dimensions per side | 300 - 6000 px |
| Total pixels (w * h) | 409600 - 927408 |
| Max file size | 50 MB |
| Frame rate | 24 - 60 FPS |
| Input type | Local path or URL |

## Reference Audio (--reference-audio)

| Property | Constraint |
|---|---|
| Formats | wav, mp3 |
| Duration per clip | 2 - 15 seconds |
| Total duration (all clips) | <= 15 seconds |
| Max count | 3 |
| Max file size | 15 MB |
| Input type | Local path or URL |

Audio cannot be the sole input — at least one reference image or video must accompany it.

## Text Prompts (--prompt)

| Property | Constraint |
|---|---|
| Languages | Chinese, English |
| Recommended length (Chinese) | < 500 characters |
| Recommended length (English) | < 1000 words |

## Output Video Specs (Seedance 2.0 / 2.0 fast)

| Resolution | Ratio | Pixels |
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

## Validation Rules

The CLI enforces these constraints at parse time:

1. `--last-frame-image` requires `--image`
2. `--image` (first-frame mode) and `--reference-*` (multimodal mode) are mutually exclusive
3. `--reference-audio` requires at least one `--reference-image` or `--reference-video`
4. Duration must be 4-15 for Seedance 2.0
5. Resolution must be 480p or 720p for Seedance 2.0
6. `--frames` is not supported for Seedance 2.0
7. `--camera-fixed` is not supported for Seedance 2.0
8. Local file paths are verified to exist before submission
