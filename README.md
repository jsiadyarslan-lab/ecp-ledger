# ECP Registration Ledger

Append-only, hash-chained public ledger of ECP registration records (see the ECP protocol repository and docs/M3-R1-ARCHITECTURE-DECISION.md). This repository is the public anchoring target of the ECP protocol. The authoritative source for protocol identity and version resolution is `ECP-IDENTITY.json` in the protocol repository (protocol 0.7.0, schema 0.15.0).

## Repository layout

- `entries/NNNNNNNN.json` — chained ledger entries (ledger-entry schema)
- `records/ECP-REG-*.json` — full registration records (canonical bytes)
- `ANCHOR.json` — published checkpoint of the chain state; verify with `python tools/ecp_cli.py ledger-verify --ledger <this repo>` from the ECP repository.

Entries are append-only: no edits, no removals, no history rewrite, no force-push. NEVER place protected ground truth (or any answer-bearing content) in this repository.

## What the fingerprints mean

- Every `records/ECP-REG-*.json` file is digested with SHA-256 over its stored bytes; the digest is recorded as `record_hash` in the matching `entries/NNNNNNNN.json`.
- Every entry is itself digested with SHA-256 into `entry_hash`; entries are chained through `prev_entry_hash` (the genesis entry uses all zeroes).
- `ANCHOR.json` publishes the checkpoint of that chain: `head_entry_hash`, `entry_count`, and the protocol/schema versions.

## What an anchor proves — and what it does not

Anchored records prove only that:

- the referenced files, with exactly the recorded SHA-256 digests, existed at the time of the anchoring commit;
- those digests have remained unchanged since publication (any content change yields a different digest);
- the entries were appended in the recorded order.

An anchor does not prove any of the following, and nothing in this repository should be read as such proof:

- scientific priority, novelty, originality, or quality of any referenced content;
- authorship or legal ownership of any external work;
- the truth, correctness, or completeness of claims made inside referenced files;
- the real-world identity behind the publishing account.

Publication here is a timestamped integrity commitment, nothing more.

## Documented reference discrepancy (ECP-REG-000001)

The notes of `records/ECP-REG-000001.json` record a source-snapshot reference as `git_commit=081d3f3f07fa380d6356ecde0b3ad449486d70f9`. Verified against the source repository: `081d3f3f…` is the SHA-1 identifier of an annotated tag object, and the underlying commit it annotates is `691b85c71b3ee308f226685876c2834f39ffff4a` (2026-10-23). The `git_commit` label in the registration notes is therefore imprecise. Because the ledger is append-only and its history must not be rewritten, the historical entry is left unchanged and this discrepancy is documented here instead.

## Independent verification

This repository is public; verification requires no special access:

1. `sha256sum records/ECP-REG-000001.json` must equal the `record_hash` recorded in `entries/00000001.json` (`4bf78630f6c9a0e5fdecccee9069f3b8a34426f31912264f808ef8525509caa2`).
2. The `entry_hash` of `entries/00000001.json` must equal the `head_entry_hash` in `ANCHOR.json` (`d9b1514f031a2cd56c71a23aeb8e8c03c5a703434a1706333f9e601724c04414`).
3. The full chain can be re-verified with the ECP CLI: `python tools/ecp_cli.py ledger-verify --ledger <clone of this repo>`.
4. The anchoring commit for entry 1 is `dfe7c3c8478de6430935ca150b714aee18efb9fd` (2026-10-25T23:05:31Z).
