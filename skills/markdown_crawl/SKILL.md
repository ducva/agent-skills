---
name: markdown_crawl
description: Fetch web content in markdown format using Cloudflare's Markdown for Agents feature. Use when fetching web pages, documentation sites, or converting HTML to markdown. Tracks metrics like token savings and usage statistics.
disable-model-invocation: false
metadata:
  version: "1.0.0"
  author: ""
  tags: ["web", "markdown", "fetch", "cloudflare", "metrics", "crawl"]
  license: ""
compatibility: "Requires curl and jq"
---

# Markdown Crawl

## When to Use

- Use this skill when you need to fetch web content in markdown format
- When comparing markdown vs HTML token efficiency
- When tracking web fetch metrics and token savings
- For research, documentation fetching, or content analysis
- When working with Cloudflare-backed websites that support Markdown for Agents

## Background

Cloudflare announced "Markdown for Agents" in February 2026, allowing AI agents to request web content in markdown format instead of HTML. This provides:

- **Cleaner content**: No HTML tags, JavaScript, or styling to parse
- **Token efficiency**: Markdown uses significantly fewer tokens than HTML
- **Better AI processing**: Structured content that's easier for agents to understand
- **Standardized format**: Consistent markdown output across different websites

Learn more: [Cloudflare - Markdown for Agents](https://blog.cloudflare.com/markdown-for-agents/)

## Instructions

This skill uses a standalone bash script located at `skills/markdown_crawl/scripts/markdown_crawl.sh` that handles all functionality.

### Command Modes

**1. Fetch Markdown (`/markdown_crawl <url>`)**
- Fetch content from the specified URL using markdown format
- Track metrics for each request

**2. Compare Mode (`/markdown_crawl <url> --compare`)**
- Fetch both markdown AND HTML versions
- Calculate and display token savings
- Track detailed comparison metrics

**3. Stats Mode (`/markdown_crawl stats`)**
- Display comprehensive usage statistics
- Show token savings and success rates
- List recent requests

**4. Reset Mode (`/markdown_crawl reset`)**
- Clear all metrics data
- Requires user confirmation

### Implementation

The skill uses a standalone bash script that handles all operations. Simply call the script with the appropriate command:

#### Fetch Mode

When the user provides a URL (e.g., `/markdown_crawl https://example.com`):

```bash
skills/markdown_crawl/scripts/markdown_crawl.sh fetch <url>
```

This will:
- Fetch the URL in markdown format
- Display the content with metadata
- Automatically track metrics

**Fallback Strategy:**

If the website doesn't support the `Accept: text/markdown` header (indicated by a warning message: "Content not in markdown format"), use the WebFetch MCP tool as a fallback:

1. Check the script output for the warning: `⚠ Content not in markdown format`
2. If present, immediately use the WebFetch tool:
   ```
   WebFetch(
     url: <same_url>,
     prompt: "Extract and return the full content of this page in markdown format. Include all text, headings, links, and structured content."
   )
   ```
3. Display the WebFetch result to the user as the markdown content
4. Note in your response that native markdown was not supported and WebFetch was used as fallback
5. The metrics from the bash script will still be tracked (showing the failed native markdown attempt)

**Important:** This fallback ensures content is always available in markdown format, even for sites that don't natively support the markdown header. WebFetch may use slightly more tokens than native markdown support, but ensures compatibility with all websites.

#### Compare Mode

When the user provides a URL with `--compare` flag (e.g., `/markdown_crawl https://example.com --compare`):

```bash
skills/markdown_crawl/scripts/markdown_crawl.sh compare <url>
```

This will:
- Fetch both markdown and HTML versions
- Calculate token savings
- Display a comparison with statistics
- Track detailed metrics

#### Stats Mode

When the user requests stats (e.g., `/markdown_crawl stats`):

```bash
skills/markdown_crawl/scripts/markdown_crawl.sh stats
```

This will display comprehensive usage statistics including:
- Total requests and success rate
- Token metrics (markdown, HTML, savings)
- Recent request history
- Top domains by request count

#### Reset Mode

When the user requests reset (e.g., `/markdown_crawl reset`):

```bash
skills/markdown_crawl/scripts/markdown_crawl.sh reset
```

This will:
- Prompt for confirmation (the script handles this interactively)
- Reset all metrics if confirmed
- Display confirmation message

## Script Features

The bash script (`markdown_crawl.sh`) automatically handles:

✅ **URL Validation**: Adds https:// if missing, validates format
✅ **HTTP Requests**: Uses proper headers (`Accept: text/markdown`)
✅ **Response Parsing**: Extracts headers and body, handles `x-markdown-tokens`
✅ **Token Calculation**: Uses header values or estimates (char_count / 4)
✅ **Metrics Tracking**: Maintains persistent JSON metrics file
✅ **Error Handling**: Clear error messages, graceful failure handling
✅ **Colored Output**: User-friendly colored terminal output
✅ **History Management**: Keeps last 50 requests to prevent file bloat

## HTTP Headers

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

## Dependencies

The script requires:
- `curl` - For HTTP requests
- `jq` - For JSON parsing and manipulation

If `jq` is not installed, the script will display an error message.

## Error Handling

The script handles errors gracefully:
- Invalid URLs: Clear error message
- Network failures: Curl error reporting
- Missing dependencies: Helpful installation guidance
- Malformed metrics: Auto-recreates metrics file

## Examples

### Example 1: Fetch Documentation

```bash
/markdown_crawl https://blog.cloudflare.com/markdown-for-agents/
```

**Output:**
- Markdown content from the page
- Token count: 725 tokens
- Metrics updated confirmation

### Example 2: Compare Token Efficiency

```bash
/markdown_crawl https://docs.example.com/guide --compare
```

**Output:**
```
📊 Comparison Results
===================
Markdown Tokens: 450
HTML Tokens: 1,250
Tokens Saved: 800 (64% reduction)
```

### Example 3: View Usage Statistics

```bash
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
```

### Example 4: Reset Metrics

```bash
/markdown_crawl reset
```

The script will prompt for confirmation before clearing all data.

## Best Practices

1. **Use with supported sites**: Check if the site is behind Cloudflare for best results
2. **Compare mode**: Use `--compare` occasionally to measure actual savings
3. **Check stats**: Run `/markdown-crawl stats` to see your token efficiency
4. **Domain tracking**: Stats show which domains you fetch from most often
5. **Fallback awareness**: Be ready to use WebFetch for sites without native support

## Limitations

- Only works on websites that support Markdown for Agents
- Non-supported sites will return HTML (still tracked in metrics)
- Request history is limited to last 50 requests to prevent file bloat
- Token estimates for HTML may not be exact (uses approximation)

## Metrics Storage

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

## Troubleshooting

**Problem:** Site doesn't return markdown
- **Solution**: Not all sites support this feature. Check if the site uses Cloudflare. The skill will automatically fall back to WebFetch.

**Problem:** Token count not showing
- **Solution**: Server may not provide `X-Markdown-Tokens` header. This is optional.

**Problem:** Metrics file corrupted
- **Solution**: The skill will auto-recreate it on next request.

**Problem:** `jq` not found
- **Solution**: Install jq: `sudo apt install jq` (Ubuntu/Debian) or `brew install jq` (macOS)

## References

- [Cloudflare: Markdown for Agents](https://blog.cloudflare.com/markdown-for-agents/)
- [Cloudflare Docs: Markdown for Agents](https://developers.cloudflare.com/fundamentals/reference/markdown-for-agents/)
- [Changelog: Introducing Markdown for Agents](https://developers.cloudflare.com/changelog/2026-02-12-markdown-for-agents/)
