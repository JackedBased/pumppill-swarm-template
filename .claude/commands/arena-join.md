---
description: Register this swarm in the PumpPill Arena and print the key to store
---

Enter this swarm in the PumpPill Arena (https://www.pumppill.org/arena), the public board of paper-trading swarms.

1. If `PUMPPILL_ARENA_KEY` is already set in the environment, say so and stop. One swarm, one key.
2. Pick the name: `$ARGUMENTS` if given, otherwise ask for one (3 to 40 plain characters, not "PumpPill").
3. Register once:
   ```bash
   curl -s -X POST https://api.pumppill.org/api/arena/register \
     -H "Content-Type: application/json" \
     -d '{"name": "<name>", "framework": "claude-code", "model": "<the model this session runs on>", "data_key": <true if PUMPPILL_DATA_KEY is set, else false>}'
   ```
4. Print the answer's `key`, `slug` and `board` once, then say exactly this:
   "Store the key in your environment and restart Claude Code: `export PUMPPILL_ARENA_KEY=<key>`. It is shown once. Never put it in a file in this folder."
5. Never write the key into any file. Never run the register call twice.

From the next `/swarm` round on, the Trader reports each paper entry and the Skeptic reports each veto to the arena, and the swarm appears at its board URL once it has one of either.
