# Ovum studies ledger

This is the public, append-only proof of when each Ovum study was registered, and with what. It holds **digests only**.
No study content, method, file name or capability name is ever written here. The studies themselves stay private, in
the lab's own repository.

**Why it exists.** Ovum claims nothing unless the study behind it was registered before it ran. For studies, git
history pushed to GitHub is the custody ledger (operator ruling, 2026-11-09). This repository makes that history public
and hard to rewrite:
- `main` has a ruleset that blocks deletion and force-pushes, with no bypass;
- anyone, including archives, can watch or copy it.

A copy kept by someone else cannot be quietly changed.

**What it proves.** A record proves that the digested bytes existed no later than the moment the record was pushed and
seen. It does not prove when they were written, and git commit dates are set by whoever commits. Each Ovum paper's chain
of custody says which of its links rest on this ledger.

## The record format

`ledger.tsv`: the schema line `ovum.studies.ledger.v1` first, then one record per line, tab-separated. Lines starting
with `#` are comments. A reader skips any column after the last it knows, so a column added later is invisible to it.
Columns are only ever appended.

| Column | Holds |
|---|---|
| `seq` | the record's number, 1 upward, never reused |
| `study` | the study's id (`v57`), nothing more |
| `kind` | `registration`, `amendment`, `results`, `receipt`, `correction`; with a `backfill-` prefix for a record entered after the fact from older history |
| `sha256` | the SHA-256 of the file's bytes exactly as committed in the lab's repository, or of the salt and the bytes for a salted record |
| `salted` | `no`, or `yes` when the item is short enough to guess, such as a verdict line. The salt is kept privately and published with the result, so anyone can then check the digest |
| `source` | `<repo>@<commit>`, the private commit that holds the file. It opens nothing to a reader without access |
| `receipt` | the Central Compiler receipt (`RCP-COMPILER-...`) for a run, or `-` |
| `note` | a few words with no method in them, or `-` |

## The rules

1. **Append only.** A line is never edited or removed. A mistake is corrected by a new `correction` record naming the
   `seq` it corrects.
2. **Registration first.** A study's `registration` record is pushed before the study runs. A run with no earlier
   registration record is not a study result.
3. **The registered lines survive.** Later records may only add: amendments and results. The lab's checker verifies that
   every registered line is still present unchanged.
4. **Backfill says so.** Studies registered before this ledger existed are entered as `backfill-registration` from the
   lab's git history, with the original commit as `source`. Their proof of time rests on that history, not on this
   ledger, and Ovum's papers say so.

Owner: the Ovum study system (lucid-juniper). Commits are made by the Hub's Central Git under agent names.
