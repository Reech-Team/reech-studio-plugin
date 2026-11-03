---
name: plan
description: Phase 2 of Reech Video Studio. Decide what a video is for and how to plan it, then produce its script and production plan (shots, format and style, deliverables per platform). Works for any kind of video (brand campaign, teaching, entertainment, news, story, opinion, launch, anything new) through library playbooks; drafts a new playbook when none fits. Use when the next step is deciding what video to make or writing its script.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__file_put, mcp__reech-studio__log_append, mcp__reech-studio__brand_save, mcp__reech-studio__campaign_save, mcp__reech-studio__video_save, mcp__reech-studio__media_upload_links, mcp__reech-studio__media_stats, mcp__reech-studio__media_transcribe, mcp__reech-studio__media_clips, mcp__reech-studio__media_read, mcp__reech-studio__media_clean, mcp__reech-studio__media_beats, mcp__reech-studio__video_stills, mcp__reech-studio__video_proxy, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page, mcp__reech-studio__media_analyze
---

# Plan: from an idea (or a goal) to a production plan

Needs an onboarded brand (otherwise run `gather` first). Videos live under a campaign; a campaign can also be a series ("weekly explainers") or a one-off.

## 1. What is this video for?

Don't assume it's a brand campaign. From what the person said, work out, and ask only what's missing (one short message):
- **The job** for the viewer: teach, entertain, inform, sell or launch, tell a story, give an opinion, show behind the scenes, start a conversation… or a mix, or something new.
- **What they bring:** an open goal (find the angle for me), a topic, an idea, a model video, raw material (a recording, a talk, a demo).
- **References:** when they point to someone else's video ("algo como el de…"), look for it in the inspiration library (`library_search` with `type: inspiration`) and read its `breakdown.md` with `file_get`; borrow its structure and techniques, never its footage, music or script. Record it in `script.json → references`. (A new video they share: see **Analyze a video they share** below.)
- **How they want to work:** propose and let them pick, iterate together line by line, or go ahead on your own. Follow their answer over any default.

## Analyze a video they share

When someone shares a video ("analiza este video", "quiero uno como este"), work it out this way, with no checkpoint before the breakdown. Answer in their language.

1. **Get the file:** `media_upload_page` with the brand and `folder: "references"`. Send the link and wait. A link to a social post can't be read: ask for the file or a screen recording. Confirm it arrived with `media_list`.
2. **Measure:** `media_analyze` with the path and, if known, `kind`, `language` and `source`. `job_wait` until done; it returns the contact sheet as an image plus the pacing and the transcript, which is usually enough. The full shot-by-shot data (`file_get` on `brands/<slug>/references/<id>/analysis.json`) is long: open it only for a question the sheet can't answer.
3. **Look:** at the sheet, then `media_view` on `frames/first.jpg` (is the hook on screen at 0?) and only on the frames where the sheet leaves a doubt. Never describe a frame you haven't seen.
4. **Read the context:** the job for the viewer, audience, offer and claims, the hook (catalog ids from `library_search` with `type: hook`), the structure with times, tone, voice, music role, CTA.
5. **Read the design:** framing and camera per shot, on-screen text (font feel, size, position, when it lands), captions, colors, graphics, transitions, the rhythm of the cuts, and how the brand appears. Tie each thing to a moment.
6. **Write it down, always, even for a recut of the brand's own video:** the next conversation starts from this. `file_put` needs the whole file, so put the `read` in `breakdown.md` rather than rewriting `analysis.json`. Keep it under 200 words: one-line summary, structure with times, the design, what to borrow, what won't transfer. It stays in this brand's folders; it never goes in the shared library.
7. **Show it** in a few lines and ask what to keep. Then map it: the closest format and style from the library and how each design element is set in them. If something can't be built with what exists (a new layout or animation), say so plainly, offer the closest version, and tell them the team can build the rest.
8. **Plan the new video** from their new context and assets with the normal flow below, recording the reference id in `script.json → references`.

Their own brand's video may be reused as footage if they say so (list it in `assets/index.json`). Someone else's video is inspiration only: its structure and techniques, never its footage, music or script.

## 2. Pick the playbook (or make one)

A playbook is the library's guide for one kind of video: what good looks like, what to work out before writing, where the person's input matters, the usual structure, and the self-check. `library_search` with `type: playbook`, then `file_get` the matching `library/playbooks/<id>/guide.md`.
- **One fits:** follow it. A mix (a funny explainer) → follow the main one and borrow from the other.
- **None fits:** don't force the closest one. Draft a playbook for this kind of video with the person in a few lines: the job and what makes it good, who the viewer is and what they bring, what to work out before writing, where the person's input matters, a usual structure, a self-check. Use it for this video. Saving it to the shared library is a Reech-side step: hand the draft to the team (paste it in the answer) so they add it for everyone.
- **Improve as you go:** when a person's corrections are about one brand's taste, put them in that brand's `system/writing.md` (`file_put`). When they would improve the playbook for everyone, tell the team.

The brand's `system/writing.md` (if it exists) wins over the playbook. Read it before writing.

## 3. Checkpoints

**In "just do it" mode** (the person said to go ahead on their own, or `autonomy` is `"auto"`; see `studio`), there are no stops: decide each of these yourself, write your choices and assumptions into `script.json → assumptions`, and go straight to `make`.

