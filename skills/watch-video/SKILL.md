---
name: watch-video
description: "Use when the user wants Claude to actually SEE a video (not read a transcript). Extracts frames with ffmpeg so Claude's vision can view them. Triggers: 'watch this video', 'look at this clip', 'see the video', 'analyze this footage', 'what happens in this video', or any request to visually inspect a .mp4/.mov/.mkv/.webm/.avi/.gif/YouTube URL. NOT for audio-only content — use transcription for that."
---

# /watch-video

Claude can see images, not video files. This skill turns a video into frames Claude can actually look at.

## Decide the mode

Pick ONE based on what the user wants to know:

| User wants... | Mode | Command |
|---|---|---|
| Gist of the whole video in one glance | **contact sheet** | tile mode |
| Follow a specific action / gesture / motion | **dense frames** | fps=2 or higher |
| Long video, get the plot | **sparse frames** | fps=1/5 or 1/10 |
| Detect a specific moment | **dense + zoom** | fps=2 then crop the hit |

## Commands

### 1. Contact sheet (start here 90% of the time)
One image, whole video visible.
```bash
ffmpeg -i "<video>" -vf "fps=1/2,scale=320:-1,tile=6x10" sheet.jpg
```
- `fps=1/2` = 1 frame every 2 seconds
- `tile=6x10` = 60 frames in a 6-wide, 10-tall grid
- Scale `320` per tile keeps sheet under ~2MB

Tune tile grid to video length:
- <30s clip: `fps=1,tile=5x6`
- 1-3 min: `fps=1/2,tile=6x10`
- 5-10 min: `fps=1/5,tile=6x12`
- 20+ min: `fps=1/15,tile=8x12`

### 2. Individual frames (when contact sheet isn't enough)
```bash
mkdir -p frames && ffmpeg -i "<video>" -vf "fps=1,scale=640:-1" frames/f_%04d.jpg
```
Then Read the frames one by one, or paste the interesting ones.

### 3. Hand gestures / fast motion (like Miles Morales web-shot graphics)
```bash
mkdir -p frames && ffmpeg -i "<video>" -vf "fps=4,scale=480:-1" frames/f_%04d.jpg
```
4 fps catches transitions. Still cheap.

### 4. YouTube URL
Download first, then run mode 1:
```bash
yt-dlp -f "bv*[height<=720]+ba/b[height<=720]" -o "video.%(ext)s" "<url>"
ffmpeg -i video.* -vf "fps=1/2,scale=320:-1,tile=6x10" sheet.jpg
```
If `yt-dlp` isn't installed: `pip install -U yt-dlp`.

### 5. Grab a specific timestamp (user says "look at 1:23")
```bash
ffmpeg -ss 00:01:23 -i "<video>" -frames:v 1 -q:v 2 moment.jpg
```

## After extracting

1. Read the sheet/frames with the Read tool — Claude's vision handles JPG/PNG.
2. If the user should see what Claude saw, send with SendUserFile (`display: "render"`).
3. Describe frame-by-frame or overall. Timestamps = `frame_number * frame_interval`.

## When NOT to use

- User wants dialogue / spoken content -> use whisper transcription instead
- User wants both visuals AND dialogue -> extract frames AND transcribe in parallel
- File is already a GIF under ~5MB -> just Read it directly, no ffmpeg needed
- Live stream -> grab a segment first: `ffmpeg -t 60 -i <url> clip.mp4`

## Prerequisites

- `ffmpeg` in PATH (`winget install ffmpeg` on Windows if missing)
- `yt-dlp` only for YouTube mode

## Output location

Put artifacts in the session scratchpad, never the project root:
- Windows: `%TEMP%\claude\...\scratchpad\`
- Otherwise: `mktemp -d`

ponytail: contact sheet first, only escalate to per-frame if the sheet isn't enough.
