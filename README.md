# YouTube Watchlist: a Claude Skill for tracking chosen channels

A Claude Code skill that checks favorite YouTube channels and playlists and lists their latest videos, using YouTube's public RSS feed (no API key required).

## How it works

[`SKILL.md`](.claude/skills/youtube-watchlist/SKILL.md) holds the instructions. When it runs, Claude:

1. Reads the channel or playlist file you named and fetches its RSS feed.
2. Skips Shorts and keeps the 5 most recent videos (title, ID, publish date, link).
3. Flags videos published in the last 7 days with 🆕.
4. Translates titles that are not in English.
5. Returns a Markdown list, with a link to the most recent video at the top.

It stores no state: whether a video is new is calculated against the current date on every run.

## Example

`/youtube-watchlist food`, run on 12 Oct 2026:

```markdown
# Latest videos: Chef Giacomo

Most recent: [Is the most famous turrón the best? | Turrón showdown](https://www.youtube.com/watch?v=oGR-C4jlpgc)

- 🆕 **[Is the most famous turrón the best? | Turrón showdown](https://www.youtube.com/watch?v=oGR-C4jlpgc)**: published 2 days ago
- 🆕 **[The Fridays ribs you always wanted to make: the real recipe at home](https://www.youtube.com/watch?v=Hmk9TY3LdwQ)**: published 4 days ago
- 🆕 **[My grandfather received the city medal: the most emotional day of my life in Tacna | Vlog](https://www.youtube.com/watch?v=aXhPoMPiqEA)**: published 7 days ago
- **[We went into the biggest market in Tacna and ended up eating this](https://www.youtube.com/watch?v=8Vid4Ipz2_c)**: published 9 days ago
```

Only 4 videos appear here: the feed holds the last 15 uploads, and the other 11 were Shorts.

## Usage

**Claude Code (terminal or VS Code)**

1. Clone the repository and open Claude Code in its folder. Claude Code finds the skill in `.claude/skills/` automatically.
2. Run the skill with the name of a channel file:
   ```
   /youtube-watchlist food
   /youtube-watchlist travel
   /youtube-watchlist comedy
   ```
   You can also just ask, for example *"any new videos from the travel channel?"*, and Claude uses the skill on its own.

To use it in every project, copy the `youtube-watchlist` folder to `~/.claude/skills/`.

**claude.ai (web)**

Zip the `youtube-watchlist` folder and upload it as a custom skill (Settings → Capabilities → Skills), then ask Claude for the latest videos of a channel.

## Files

```
.claude/skills/youtube-watchlist/
├── SKILL.md            # Skill instructions: read the feed, skip Shorts, format the output
└── channels/
    ├── food.md         # Chef Giacomo: cooking
    ├── travel.md       # BarbieBac La Azafata: travel
    └── comedy.md       # "ANTES DE PARTIR" playlist by No Somos TV: comedy
```

## Adding a channel or playlist

Create a new `.md` file in `channels/`. The file name is what you pass to the skill (`channels/music.md` → `/youtube-watchlist music`).

**Channel**

```markdown
## Channel info

- Name: <channel name>
- Topic: <topic>
- RSS feed: https://www.youtube.com/feeds/videos.xml?channel_id=<channel id>
```

**Playlist**

```markdown
## Playlist info

- Playlist name: <playlist name>
- Topic: <topic>
- Channel: <channel name>
- RSS feed: https://www.youtube.com/feeds/videos.xml?playlist_id=<playlist id>
```

- **Channel ID** (starts with `UC`): open the channel page, view the page source and search for `"channelId"`.
- **Playlist ID** (starts with `PL`): it is the part after `list=` in the playlist URL.