**Otherwise, at most three stops before the finished video**, unless the person asked to work differently in step 1. Every stop costs them a wait and a reply, so fold everything you need into these:
1. **What to make:** the brand (only if it isn't clear) and what they have in mind, in one question. If they ask for ideas, the ideas come in your next reply.
2. **Which idea:** only when you proposed ideas. Give each one its premise line, so the pick is already the angle.
3. **The script:** the one review before money is spent. Show the script together with whatever the playbook asks the person to check before writing (the viewer's doubts, the angle, the comparison: put them next to the lines that answer them, not in a separate round), the facts and their sources, what is invented to illustrate, and the production choices still open (voice, music, length) with your recommendation for each. Ask once: "Does it work, or what should change?" Their answer approves the script and the choices you recommended.

The playbook's checkpoints that come before the script are answered inside stop 3; don't hold a separate round for them. After stop 3 there are no more questions until delivery: don't ask to approve the cut, the stills or the render (`make` judges those), and only ask again when something truly needs the person (a file only they have, or a choice the brand system doesn't settle and you can't decide). When a step is a choice among options and `system.json → autonomy` is `"auto"`, make the choice yourself and say why in one line. `/reech-studio-cloud:approve` records the person's approval of a script.

## 4. Production plan (every kind of video)

**Before writing it, test the premise:** say the video in one line, without the visuals. If that line isn't interesting on its own, change the idea: great footage can't save a boring premise, but a good premise survives simple footage. Also write in one line which part of the brand's positioning this video builds (`script.json → builds`).

Save the script with `video_save` (`brand`, `campaign`, `video` folder name in lowercase-with-dashes, `script`): `profile` (`studio/brands/<slug>/system/profile.json`), `playbook`, `concept`, `format`, `objective`, `engagement`, `hook_type`, `beats[] {id, vo, card/shot notes}`, `facts_checked {claim: source}`, `footage[]` (paths under `brands/<slug>/` or `library/`, relative to `media/`), plus whatever the playbook adds. Look at an existing video's `script.json` (`video_get`) for the shape. Then:

- **Voice-led or picture-led?** Decide first.
  - **Voice-led** (explainers, listicles, a brand's series): the voice is the spine; on-screen text and footage illustrate the line being spoken. Write for the ear: short sentences, the local language and dialect, the brand's voice direction.
  - **Picture-led** (commercials, brand films, stories, anything shot or generated as scenes): write the film first, shot by shot, as if it had no voice. Then add only the few lines that say what the picture can't: about 1.5 words a second at most, planned silences (`script.json → max_voiceless_gap_s`, with the reason), and no line that describes what's on screen.
  - Length from the playbook, the platform plan or the model video; otherwise 20–40 s.
- **The voice is chosen for this piece.** With ElevenLabs enabled, the brand's profile voice only when the playbook or the person says it fits; otherwise `script.json → voice {voice_id, name, why}` overrides the profile. Take the `voice_id` from `voices_list`; a voice that isn't on the account fails. Without ElevenLabs, the plan names who records it and how (`script.json → voice_source: "recording"`), and the recording is transcribed in `make`.
- **Hook** in the first second, in both the words and the picture. Pick it from the hook catalog (`library_search` with `type: hook`: each entry has the feeling, the question, how to make it, whether it works muted, and example references); combine two when it helps. Testing rules (rulebook `research-testing`; follow them by default, break them on purpose when the idea needs it, and say so): the hook makes the viewer feel something and leaves a question (RT-05); it opens broad and narrows in the middle and CTA (RT-06); the brand is recognizable within 2 s, in the scene, spoken or by sound, not a bare logo (RT-01, `script.json → brand_cues [{t, asset, kind}]`). **CTA** that asks for the action the video is after.
- **Feeling and showmanship** (RT-02/03/04): name the feeling the video builds (`script.json → feeling`); count people, place, story, dialogue and melody against voice-over monologue, wall-to-wall text, fast montage and abstract product shots (`showmanship {right, left}`); let the first on-screen text frame how to watch rather than cover the video. The brand's recurring device, if it has one, goes in `brand_device` (RT-07).
- **One test when it's cheap.** If a testing rule is in question for this brand and the video can carry two variants, plan one: `script.json → test {rule, a, b}`. Learn compares them. Don't test more than one thing per video.
- **Rotation:** check `posts_list` and `history.json` so the same device, hook or phrase isn't repeated too soon.
- **Shots:** what each beat needs, marking what already exists (`media_list` for the brand; `library_search` with `type: media` for shared stock) and what must be sourced, uploaded or generated.
- **Format × style:** the format that fits (`library_search` with `type: format`, read its manifest and config type with `file_get`), and a look that fits the piece. A brand's series style belongs to that series. For other pieces take the brand's essentials and let the piece's own look lead; the brand serves the piece, it doesn't cover it. If it needs something the library doesn't have, say so: new formats, styles and components are a Reech-side step (ask the team); `make` works with what the library offers.
- **Deliverables per platform** (`library_search` with `type: platform`): aspect ratios, lengths and cutdowns, cover frame, caption file, A/B hook variants if a test is worth running.

- **Walk it in 3-second blocks, as the viewer.** Before anything is produced, go through the script and shots three seconds at a time as someone who doesn't care yet: what do I see and hear, do I care, and what makes me want the next three seconds? Rewrite any block that fails. Write the walk in `script.json → viewer_walk` (`[{t: "0-3", sees, hears, why_stay}]`), and show it with the script when the playbook has a script checkpoint. Write the hook in `script.json → hook {ids, emotion, question}`.

Fact-check every claim before any money is spent on audio. Then hand over to `make`.
