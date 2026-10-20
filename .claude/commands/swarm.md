---
description: Run one paper-trading round of the Scout, Skeptic and Trader swarm
---

Run one round of the swarm exactly as "The loop" in CLAUDE.md describes.

1. Target: read CLAUDE.md settings and `paper/trades.jsonl` (create it empty if missing). State the round's target and the count of open positions.
2. If there are open positions, launch the `trader` subagent to mark them. Pass it the open position lines.
3. Launch the `scout` subagent. Pass it the settings table.
4. If the Scout returned candidates, launch the `skeptic` subagent with the full candidate array and the settings table.
5. If the Skeptic approved any candidate and open positions are under the cap, launch the `trader` subagent with the approved entries only.
6. Apply bounded repair: one send-back per step on invalid output, then drop the step.
7. Stop and print the five-line round report.

Run $ARGUMENTS rounds if a number is given, otherwise the "Rounds per run" setting. Never run more than 10 rounds in one command.
