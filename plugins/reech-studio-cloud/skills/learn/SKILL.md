---
name: learn
description: Phase 4 of Reech Video Studio. Log what was posted, bring in results from any source (analytics exports, screenshots, dashboards, notes), link them to each video's creative attributes, and update the brand's rules and notes. Use when the user shares results, says a video was posted, or asks what's working for a brand.
allowed-tools: mcp__reech-studio__me, mcp__reech-studio__brands_list, mcp__reech-studio__brand_get, mcp__reech-studio__campaign_get, mcp__reech-studio__video_get, mcp__reech-studio__file_get, mcp__reech-studio__library_search, mcp__reech-studio__activity, mcp__reech-studio__posts_list, mcp__reech-studio__locks_list, mcp__reech-studio__media_list, mcp__reech-studio__media_links, mcp__reech-studio__job_get, mcp__reech-studio__job_wait, mcp__reech-studio__jobs_list, mcp__reech-studio__lock_acquire, mcp__reech-studio__lock_release, mcp__reech-studio__log_append, mcp__reech-studio__file_put, mcp__reech-studio__brand_save, mcp__reech-studio__media_view, mcp__reech-studio__media_upload_page
---

# Learn: results change the next plan

## 1. Posting

When a video is posted, `log_append` one line to `brands/<slug>/posts.jsonl`:
`{"type":"posted","post":"<video-id>","date","platform","url","caption","posted_at"}`.
Then follow up for results about 7 and 30 days after posting: mention it at the start of a session for that brand when they're due (`posts_list` shows the dates).

## 2. Results, from any source

Accept whatever the person has: a CSV or XLSX export, a screenshot of Insights, a Triple Whale or Meta Ads query (if those tools are connected), or a sentence ("that one did 3× our usual saves"). Read it, and `log_append` one line per video per reading:
`{"type":"result","post":"<video-id>","platform","window":"7d|30d|…","source","views","avg_watch_s","completion","saves","sends","shares","comments","likes","follows","clicks","profile_visits","drop_s","spend","note"}`.
Leave out anything you don't know. Never estimate a metric. Lines are append-only: a correction is a new line with `"corrects": true`.

## 3. Link results to creative attributes

Each post line carries its attributes: device, hook, format, style, length, voice source, platform (`posts_list`). Compare them against the brand's own baseline (the median of its posts on the same platform), not against other brands. Look for:
- which hooks hold the first 3 seconds (watch time, completion), by hook catalog id;
- which devices earn the engagement action the campaign wanted (saves vs sends vs comments);
- what length and pace work for this audience.

**Retention finds the weak block; it isn't the whole verdict.** Read where viewers leave and compare it with the video's `viewer_walk` from planning (`video_get`): the block where they left is the block to fix next time. Store the drop-off when it's known (`"drop_s"`). But judge a video on a balanced scorecard: the action the campaign wanted, brand signals (follows, profile visits, branded searches) and retention together. Never optimise on retention alone.

**Reach vs the right people.** A video that went far isn't automatically good for the brand. Check whether its reach came with signs of the right audience and whether it built what `script.json → builds` said. Report the two separately: "reached a lot" and "reached the right people".

**Validate the testing rules.** For each video with results, read its `audit.json` (`file_get`): the `research-testing` rules it passed or failed, and `script.json → test` if it carried an A/B. Compare videos that followed a rule with those that broke it, on the brand's baseline. Write what you see in the brand's `history.json → tests` (`{rule, followed: [ids], broke: [ids], metric, direction, posts, date}`). Patterns that hold across brands belong in the shared rulebook: tell the team (the library is theirs to change).

Be honest about sample size: with fewer than about 5 posts per platform, report observations, not rules.

## 4. Update the brand

- **The style's ficha** (`brands/<slug>/style/<name>/ficha.md`): every correction the person made on a real video becomes a rule in a dated section `## Learned in <video> (<date>)` at the end of the ficha ("text never on the board while it draws", "the wall is warm, the paper too"); a rule that holds for every style of the brand goes to section 0 of `style/catalog.md` instead. A style is `draft` until about three real videos have their corrections written; then set `status: "approved"` in its `look.json`. A rule that contradicts an earlier one replaces it and says why.

- `history.json` (`brand_save` with `history`, or `file_put`): add learned rules with evidence and date, retire phrases and insights that are worn out, and keep the rotation rule.
- `system/system.json`: when a brand has about 5 posts with results and the team agrees, `autonomy` can move to `"auto"`.
- Brand-specific numbers stay in that brand's folder; the shared library only gets the pattern, through the team.

## 5. Report

Summarize for the person in a few lines: what worked, what didn't, what changes for the next video, and which experiment would teach the most next (for example two hooks on the same video).
