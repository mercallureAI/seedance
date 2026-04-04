# meme-master CLI Examples & Recipes

Ready-to-use command patterns for common video generation tasks.

## Table of Contents
1. [Text-to-Video](#text-to-video)
2. [First Frame](#first-frame)
3. [First + Last Frame](#first-last-frame)
4. [Multimodal Reference](#multimodal-reference)
5. [Video Editing](#video-editing)
6. [Video Extension](#video-extension)
7. [Web Search](#web-search)
8. [Video Chaining](#video-chaining)
9. [Quality vs Speed](#quality-vs-speed)
10. [Managing Tasks](#managing-tasks)

---

## Text-to-Video

Simplest mode — just a prompt:

```bash
meme-master video generate \
  --prompt "A cat yawning at the camera, soft golden hour lighting, close-up shot"
```

With specific settings:

```bash
meme-master video generate \
  --prompt "Aerial drone shot of a coastal city at sunset, waves crashing" \
  --aspect-ratio 21:9 \
  --duration 8 \
  --generate-audio on
```

Silent output (good for scripting):

```bash
meme-master video generate \
  --prompt "Time-lapse of flowers blooming in a garden" \
  --duration 10 \
  --silent
```

---

## First Frame

Use a local image as the starting frame:

```bash
meme-master video generate \
  --prompt "The girl opens her eyes and smiles gently at the camera" \
  --image ./portrait.png \
  --duration 5
```

---

## First + Last Frame

Control both start and end of the video:

```bash
meme-master video generate \
  --prompt "360 degree rotation around the sculpture" \
  --image ./front-view.png \
  --last-frame-image ./back-view.png \
  --duration 6
```

---

## Multimodal Reference

Combine reference images, videos, and audio:

```bash
# Character from image + camera movement from video + background music
meme-master video generate \
  --prompt "Use video 1's camera movement. Image 1's character walks through a forest. Background music from audio 1." \
  --reference-image ./character.png \
  --reference-video ./camera-movement.mp4 \
  --reference-audio ./bgm.mp3 \
  --aspect-ratio 16:9 \
  --duration 10
```

Multiple reference images:

```bash
meme-master video generate \
  --prompt "[image 1] the boy in blue and [image 2]'s corgi dog, playing in [image 3]'s park, 3D cartoon style" \
  --reference-image ./boy.png \
  --reference-image ./corgi.jpg \
  --reference-image ./park.jpg \
  --duration 8
```

---

## Video Editing

Replace or modify elements in a video:

```bash
# Replace an object
meme-master video generate \
  --prompt "Replace the perfume in video 1's gift box with image 1's cream jar, keep camera movement unchanged" \
  --reference-video ./gift-box-scene.mp4 \
  --reference-image ./cream.jpg \
  --duration 5

# Change environment
meme-master video generate \
  --prompt "Change video 1's background to a snowy winter scene like image 1" \
  --reference-video ./outdoor-scene.mp4 \
  --reference-image ./snow-reference.jpg \
  --duration 5
```

---

## Video Extension

Extend a clip or stitch multiple clips:

```bash
# Extend forward
meme-master video generate \
  --prompt "Extend video 1 forward, the car drives smoothly into a desert oasis" \
  --reference-video ./car-driving.mp4 \
  --duration 10

# Stitch three clips with transitions
meme-master video generate \
  --prompt "Video 1's window opens into a gallery interior, connecting to video 2, then the camera enters the painting, connecting to video 3" \
  --reference-video ./window.mp4 \
  --reference-video ./gallery.mp4 \
  --reference-video ./painting.mp4 \
  --duration 12
```

---

## Web Search

Enable web search for real-world visual accuracy (text-only input):

```bash
meme-master video generate \
  --prompt "A macro shot of a glass frog on a rainforest leaf, its transparent belly revealing a beating red heart" \
  --tool web_search \
  --duration 10
```

Note: `--tool web_search` only works with text-only input (no images/videos/audio).

---

## Video Chaining

Create multi-segment continuous videos using the last frame:

```bash
# Segment 1 — request the last frame
meme-master video generate \
  --prompt "Scene 1: A woman walks through a sunlit garden" \
  --return-last-frame on \
  --duration 5

# Check the output for Last Frame URL, then use it for segment 2
meme-master video generate \
  --prompt "Scene 2: She sits on a bench and opens a book" \
  --image "https://the-last-frame-url-from-segment-1.png" \
  --return-last-frame on \
  --duration 5

# Repeat for segment 3, 4, etc.
```

---

## Quality vs Speed

```bash
# Fast (default) — good quality, quick turnaround
meme-master video generate \
  --prompt "A playful kitten chasing a butterfly" \
  --duration 5

# Maximum quality — use the standard model
meme-master video generate \
  --prompt "A playful kitten chasing a butterfly" \
  --model doubao-seedance-2-0-260128 \
  --duration 5
```

---

## Managing Tasks

```bash
# List all local generation runs
meme-master video list

# Check a task's status on the server
meme-master video get --task-id cgt-20260403112501-xxxxx

# Download a completed video
meme-master video get --task-id cgt-20260403112501-xxxxx --download

# Custom output directory
meme-master video generate \
  --prompt "Product showcase video" \
  --out-dir ./my-project/videos
```

---

## Verbose Output

```bash
# Default output — clean progress updates
meme-master video generate --prompt "..."

# Nerd mode — see all internal details
meme-master video generate --prompt "..." --nerd

# Silent — just the final result path
meme-master video generate --prompt "..." --silent
```
