---
name: studio
description: Start here for any Reech video work. Covers making a social or campaign video, reel, TikTok, Short, ad or podcast clip for any brand, and deciding which phase (gather, plan, make, learn) comes next. Use when the user mentions videos, content for a brand, a campaign, or Reech Studio.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page, mcp__reech-studio__media_clips, mcp__reech-studio__media_read, mcp__reech-studio__media_clean
---

# Reech Video Studio (cloud)

A system for making videos for **any brand or purpose** in four phases. Each phase is its own skill:

| Phase | Skill | Output |
|---|---|---|
| 1. Gather | `gather` | Context: who the brand is, the goal, assets, data, constraints, a fresh news/trend scan |
| 2. Plan | `plan` | What the video is for → the matching library playbook (or a new one) → script and production plan |
| 3. Make | `make` | Audio, footage, the edit, previews, render, checks, delivery, logged post |
| 4. Learn | `learn` | Results linked to what was made; brand rules updated |

## The journey: collaborate once, then reuse and go faster

What the person lives through, whichever phase you're in. The first video builds their style with them; every later one starts from it.

1. **They start however they want:** "make me a video like this" (one reference is normal, several is a bonus), a description of what they want, or just their recording. Take what they bring; never send them looking for more references.
2. **You ask the few questions a reference can't answer, then propose** (`gather` → style intake): what they like and dislike in what they showed, what the viewer should feel, what's on screen from the first second, what must never happen. A brand's first video: two short proposals on their real script, with sound, to pick from (`make` §0).
3. **The cut is theirs** (`make` → The cut): they cut it in their app, decide it with you sentence by sentence, or you propose and they correct.
4. **Editing rounds:** previews with sound, their corrections, the next preview, in the chat or in **their dashboard** (`me` → `dashboard`: they see and adjust everything there by hand while you execute). Each round is numbered and recorded in the video's `rounds.json` (`make` → Editing rounds), so "go back to round 2" works and nothing they said is lost.
5. **Approved → their style is saved** (`make` → Save the style): the moment they approve a final, the look, the rules from their corrections and the stills become `brands/<slug>/style/<name>/`, ready to reuse. Tell them its name.
6. **Reuse:** "use my style" (`brand_get` → `style.styles`) skips the intake and the two proposals: cut, highlights, build from the style, one preview round. "My style but…" changes that one video; if they like the change, save it as a variant.
7. **It gets faster:** every approved video adds its rules to the style; by the third or fourth video, "here's my recording, use my style" should be one round.

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
| Cut many short clips out of one long video (one job) | `media_clips` |
| Read a document they gave (brand guidelines, brief, deck: PDF, Word, PowerPoint, text) | `media_read` |
| Clean a recorded voice: remove background noise, enhance the voice | `media_clean` |
| The cut of a recording: a sheet to edit by sentence, or their own edit mapped onto the original | `media_cut` |
| The brand's saved video styles (to reuse) | `brand_get` → `style` |
| Their dashboard (what needs them, every video's stage, brands, styles, a video's previews, rounds and cut, activity with undo) | `me` → `dashboard`, `dashboardVideo` |
| Break down a video someone shares; look at stored images | `media_analyze`, `media_view` |
| Upload a file you can read (their project folder) yourself; or give them the page to upload from their browser or phone; download media | `media_upload_links` + a PUT, `media_upload_page`, `media_links` |
| Voice-over for a script (organizations with ElevenLabs) | `voices_list` to choose, `video_audio` |
| Look at the video: frames, a low-res cut | `video_stills`, `video_proxy` |
| Final render, checks, delivery | `video_render`, `video_qa`, `video_audit`, `video_deliver` |
| Jobs: wait for a result, progress, links, the contact sheet as an image | `job_wait`, `job_get`, `jobs_list` |
| Who is editing what, recent changes, the posts log | `lock_acquire`, `locks_list`, `activity`, `posts_list` |

