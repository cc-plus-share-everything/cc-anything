# Prompts

[中文](PROMPTS.md) | English

Two parts: what was actually said to Claude Code in this episode, and stage-by-stage prompts you can copy to remake a different video.

## 1. What was actually said

The whole video took only the sentences below. Claude Code drove everything else. The originals were in Chinese; translations follow each one.

1. `下面开始复刻 基准：中华民族的救亡图存 这条视频，原版还原。写代码的形式还原`
   "Start the remake. Reference: the video 中华民族的救亡图存. Reproduce it faithfully, and do it by writing code."
2. (Claude Code asked four multiple-choice questions; the recommended option was chosen each time.) Map data from Alibaba Cloud DataV; download the original locally for study only; archive photos from the public domain on Wikimedia Commons; picture first, audio later.
3. `打开项目目录，我来看下`
   "Open the project folder, let me take a look."
4. `很好，基本已经复刻全了。音乐再配上去吧`
   "Very good, it's basically all there. Now add the music."
5. `还是有些瑕疵的，2.09左右，北平，部队入城的照片没有了`
   "There are still some flaws. Around 2.09, the photo of troops entering Beiping is missing."

It could be this short because a constraints file was already in the project, and it gets read at the start of every session.

## 2. Constraints file (in the project root)

```text
# Video production constraints

- Paid generation budget is 0: do not call any paid video, image, voice or upscaling service.
- Render all visuals locally with code (Blender / PIL / ffmpeg).
- Do not ask me to watch anything before the render is finished. Tell me where the file is only after it has rendered and passed a full decode check.
- Ask me before downloading any file or publishing anything externally.
- Log the source and licence of every external asset (map data, photos, fonts).
- Someone else's original video is for study and comparison only and never goes into the final video; do not reuse its narration or music.
- Report honestly: state plainly what is unfinished, unverified, or still different from the original.
```

## 3. Stage-by-stage prompts

Replace `<…>` with your own content. Give them all at once or one stage at a time.

### 1. Breakdown

```text
The reference for this remake is this video: <link>. Do not write any render code yet. Only break it down:

1. Extract 2 frames per second, tile every 8 seconds into a 4×4 contact sheet, and look through every sheet.
2. Run local speech recognition on the audio and tell me whether there is narration, and what the music and sound effects are.
3. Output a shot list: time range / on-screen title / what the 3D picture is doing / info card and photos / subtitle text.
4. List the visual elements: scene, objects, materials, motions, post overlay layers.
5. Judge roughly how the original was made: what was done in 3D and what was overlaid in post.

Write it into the project README.
```

### 2. Ask everything once before starting

```text
Before production starts, ask me everything I need to decide in one go, with a recommended option and a reason for each:
data source, asset source and licence, whether to download the original locally, how to handle audio.
After I confirm, record it in the README and do not ask again for the same plan.
```

### 3. Data and textures

```text
Prepare the map data:
- Beyond province borders, cut the provinces the front line passes through down to prefecture level; each one is a separate puzzle piece.
- Use one equal-area projection and fix "1 unit = how many km"; every later script shares the same projection and extent constants.
- Simplify borders moderately; small islands do not become separate pieces.

Then generate textures with PIL: one full-map flag face per faction, with each piece sampling the part at its own position;
plus one scorch texture (one channel for intensity, one for appearance time) and one board base map.
Export a small preview of each texture and compare it with screenshots of the original.
```

### 4. Timeline

```text
Write the scheduling for the whole video as one timeline file that both the 3D render and the 2D post read:
- Flip events: start and end second, target faction, which pieces are selected, the longitude/latitude it spreads from
- Camera keyframes: second, look-at longitude/latitude, distance, elevation, azimuth; list hard cuts separately
- Start and end times and text for titles, subtitles, info cards, photos and battle markers

Times follow the shot list, aligned against the original at one frame every 0.5 seconds.
```

### 5. Blender scene

```text
Write a Blender Python script that runs headless:
- Generate puzzle pieces with thickness from outlines, with a gilded bevel around the top face and wood-coloured sides.
- All pieces share one material; use each object's own property to decide which flag face to show. Do not make one material per piece.
- Flip motion: lift, rotate, drop, staggered by distance from the spread centre.
- Lights follow the camera target; depth of field on close shots; motion blur on.
- It must support two modes: render low-resolution stills at specified seconds only; render a frame range while skipping frames that already exist.
- Export the pixel coordinates of key locations for every frame, for post.

Render stills at 4–6 timestamps first and check them yourself. Do not render the whole video straight away.
```

### 6. 2D post

```text
Write frame-by-frame post with PIL, multi-process: grading, vignette, top-left title, bottom subtitles, info cards,
white-bordered photos with captions, battle markers, landing sparks. Measure fonts, sizes and positions on screenshots of the original and convert to the output resolution.
When an asset is missing, use a grey placeholder instead of failing. Support processing a given frame range only.
```

### 7. Compare and iterate

```text
Write a comparison script: given a list of timestamps, tile the original and the remake at the same second side by side into one image.
Check the whole video at one point every 8 seconds, list the gaps, sort them by impact, and fix the timeline or post parameters one by one.
Re-render only the affected frames. Update "known gaps" in the README after each round.
```

### 8. Full render and delivery

```text
Once the stills comparison passes, render the whole video: split into two parallel processes that resume automatically after a crash.
Then encode to mp4, and also produce a side-by-side "original left / remake right" version.
Check total frame count, duration and that it decodes completely, and only then tell me the file path.
```

### 9. Flaw-spotting after watching

```text
Around <timestamp>, <what is wrong>.
```

For example: "Around 2.09, the photo of troops entering Beiping is missing." A time and a symptom are enough. It will compare against that second of the original, find the asset, add it, and redo only the affected segment.

## 4. A few lessons

- In the first sentence, say all of it: which video is the reference, how faithful it should be, and what form the work takes.
- Put constraints in a file instead of repeating them out loud.
- Having it produce stills before the full render saves most of the rework.
- When reporting a flaw, a timestamp is more useful than an adjective.
