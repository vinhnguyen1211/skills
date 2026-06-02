---
name: ketch
description: Use the `ketch` CLI for all web search, scraping, code search, and library doc lookups. Trigger whenever the user says "use ketch", asks to search the web, scrape a URL, look up OSS code examples, or fetch library docs — even if they don't mention ketch by name.
license: MIT
---

# ketch

Fast, stateless CLI for web search, scraping, code search, and library docs. Always prefer `--json` for structured output.

## Commands

```bash
# Web search
ketch search "query"                        # titles, URLs, snippets
ketch search "query" --scrape               # + full page content
ketch search "query" --limit 10 --json

# Scrape URLs → clean markdown
ketch scrape https://example.com
ketch scrape https://a.com https://b.com    # concurrent batch

# Crawl a site
ketch crawl https://docs.example.com --depth 2
ketch crawl https://example.com/sitemap.xml --sitemap --background
ketch crawl status                          # list crawls
ketch crawl status <id>                     # check specific crawl

# Code search (OSS)
ketch code "query" --lang go                # Sourcegraph (default)
ketch code "query" --lang go -b github      # GitHub Code Search

# Library docs (Context7)
ketch docs "query"                          # auto-resolve library
ketch docs "query" --library /org/repo      # direct lookup
ketch docs --resolve "glamour"              # list matching library IDs

# Config & cache
ketch config                                # show effective config as JSON
ketch config set backend searxng
ketch cache                                 # cache stats
```

## Key flags

| Flag | Description |
|------|-------------|
| `--json` | Structured JSON output |
| `--scrape` | Fetch full content from search results |
| `--limit, -l` | Max results (default 5) |
| `--lang` | Language filter for code search |
| `-b` | Backend: `brave`/`ddg`/`searxng` (search), `sourcegraph`/`github` (code) |
| `--depth` | BFS crawl depth (default 3) |
| `--background` | Run crawl in background, returns crawl ID |
| `--no-cache` | Bypass page cache |

## Local config

| Feature | Backend | Endpoint |
|---------|---------|----------|
| Search | SearXNG | http://homedev:8080 |
| Code search | Sourcegraph | https://sourcegraph.com |
| Docs search | Context7 | — |

## Notes

- JS-rendered pages are detected automatically and re-fetched with headless Chrome if configured.
- Crawled pages are cached — use `--no-cache` to force re-fetch.

## Output

Inline in chat. No file needed.
