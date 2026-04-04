# Seedance 2.0 Prompt Tips

## Table of Contents
1. [Prompt Structure](#prompt-structure)
2. [Text Overlays and Slogans](#text-overlays)
3. [Subtitles](#subtitles)
4. [Speech Bubbles](#speech-bubbles)
5. [Image References](#image-references)
6. [Video References](#video-references)
7. [Audio References](#audio-references)
8. [Multimodal Reference Indexing](#reference-indexing)

## Prompt Structure

The basic formula is: **Subject + Action** (required) + **Environment + Aesthetics** (optional) + **Camera + Audio** (optional).

Seedance 2.0 follows natural language deeply, so write prompts as descriptive narratives rather than keyword lists.

## Text Overlays

To add slogans or advertising text in the video:

```
Template: At [time], [text content] appears on the [position] of the screen
Example: "At the end, the slogan 'Fresh and Natural' appears at the bottom of the screen in elegant white font"
```

The model auto-selects style and color, but you can specify: color, font style, appearance timing, position.

## Subtitles

To add synchronized subtitles:

```
Template: Subtitles appear at the bottom of the screen, content is "...", synced with the audio rhythm.
```

## Speech Bubbles

For comic-style dialogue bubbles:

```
Template: [Character] says "...", speech bubbles with the dialogue appear near the character.
```

## Image References

When using multiple reference images, order matters. Reference them as "image 1", "image 2", etc. based on their order in the content array (among image_url items, 1-indexed).

**Multi-angle character reference:**
```
Template: Generate a video based on the multi-angle views of the character in image 1. [Scene and action description].
```
Upload multiple angles of the same character to maintain consistent appearance.

**Multi-image scene composition:**
For better instruction following with multiple images, use bracket notation:
```
[image 1] the boy wearing glasses and blue t-shirt and [image 2]'s corgi dog, sitting on [image 3]'s lawn, 3D cartoon style
```

## Video References

Reference videos as "video 1", "video 2", etc. (among video_url items, 1-indexed).

**Action reference:** "The character performs the action from video 1"
**Camera movement reference:** "Use the camera movement/tracking from video 1"
**Style reference:** "Match the visual style of video 1"

## Audio References

Reference audio as "audio 1", etc. (among audio_url items, 1-indexed).

**Background music:** "Use audio 1 as background music throughout"
**Sound effects:** "Apply the sound effect from audio 1 at the moment of impact"
**Voice/tone reference:** "Use the voice tone from audio 1 for narration"

## Reference Indexing

The numbering is per-type and 1-indexed based on array order:
- "image 1" = the 1st `type: "image_url"` item in the content array
- "video 2" = the 2nd `type: "video_url"` item
- "audio 1" = the 1st `type: "audio_url"` item

Asset IDs (e.g., `asset://asset-xxx`) are only for passing the resource to the model. In the text prompt, always use the "type + number" format to refer to them.
