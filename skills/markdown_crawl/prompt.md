# Markdown Crawl

## Purpose

Fetch web content in markdown format using Cloudflare's "Markdown for Agents" feature. This skill automatically tracks usage metrics and token savings.

## Instructions

You are a specialized agent for fetching web content in markdown format and tracking usage metrics.

This skill uses a standalone bash script located at `skills/markdown_crawl/markdown_crawl.sh` that handles all functionality.

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
skills/markdown_crawl/markdown_crawl.sh fetch <url>
```

This will:
- Fetch the URL in markdown format
- Display the content with metadata
- Automatically track metrics

#### Compare Mode

When the user provides a URL with `--compare` flag (e.g., `/markdown_crawl https://example.com --compare`):

```bash
skills/markdown_crawl/markdown_crawl.sh compare <url>
```

This will:
- Fetch both markdown and HTML versions
- Calculate token savings
- Display a comparison with statistics
- Track detailed metrics

#### Stats Mode

When the user requests stats (e.g., `/markdown_crawl stats`):

```bash
skills/markdown_crawl/markdown_crawl.sh stats
```

This will display comprehensive usage statistics including:
- Total requests and success rate
- Token metrics (markdown, HTML, savings)
- Recent request history
- Top domains by request count

#### Reset Mode

When the user requests reset (e.g., `/markdown_crawl reset`):

```bash
skills/markdown_crawl/markdown_crawl.sh reset
```

This will:
- Prompt for confirmation (the script handles this interactively)
- Reset all metrics if confirmed
- Display confirmation message

### Script Features

The bash script (`markdown_crawl.sh`) automatically handles:

✅ **URL Validation**: Adds https:// if missing, validates format
✅ **HTTP Requests**: Uses proper headers (`Accept: text/markdown`)
✅ **Response Parsing**: Extracts headers and body, handles `x-markdown-tokens`
✅ **Token Calculation**: Uses header values or estimates (char_count / 4)
✅ **Metrics Tracking**: Maintains persistent JSON metrics file
✅ **Error Handling**: Clear error messages, graceful failure handling
✅ **Colored Output**: User-friendly colored terminal output
✅ **History Management**: Keeps last 50 requests to prevent file bloat

### Dependencies

The script requires:
- `curl` - For HTTP requests
- `jq` - For JSON parsing and manipulation

If `jq` is not installed, the script will display an error message.

### Error Handling

The script handles errors gracefully:
- Invalid URLs: Clear error message
- Network failures: Curl error reporting
- Missing dependencies: Helpful installation guidance
- Malformed metrics: Auto-recreates metrics file

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
