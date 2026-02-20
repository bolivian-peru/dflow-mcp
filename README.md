# DFlow MCP Server — Multi-Platform Prediction Markets

[![smithery badge](https://smithery.ai/badge/@openSVM/dflow-mcp)](https://smithery.ai/server/@openSVM/dflow-mcp)

**35 tools** for comprehensive prediction market coverage across centralized and decentralized platforms:

| Platform | Type | Markets | Tools | Settlement |
|----------|------|---------|-------|------------|
| [**Kalshi**](https://kalshi.com) via DFlow | CeFi (CFTC-regulated) | Order book | 23 | USD |
| [**Baozi.bet**](https://baozi.bet) | DeFi (Solana) | Pari-mutuel pools | 12 | SOL |

One MCP server. Two prediction market ecosystems. Full cross-platform analysis.

**Demo:** [https://dflow.opensvm.com](https://dflow.opensvm.com) | **Baozi:** [https://baozi.bet](https://baozi.bet)

## Features

### DFlow / Kalshi (Centralized)
- **Event Management**: Get events, search events, retrieve event metadata
- **Market Data**: Market information, batch queries, market lookups by mint
- **Trading Data**: Trade history, trades by market, pagination support
- **Forecast Analytics**: Forecast percentile history, time series data
- **Candlestick Data**: OHLC data for events and markets
- **Live Data**: Real-time data feeds, milestone information
- **Series Information**: Series templates, categories, and metadata
- **Utility Functions**: Outcome mint queries, filtering, and search capability

### Baozi.bet (Decentralized — Solana)
- **Market Discovery**: Browse active pari-mutuel markets (binary Yes/No + multi-outcome race markets)
- **Real-Time Odds**: Live pool sizes and implied probabilities that shift with every bet
- **Bet Quoting**: Calculate exact payouts, fees, and odds before placing a bet
- **Portfolio Tracking**: View all positions for any Solana wallet with P&L data
- **Oracle Proofs**: Transparent resolution evidence from the "Grandma Mei" oracle system
- **Agent Ecosystem**: 69 MCP tools via dedicated `@baozi.bet/mcp-server`, agent registration, affiliate system
- **Share Cards**: Generate social media share images (1200x630 PNG) for any market
- **Protocol Docs**: Full on-chain program IDL, PDA seeds, and integration guides

## Installation

### Method 1: Install via Smithery (Recommended)

```bash
npx @smithery/cli install dflow-mcp-server --client claude
```

### Method 2: Manual Installation

```bash
# Prerequisites: Bun (recommended) or Node.js 18+
bun install
```

## Usage

### Starting the Server

```bash
# Development mode (with hot reload)
bun run dev

# Production mode
bun start

# Or directly
bun run src/index.ts
```

### Claude Desktop Configuration

```json
{
  "mcpServers": {
    "dflow-mcp": {
      "command": "bun",
      "args": ["run", "/path/to/dflow-mcp/src/index.ts"]
    }
  }
}
```

Compatible with any MCP client: Claude Desktop, Claude Code, Cursor, Continue, and more.

## Available Tools

### DFlow / Kalshi Tools (23)

#### Events
| Tool | Description |
|------|-------------|
| `get_event` | Get a single event by ticker |
| `get_events` | Get paginated list of all events |
| `search_events` | Search events by title/ticker |

#### Markets
| Tool | Description |
|------|-------------|
| `get_market` | Get market details by ticker |
| `get_market_by_mint` | Get market by mint address |
| `get_markets` | Get paginated list of markets |
| `get_markets_batch` | Get multiple markets (up to 100) |

#### Trading & Analytics
| Tool | Description |
|------|-------------|
| `get_trades` | Get trades across markets |
| `get_trades_by_mint` | Get trades for specific market |
| `get_forecast_percentile_history` | Get forecast history |
| `get_forecast_percentile_history_by_mint` | Forecast history by mint |
| `get_event_candlesticks` | Event candlestick data |
| `get_market_candlesticks` | Market candlestick data |
| `get_market_candlesticks_by_mint` | Candlesticks by mint |

#### Live Data & Series
| Tool | Description |
|------|-------------|
| `get_live_data` | Get live data for milestones |
| `get_live_data_by_event` | Live data for event |
| `get_live_data_by_mint` | Live data by mint |
| `get_series` | Get all series templates |
| `get_series_by_ticker` | Get specific series |

#### Utilities
| Tool | Description |
|------|-------------|
| `get_outcome_mints` | Get outcome mint addresses |
| `filter_outcome_mints` | Filter addresses to outcome mints |
| `get_tags_by_categories` | Get category-tag mapping |
| `get_filters_by_sports` | Get sports filtering options |

### Baozi.bet Solana Tools (12)

| Tool | Description |
|------|-------------|
| `baozi_get_markets` | List active markets with odds, pools, and closing times |
| `baozi_get_market` | Get detailed market info by Solana public key |
| `baozi_get_agent_markets` | Agent-optimized market list (filter by betting status) |
| `baozi_get_quote` | Calculate payout/odds for a potential bet before placing it |
| `baozi_get_positions` | Get all positions for a Solana wallet |
| `baozi_get_market_metadata` | Batch fetch off-chain metadata (descriptions, rules, images) |
| `baozi_get_agent_info` | Agent Kitchen info — registered agents, MCP tools, fees |
| `baozi_get_oracle_proofs` | Oracle resolution proofs with evidence and rationale |
| `baozi_get_skill_docs` | Complete protocol documentation (69 MCP tools reference) |
| `baozi_get_guardrails` | Pari-mutuel market creation rules v7.2 |
| `baozi_get_program_idl` | Anchor IDL for direct on-chain interaction |
| `baozi_get_share_card` | Generate 1200x630 PNG share card for any market |

## Example Tool Calls

### DFlow: Get a specific event
```json
{
  "tool": "get_event",
  "arguments": {
    "event_id": "US-PRESIDENT-2024",
    "withNestedMarkets": true
  }
}
```

### Baozi: List active Solana markets
```json
{
  "tool": "baozi_get_markets",
  "arguments": {
    "status": "Active",
    "limit": 20
  }
}
```

### Baozi: Calculate bet payout before betting
```json
{
  "tool": "baozi_get_quote",
  "arguments": {
    "market": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
    "side": "Yes",
    "amount": 1.0
  }
}
```

### Baozi: Check wallet portfolio
```json
{
  "tool": "baozi_get_positions",
  "arguments": {
    "wallet": "YourSolanaWalletAddress"
  }
}
```

### Baozi: Get oracle resolution proof
```json
{
  "tool": "baozi_get_oracle_proofs",
  "arguments": {
    "layer": "official"
  }
}
```

## Prompts

| Prompt | Description |
|--------|-------------|
| `analyze_market_trends` | Analyze DFlow market trends (volume, liquidity, price movements) |
| `compare_events` | Compare multiple DFlow events side-by-side |
| `baozi_market_analysis` | Find best Baozi.bet opportunities (odds, pools, timing) |
| `cross_platform_comparison` | Compare the same topics across Kalshi and Baozi.bet |

## Resources

| URI | Description |
|-----|-------------|
| `dflow://api/events` | Live DFlow prediction market events |
| `dflow://api/markets` | Active DFlow markets |
| `dflow://api/docs` | DFlow API documentation |
| `baozi://markets` | Active Baozi.bet Solana markets |
| `baozi://agents` | Baozi agent ecosystem info |
| `baozi://docs` | Complete Baozi protocol documentation |

## Baozi.bet Platform Overview

[Baozi.bet](https://baozi.bet) is a decentralized prediction market protocol on Solana:

- **Pari-mutuel pools** — Odds shift dynamically with every bet (no order book needed)
- **SOL-native** — Bet and win in SOL, no USDC or wrapped tokens
- **3 market layers** — Official (curated), Labs (community-created), Private (invite-only)
- **AI agent-first** — 69 MCP tools via `@baozi.bet/mcp-server`, agent registration, affiliate commissions
- **On-chain oracle** — "Grandma Mei" oracle with transparent resolution proofs and 6-hour dispute window
- **$BAOZI token** — Revenue sharing with stakers from protocol fees
- **Program:** `FWyTPzm5cfJwRKzfkscxozatSxF6Qu78JQovQUwKPruJ` (Solana mainnet)

### Fee Structure

| Layer | Platform Fee | Creation Fee |
|-------|-------------|--------------|
| Official | 2.5% | 0.01 SOL |
| Labs | 3.0% | 0.01 SOL |
| Private | 2.0% | 0.01 SOL |

### For AI Agents

Agents can earn affiliate commissions and create Lab markets. Install the full MCP server for write operations:

```bash
npm install -g @baozi.bet/mcp-server
# or
npx @baozi.bet/mcp-server
```

Learn more: [baozi.bet/agents](https://baozi.bet/agents) | [baozi.bet/skill](https://baozi.bet/skill)

## Development

### Project Structure
```
dflow-mcp/
├── src/
│   └── index.ts          # Main server (DFlow + Baozi tools)
├── package.json          # Dependencies and scripts
├── tsconfig.json         # TypeScript configuration
├── llms_dflow.json       # DFlow API specification
└── README.md             # This file
```

### API Base URLs

| Platform | URL |
|----------|-----|
| DFlow | `https://prediction-markets-api.dflow.net` |
| Baozi.bet | `https://baozi.bet` |

### Building & Testing

```bash
bun run build
bun run test
```

## License

MIT License - See LICENSE file for details.

---

**OpenSVM x Kalshi x DFlow x Baozi.bet**
