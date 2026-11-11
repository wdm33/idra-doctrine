# IDRA Doctrine

The Invariant-Driven Reasoning Architecture (IDRA) doctrine.

## Current version

| | |
|---|---|
| File | `IDRA Doctrine.md` |
| Version | 1.1 |
| Status | Approved by the designated human authority on 2026-11-10 |
| sha256 | `b367ad21498038ac616a75fb8324dcd765265de803da020e4cf8ba18488789c4` |

`IDRA Doctrine.md` is the single authority for IDRA obligations (IDRA-4.7). Nothing else in this repository adds to, removes from or reinterprets it. The approval and change record is `~/.claude/methodology/idra-doctrine.toml`, and projects pin the version and sha256 in their `.idra-config.json`.

## Changing the doctrine

Every change is a new version: edit `IDRA Doctrine.md`, get human approval of the exact text, record the approval in `idra-doctrine.toml`, commit it here, and move the superseded text into `archive/` with its hash listed below.

## Layout

| Path | Contents |
|---|---|
| `IDRA Doctrine.md` | The current doctrine |
| `decisions/` | Design decision registers that fed into the doctrine |
| `archive/` | Superseded doctrine texts, kept for the record only |

## Decision registers

| File | Contents | sha256 |
|---|---|---|
| `decisions/IDRA_Decisions_DDR001-012.md` | DDR-001 to DDR-012. Version 1.1 incorporates DDR-001 to DDR-006 and DDR-008 to DDR-012. | `ae3486d083e67f4ea642879eeffa57338d95bf5b3cd2d4ffb61559dc79a1fbe3` |
| `decisions/IDRA_Design_Decisions.md` | DDR-001 to DDR-006 only; an earlier capture of the same register | `30b6567ade29f8e19920c5712a46301e829b3206f3942f592a441e04a2685609` |

## Archive

These files are retired and are not a source of IDRA obligations.

| File | Version | Status | sha256 |
|---|---|---|---|
| `archive/IDRA_Doctrine_1.1-draft.md` | IDRA 1.1-draft | Superseded by 1.1 on 2026-11-10. Byte-identical to `IDRA Doctrine.md` at commit 5f55d34. | `7d2fd0cc8aaee6796e0d6a68ed71ce69275db7fc04a9bb3772e81911b10c57ee` |
| `archive/IDRA_Doctrine_1.1-draft-superseded.md` | IDRA 1.1-draft, earlier text (formerly `IDRA_Doctrine_Current.md`) | Retired before the 1.1-draft above was agreed | `2931fada504450dd50c333c222f534cec1d664764ecff71d69635def0b4c03e9` |
| `archive/IDSE_Doctrine_v3.0.md` | IDSE 3.0 | Predecessor doctrine. Legacy IDSE projects use the copy at `/mnt/store/vadrant/docs/IDSE_Doctrine_v3.0.md`. | `7065ed7c5d24b12b27fb912d89bfdfd0542ef70cbf871ab668edbb7c773d3a17` |
