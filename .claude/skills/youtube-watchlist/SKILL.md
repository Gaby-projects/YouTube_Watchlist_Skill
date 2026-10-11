---
name: youtube-watchlist
description: Lists the latest videos of a saved YouTube channel or playlist from its public RSS feed, flags uploads from the last 7 days, and translates the titles into English. Use when the user asks for new videos from one of the channels or playlists in the channels/ folder (food, travel, comedy, ...).
argument-hint: <channel file name, e.g. food>
---

# YouTube Watchlist

Checks a YouTube channel or playlist for new uploads and lists its 5 most recent videos, using the public RSS feed (no API key required).

## Input

The user names the channel or playlist to check: `$ARGUMENTS`

Each channel or playlist has its own file in the `channels/` folder next to this file (for example `channels/food.md`). Read the matching file and use the RSS feed URL in it. If no name is given, or no file matches, list the available files in `channels/` and ask which one to check.

## Steps

### 1. Fetch the feed

Retrieve the RSS/XML content from the `RSS feed` URL in the channel file. A file can describe either:

- **a channel** (`## Channel info`, feed URL with `channel_id=`): all uploads of the channel, or
- **a playlist** (`## Playlist info`, feed URL with `playlist_id=`): only the videos in that playlist.

Both feeds have the same format, so the next steps are the same.

### 2. Parse the entries

Each `<entry>` in the feed contains:

- `<title>`: video title
- `<yt:videoId>`: unique video ID
- `<published>`: publish date and time
- `<link rel="alternate" href="...">`: video URL

The feed is ordered newest first and only holds the last 15 uploads.

### 3. Skip Shorts

Skip any entry whose link contains `/shorts/`. Channels often post many Shorts that only promote their latest video, and they would push the real videos out of the list. Keep the first 5 remaining entries. If fewer than 5 are left, show the ones there are.

### 4. Check recency

For each video, compare its `<published>` date with the current date:

- **7 days or less**: mark it as new.
- **More than 7 days**: do not mark it.

This is calculated on every run, so the skill does not need to store anything between runs.

### 5. Translate the titles

If a title is not in English, translate it into English. Show only the English title, not the original.

### 6. Report to the user

Start with a link to the most recent video, whether or not it is new. Then list the videos as Markdown, with 🆕 in front of each video from the last 7 days:

```markdown
# Latest videos: <channel or playlist name>

Most recent: [Translated title 1](link)

- 🆕 **[Translated title 1](link)**: published 2 days ago
- **[Translated title 2](link)**: published 9 days ago
- **[Translated title 3](link)**: published 3 weeks ago
- **[Translated title 4](link)**: published 1 month ago
- **[Translated title 5](link)**: published 2 months ago
```

If none of the videos is from the last 7 days, say "No new videos in the last 7 days." at the top, before the list.

Only output the list: no extra commentary, summaries or video descriptions.

## Notes

- No YouTube API key is needed; the RSS feeds are public.
- Summarizing a video's content is out of scope for this skill.
