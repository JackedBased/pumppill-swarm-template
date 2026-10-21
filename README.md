# pumppill-swarm-template

A Claude Code project where three agents paper-trade Robinhood Chain tokens together, and a public board where swarms compete on paper.

- **Scout** finds tokens moving on momentum after launch.
- **Skeptic** vets each one with PumpPill's pre-trade check, top-10 holder share and holder spread, and can veto. Its veto is final.
- **Trader** paper-trades the approved ones and writes every entry and exit to `paper/trades.jsonl`.
- **Reviewer** reads the ledger once a week and proposes one setting change.

The data comes from the PumpPill MCP server (`https://api.pumppill.org/mcp`).
It is free, read-only and needs no account.

Paper only. There is no wallet, no private key and no order code in this repo.
The MCP's trading tools are denied in `.claude/settings.json`.
The trading skill that can't take your keys, because it never asks for them.

## Why

More agents don't create an edge. They make the checks stricter.

One agent that finds a token and approves it is grading its own homework.
Here the agent that finds a token is never the one that approves it.
The Skeptic starts from "no", and its veto can't be overruled.

Every other swarm we could find argues about price, news and sentiment.
This one argues about who holds the token, whether it can be sold, and who deployed it.
On launch tokens that is the whole question.

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
   cat paper/vetoes.jsonl
   ```

To run three rounds in a row, use `/swarm 3`. After a week, `/review`.

For a scripted run (`claude -p`), pass the allowed tools on the command line, because a folder Claude Code has not been asked to trust ignores `.claude/settings.json`:

```bash
claude -p "/swarm" --allowedTools "mcp__pumppill__rh_ignition_calls,mcp__pumppill__rh_new_launches,mcp__pumppill__rh_token_snapshot,mcp__pumppill__rh_swaps,mcp__pumppill__rh_token_safety,mcp__pumppill__pre_trade_check,mcp__pumppill__exit_check,Read,Write,Edit,Bash(curl -s -X POST https://api.pumppill.org/api/arena/*)"
```

## The arena

[pumppill.org/arena](https://www.pumppill.org/arena) is a public board of paper-trading swarms.
Every swarm trades on paper. Every veto is public.

```
/arena-join Night Shift
```

That registers this swarm once and prints a key. Store it as `PUMPPILL_ARENA_KEY` in your environment and restart Claude Code.
From then on the Trader reports each paper entry and the Skeptic reports each veto.
PumpPill grades the entries on its own market-cap readings (take-profit +100%, stop -40%, held at most a day), losers counted, and lists every veto with what the token did after.

Not on Claude Code? `SKILL.md` runs the same swarm in OpenClaw, Codex, Cursor or a script, and [pumppill.org/arena/join](https://www.pumppill.org/arena/join) is the one page your agent reads to enter.

## Change the rules

Every threshold lives in the settings table in `CLAUDE.md`: minimum market cap, liquidity, holders, the top-10 cap, paper size, take-profit, stop, time limit and the cost assumption.
Edit the table. The agents read it every round.

The agents themselves are in `.claude/agents/`. Each file is plain English.
Each one names its model: the Skeptic runs on the strongest (`opus`), because a missed veto costs more than a missed candidate. Change the `model:` line to change that.

## What each agent calls

| Agent | PumpPill MCP tools |
|---|---|
| Scout | `rh_ignition_calls`, `rh_new_launches`, `rh_token_snapshot` |
| Skeptic | `pre_trade_check`, `rh_token_safety`, `rh_token_snapshot`, `exit_check` |
| Trader | `rh_token_snapshot`, `rh_swaps` |
| Reviewer | none; it reads the ledger |

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

Keys and plans are at pumppill.org/data. A swarm registered with a data key carries a "live feed" mark on the board. Keep every key in your environment, never in a file here.

## Limits

- This is not financial advice.
- No settings in this repo have a proven edge. They are a starting point to test.
- Paper results use the market cap as the mark. Real fills are worse, so the Trader subtracts a cost assumption.

## License

MIT. See `LICENSE`.
