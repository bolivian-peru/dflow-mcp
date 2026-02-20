# AGENTS.md

This document helps agents work effectively with the DFlow MCP Server repository.

## Project Overview

This is a Model Context Protocol (MCP) server providing access to prediction market data from two platforms:

1. **DFlow/Kalshi** — CFTC-regulated centralized exchange (order book, USD settlement)
2. **Baozi.bet** — Decentralized pari-mutuel markets on Solana (pool-based, SOL settlement)

**35 total tools** across both platforms.

## Essential Commands

### Development
```bash
# Install dependencies
bun install

# Development mode with hot reload
bun run dev

# Start production server
bun start

# TypeScript compilation check
bun run tsc --noEmit

# Build for distribution
bun run build
```

### Testing
```bash
# Run comprehensive server test
./test-server.sh

# Run TypeScript tests
bun run test
```

## Project Structure

```
dflow-mcp/
├── src/
│   └── index.ts              # Main MCP server (DFlow + Baozi tools)
├── tests/
│   └── server.test.ts        # TypeScript test suite
├── .github/workflows/
│   └── test.yml              # CI/CD pipeline
├── package.json              # Dependencies and scripts
├── tsconfig.json             # TypeScript configuration
├── README.md                 # User documentation
├── llms_dflow.json           # DFlow API specification
├── AGENTS.md                 # This file
└── test-server.sh            # Quick server validation script
```

## Code Organization

### Main Server (`src/index.ts`)

- **DFlowAPIClient**: HTTP client for DFlow/Kalshi API (GET + POST, timeout handling)
- **BaoziAPIClient**: HTTP client for Baozi.bet API (GET, handles JSON + markdown responses)
- **Server Setup**: MCP server configuration and initialization
- **Tool Definitions**: 35 MCP tools (23 DFlow + 12 Baozi)
- **Prompts**: 4 analysis prompts (market trends, event comparison, Baozi analysis, cross-platform)
- **Resources**: 6 data resources (3 DFlow + 3 Baozi)
- **Request Handling**: JSON-RPC request routing and response formatting

## Tool Categories

### DFlow / Kalshi Tools (23)

#### Event Management (4 tools)
- `get_event` - Single event by ticker
- `get_events` - Paginated event list with filtering
- `search_events` - Search by title/ticker
- `get_live_data_by_event` - Live data for event

#### Market Operations (6 tools)
- `get_market` - Market by ticker
- `get_market_by_mint` - Market by mint address
- `get_markets` - Paginated markets with filters
- `get_markets_batch` - Batch market lookup (up to 100)
- `get_market_candlesticks` - OHLC data for market
- `get_market_candlesticks_by_mint` - Candlesticks by mint

#### Trade & Analytics (4 tools)
- `get_trades` - Trades across markets
- `get_trades_by_mint` - Trades for specific market
- `get_forecast_percentile_history` - Forecast analytics
- `get_forecast_percentile_history_by_mint` - Forecast by mint

#### Live Data & Series (5 tools)
- `get_live_data` - Live data for milestones
- `get_live_data_by_mint` - Live data by mint
- `get_series` - All series templates
- `get_series_by_ticker` - Series by ticker
- `get_event_candlesticks` - Event candlesticks

#### Utilities (4 tools)
- `get_outcome_mints` - Outcome mint addresses
- `filter_outcome_mints` - Filter addresses to outcome mints
- `get_tags_by_categories` - Category-tag mapping
- `get_filters_by_sports` - Sports filtering options

### Baozi.bet Solana Tools (12)

#### Market Discovery (3 tools)
- `baozi_get_markets` - List markets with status/limit/offset filtering
- `baozi_get_market` - Single market by Solana public key
- `baozi_get_agent_markets` - Agent-optimized list (filter by betting_open)

#### Bet Analysis (1 tool)
- `baozi_get_quote` - Calculate payout, odds, fees for a potential bet

#### Portfolio (1 tool)
- `baozi_get_positions` - All positions for a Solana wallet (binary + race)

#### Metadata (1 tool)
- `baozi_get_market_metadata` - Batch fetch descriptions, rules, images, categories

#### Agent Ecosystem (1 tool)
- `baozi_get_agent_info` - Registered agents, MCP details, fees, registration guide

#### Oracle (1 tool)
- `baozi_get_oracle_proofs` - Resolution proofs with evidence and rationale

#### Documentation (3 tools)
- `baozi_get_skill_docs` - Complete protocol docs (69 MCP tools reference)
- `baozi_get_guardrails` - Pari-mutuel market creation rules v7.2
- `baozi_get_program_idl` - Anchor IDL for direct on-chain RPC

#### Social (1 tool)
- `baozi_get_share_card` - Generate 1200x630 PNG share card URL

## API Reference

### DFlow Configuration
- **API URL**: `https://prediction-markets-api.dflow.net`
- **Auth**: None required
- **Timeout**: 30 seconds (configurable)

### Baozi Configuration
- **API URL**: `https://baozi.bet`
- **Auth**: None required for read operations
- **Program**: `FWyTPzm5cfJwRKzfkscxozatSxF6Qu78JQovQUwKPruJ` (Solana mainnet)
- **Full MCP**: `@baozi.bet/mcp-server` (69 tools, read+write, requires Solana wallet)

### Baozi API Endpoints

| Endpoint | Method | Returns |
|----------|--------|---------|
| `/api/v4/markets` | GET | Markets list (JSON) |
| `/api/v4/market/:key` | GET | Single market (JSON) |
| `/api/v4/agent/markets` | GET | Agent-optimized markets (JSON) |
| `/api/v4/quote` | GET | Bet payout calculation (JSON) |
| `/api/v4/positions/:wallet` | GET | Wallet positions (JSON) |
| `/api/markets/metadata` | GET | Off-chain metadata batch (JSON) |
| `/api/v4/agents` | GET | Agent ecosystem info (JSON) |
| `/api/agents/proofs` | GET | Oracle resolution proofs (JSON) |
| `/api/skill` | GET | Protocol documentation (markdown) |
| `/api/pari-mutuel-guardrails` | GET | Market creation rules (markdown) |
| `/api/mcp/idl` | GET | Program IDL (JSON) |
| `/api/share/card` | GET | Share card image (PNG) |

## Development Patterns

### Adding New Tools

1. **Define Tool Schema**: Add to TOOLS array with `baozi_` prefix for Baozi tools
2. **Implement Handler**: Add case in main switch statement
3. **Map API Endpoint**: Use `baoziClient.get()` for Baozi or `apiClient.get()` for DFlow
4. **Validate Parameters**: MCP handles schema validation

### Naming Convention
- DFlow tools: `get_*` (existing pattern)
- Baozi tools: `baozi_*` prefix (platform-namespaced)

## Agent-Specific Tips

1. **Always test after changes**: Run `./test-server.sh` to validate
2. **Check API specification**: `llms_dflow.json` for DFlow, `baozi.bet/skill` for Baozi
3. **Maintain type safety**: Use TypeScript strict mode
4. **Follow MCP patterns**: Standard JSON-RPC 2.0 with stdio transport
5. **Document new tools**: Update README.md and AGENTS.md when adding tools
6. **Platform prefix**: All Baozi tools use `baozi_` prefix to avoid naming conflicts