Every heavy step returns a **job**: call `job_wait` (it waits up to 24 s on the server) until the status is `done` or `failed`, read its `error` when it fails, and look at the images it returns (stills and QA sheets come back as pictures: look at them, don't describe them from the numbers). `video_get` returns an etag per file (`etags.script`, `etags.config`, `etags.review`…): pass them to `video_save`.

If the connector isn't connected, say so: the person adds it from the plugin (it asks them to sign in with their Reech Studio account, email + password or Google). Someone without an organization asks Reech to be added; there is no self sign-up.

## Reviewers

`me` → `role: "reviewer"` (someone a brand invites to check its videos, e.g. a client's marketing lead): they can read everything in their brands, make previews and frame sheets (`video_stills`, `video_proxy`), leave notes and approve rounds (`rounds.json`, their own items only); every other change or job is refused by the engine. With a reviewer, don't attempt edits, renders or uploads: show them the video (their dashboard link from `me`), take their notes into `rounds.json` in their words (`by` their email, `via: "dashboard"`, `status: "open"`), and tell them the team will make the changes. Admins set roles with `admin_user_set` (`role: "member" | "reviewer"`) or in the dashboard's Settings; a change takes effect within 5 minutes.

## Keep it light

Everything a tool returns stays in the conversation and counts again on every later turn, and the person's Claude plan has a limit. Running out in the middle of a video is a failure of this work, so:

- **One video per conversation.** Everything is saved in Studio, so nothing is lost: when a video is finished, tell the person to open a new chat for the next one. If they ask for another in a long conversation, suggest it first.
- **Read once.** Don't call `brand_get`, `campaign_get` or `video_get` again for something you already read, unless an etag conflict says it changed.
- **Look only at what changed.** A contact sheet costs about 3,000 tokens. Use the full sheet once; after a small edit, ask for just the `times` it touched.
- **Never retype word timings.** Import the `.words.json` that `media_transcribe` wrote (see `make`). A long list copied into a file is paid for twice.
- **Long answers only when needed:** `allFiles` on `campaign_get` / `video_get`, `verbose` on jobs, `analysis.json` (the job already returns the pacing and the transcript), single frames with `media_view` (the sheet usually answers).

## Take what they bring, and execute

People bring what they have: brand guidelines, a logo, example videos, footage, a long recording, goals, a deadline, or just a sentence. Take all of it in one go (`media_upload_page` for files; read links and text as given), file it where it belongs (`gather` section 3), and move on. Never ask for something you can find or reasonably infer.

**"Just do it" mode.** When the person says to go ahead on their own ("hazlo", "ejecuta", "tú decide", "just make it", "no me preguntes"), or the brand's `system.json → autonomy` is `"auto"`, there are no stops at all: you choose the brand's look from their guidelines or site, the idea, the script, the voice and the music, render, check and deliver. Fill every gap yourself, then record each assumption where it lives (`source: "inferred"` on brand tokens and identity, `script.json → assumptions` for the video) and list them in two or three lines with the finished video, so the person can correct any of them next time. Facts are the only thing you never invent: a claim without a source is left out, not guessed.

## Principles (set by the team; don't re-argue them)

- **Engagement over ads.** Posts entertain, educate or inform (or a mix). Success is saves, sends, comments, watch time. Storytelling means **cohesive composition** (voice, text, footage, sound all serving one promise), not a literal narrative.
- **Ask, don't assume.** People hold different data, assets and guidelines. Never invent a brief, a claim or a brand rule. If something is missing, ask once, briefly, or research it and ask to confirm.
- **Any kind of video.** Brand campaigns are one kind; teaching, entertainment, news, stories, opinion and anything new are others. `plan` follows the library playbook for the kind of video, and writes a new one when none fits.
- **Library first, never locked in.** Search the library before building, then reuse, extend or create. Nothing is a default: the brief and the concept choose the format and style, not habit.
- **Minimal human checkpoints.** Humans only do what Claude can't: hear voice and music quality once per brand, and watch the first finished video. After that, a brand runs autonomously as far as its `system.json → autonomy` allows. A video has at most three stops before it's delivered: what to make, which idea (only if you proposed ideas), and the script with its production choices (`plan` section 3). Never ask to approve stills, a proxy or the cut. The person can always ask to work differently.
- **Never mix brands.** Everything about a brand lives under its own folders and is used only for that brand. The connector enforces it: a video's code may only use its own brand's files. The one deliberate exception is a creator brand (`identity.kind: "creator"`, a person who works for several brands) that a brand lists in `system.talent` (see `gather`).
- **Sources for facts.** Every factual claim in a script has a source; product claims only from the brand's own product page. Unverifiable numbers are left out.
- **No legal/compliance rules built in.** If a team provides them, store them as brand or campaign context and follow them.
- **Spend with care.** Previews (stills, proxies) are cheap; final renders count against the organization's monthly limit (`me` shows usage). Render once the script is approved and the stills pass your own look, and catch problems before the render (the voice-over check, the stills) so a video is rendered once.

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

Work it out from the request, the open campaign, or the last brand worked on (`brands_list`, `brand_get`). If two could match, ask, and in the same question ask what they have in mind (a topic, an idea, or "suggest ideas"): one question, not two. Before writing anything, say in one line which brand and campaign you're working on, for example: `Dermohelp · 2026-10-spf-sun-awareness · news scan 3 days old`. Say you're editing with `lock_acquire` on the video folder so teammates see it.

## Commands

`/reech-studio-cloud:brief` starts a campaign from a short conversation, `/reech-studio-cloud:approve` records an approval (script or cut) and moves on, `/reech-studio-cloud:deliver` runs render → checks → delivery for a finished cut.
