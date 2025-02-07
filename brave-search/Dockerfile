FROM node:18

WORKDIR /app

# Install required packages globally
RUN npm install -g supergateway @modelcontextprotocol/server-brave-search

# The command runs supergateway as a bridge between the Brave Search MCP server and SSE
# --stdio: The command to run the MCP server
# --port: The port supergateway will listen on (8003 assigned for Brave Search)
CMD ["npx", "-y", "supergateway", "--stdio", "npx -y @modelcontextprotocol/server-brave-search", "--port", "8003"]
