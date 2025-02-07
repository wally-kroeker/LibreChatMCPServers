# LibreChat MCP Servers

This directory contains Model Context Protocol (MCP) server implementations that extend LibreChat's capabilities through the Supergateway bridge.


These are the projects I am tiing together.  
- This is the think that gives any kind of tool to the Agent.  [Model Context Protocol Specification](https://spec.modelcontextprotocol.io/)
- Converts stdio MCP servers to SSE and each server can run in its own container.  I will see how this scales [Supergateway Documentation](https://github.com/supercorp-ai/supergateway)
- Multi-LLM agent and MCP client [LibreChat Documentation](https://docs.librechat.ai/)


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


# Adding MCP Servers to LibreChat with Supergateway

This guide provides step-by-step instructions for adding any MCP server to LibreChat using Supergateway as a bridge.

## Overview

Supergateway allows you to run stdio-based MCP servers over SSE (Server-Sent Events), making it ideal for integrating MCP servers with LibreChat in a Docker environment.

## Prerequisites

1. A running LibreChat installation
2. Docker and Docker Compose
3. The MCP server package you want to integrate

## Implementation Steps

### 1. Create Server Directory

Create a directory for your MCP server:

```bash
mkdir mcp-server-name
```

### 2. Create Dockerfile

Create `mcp-server-name/Dockerfile`:

```dockerfile
FROM node:18

WORKDIR /app

# Install both supergateway and your MCP server package
RUN npm install -g supergateway @organization/mcp-server-package

# The command runs supergateway as a bridge between your stdio MCP server and SSE
# --stdio: The command to run your MCP server
# --port: The port supergateway will listen on
CMD ["npx", "-y", "supergateway", "--stdio", "npx -y @organization/mcp-server-package", "--port", "8002"]
```

### 3. Update docker-compose.override.yml

Add your MCP server service:

```yaml
services:
  mcp-server-name:
    build:
      context: ./mcp-server-name
    ports:
      - "8002:8002"  # Adjust port as needed
    networks:
      - librechat_default
    environment:
      - API_KEY=${YOUR_API_KEY}  # If your server needs API keys
    volumes:
      - ./mcp-server-name:/app  # If your server needs access to local files
```

### 4. Update librechat.yaml

Add your MCP server configuration:

```yaml
mcpServers:
  server-name:
    type: sse
    url: "http://mcp-server-name:8002/sse"
```

## Understanding the Architecture

1. **Stdio to SSE Bridge**:
   - Your MCP server communicates via stdio
   - Supergateway acts as a bridge, converting stdio to SSE
   - LibreChat connects to the SSE endpoint

2. **Docker Networking**:
   - All services run on the `librechat_default` network
   - Services can communicate using their service names as hostnames
   - Only necessary ports are exposed

3. **Configuration Flow**:
   - LibreChat reads mcpServers config from librechat.yaml
   - Connects to Supergateway's SSE endpoint
   - Supergateway forwards requests to your MCP server

## Common Configurations

### 1. Basic MCP Server

```yaml
mcpServers:
  basic-server:
    type: sse
    url: "http://basic-server:8002/sse"
```

### 2. MCP Server with API Key

```yaml
# docker-compose.override.yml
services:
  api-server:
    environment:
      - API_KEY=${API_KEY}

# librechat.yaml
mcpServers:
  api-server:
    type: sse
    url: "http://api-server:8002/sse"
```

### 3. MCP Server with File Access

```yaml
# docker-compose.override.yml
services:
  file-server:
    volumes:
      - ./data:/app/data

# librechat.yaml
mcpServers:
  file-server:
    type: sse
    url: "http://file-server:8002/sse"
```

## Troubleshooting

1. Connection Issues:
   - Verify network configuration
   - Check container logs: `docker compose logs mcp-server-name`
   - Ensure ports are not conflicting

2. Server Not Starting:
   - Verify package installation in Dockerfile
   - Check for environment variables
   - Review server logs

3. Communication Problems:
   - Verify service names match in all configurations
   - Check network connectivity between containers
   - Ensure SSE endpoint is correct

## Best Practices

1. **Security**:
   - Never commit API keys or sensitive data
   - Use environment variables for secrets
   - Limit container permissions

2. **Networking**:
   - Only expose necessary ports
   - Use internal Docker networking when possible
   - Follow least privilege principle

3. **Configuration**:
   - Keep configurations modular
   - Document environment variables
   - Use version control for configurations

4. **Monitoring**:
   - Log important events
   - Monitor resource usage
   - Set up health checks

## Example: Complete Setup

Here's a complete example of adding a new MCP server:

1. **Directory Structure**:
```
librechat/
├── mcp-server/
│   └── Dockerfile
├── docker-compose.override.yml
└── librechat.yaml
```

2. **Dockerfile**:
```dockerfile
FROM node:18
WORKDIR /app
RUN npm install -g supergateway @org/mcp-server
CMD ["npx", "-y", "supergateway", "--stdio", "npx -y @org/mcp-server", "--port", "8002"]
```

3. **docker-compose.override.yml**:
```yaml
services:
  mcp-server:
    build:
      context: ./mcp-server
    ports:
      - "8002:8002"
    networks:
      - librechat_default
    environment:
      - API_KEY=${MCP_API_KEY}
```

4. **librechat.yaml**:
```yaml
mcpServers:
  mcp-server:
    type: sse
    url: "http://mcp-server:8002/sse"
```

## References

- [Model Context Protocol Specification](https://spec.modelcontextprotocol.io/)
- [Supergateway Documentation](https://github.com/supercorp-ai/supergateway)
- [LibreChat Documentation](https://docs.librechat.ai/)
