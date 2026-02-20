# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an MCP (Model Context Protocol) server that provides access to prediction market data from **two platforms**:

1. **DFlow/Kalshi** — CFTC-regulated centralized prediction market (order book model)
2. **Baozi.bet** — Decentralized pari-mutuel prediction markets on Solana

The server wraps both REST APIs and exposes them as MCP tools for use with Claude Desktop, Cursor, and other MCP clients. **35 total tools** (23 DFlow + 12 Baozi).

**Base API URLs**:
- DFlow: `https://prediction-markets-api.dflow.net`
- Baozi: `https://baozi.bet`

## Commands

### Development
```bash
# Start server in development mode with hot reload
bun run dev

# Start server in production mode
bun start

# Build the project
bun run build

# Run tests
bun run test
```

### Running the server directly
```bash
bun run src/index.ts
```

## Architecture

### Single-File MCP Server

The entire server implementation is contained in `src/index.ts`. This is intentional for simplicity — the server is a thin wrapper around external APIs.

**Key components:**

1. **DFlowAPIClient**: HTTP client for DFlow/Kalshi API communication
   - Implements request timeout handling (30s default)
   - Constructs URLs with query parameters
   - Handles GET and POST requests

2. **BaoziAPIClient**: HTTP client for Baozi.bet API communication
   - Same timeout/error handling pattern as DFlowAPIClient
   - Handles both JSON and text/markdown responses
   - Connects to Solana prediction market endpoints

3. **TOOLS array**: Complete tool definitions (35 tools)
   - DFlow tools (23): Events, markets, trades, forecasts, candlesticks, live data, series, utilities
   - Baozi tools (12): Markets, quotes, positions, metadata, oracle proofs, docs, share cards

4. **Request handlers**:
   - `ListToolsRequestSchema`: Returns all 35 tools
   - `CallToolRequestSchema`: Routes tool calls via switch statement
   - `ListPromptsRequestSchema`: 4 prompts (2 DFlow + 2 Baozi/cross-platform)
   - `ListResourcesRequestSchema`: 6 resources (3 DFlow + 3 Baozi)
   - Error handling returns descriptive messages

### Baozi.bet Tool Categories

| Category | Tools | Endpoint Pattern |
|----------|-------|-----------------|
| Market Discovery | `baozi_get_markets`, `baozi_get_market`, `baozi_get_agent_markets` | `/api/v4/markets`, `/api/v4/market/:key`, `/api/v4/agent/markets` |
| Bet Analysis | `baozi_get_quote` | `/api/v4/quote?market=&side=&amount=` |
| Portfolio | `baozi_get_positions` | `/api/v4/positions/:wallet` |
| Metadata | `baozi_get_market_metadata` | `/api/markets/metadata?marketIds=` |
| Agent Ecosystem | `baozi_get_agent_info` | `/api/v4/agents` |
| Oracle | `baozi_get_oracle_proofs` | `/api/agents/proofs` |
| Documentation | `baozi_get_skill_docs`, `baozi_get_guardrails`, `baozi_get_program_idl` | `/api/skill`, `/api/pari-mutuel-guardrails`, `/api/mcp/idl` |
| Social | `baozi_get_share_card` | `/api/share/card?market=` (returns URL) |

### MCP Transport

The server uses **stdio transport**, the standard for MCP servers:
- Communication happens over stdin/stdout
- The server must be launched as a subprocess by the MCP client
- No HTTP server or network configuration needed

## Tool Implementation Pattern

When adding or modifying tools, follow this pattern:

1. Add tool definition to `TOOLS` array with name, description, and `inputSchema`
2. Add corresponding case to the switch statement in the `CallToolRequestSchema` handler
3. Use `apiClient.get()` for DFlow or `baoziClient.get()` for Baozi
4. Extract path parameters from args and pass remaining args as query params

## Configuration

Smithery config schema supports:
- `apiUrl`: DFlow API base URL (default: `https://prediction-markets-api.dflow.net`)
- `baoziApiUrl`: Baozi API base URL (default: `https://baozi.bet`)
- `requestTimeout`: Timeout in ms (default: 30000)

## Key Technical Details

- **Runtime**: Designed for Bun but compatible with Node.js 18+
- **MCP SDK Version**: `@modelcontextprotocol/sdk` ^0.5.0
- **Request Timeout**: 30 seconds (configurable)
- **Baozi Program ID**: `FWyTPzm5cfJwRKzfkscxozatSxF6Qu78JQovQUwKPruJ` (Solana mainnet)
- **Baozi MCP Server**: `@baozi.bet/mcp-server` (69 tools for full read+write operations)
