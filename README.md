# Claude Update Tracker

Real-time tracking of the latest Claude AI and Claude Code updates so you don't have to.

Built around the iconic "babe wake up, new update just dropped" meme cat.

## Live

https://shinyyim.github.io/claude-update-tracker/

## Features

- Spinning tinfoil hat cat hero section
- Update cards with What's New / How to Update / How to Use / Community sections
- Swipe to dismiss cards you've already read
- Per-section checkboxes to track what you've reviewed
- Filter by category (Model / CLI / Feature)
- Dark theme, responsive design

## Data Source

Updates sourced from [@bcherny](https://x.com/bcherny) (Boris Cherny, Head of Claude Code at Anthropic).

## Auto-Collection (Planned)

### How it works

GitHub Actions cron job runs daily to automatically collect and publish new Claude updates.

```
Cron (daily 9am) → Check sources → New update found? → Claude API summarizes → Card added to HTML → Auto deploy
```

### Pipeline

1. **Trigger**: GitHub Actions cron schedule (`0 9 * * *` = every day at 9am UTC)
2. **Source Check**: Scrape / fetch from multiple sources
   - Anthropic blog & changelog (`docs.anthropic.com`, `code.claude.com/docs/en/changelog`)
   - @bcherny X posts (via RSS bridge or API)
   - Claude Code GitHub releases (`github.com/anthropics/claude-code/releases`)
3. **Detect New**: Compare against last known update (stored in `data/updates.json`)
4. **Summarize**: Send raw content to Claude API to generate structured card data
   - What's New
   - How to Install/Update
   - How to Use
   - Community reactions
5. **Generate**: Inject new card into `index.html` (or switch to JSON data + JS rendering)
6. **Commit & Deploy**: Auto-commit changes and trigger GitHub Pages deploy

### Architecture

```
.github/
  workflows/
    collect.yml          # Cron workflow (daily)
    deploy.yml           # GitHub Pages deploy
scripts/
  collect.py             # Main collection script
  sources/
    changelog.py          # Anthropic changelog scraper
    twitter.py            # @bcherny post fetcher
    github_releases.py    # Claude Code releases
  summarize.py           # Claude API card generation
  inject.py              # HTML card injection
data/
  updates.json           # Update history (source of truth)
index.html               # Frontend
cat.png                  # The cat
```

### Required Secrets

| Secret | Purpose |
|--------|---------|
| `ANTHROPIC_API_KEY` | Claude API for summarization |
| `TWITTER_BEARER_TOKEN` | X API for @bcherny posts (optional) |

### Cost

- Claude API: ~$0.01-0.05 per update (Haiku for summarization)
- GitHub Actions: Free (public repo)
- X API: Free tier or Nitter RSS fallback

## Tech

Single HTML file. No build step. No dependencies.

## Run

Open `index.html` in a browser. That's it.

## Deploy

Hosted on GitHub Pages. Auto-deploys on push to `main`.

## License

MIT
