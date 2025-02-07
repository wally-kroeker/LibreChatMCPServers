# Brave Search MCP Server

This MCP server implementation integrates the Brave Search API with LibreChat, providing both web and local search capabilities.

## Features

- Web Search: General queries, news, articles, with pagination and freshness controls
- Local Search: Find businesses, restaurants, and services with detailed information
- Flexible Filtering: Control result types, safety levels, and content freshness
- Smart Fallbacks: Local search automatically falls back to web when no results are found

## Tools

### brave_web_search
Execute web searches with pagination and filtering
- Inputs:
  - query (string): Search terms
  - count (number, optional): Results per page (max 20)
  - offset (number, optional): Pagination offset (max 9)

### brave_local_search
Search for local businesses and services
- Inputs:
  - query (string): Local search terms
  - count (number, optional): Number of results (max 20)
- Automatically falls back to web search if no local results found

## Configuration

### Environment Variables

- `BRAVE_API_KEY`: Your Brave Search API key (required)

### Getting an API Key

1. Sign up for a Brave Search API account
2. Choose a plan (Free tier available with 2,000 queries/month)
3. Generate your API key from the developer dashboard

## Implementation Details

- Port: 8003
- Network: librechat_default
- Base Image: node:18
- Packages:
  - supergateway
  - @modelcontextprotocol/server-brave-search

## Usage Example

```yaml
# In librechat.yaml
mcpServers:
  brave-search:
    type: sse
    url: "http://mcp-brave-search:8003/sse"
```

## Troubleshooting

1. Connection Issues:
   - Verify BRAVE_API_KEY is set correctly
   - Check container logs: `docker compose logs mcp-brave-search`
   - Ensure port 8003 is not in use

2. Search Problems:
   - Verify API key has sufficient quota
   - Check query formatting
   - Review rate limits

## References

- [Brave Search API Documentation](https://brave.com/search/api/)
- [Model Context Protocol Specification](https://spec.modelcontextprotocol.io/)
- [Supergateway Documentation](https://github.com/supercorp-ai/supergateway)
