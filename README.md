# MCP Toggl Server

A Model Context Protocol (MCP) server for Toggl Track integration, providing time tracking, reporting, and project/client management with intelligent caching for optimal performance.

Forked from [verygoodplugins/mcp-toggl](https://github.com/verygoodplugins/mcp-toggl).

## Features

- **Time Tracking**: Start/stop timers, create/update/delete time entries
- **Project & Client Management**: Create projects, clients, update and archive
- **Smart Reporting**: Daily/weekly reports with project and workspace breakdowns
- **Performance Optimized**: Intelligent caching system minimizes API calls
- **Data Hydration**: Automatically enriches time entries with project/workspace/client names
- **Flexible Filtering**: Query by date ranges, workspaces, or projects

## Quick Start (Recommended)

Use via npx without cloning or building locally.

### Claude Desktop
Add this to `~/Library/Application Support/Claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "mcp-toggl": {
      "command": "npx @verygoodplugins/mcp-toggl@latest",
      "env": {
        "TOGGL_API_KEY": "your_api_key_here",
        "TOGGL_DEFAULT_WORKSPACE_ID": "123456",
        "TOGGL_CACHE_TTL": "3600000",
        "TOGGL_CACHE_SIZE": "1000"
      }
    }
  }
}
```

### Cursor
Add this to your Cursor MCP settings (e.g., `~/.cursor/mcp.json`):
```json
{
  "mcp": {
    "servers": {
      "mcp-toggl": {
        "command": "npx",
        "args": ["@verygoodplugins/mcp-toggl@latest"],
        "env": {
          "TOGGL_API_KEY": "your_api_key_here",
          "TOGGL_DEFAULT_WORKSPACE_ID": "123456",
          "TOGGL_CACHE_TTL": "3600000",
          "TOGGL_CACHE_SIZE": "1000"
        }
      }
    }
  }
}
```

## Manual Installation

```bash
npm install
npm run build
```

## Configuration

1. Get your Toggl API key from: https://track.toggl.com/profile

2. Create a `.env` file:
```env
TOGGL_API_KEY=your_api_key_here

# Aliases also supported (use one of these only if needed):
# TOGGL_API_TOKEN=your_api_key_here
# TOGGL_TOKEN=your_api_key_here

# Optional configuration
TOGGL_DEFAULT_WORKSPACE_ID=123456  # Your default workspace
TOGGL_CACHE_TTL=3600000            # Cache TTL in ms (default: 1 hour)
TOGGL_CACHE_SIZE=1000              # Max cached entities (default: 1000)
```

3. Add to your MCP configuration:

### Claude Desktop
Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "mcp-toggl": {
      "command": "node",
      "args": ["/path/to/mcp-toggl/dist/index.js"],
      "env": {
        "TOGGL_API_KEY": "your_api_key_here"
      }
    }
  }
}
```

### Cursor
Edit `.mcp.json` in your project:
```json
{
  "mcpServers": {
    "mcp-toggl": {
      "command": "node",
      "args": ["./mcp-servers/mcp-toggl/dist/index.js"],
      "env": {
        "TOGGL_API_KEY": "your_api_key_here"
      }
    }
  }
}
```

## Available Tools

### Time Tracking

#### `toggl_get_time_entries`
Get time entries with optional filters.
```json
{
  "period": "today",
  "workspace_id": 123456,
  "project_id": 789012
}
```
Supported periods: `today`, `yesterday`, `week`, `lastWeek`, `month`, `lastMonth`

#### `toggl_get_current_entry`
Get the currently running timer.

#### `toggl_start_timer`
Start a new time entry.
```json
{
  "description": "Working on feature",
  "project_id": 123456,
  "tags": ["development"]
}
```

#### `toggl_create_entry`
Create a completed time entry with specific start and stop times.
```json
{
  "description": "Client meeting",
  "start": "2026-02-17T09:00:00-06:00",
  "stop": "2026-02-17T10:00:00-06:00",
  "project_id": 123456,
  "billable": true
}
```

#### `toggl_stop_timer`
Stop the currently running timer.

#### `toggl_update_time_entry`
Update an existing time entry (change project, description, tags, times).
```json
{
  "time_entry_id": 3456789012,
  "project_id": 654321,
  "description": "Updated description",
  "tags": ["corrected"]
}
```

#### `toggl_delete_time_entry`
Delete a time entry.
```json
{
  "time_entry_id": 3456789012
}
```

### Reporting

#### `toggl_daily_report`
Generate a daily report with project/workspace breakdowns.
```json
{
  "date": "2026-02-17",
  "format": "json"
}
```
Formats: `json` (default), `text` (formatted for display)

#### `toggl_weekly_report`
Generate a weekly report with daily breakdowns.
```json
{
  "week_offset": 0,
  "format": "json"
}
```

#### `toggl_project_summary`
Get total hours per project for a date range.
```json
{
  "period": "month",
  "workspace_id": 123456
}
```

#### `toggl_workspace_summary`
Get total hours per workspace.

### Management

#### `toggl_list_workspaces`
List all available workspaces.

#### `toggl_list_projects`
List projects in a workspace.
```json
{
  "workspace_id": 123456
}
```

#### `toggl_list_clients`
List clients in a workspace.

#### `toggl_create_client`
Create a new client in a workspace.
```json
{
  "name": "Acme Corp",
  "notes": "Retainer client"
}
```

#### `toggl_create_project`
Create a new project in a workspace.
```json
{
  "name": "Website Redesign (retainer)",
  "client_id": 12345678,
  "billable": true
}
```

#### `toggl_update_project`
Update an existing project (rename, change client, archive).
```json
{
  "project_id": 87654321,
  "name": "New Project Name",
  "active": false
}
```

### Cache Management

#### `toggl_warm_cache`
Pre-fetch workspace/project/client data for better performance.

#### `toggl_cache_stats`
View cache performance metrics.

#### `toggl_clear_cache`
Clear all cached data.

## Performance Optimization

The server uses an intelligent caching system to minimize API calls:

1. **First Run**: Warms cache by fetching workspaces, projects, and clients
2. **Subsequent Calls**: Uses cached names for hydration (95%+ cache hit rate)
3. **Smart Invalidation**: TTL-based expiry with configurable duration
4. **Write-Through**: Cache is automatically invalidated on create/update operations
5. **Memory Efficient**: LRU eviction keeps memory usage under 10MB

### Typical Performance
- First report: 2-3 API calls (warm cache + get entries)
- Subsequent reports: 1 API call (just time entries)
- Cache hit rate: >95% for typical usage

## Troubleshooting

### API Key Issues
- Ensure your API key is correct (get from https://track.toggl.com/profile)
- API key goes in the username field, "api_token" as password for basic auth
- Trim whitespace: copy/paste can include trailing spaces/newlines which cause 401/403
- Accepted env var names: `TOGGL_API_KEY` (preferred), `TOGGL_API_TOKEN`, or `TOGGL_TOKEN`
- If you see 401/403, regenerate the token on your Toggl profile and update your MCP config

### Security & Token Lifecycle
- This server uses Basic Auth with a Toggl API token, not OAuth; there is no refresh token to manage.
- Toggl API tokens do not expire automatically. They only change if you manually regenerate them or if Toggl invalidates them during a security event.
- If you regenerate your token, the old one stops working immediately. Update the `TOGGL_API_KEY` in your Claude/Cursor config and restart the client.
- Never commit real secrets to version control. Use placeholders like `your_api_key_here` in docs and examples.

### Quick Auth Check
You can verify connectivity by calling the `toggl_check_auth` tool, which pings `/me` and lists your available workspaces without exposing your token.

### Rate Limiting
- The server implements automatic retry with exponential backoff
- Respects Toggl's rate limits (max 1 request per second)

### Cache Issues
- Run `toggl_clear_cache` if data seems stale
- Adjust `TOGGL_CACHE_TTL` for your needs (default: 1 hour)

## Development

```bash
# Run in development mode
npm run dev

# Build for production
npm run build

# Test the server
npm test
```

## License

GPL-3.0

## Credits

Originally created by [Very Good Plugins](https://verygoodplugins.com). This fork is maintained by [84EM](https://84em.com).
