# Markdown Crawl Skill

## Overview

The `markdown_crawl` skill enables Claude Code agents to fetch web content in markdown format using Cloudflare's "Markdown for Agents" feature. It automatically tracks usage metrics including token savings, success rates, and request history.

## Background

Cloudflare announced "Markdown for Agents" in February 2026, allowing AI agents to request web content in markdown format instead of HTML. This provides:

- **Cleaner content**: No HTML tags, JavaScript, or styling to parse
- **Token efficiency**: Markdown uses significantly fewer tokens than HTML
- **Better AI processing**: Structured content that's easier for agents to understand
- **Standardized format**: Consistent markdown output across different websites

Learn more: [Cloudflare - Markdown for Agents](https://blog.cloudflare.com/markdown-for-agents/)

## Features

### Core Functionality
- ✅ Fetch web content in markdown format
- ✅ Automatic metrics tracking
- ✅ Token savings calculation
- ✅ Success rate monitoring
- ✅ Request history
- ✅ Domain statistics
- ✅ Standalone bash script (can be used independently)
- ✅ Claude Code skill integration

### Implementation

This skill includes a standalone bash script (`markdown_crawl.sh`) that can be used in two ways:

1. **As a Claude Code skill**: `/markdown_crawl <url>`
2. **As a standalone CLI tool**: `./skills/markdown_crawl/markdown_crawl.sh fetch <url>`

### Commands

#### 1. Fetch Markdown Content
```
/markdown_crawl <url>
```
Fetches the specified URL in markdown format and displays the content.

**Example:**
```
/markdown_crawl https://blog.cloudflare.com/markdown-for-agents/
```

**Output:**
- Markdown content from the page
- Token count (if provided by server)
- Metrics updated confirmation

---

#### 2. Compare Markdown vs HTML
```
/markdown_crawl <url> --compare
```
Fetches both markdown and HTML versions to show token savings.

**Example:**
```
/markdown_crawl https://example.com --compare
```

**Output:**
- Both markdown and HTML content
- Token counts for each format
- Tokens saved
- Percentage reduction

---

#### 3. View Statistics
```
/markdown_crawl stats
```
Displays comprehensive usage statistics and metrics.

**Example:**
```
/markdown_crawl stats
```

**Output:**
```
📊 Markdown Crawl Statistics
============================

Total Requests: 42
Successful: 38
Failed: 4
Success Rate: 90.5%

Token Metrics:
- Total Markdown Tokens Fetched: 28,450
- Total HTML Tokens (from comparisons): 54,230
- Total Tokens Saved: 25,780
- Average Tokens per Request: 748
- Average Savings per Comparison: 1,289

Recent Requests (last 10):
1. https://blog.cloudflare.com/... - 725 tokens saved (48%)
2. https://docs.example.com/... - 1,250 tokens saved (52%)
...

Top Domains:
1. cloudflare.com - 15 requests
2. example.com - 8 requests
3. docs.github.com - 5 requests
```

---

#### 4. Reset Metrics
```
/markdown_crawl reset
```
Clears all collected metrics (requires confirmation).

**Example:**
```
/markdown_crawl reset
```

**Prompt:**
- Asks for confirmation before resetting
- Cannot be undone

---

## How It Works

### HTTP Headers

The skill uses the following HTTP headers:

**Request:**
```
Accept: text/markdown
User-Agent: Claude-Agent/1.0
```

**Response (when supported):**
```
Content-Type: text/markdown; charset=utf-8
X-Markdown-Tokens: 725
Content-Signal: ai-train=yes, search=yes, ai-input=yes
```

### Metrics Tracking

Metrics are stored in `skills/markdown_crawl/metrics.json`:

```json
{
  "total_requests": 42,
  "successful_markdown_requests": 38,
  "failed_requests": 4,
  "total_markdown_tokens": 28450,
  "total_html_tokens": 54230,
  "total_tokens_saved": 25780,
  "requests_history": [...]
}
```

Each request entry includes:
- URL and domain
- Timestamp
- Token counts (markdown and HTML if compared)
- Tokens saved
- Success status
- Content type received

### Token Calculation

**Markdown Tokens:**
- Extracted from `X-Markdown-Tokens` response header
- Provided by Cloudflare when available

**HTML Tokens:**
- From response headers if available
- Otherwise estimated: `character_count / 4`

**Savings:**
- `tokens_saved = html_tokens - markdown_tokens`
- `percentage_saved = (tokens_saved / html_tokens) * 100`

## Requirements

- The target website must support Cloudflare's Markdown for Agents feature
- Available on Cloudflare Pro, Business, Enterprise plans
- The skill works with any URL but markdown conversion only works on supported sites

## Use Cases

### 1. Research & Documentation
Fetch documentation, blog posts, and articles in a clean markdown format for analysis.

### 2. Content Analysis
Compare markdown vs HTML to understand token efficiency gains.

### 3. Web Scraping
Extract structured content from websites without HTML overhead.

### 4. Performance Tracking
Monitor how much token savings you achieve over time.

## Tips

1. **Use with supported sites**: Check if the site is behind Cloudflare for best results
2. **Compare mode**: Use `--compare` occasionally to measure actual savings
3. **Check stats**: Run `/markdown_crawl stats` to see your token efficiency
4. **Domain tracking**: Stats show which domains you fetch from most often

## Limitations

- Only works on websites that support Markdown for Agents
- Non-supported sites will return HTML (still tracked in metrics)
- Request history is limited to last 50 requests to prevent file bloat
- Token estimates for HTML may not be exact (uses approximation)

## Technical Details

### File Structure
```
skills/markdown_crawl/
├── skill.json          # Skill metadata
├── prompt.md           # Agent instructions
├── markdown_crawl.sh   # Standalone bash script (executable)
├── README.md           # This file
└── metrics.json        # Metrics storage (auto-created)
```

### Dependencies
- `curl` - For HTTP requests
- `jq` - For JSON parsing (required)

### Metrics File Location
`skills/markdown_crawl/metrics.json`

The file is automatically created on first use and persists across sessions.

## Examples

### Example 1: Quick Fetch
**As a skill:**
```bash
/markdown_crawl https://blog.example.com/article
```

**Standalone:**
```bash
./skills/markdown_crawl/markdown_crawl.sh fetch https://blog.example.com/article
```

### Example 2: Measure Savings
**As a skill:**
```bash
/markdown_crawl https://docs.example.com/guide --compare
```

**Standalone:**
```bash
./skills/markdown_crawl/markdown_crawl.sh compare https://docs.example.com/guide
```

### Example 3: Check Your Stats
**As a skill:**
```bash
/markdown_crawl stats
```

**Standalone:**
```bash
./skills/markdown_crawl/markdown_crawl.sh stats
```

### Example 4: Reset Everything
**As a skill:**
```bash
/markdown_crawl reset
```

**Standalone:**
```bash
./skills/markdown_crawl/markdown_crawl.sh reset
```

## Troubleshooting

**Problem:** Site doesn't return markdown
- **Solution**: Not all sites support this feature. Check if the site uses Cloudflare.

**Problem:** Token count not showing
- **Solution**: Server may not provide `X-Markdown-Tokens` header. This is optional.

**Problem:** Metrics file corrupted
- **Solution**: The skill will auto-recreate it on next request.

## References

- [Cloudflare: Markdown for Agents](https://blog.cloudflare.com/markdown-for-agents/)
- [Cloudflare Docs: Markdown for Agents](https://developers.cloudflare.com/fundamentals/reference/markdown-for-agents/)
- [Changelog: Introducing Markdown for Agents](https://developers.cloudflare.com/changelog/2026-02-12-markdown-for-agents/)

## Version History

- **1.0.0** (2026-02-14): Initial release
  - Fetch markdown content
  - Compare mode with token savings
  - Comprehensive metrics tracking
  - Stats display
  - Reset functionality
