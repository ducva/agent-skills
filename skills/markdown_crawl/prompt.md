# Markdown Crawl

## Purpose

Fetch web content in markdown format using Cloudflare's "Markdown for Agents" feature. This skill automatically tracks usage metrics and token savings.

## Instructions

You are a specialized agent for fetching web content in markdown format and tracking usage metrics.

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

### Implementation Steps

#### For Fetch Mode:

1. **Validate the URL**
   - Ensure it's a valid HTTP/HTTPS URL
   - Add https:// if protocol is missing

2. **Make the HTTP Request**
   - Use curl with the following command:
   ```bash
   curl -L -H "Accept: text/markdown" -H "User-Agent: Claude-Agent/1.0" -i "<url>"
   ```
   - `-L` follows redirects
   - `-H "Accept: text/markdown"` requests markdown format
   - `-i` includes headers in output

3. **Parse the Response**
   - Extract response headers (first section before blank line)
   - Extract response body (content after headers)
   - Look for these key headers:
     - `content-type`: Should contain `text/markdown` if successful
     - `x-markdown-tokens`: Token count estimate
     - `content-signal`: AI usage permissions

4. **Display the Content**
   - Show the markdown content to the user
   - Display token count if `x-markdown-tokens` header is present
   - Note the content type received

5. **Track Metrics**
   - Load existing metrics from `skills/markdown_crawl/metrics.json`
   - If file doesn't exist, create it with initial structure
   - Update metrics with this request's data
   - Save updated metrics back to the file

#### For Compare Mode (--compare flag):

1. **Fetch Markdown Version**
   - Follow the same steps as fetch mode
   - Store response and token count

2. **Fetch HTML Version**
   - Use curl with `Accept: text/html`:
   ```bash
   curl -L -H "Accept: text/html" -H "User-Agent: Claude-Agent/1.0" -i "<url>"
   ```
   - Store response

3. **Calculate Token Savings**
   - Get markdown tokens from `x-markdown-tokens` header
   - Estimate HTML tokens:
     - If HTML has `x-tokens` or similar header, use it
     - Otherwise, estimate: `token_count ≈ character_count / 4`
   - Calculate savings: `saved = html_tokens - markdown_tokens`
   - Calculate percentage: `percentage = (saved / html_tokens) * 100`

4. **Display Comparison**
   - Show both content types
   - Display token counts for each
   - Highlight the savings
   - Show percentage reduction

5. **Track Detailed Metrics**
   - Include both markdown and HTML token counts
   - Store the savings amount

#### For Stats Mode:

1. **Load Metrics**
   - Read from `skills/markdown_crawl/metrics.json`
   - If file doesn't exist, show "No metrics collected yet"

2. **Calculate Statistics**
   - Success rate: `(successful_requests / total_requests) * 100`
   - Average markdown tokens: `total_markdown_tokens / successful_requests`
   - Average savings: `total_tokens_saved / comparison_count`
   - Top domains: Count domain frequency from request history

3. **Display Formatted Stats**
   ```
   📊 Markdown Crawl Statistics
   ============================

   Total Requests: {total_requests}
   Successful: {successful_markdown_requests}
   Failed: {failed_requests}
   Success Rate: {success_rate}%

   Token Metrics:
   - Total Markdown Tokens Fetched: {total_markdown_tokens}
   - Total HTML Tokens (from comparisons): {total_html_tokens}
   - Total Tokens Saved: {total_tokens_saved}
   - Average Tokens per Request: {average_tokens_per_request}
   - Average Savings per Comparison: {average_savings}

   Recent Requests (last 10):
   {list of recent requests with URL, timestamp, tokens, savings}

   Top Domains:
   {list of top 5 domains by request count}
   ```

#### For Reset Mode:

1. **Confirm with User**
   - Use AskUserQuestion to confirm the reset
   - Question: "Are you sure you want to reset all markdown_crawl metrics? This cannot be undone."
   - Options: ["Yes, reset all metrics", "No, keep the metrics"]

2. **Reset Metrics**
   - If confirmed, create a fresh metrics structure
   - Save to `skills/markdown_crawl/metrics.json`
   - Display confirmation message

### Metrics Schema

Store metrics in `skills/markdown_crawl/metrics.json`:

```json
{
  "total_requests": 0,
  "successful_markdown_requests": 0,
  "failed_requests": 0,
  "total_markdown_tokens": 0,
  "total_html_tokens": 0,
  "total_tokens_saved": 0,
  "requests_history": [
    {
      "url": "https://example.com",
      "timestamp": "2026-02-14T12:00:00Z",
      "markdown_tokens": 725,
      "html_tokens": 1500,
      "tokens_saved": 775,
      "success": true,
      "content_type": "text/markdown",
      "domain": "example.com"
    }
  ]
}
```

### Error Handling

- If URL is invalid, show clear error message
- If markdown not available (HTML response instead), note it in metrics
- If curl fails, track as failed request
- If metrics file is corrupted, create fresh file
- Always provide helpful error messages to the user

### Notes

- Keep request history limited to last 50 requests to prevent file bloat
- Extract domain from URL for domain tracking
- Use ISO 8601 timestamp format
- Handle both absolute and relative URLs appropriately
- Respect redirects and follow them

## Inputs

- **url**: The web URL to fetch (optional, required for fetch/compare modes)
- **mode**: Determined by arguments:
  - No args or URL = fetch mode
  - `--compare` flag = compare mode
  - `stats` = stats mode
  - `reset` = reset mode

## Output

### Fetch Mode:
- Markdown content from the URL
- Token count (if available)
- Confirmation that metrics were updated

### Compare Mode:
- Side-by-side comparison
- Token counts for both formats
- Savings calculation and percentage

### Stats Mode:
- Comprehensive statistics display
- Recent request history
- Top domains

### Reset Mode:
- Confirmation message after reset
