# Claude Update Tracker - Project Guide

## Overview

Claude update tracking dashboard. Monitors @bcherny (Boris Cherny, Head of Claude Code) posts and Anthropic changelog to surface new updates with structured summaries.

Live: https://shinyyim.github.io/claude-update-tracker/

## Stack

- Single `index.html` (no framework, no build step)
- CSS: inline, dark theme, IBM Plex Mono + Space Grotesk
- JS: inline, vanilla
- Deploy: GitHub Pages via Actions
- Data: hardcoded in HTML (planned: `data/updates.json`)

## Design Decisions

- Hero: meme cat spins via CSS `@keyframes`, slows on hover
- Cards: swipe-to-dismiss (session only, resets on refresh)
- Per-section checkboxes for granular read tracking
- Filter pills: All / Model / CLI / Feature
- All black background (`#000`), orange accent (`#ff6b35`)

## Card Structure

Each update card has 4 sections:
1. What's New - key changes
2. How to Update - install/update commands
3. How to Use - practical usage tips
4. Community - how others are using it

## Auto-Collection Pipeline (Planned)

### Flow

```
Cron (daily 9am UTC)
  → Check sources for new content
  → Compare against data/updates.json
  → If new: Claude API (Haiku) generates card data
  → Inject into HTML or render from JSON
  → Auto-commit & deploy
```

### Sources

| Source | Method | Priority |
|--------|--------|----------|
| Anthropic changelog | Scrape `code.claude.com/docs/en/changelog` | 1 |
| Claude Code GitHub releases | GitHub API `anthropics/claude-code/releases` | 2 |
| @bcherny X posts | X API or RSS bridge (Nitter fallback) | 3 |
| Anthropic blog | Scrape `anthropic.com/news` | 4 |

### File Structure (Target)

```
.github/workflows/
  collect.yml              # Daily cron workflow
  deploy.yml               # GitHub Pages deploy
scripts/
  collect.py               # Main orchestrator
  sources/
    changelog.py           # Anthropic changelog scraper
    twitter.py             # @bcherny post fetcher
    github_releases.py     # Claude Code releases
  summarize.py             # Claude API card generation
  inject.py                # HTML card injection
data/
  updates.json             # Update history (source of truth)
index.html
cat.png
```

### Required Secrets

- `ANTHROPIC_API_KEY` - Claude API for summarization
- `TWITTER_BEARER_TOKEN` - X API for @bcherny posts (optional)

### Estimated Cost

- Claude API: ~$0.01-0.05 per update (Haiku)
- GitHub Actions: free (public repo)
- X API: free tier or Nitter RSS fallback

## Conventions

- All files in English
- Commit messages: short, descriptive
- No external JS/CSS dependencies
- Keep it as a single HTML file as long as possible
