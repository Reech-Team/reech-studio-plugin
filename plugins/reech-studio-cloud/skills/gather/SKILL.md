---
name: gather
description: Phase 1 of Reech Video Studio. Collect the context for a video or campaign (goal, brand, guidelines, assets, data, audience, source material, constraints) and run the news/trend/calendar scan. Use to onboard a new brand, start a new campaign, or when a brand's context is missing or expired.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__file_put, mcp__reech-studio__log_append, mcp__reech-studio__brand_save, mcp__reech-studio__campaign_save, mcp__reech-studio__video_save, mcp__reech-studio__media_upload_links, mcp__reech-studio__media_stats, mcp__reech-studio__media_transcribe, mcp__reech-studio__media_clips, mcp__reech-studio__media_beats, mcp__reech-studio__video_stills, mcp__reech-studio__video_proxy, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page, mcp__reech-studio__media_analyze
---

# Gather: an adaptive intake

Output: a **context pack** saved in the brand's folders in Reech Studio storage, so any teammate or session starts from the same place. Ask only for what's missing or expired; never re-ask what's already recorded and still valid.

## 1. Check what's already known

`brand_get` returns identity, profile, system, history and the campaigns in one call; `file_get` reads anything else. Sort it by shelf life:

| Layer | Where | Valid | Re-ask when |
|---|---|---|---|
| Identity | `identity.json` | months–years | `revalidate_after` has passed, or the site clearly changed |
| Brand system | `system/profile.json`, `system/system.json`, `system/writing.md`, `brand.ts`, `assets/index.json` | until changed | a person says it changed, or something needed is missing |
| Campaign | `campaigns/<id>/brief.json` | until its end date | a new campaign starts; set `status: "closed"` after `dates.end` |
| Moment | `moment/<date>.json` | ~7 days | older than 7 days: rescan automatically, don't ask |
| History | `history.json`, `posts.jsonl` | permanent | never re-ask; Learn adds to it |

Then say in one line what you have and what you'll ask, for example: "Dermohelp is onboarded; there's no open campaign and the news scan is 12 days old. Two questions about the campaign, then I'll rescan."

## 2. Ask for what's missing, in one short round

In "just do it" mode (see `studio`) skip this round: research what you can (their site, their uploads, their posts), infer the rest, and mark it `source: "inferred"`. Otherwise batch the questions (use AskUserQuestion when the options are clear), and only for what you can't find or infer. Answers can be text, files, links, Drive, or "don't have it". Accept any format and read what you're given. Or run `/reech-studio-cloud:brief`, which asks the campaign questions in order.

