---
description: Record an approval (a script or a cut) for a video and move to the next step
argument-hint: [brand/campaign/video] [script|cut] [notes]
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__file_put, mcp__reech-studio__log_append, mcp__reech-studio__brand_save, mcp__reech-studio__campaign_save, mcp__reech-studio__video_save, mcp__reech-studio__media_upload_links, mcp__reech-studio__media_stats, mcp__reech-studio__media_transcribe, mcp__reech-studio__media_beats, mcp__reech-studio__video_stills, mcp__reech-studio__video_proxy, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page
---

Record the person's approval for $ARGUMENTS and continue.

1. Work out which video (`campaign_get` lists them with their state) and what is being approved: the **script** (a `script.json` that hasn't been produced yet) or the **cut** (a config that has stills or a proxy the person looked at). Ask if it isn't clear.
2. Apply their notes first: edits to the script (`video_save` with `script`, passing the etag from `video_get`) or to the edit (`config`), then show the change (a line for the script; new `video_stills` + `job_wait` for the cut).
3. Record the approval in the video's `review.md` (`video_save` with `review` and `etags.review` from `video_get`): `approved: script|cut`, `by: <the person>`, `date`, `notes`. For a cut, also `render: pending`.
4. Next step: an approved **script** goes to `make` (audio, footage, edit, stills); an approved **cut** goes to `/reech-studio-cloud:deliver` (render → QA → audit → review → deliver). Say which, in one line, and start it unless they said to stop.
