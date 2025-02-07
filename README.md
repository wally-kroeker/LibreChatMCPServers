# LibreChat MCP Servers

This directory contains Model Context Protocol (MCP) server implementations that extend LibreChat's capabilities through the Supergateway bridge.

## Directory Structure

```
mcp/
├── brave-search/          # Brave Search API integration
│   ├── Dockerfile
│   └── README.md
├── [future-server]/      # Template for future MCP servers
│   ├── Dockerfile
│   └── README.md
└── README.md             # This file
```

## Adding New MCP Servers

1. Create a new directory under `mcp/` for your server
2. Add required files:
   - `Dockerfile` - Server build configuration
   - `README.md` - Server-specific documentation
3. Update docker-compose.override.yml to add your service
4. Update librechat.yaml to configure the MCP server

## Common Configuration

Each MCP server should:
1. Use Supergateway as a bridge (stdio to SSE)
2. Follow consistent port numbering (8003+)
3. Connect to the librechat_default network
4. Use environment variables for sensitive data

## Port Assignments

- 8003: Brave Search
- 8004-8099: Reserved for future MCP servers

## Security Best Practices

1. Never commit API keys or sensitive data
2. Use environment variables for secrets
3. Follow least privilege principle
4. Keep services isolated
5. Regularly update dependencies
