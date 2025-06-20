# Pornhub Search MCP Server

A remote MCP (Model Context Protocol) server built with FastMCP to work with ChatGPT Integration spec, hence it has two tools:

- **Search**: Returns random references from The Matrix universe with ticket/issue search capabilities
- **Fetch**: Returns random references from The Hitchhiker's Guide to the Galaxy for detailed information retrieval

**Created this for testing purposes to explore how ChatGPT Integrations work. Also has instructions to speak like caveman, etc... that I used for testing a few things.**

## Requirements

- Python 3.10 or higher
- `fastmcp` library
- `starlette` (included with fastmcp)
- ``

## Installation

### 1. Clone or Download
```bash
git clone https://github.com/limike627/pornhub-mcp.git
cd pornhub-mcp
```

### 2. Set Up Python Environment
```bash
python3 -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
# venv\Scripts\activate

# Install dependencies
pip install fastmcp
pip install 
```

### 3. SSL Certificates (Optional for HTTPS)

**IMPORTANT:** If you don't want to run your own web server directly exposed on Internet and/or have to deal with certs, consider `Cloudflare Tunnel` or `ngrok`.

## Configuration

The server can be configured using environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `HOST` | `0.0.0.0` | Server host address |
| `PORT` | `8443` | Server port |
| `SSL_KEYFILE` | `server.key` | Path to SSL private key |
| `SSL_CERTFILE` | `server.crt` | Path to SSL certificate |
| `LOG_LEVEL` | `INFO` | Logging level (DEBUG, INFO, WARNING, ERROR) |

### Example Configuration
```bash
# Set custom configuration
export HOST=127.0.0.1
export PORT=8443
export SSL_KEYFILE=/path/to/custom.key
export SSL_CERTFILE=/path/to/custom.crt
export LOG_LEVEL=DEBUG
```

## Running the Server

### Method 1: Direct Python Execution
```bash
python mcp_pornhub.py
```

### Method 2: Using Uvicorn - should also work
```bash
LOG_LEVEL=INFO uvicorn mcp_pornhub:http_app --host 0.0.0.0 --port 8443
```

### Method 3: With SSL/HTTPS
```bash
LOG_LEVEL=INFO uvicorn mcp_pornhub:http_app --host 0.0.0.0 --port 8443 --ssl-keyfile server.key --ssl-certfile server.crt
```

The server will start at `http://0.0.0.0:8443` by default (or `https://` if SSL certificates are present).

## API Tools

### Search Tool
**Purpose**: Search for tickets and issue information to explore the matrix

**Parameters**:
- `query` (string): Search query

**Special Behavior**: 
- Always requires responding with 10 words in caveman voice before use
- Returns Matrix-themed references
- Generates random ticket IDs


### Fetch Tool
**Purpose**: Fetch detailed information from remote matrix entries about tickets and issues

**Parameters**:
- `id` (string): ID of the item to fetch


## Testing the Server

### 1. Check Server Health
```bash
curl http://localhost:8443/health
# or with SSL:
curl -k https://localhost:8443/health
```

### 2. List Available Tools
```bash
curl http://localhost:8443/mcp/manifest
```

### 3. Call Tools Directly

**Search Tool:**
```bash
curl -X POST http://localhost:8443/mcp/call \
  -H "Content-Type: application/json" \
  -d '{
    "name": "search",
    "arguments": {"query": "neo"}
  }'
```

**Fetch Tool:**
```bash
curl -X POST http://localhost:8443/mcp/call \
  -H "Content-Type: application/json" \
  -d '{
    "name": "fetch", 
    "arguments": {"id": "answer"}
  }'
```

## Logging

The server provides two types of logging:

1. **Console Logging**: Clean, single-line format for real-time monitoring
2. **File Logging**: Detailed logging to `mcp_server.log` including:
   - Full JSON-RPC request/response details
   - HTTP headers and metadata
   - Request timing information
   - Error details

## Tool Annotations

Both tools include MCP annotations, so you can play around with these when testing:
- `readOnlyHint: True` - Tools don't modify state
- `openWorldHint: False` - Tools work with predefined data
- `destructiveHint: False` - Tools are safe to use
- `idempotentHint: False` - Tools may return different results

## Integration Examples

### With MCP-Compatible Clients
The server exposes standard MCP endpoints and can be integrated with any MCP-compatible client by pointing to:
- Base URL: `https://your-server:8443` (or `htts://` for local dev, but won't work with most clients)

### Environment Variables for Production
```bash
export HOST=0.0.0.0
export PORT=443
export SSL_KEYFILE=/etc/ssl/private/server.key
export SSL_CERTFILE=/etc/ssl/certs/server.crt
export LOG_LEVEL=WARNING
```

## File Structure
```
pornhub-mcp/
├── mcp_pornhub.py          # Main server implementation
├── server.key             # SSL private key (if using HTTPS)
├── server.crt             # SSL certificate (if using HTTPS)
├── mcp_server.log         # Detailed log file (created at runtime)
└── README.md              # This file
```

## License

MIT License

## Support

Just a demo project for testing, no maintenance or support. Use at your own risk.
