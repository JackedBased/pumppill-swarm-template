# pumppill-swarm-template

A Claude Code project where three agents paper-trade Robinhood Chain tokens together.

- **Scout** finds tokens moving on momentum after launch.
- **Skeptic** vets each one with PumpPill's pre-trade check, top-10 holder share and holder spread, and can veto.
- **Trader** paper-trades the approved ones and writes every entry and exit to `paper/trades.jsonl`.

The data comes from the PumpPill MCP server (`https://api.pumppill.org/mcp`).
It is free, read-only and needs no account.

Paper only. There is no wallet, no private key and no order code in this repo.
The MCP's trading tools are denied in `.claude/settings.json`.

## Why

More agents don't create an edge. They make the checks stricter.

One agent that finds a token and approves it is grading its own homework.
Here the agent that finds a token is never the one that approves it.
The Skeptic starts from "no", and its veto can't be overruled.

The Scout looks for momentum after launch on purpose.
PumpPill's own research found that signal held up, and launch-minute signals did not.

## Quickstart (5 minutes)

You need [Claude Code](https://docs.claude.com/en/docs/claude-code) installed.

1. Get the template.
   ```bash
   git clone https://github.com/JackedBased/pumppill-swarm-template
   cd pumppill-swarm-template
   ```
2. Start Claude Code in the folder.
   ```bash
   claude
   ```
   Approve the `pumppill` MCP server when Claude Code asks. It comes from `.mcp.json`.
3. Check the connection.
   ```
   /mcp
   ```
   `pumppill` should show as connected.
4. Run one round.
   ```
   /swarm
   ```
   You'll see the Trader mark open positions, the Scout's candidates, the Skeptic's verdict on each, and the Trader's paper entries.
5. Read the ledger.
   ```bash
   cat paper/trades.jsonl
   ```

To run three rounds in a row, use `/swarm 3`.

## Change the rules

Every threshold lives in the settings table in `CLAUDE.md`: minimum market cap, liquidity, holders, the top-10 cap, paper size, take-profit, stop, time limit and the cost assumption.
Edit the table. The agents read it every round.

The agents themselves are in `.claude/agents/`. Each file is plain English.

## What each agent calls

| Agent | PumpPill MCP tools |
|---|---|
| Scout | `rh_ignition_calls`, `rh_new_launches`, `rh_token_snapshot` |
| Skeptic | `pre_trade_check`, `rh_token_safety`, `rh_token_snapshot`, `exit_check` |
| Trader | `rh_token_snapshot`, `rh_swaps` |

## The loop and its gates

Each round runs four gates: a stated target, observation of every agent's output, one bounded repair per step, and a stop rule.
The details are in `CLAUDE.md`.

## Free tier and data key

Everything above runs on the free MCP.
Two parts get better with a PumpPill data API key in `PUMPPILL_DATA_KEY`:

- **Scout:** reads the Ignition momentum feed from `/api/partner/v1/signals/ignition`. Builder and Pro keys get it live. The free MCP gets it after a delay.
- **Skeptic:** reads the top-10 holder share as a number (`scan.top10_pct`) from `/api/partner/v1/tokens/{address}`. Without a key it reads it from text, and vetoes when it can't find one.

```bash
export PUMPPILL_DATA_KEY=<your key>
```

Keys and plans are at pumppill.org/data. Keep the key in your environment, never in a file here.

## Limits

- This is not financial advice.
- No settings in this repo have a proven edge. They are a starting point to test.
- Paper results use the market cap as the mark. Real fills are worse, so the Trader subtracts a cost assumption.

## License

MIT. See `LICENSE`.
