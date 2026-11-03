---
description: Start a campaign from a short conversation (goal, platforms, dates, assets) and save its brief
argument-hint: [brand] [campaign name]
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__file_put, mcp__reech-studio__log_append, mcp__reech-studio__brand_save, mcp__reech-studio__campaign_save, mcp__reech-studio__video_save, mcp__reech-studio__media_upload_links, mcp__reech-studio__media_stats, mcp__reech-studio__media_transcribe, mcp__reech-studio__media_clips, mcp__reech-studio__media_beats, mcp__reech-studio__video_stills, mcp__reech-studio__video_proxy, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page, mcp__reech-studio__media_analyze
---

Start or update a campaign brief for $ARGUMENTS using the Reech Studio connector. Follow the `gather` skill.

1. `brands_list`, then `brand_get` on the brand (ask which one if it isn't clear). Say in one line what the brand already has (open campaigns, when its news scan was).
2. Ask for what's missing in **one** round (use AskUserQuestion when options are clear): the objective and the engagement action that matters, platforms and placements, campaign name and dates, how many videos, what they bring (footage, recordings, voice talent, music, products, offers), constraints (approvers, deadline, languages, sensitive topics, rules they provide), and whether facts come from their product page or from documents they'll share.
3. Save with `campaign_save`: `brief` (`name`, `product`, `objective`, `engagement`, `platforms`, `dates {start, end}`, `status: "open"`, `source`, plus the rules and offers exactly as given), and a first `plan` (markdown: what gets made, in what order, with the checkpoints the playbook names).
4. If they mentioned assets, give them the upload page (`media_upload_page` with `folder: "<campaign>"`), then `media_stats` on footage and music.
5. Close with the campaign id and the next step (`plan` for the first video). Don't start writing scripts inside this command.
