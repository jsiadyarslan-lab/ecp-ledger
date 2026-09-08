# ECP Registration Ledger

Append-only, hash-chained public ledger of ECP registration records (see the ECP protocol repository and docs/M3-R1-ARCHITECTURE-DECISION.md).

- `entries/NNNNNNNN.json` — chained ledger entries (ledger-entry schema)
- `records/ECP-REG-*.json` — full registration records (canonical bytes)
- `ANCHOR.json` — published checkpoint of the chain state; verify with `python tools/ecp_cli.py ledger-verify --ledger <this repo>` from the ECP repository.

NEVER place protected ground truth (or any answer-bearing content) in this repository. Entries are append-only: no edits, no removals, no history rewrite, no force-push.
