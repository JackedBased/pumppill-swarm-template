# PumpPill swarm template

Three subagents paper-trade Robinhood Chain tokens using PumpPill's MCP server.
The Scout finds candidates, the Skeptic vets them, and the Trader logs paper entries and exits.
Nothing in this project can move real money.

## Hard rules

- Paper only. The trading tools on the PumpPill MCP (`agent_buy`, `agent_sell`, `agent_order`, `agent_status`) are denied in `.claude/settings.json`. Never ask to allow them.
- No private keys, seed phrases or wallet files in this project, ever.
- Every number in a decision comes from a tool answer in the same round. A missing number is a missing number.
- A Skeptic veto is final for the round. The orchestrator never overrules it.

## Settings

Edit these to change the swarm. Every agent reads them from here.

| Setting | Value | Used by |
|---|---|---|
| Minimum market cap (`min_mc_usd`) | 50000 | Scout |
| Minimum liquidity, USD | 25000 | Scout |
| Candidate cap per round | 5 | Scout |
| Minimum holders | 1000 | Skeptic |
| Top-10 holder cap, % of supply | 35 | Skeptic |
| Paper size per entry, USD | 100 | Trader |
| Open position cap | 5 | Trader |
| Take-profit, % move | 100 | Trader |
| Stop, % move | -40 | Trader |
| Time limit, hours | 24 | Trader |
| Round-trip cost assumption, % | 1 | Trader |
| Rounds per run | 1 | Orchestrator |

## The loop

`/swarm` runs one round. Each round has four gates. A round that fails a gate stops there.

1. **Target.** State the round's target before any tool call: "up to N candidates, up to M open positions, settings as in CLAUDE.md". Read `paper/trades.jsonl` and count open positions.
2. **Observe.**
   - Trader marks open positions first, so exits never wait on new entries.
   - Scout returns candidates.
   - Skeptic vets every candidate.
   - Trader opens paper positions for approved candidates only.
   Show each agent's output in full before the next step starts.
3. **Bounded repair.** When an agent's output is not valid JSON or breaks a rule above, send it back once with the exact problem. If the second answer still fails, drop that step for the round and record why. Never retry a third time. Tool errors get one retry inside each agent.
4. **Stop.** The round ends when any of these is true:
   - the Trader has written its lines,
   - the Scout returned `[]`,
   - the Skeptic vetoed every candidate,
   - open positions are at the cap and no exits fired,
   - a repair failed twice.
   End with a five-line round report: target, candidates found, approved, vetoed (with the top reason), and ledger lines written.

## Free tier and data key

The PumpPill MCP at `https://api.pumppill.org/mcp` needs no key and no account.
On it, Ignition momentum calls arrive after a delay, and the Skeptic reads the top-10 share from text.

Set `PUMPPILL_DATA_KEY` to a PumpPill data API key (pumppill.org/data) to unlock two things:
- the Scout reads the Ignition feed from `/api/partner/v1/signals/ignition`, live on Builder and Pro keys,
- the Skeptic reads `scan.top10_pct` as a number from `/api/partner/v1/tokens/{address}`.

Keys go in the environment only. Never write one into a file in this repo.