- **Goal & campaign:** objective (entertain / educate / inform / convert), the engagement action that matters (save, send, comment, click), platforms and placements, campaign name and dates, number of videos, budget for stock, voice and music.
- **Brand:** guidelines (PDF, Figma, site), tone of voice, do's and don'ts, logo rules, any mandatory text they provide. If none exist, research the site and ask them to confirm a short summary.
- **Assets:** footage, photos, packshots, logos, music, voice talent, previous videos. Use theirs first; stock or generated media fills only the gaps. They upload on the page `media_upload_page` gives you: send them the link, and when they say it's done check `media_list`. A file attached to the chat doesn't reach Studio storage, so always give the link. List what arrived in `assets/index.json` with source and license, then `media_stats` on footage and music so the edit knows how each clip moves and how loud each track is.
- **Audience & insight:** who it's for, what they believe or struggle with, what already works for the brand.
- **Data:** analytics exports, past winners and losers, ad reports, screenshots, notes. Any source; summarize what it says.
- **Source material:** is the voice generated from a script, or is there a recording (podcast, interview, shoot, the client's own voice-over)? The same question for footage and music. This decides the recipe (`library_search` with `type: recipe`). Organizations without ElevenLabs always bring their own voice.
- **Constraints:** approvers, deadline, languages and markets, sensitive topics, any rules they provide (store them as context; no legal rules are built in).

## 3. Record it

- **A creator who works for several brands** (an influencer, a presenter, an agency's face) gets a brand of their own with `identity.kind: "creator"`: their voice (`profile.voice`, a clone on the organization's ElevenLabs account or "recording"), how they talk and write (`system/writing.md`), their look on camera, and their recordings and footage under `media/brands/<creator>/`. Each client brand that works with them lists them in `system.talent: ["<creator>"]`; only then may that brand's videos use the creator's voice, files and media. Nothing else crosses between brands: a client's assets never go to another client, and the creator's own brand never takes a client's assets.
- New brand: `brand_save` with `identity` (`name`, `site`, `what`, `markets`, `languages`, `audience`, `source`, `confidence`, `revalidate_after` +6 months; the slug is lowercase-with-dashes, with a market suffix if the brand has several, like `atrapalo-co`).
- Brand system: `brand_save` with `system` (`style`: a library style id, or `"new"`; `autonomy`: `"pick-from-top-3"` until the brand has about 5 posts with results, then the team may set `"auto"`; `ruleOverrides: []`; any team-provided rules) and `profile` (voice, music, mix, visual tokens, compliance notes they gave). Then the look, with `brand_save` → `tokens` (it writes `brand.ts`, no code): `accent` (the main brand color, hex), `displayFont` / `bodyFont` (the Google font closest to theirs, name without spaces: `Poppins`, `BarlowCondensed`, `Montserrat`), `displayWeight`, `upper` (all-caps headlines), `logo` (upload it first; `brands/<slug>/logo/<file>`), `style` (a library style id that fits), and `source` (where each value came from: their guidelines, their site, or "inferred"). Take them from the guidelines they gave, else from their site or logo, else infer them and say so; never block on missing tokens. A new brand saved without tokens gets a neutral starter, so previews work from the first minute. If the brand needs the style a little different (bigger or smaller text, calmer or snappier motion, bigger or all-caps captions, quieter or no SFX, no punch-ins), add `tune` to the tokens (`{ textScale 0.9–1.15, motion 0.75–1.35, captions: { scale 0.85–1.2, upper }, sfx 0–1.5, punch }`) instead of copying the style. A video's `config.ts` imports the name `brand_save` returns as `brandExport`.
- Campaign: `campaign_save` with `brief` (`name`, `product`, `objective`, `engagement`, `platforms`, `dates {start, end}`, `status: "open"`, `source`: who said it) and, when known, `plan` and `deliverables`.
- Every fact records its source and date, and how sure you are when it's not first-hand.

## 4. Context scan (automatic when the moment is older than 7 days and the video needs an angle; skip it when the person already brings the topic or idea)

Search the web for the brand's market and category: news in the last 2–4 weeks, trends and formats people are using, the cultural and commercial calendar for the next 6 weeks, competitor activity, and anything in the brand's own posts. Save `brands/<slug>/moment/<today>.json` with `file_put`: `layer: "moment"`, `expires` (+7 days), `items[] {what, why_it_matters, source, date}`, and `angles[]` worth a concept. Prefer what's timely and specific to the market over generic tips. Check `history.json → retired_phrases` and don't bring those back.

## 5. Calibrate a new brand once (the one human checkpoint)

A brand without `system/profile.json → calibrated` needs its sound decided (in "just do it" mode, pick the voice and music that best fit the brand yourself, mark them `calibrated: "inferred <date>"`, and say which you picked with the finished video):
- **Organizations with ElevenLabs** (`me` → flags.eleven): write a short sample script in the brand's voice as a temporary video (`video_save` into `campaigns/<id>/videos/calibration/` with 2–3 candidate `voice` choices from `voices_list`, one `video_audio` job each; ask first whether they already have voices they want, such as clones of their own people), give the person the audio links from `media_links`, and let them pick a voice and whether music belongs. Write the picks into `profile.voice` / `profile.music` / `profile.mix` with `calibrated: <date>`; delete the calibration video's files afterwards.
- **Organizations without ElevenLabs:** they bring recordings or a talent. Record who speaks, how, and the mix levels in `profile`; `calibrated` means "we know the sound".

## Done when

The brand, campaign, assets, source material and a moment less than 7 days old are in storage. Say in one line what the context pack contains, then continue with `plan`.
