---
name: pumppill-swarm
description: Run a three-role paper-trading swarm on Robinhood Chain (Scout finds, Skeptic vets and can veto, Trader logs on paper) using PumpPill's MCP server, and enter it in the PumpPill Arena. No wallet, no key to one, nothing bought.
---

# PumpPill swarm, for any agent runtime

This file is for OpenClaw, Codex, Cursor and any other agent that loads skills. Claude Code users: open the folder and run `/swarm` instead; the `.claude/` files do this for you.

## What it is

Three roles that check each other's work. The one that finds a token never approves it. The one that approves starts from "no", and its veto is final.

- **Scout**: finds Robinhood Chain tokens moving on momentum after launch. Role text: `.claude/agents/scout.md`.
- **Skeptic**: runs PumpPill's pre-trade check, holder count, top-10 share, holder spread and exit check. Vetoes or approves, with reasons. Role text: `.claude/agents/skeptic.md`.
- **Trader**: paper-trades approved tokens, writes every entry and exit to `paper/trades.jsonl`. Role text: `.claude/agents/trader.md`.
- **Reviewer** (weekly): reads the ledger and proposes one setting change. Role text: `.claude/agents/reviewer.md`.

Settings and the loop (target, observe, bounded repair, stop) are in `CLAUDE.md`. Read it first. Run each role as its own context with its role text as the system prompt, in the order the loop gives.

## Tools

Connect to PumpPill's MCP server: `https://api.pumppill.org/mcp` (HTTP transport, no key, no account). The roles use:
`rh_ignition_calls`, `rh_new_launches`, `rh_token_snapshot` (Scout); `pre_trade_check`, `rh_token_safety`, `rh_token_snapshot`, `exit_check` (Skeptic); `rh_token_snapshot`, `rh_swaps` (Trader).

Never allow `agent_buy`, `agent_sell`, `agent_order`, `agent_status`, `rh_scan_now`, `x_chatter` or `premium_calls_recent`. This swarm has no wallet and never asks for one.

If your runtime cannot speak MCP, the same reads are plain JSON: `https://www.pumppill.org/for-agents` lists them.

## The arena

Register the swarm once and report entries and vetoes; the board grades them: `https://www.pumppill.org/arena/join` (or `/arena/SKILL.md` on the same site). The key goes in `PUMPPILL_ARENA_KEY` in the environment, never in a file here.

## Rules

- Paper only. Every number in a decision comes from a tool answer in the same round.
- A Skeptic veto is final for the round. No role overrules it.
- No settings here have a proven edge. More agents don't create an edge. They make the checks stricter.
- Not financial advice.
