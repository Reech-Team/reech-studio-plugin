---
name: make
description: Phase 3 of Reech Video Studio. Produce a planned video in the cloud. Covers voice-over (generated or recorded), footage, music, the edit from library formats and styles, previews (stills, proxy), the final render, QA and the rulebook audit, the creative review, delivery and logging. Use when a video has a script and production plan, or the user asks to build, edit, preview or render one.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__file_put, mcp__reech-studio__log_append, mcp__reech-studio__brand_save, mcp__reech-studio__campaign_save, mcp__reech-studio__video_save, mcp__reech-studio__media_upload_links, mcp__reech-studio__media_stats, mcp__reech-studio__media_transcribe, mcp__reech-studio__media_beats, mcp__reech-studio__video_stills, mcp__reech-studio__video_proxy, mcp__reech-studio__video_audio, mcp__reech-studio__video_render, mcp__reech-studio__video_qa, mcp__reech-studio__video_audit, mcp__reech-studio__video_deliver, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page, mcp__reech-studio__media_analyze
---

# Make: library first, then preview, render and check

Needs a `script.json` from `plan` (`video_get` shows it). Follow the recipe that fits the source material (`library_search` with `type: recipe`; the usual one is `short-form-brand-video`). Prefer quality over speed: look at frames, run the checks, and fix before the final render. Say you're on it with `lock_acquire` on the video folder.

## 1. Library first: reuse, tune, extend

`library_search` for formats, styles, components and media. Then:
- **Reuse** an entry as is when it fits the concept.
- **Tune** it for one brand when only sizes, motion speed, captions, SFX level or punch-ins differ: `tune` in the brand's `brand.ts` (`file_get` / `file_put`), no new code.
- **Extend or create** a format, style or component: that's a Reech-side step (new library code is reviewed and released by the team). Describe exactly what the piece needs and ask the team; meanwhile use the closest library entry so the rest of the work continues.

## 2. Audio

Everything downstream reads **one timed transcript**: word timings for captions, cuts and the audit.

- **Generated voice** (organizations with ElevenLabs; `me` → flags.eleven): `video_audio` on the video. It uses `script.json → voice` if the plan chose one, otherwise the brand's calibrated voice, then finishes the audio like an editor would (tightens pauses, caps the speed-up by the style's energy, voice chain, a music bed at the video's exact length, carved so the voice sits on top) and writes `media/brands/<slug>/audio/<video>/voices.json` with word timings and measured levels. Listen to it through `media_links`. If the video runs over its target length at the style's speed cap, cut words from the script and run it again; don't raise the speed.
- **Recorded voice** (every organization): the person uploads the recording (`media_upload_page` with `folder: "audio/<video>"`), then `media_transcribe` gives word timings, speech segments and speakers next to the file. Cut on that transcript. Don't speed it up or re-voice it.
- **Music:** the brand's own tracks or shared library music (`library_search`, `type: media`); `media_beats` on the track (with `campaign` + `video`) writes the video's `beats.json` for cutting on the beat. The edit sets music and SFX levels from the measured loudness (`media_stats` on the audio folder writes `levels.json`): music 14 dB under the voice, +5 dB in pauses; SFX ≥ 8 dB under the voice, at most one per 2 s. Music must have mid-register parts (strings, piano, pads, plucks); `media_stats` warns when a track is mostly sub-bass, which disappears on phones. Never end on silence.
- **Footage audio:** generated or stock clips often come with sound, sometimes speech in another language. Mute it in the edit (`script.json → footage_audio: "muted"`, list the files in `footage`) and add the ambience you want on purpose.

## 3. Footage

Use the brand's own media first (`media_list`), then the shared library footage (`library_search`, `type: media`), then stock the person provides, then generated media they bring (the person uploads; `media_upload_page`). Brand-owned media stays under `media/brands/<slug>/` and is listed in `assets/index.json` with source and license. Run `media_stats` on new footage so the edit knows how each clip moves and its color (no digital push on moving footage, shots matched in color).

**Talking to camera** (creator, UGC, presenter, testimonial): best is a real person recording on their phone (transcribe it, cut on the transcript, format `creator`). Lip-synced AI presenters are a Reech-side production step for now; ask the team.

**Memes and text formats:** format `meme`: beats with layouts (`text`, `full`, `split`) and text styles, media from the brand's own footage, stock or images the person brings (never a copyrighted meme template), a bed and a hit per beat.

## 4. The edit

