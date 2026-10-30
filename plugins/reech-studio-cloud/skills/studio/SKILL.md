---
name: studio
description: Start here for any Reech video work. Covers making a social or campaign video, reel, TikTok, Short, ad or podcast clip for any brand, and deciding which phase (gather, plan, make, learn) comes next. Use when the user mentions videos, content for a brand, a campaign, or Reech Studio.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release
---

# Reech Video Studio (cloud)

A system for making videos for **any brand or purpose** in four phases. Each phase is its own skill:

| Phase | Skill | Output |
|---|---|---|
| 1. Gather | `gather` | Context: who the brand is, the goal, assets, data, constraints, a fresh news/trend scan |
| 2. Plan | `plan` | What the video is for → the matching library playbook (or a new one) → script and production plan |
| 3. Make | `make` | Audio, footage, the edit, previews, render, checks, delivery, logged post |
| 4. Learn | `learn` | Results linked to what was made; brand rules updated |

Learn feeds the next Gather and Plan for that brand. Go in order, but skip what's already known: a brand that's onboarded with an open campaign and a fresh scan goes straight to Plan, and someone who already brings a topic or idea doesn't need a scan unless the playbook uses one.

## Everything happens through the Reech Studio connector

There are no files on this computer, no commands and nothing to install. Brands, campaigns, videos, the shared library and media live in Reech Studio storage, and every step (previews, render, checks, delivery) runs in the cloud. Use the connector's tools:

| Need | Tool |
|---|---|
| Who am I, which organization, what's enabled, usage against limits | `me` |
| Brands, one brand, a campaign, a video | `brands_list`, `brand_get`, `campaign_get`, `video_get` |
| Create or change a brand, campaign, video (shape-checked, etag-safe) | `brand_save`, `campaign_save`, `video_save` |
| Any other text file (library guides, rules, research) | `file_get`, `file_put` (pass the etag you read), `log_append` for `.jsonl` logs |
| The shared library (formats, styles, playbooks, hooks, rules, recipes, sfx, stock) | `library_search` |
| A brand's media; measure, transcribe, beat grid | `media_list`, `media_stats`, `media_transcribe`, `media_beats` |
| Upload or download media | `media_upload_links`, `media_links` |
| Voice-over for a script (organizations with ElevenLabs) | `video_audio` |
| Look at the video: frames, a low-res cut | `video_stills`, `video_proxy` |
| Final render, checks, delivery | `video_render`, `video_qa`, `video_audit`, `video_deliver` |
| Jobs: wait for a result, progress, links, the contact sheet as an image | `job_wait`, `job_get`, `jobs_list` |
| Who is editing what, recent changes, the posts log | `lock_acquire`, `locks_list`, `activity`, `posts_list` |

Every heavy step returns a **job**: call `job_wait` (it waits up to 20 s on the server) until the status is `done` or `failed`, read its `error` when it fails, and look at the images it returns (stills and QA sheets come back as pictures: look at them, don't describe them from the numbers). `video_get` returns an etag per file (`etags.script`, `etags.config`, `etags.review`…): pass them to `video_save`.

If the connector isn't connected, say so: the person adds it from the plugin (it asks them to sign in with their Reech Studio account, email + password or Google). Someone without an organization asks Reech to be added; there is no self sign-up.

## Principles (set by the team; don't re-argue them)

- **Engagement over ads.** Posts entertain, educate or inform (or a mix). Success is saves, sends, comments, watch time. Storytelling means **cohesive composition** (voice, text, footage, sound all serving one promise), not a literal narrative.
- **Ask, don't assume.** People hold different data, assets and guidelines. Never invent a brief, a claim or a brand rule. If something is missing, ask once, briefly, or research it and ask to confirm.
- **Any kind of video.** Brand campaigns are one kind; teaching, entertainment, news, stories, opinion and anything new are others. `plan` follows the library playbook for the kind of video, and writes a new one when none fits.
- **Library first, never locked in.** Search the library before building, then reuse, extend or create. Nothing is a default: the brief and the concept choose the format and style, not habit.
- **Minimal human checkpoints.** Humans only do what Claude can't: hear voice and music quality once per brand, and watch the first finished video. After that, a brand runs autonomously as far as its `system.json → autonomy` allows. Don't add approval steps beyond the ones each playbook names. The person can always ask to work differently.
- **Never mix brands.** Everything about a brand lives under its own folders and is used only for that brand. The connector enforces it: a video's code may only use its own brand's files.
- **Sources for facts.** Every factual claim in a script has a source; product claims only from the brand's own product page. Unverifiable numbers are left out.
- **No legal/compliance rules built in.** If a team provides them, store them as brand or campaign context and follow them.
- **Spend with care.** Previews (stills, proxies) are cheap; final renders count against the organization's monthly limit (`me` shows usage). Render only approved cuts.

## Where things live (paths in Studio storage)

```
brands/<slug>/                 one brand; never mixed
  identity.json                who they are (durable; revalidate_after)
  system/profile.json          calibrated voice, music, mix, visual tokens, compliance notes the team gave
  system/system.json           style, autonomy, ruleOverrides
  system/writing.md            optional: how the brand writes (wins over the playbook)
  brand.ts                     tokens for the renderer (colors, fonts, logo, style id, optional `tune`)
  assets/index.json            brand-owned media: what, where, license
  research/                    site captures, catalogs, guidelines they gave
  moment/<YYYY-MM-DD>.json     news, trends, calendar scan (expires after ~7 days)
  history.json                 learned rules (retired phrases, rotation rules)
  posts.jsonl                  one line per video made/posted, and result lines (append-only)
  campaigns/<id>/brief.json    goal, KPIs, platforms, dates, status
  campaigns/<id>/plan.md       the campaign's production plan
  campaigns/<id>/videos/<v>/   script.json, config.ts, beats.json, qa/, project.json, audit.md, review.md, deliver/
library/                       shared, brand-neutral: formats, styles, components, playbooks, hooks, rules, recipes…
media/brands/<slug>/           the brand's media: footage, audio per video, logo, products, deliverables/
media/library/                 shared footage and SFX
```

In a `config.ts`, media paths are relative to `media/` (`brands/<slug>/audio/<video>/voice.mp3`), and code imports use the repo layout: `../../../../brand` for the brand's tokens, `../../../../../../library/formats/<id>/code/timeline` for a library format, `../../../../../../src/…` for generic code. The connector rejects a config that reaches into another brand.

## Picking the brand

Work it out from the request, the open campaign, or the last brand worked on (`brands_list`, `brand_get`). If two could match, ask. Before writing anything, say in one line which brand and campaign you're working on, for example: `Dermohelp · 2026-10-spf-sun-awareness · news scan 3 days old`. Say you're editing with `lock_acquire` on the video folder so teammates see it.

## Commands

`/reech-studio-cloud:brief` starts a campaign from a short conversation, `/reech-studio-cloud:approve` records an approval (script or cut) and moves on, `/reech-studio-cloud:deliver` runs render → checks → delivery for an approved cut.
