# Trainer Website project

## What this is

An internal staff portal for F45 Crows Nest trainers. Public URL (no auth gate, all content is non-sensitive). The site collects coaching scripts, cleaning procedures, sales playbooks, and pricing references in one place so a new hire can self-serve.

**Live at:** https://zexinjin-gif.github.io/trainer-hub/

## Stack

- **Plain HTML + CSS + vanilla JS** (no framework, no build step)
- **GitHub Pages** for hosting (free, public repo `zexinjin-gif/trainer-hub`)
- **YouTube Shorts** for coaching videos (embedded by ID, thumbnails pulled from `i.ytimg.com`)
- Auto-deploys on every push to `main`

## File layout

- `index.html` is the homepage with three tiles: Coaching, Operations, Sales
- Each section has its own root-level page (`coaching.html`, `operations.html`, `sales.html`)
- `scripts/` holds long-form text pages (coaching scripts, philosophy)
- `images/put-away/` holds web-ready JPGs for the Put Things Away gallery
- `videos/` holds local mp4 fallbacks for videos not yet on YouTube
- `files/` holds downloadable PDFs
- `style.css` is the single shared stylesheet

## Conventions

- Trainer's owner is **Jino** (Studio Manager). Owners of the gym are **Armand and Brigid**.
- Casual, manager-to-team tone in any user-facing copy.
- No mention of brand competitors or "72 Training" anywhere.
- Mobile-first design. Trainers mostly view this on phones between classes.
- Dark theme with yellow (`#ffb400`) accent for highlights and badges.
- No em dashes, en dashes, or dashes as punctuation anywhere (use commas, colons, parentheses).

## Media workflow (important)

**iPhone HEIC photos must be converted to JPG before they hit the website.** When the user drops `.HEIC` files into the project (typically in `images/`), do this without being asked:

1. Convert to JPG with `sips -Z 1600 -s format jpeg "INPUT.HEIC" --out "INPUT.jpg"` (resizes to 1600px max width, keeps aspect ratio)
2. Delete the original `.HEIC` files after successful conversion
3. The JPG keeps the same base filename so the user can match them up
4. `.HEIC` and `.heic` are in `.gitignore` already, so iPhone originals never get committed
5. Don't `git add .` blindly when new files are sitting around. Stage only the files you intend to commit, especially in `images/`

**Why:** HEIC is an Apple-only format that doesn't display in most browsers. Raw iPhone photos are also large (3-5MB each) and would bloat the repo and slow page loads. Converted JPGs are typically 5x smaller.

## Connected routine (weekly changelog)

There is a weekly **remote Claude routine** that generates the Tuesday trainer-group WhatsApp message. The whole point of this routine is to send a **weekly changelog**: what's new on the Trainer Hub since last Tuesday, so trainers stay engaged with new content.

- **ID:** `trig_01WQGaD88yz7K5cv7F9xkBXn`
- **Name:** "Tuesday Trainer Video + Website Nudge"
- **Schedule:** Mondays at 21:00 UTC = Tuesdays 7am Sydney (creates a Calendar event for 11am that same day, with the WhatsApp draft inside)
- **The routine fires from Anthropic's cloud, not this local session.**

**This Claude's job (the Trainer Website project Claude):**

Once a week, before Tuesday morning Sydney time, update the routine prompt with a list of changes from the past 7 days. The routine's message generator uses this list as the actual content of the WhatsApp message. Without an updated list, the routine falls back on stale info.

**Weekly update workflow:**

1. Run `git log --since="<last Tuesday's date>" --pretty=format:'%ad | %s' --date=short` to see the week's commits.
2. Group commits into 2-5 user-facing bullets (skip purely cosmetic commits, typo fixes, scaffolding). Phrase from a trainer's perspective ("Cleaning Walkthroughs is live, 21 photos with tips" not "added cleaning-walkthroughs.html"). Ignore commits that don't touch trainer-facing content (CLAUDE.md, gitignore, internal refactors).
3. Replace the **`## THIS WEEK'S UPDATES`** block in the routine prompt with the new bullets. Use `RemoteTrigger` with `action: get` first, then `action: update` with the full job_config.
4. Do this any time during the week, but ideally on Sunday or Monday so it's fresh when the routine fires Tuesday morning.

**If nothing meaningful shipped that week:** put a single bullet like "no big changes this week, just keep the hub bookmarked and watch the videos if you haven't" so the routine still produces a message instead of going stale.

**Loading RemoteTrigger:** `ToolSearch select:RemoteTrigger` (the tool is deferred and not loaded by default).

## Update workflow with Claude

When changes are made in a Claude session, the user expects Claude to also push the changes:
```
git add <specific files>
git commit -m "..."
git push
```
The user does not run git commands. Just say "push it live" or similar to trigger.

**Never `git add .`** unless you've verified what's in the working tree. The user often drops raw work files (HEICs, source videos, drafts) in subfolders that shouldn't be committed.