Write `config.ts` with `video_save` (`config`), copying the shape from an existing video of the same format (`video_get` on it) and the format's config type (`file_get library/formats/<id>/code/timeline.ts` or the format's manifest). The shape: `export const compositionId = "<BrandVideo>";` and `export const <name>: <Format>Config = { brand: <brand>Brand, voiceFile: voices.A.tight.file, voiceWords: voices.A.tight.words, voiceDuration: voices.A.tight.duration, musicFile: voices.post.musicFile, audioPost: voices.post, shots: [...] … }`, with `import voices from "../../../../../../public/brands/<slug>/audio/<video>/voices.json"` and `import { <brand>Brand } from "../../../../brand"`. For a recorded voice, import the `<name>.words.json` the transcription wrote the same way. Mark `punch: true` only on up to 4 shots that land on emphasis words. Text placement, type sizes, caption paging, title hold times, footage push and color matching come from the template; don't override them per video. Cut on the voice: shots change on the words they illustrate (`at: { re: "^word" }`), and text appears on the word it repeats. The connector checks the config's shape and that it only touches this brand; a mistake in the code shows up as the stills job's error (read it, fix, retry).

**Canvas.** Videos are 9:16 (1080×1920) unless `config.ts` also exports `aspect`: `export const aspect = "16:9";` or a list, `export const aspect = ["9:16", "16:9"];`, which makes one composition per aspect from the same config. Presets: `9:16`, `16:9`, `1:1`, `4:5`. Jobs take `aspect` to pick one. `export const platform = "<id>"` overrides the audit platform.

**Look before rendering:** `video_stills` (default 8 spread frames; ask for `times` at the hook, every card and the CTA), then `job_wait`: the contact sheet comes back as an image. Look at it: safe zones, text size, that frame 0 already shows the hook, that footage and text match the script. `video_proxy` (a 540p cut, `from`/`to` in seconds) for motion, timing and sound; give the person the link when their eyes help. Fix the config and look again; iterate here, not on final renders.

## 5. Render and the checks

1. **`video_render`** when the cut is approved (`lufs: -14` for social; the default −16 is for long-form). It counts against the organization's monthly renders. `job_wait` returns the MP4 link, the file `name` and the report (frames, loudness, delivery checks); a report with blocking problems fails the job: read the error.
2. **`video_qa`** on that render: the transcript must match the script (dialect-aware) with no missing words and no unscripted speech, no voiceless gap over the script's limit (1.5 s unless planned silences raise it), no speech in unmuted footage, and the loudness on target. The sheet comes back as an image. Fix and re-render if anything fails.
3. **`video_audit`** on that render: the rulebook (safe zones, hook, pacing, music, captions, platform conventions). Fix every **block**. Fix **warn**s unless the brand has an override (`system.json → ruleOverrides` with a reason) or the concept needs them; then add an override with a reason rather than ignoring them. Read `audit.md` with `file_get` for the details.
4. **Creative review (blocks delivery: rule REVIEW-01).** Technical QA only proves the file is correct; it can't tell whether the video is good. Watch the proxy and the stills at every beat, read the transcript, listen for anything unplanned, and judge the render as a viewer against the concept. Write `review.md` with `video_save` (`review`, with `etags.review` from `video_get` when it exists): `render: <the render job's result name, e.g. a-antiacne.mp4; the audit compares exactly that>`, `verdict: pass|fix`, and an answer with what you saw for each:
   1. Does it feel like the concept (the premise line)?
   2. Does the look follow the footage and the story? Is the brand serving the piece or covering it?
   3. Does the voice fit the tone, and was it chosen for this piece?
   4. Does the script match the picture: room for the images, no line describing what's on screen?
   5. Anything unplanned: speech in the footage or another language, garbled text, artifacts, odd places, jumps? Is every planned sound layer actually audible (measured, not assumed)?
   6. The 3-second walk on the real render: where would a viewer leave?
   7. Does it look native to where it runs? Social (TikTok, Reels, Shorts) should look like the feed; a commercial can be cinematic. A social piece that looks like an ad fails this question.
   Anything that fails: fix it and review the new render. Then show the person (the MP4 link); their reaction is the real verdict, and their corrections go into the brand's `writing.md` (for that brand) or to the team (for the playbook).

## 6. Deliver and log

`video_deliver` (or `/reech-studio-cloud:deliver`): it requires a passing QA and a non-blocking audit on that render (or `force: true` with the reason in the post), copies the MP4 and its report to `media/brands/<slug>/deliverables/<campaign>/`, writes the cover and the SRT captions and a `post.md` template into the video's `deliver/` folder, and appends the post line to `posts.jsonl`. Its result says whether `deliver/post.md` still needs the copy (`post.needsCopy`). Then write the post copy into `deliver/post.md` with `file_put` (a hook line that isn't the video's first line, the CTA, 3–5 hashtags) and remove every TODO. Give the person the deliverable links (`media_links` on the MP4, cover and SRT) and summarize the video in 2–3 lines.

**Human checkpoint, only for a brand's first video** (or after a brand-system change): ask the person to watch it once. Otherwise deliver.

Each aspect (9:16, 16:9…) is rendered, checked and delivered separately.
