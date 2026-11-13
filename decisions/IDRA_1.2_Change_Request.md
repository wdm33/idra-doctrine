# IDRA 1.2 change request

**Record status:** Approved by the designated human authority (wdm33) on 2026-11-12. Installed as `IDRA Doctrine.md` on 2026-11-12 after the tooling reconciliation (`~/.claude` ff6074d); the approved text is that file, and the draft is `archive/IDRA_Doctrine_1.2-draft.md`. It takes effect for each project when that project's pin moves.
**Opened:** 2026-11-12
**Draft:** `drafts/IDRA_Doctrine_1.2-draft.md`, sha256 `3ac0caaa60b4764101722acce8cac3340030f6e751781b05079116e2704a4b2d` (Claude ↔ Codex consensus, round 2; see `IDRA_1.2_Change_Request.consensus.md`)
**Approved text:** `drafts/IDRA_Doctrine_1.2.md`, sha256 `84d887aae0ae433daa48255366f2ab40931dcfeee283f4f1be0c9de17a1cdfab`; the draft with only its Status and Version lines changed
**Supersedes, on installation:** IDRA Doctrine 1.1, sha256 `b367ad21498038ac616a75fb8324dcd765265de803da020e4cf8ba18488789c4`

## Human decisions, 2026-11-12

| Question | Decision |
|---|---|
| Approve draft `3ac0caaa…` as 1.2 | Approved |
| Rule 3 intent sentence | Kept verbatim (Codex note 10 deferred to any later editorial amendment) |
| When 1.2 becomes the master | After Phase 2 tooling reconciliation, so pinned projects keep passing their pin check meanwhile |
| CVM compatibility | Update CVM now: CVM 1.1 (companion line IDRA 1.2) approved, effective with this installation |

## Why

Vadrant C001 has been through three closure reviews. In each round, both reviewers wrote hand mutants that the suite missed, even though the code at the reviewed revision was correct. Each such mutant became a BLOCK, a remediation slice and a full re-review. Three 1.1 rules combine to make closure an open-ended search:

- IDRA-3.2 requires each L2 and L3 check to fail on a deliberate violation.
- IDRA-3.7 item 7 makes any unmet "mandatory verification obligation" a BLOCK, without saying what makes an obligation mandatory.
- The methodology re-reviews the complete cluster after every correction.

1.2 makes an evidenced **enforcement argument** the unit of assurance. Tests, mutations, static analysis and reviews substantiate that argument. The four rules and their intent statements, human authority, IDRA-3.4, BLUE/GREEN/RED, and every identifier and section anchor are unchanged.

## Decisions

| DDR | Decision | Clauses |
|---|---|---|
| 013 | Enforcement argument is the unit of assurance | IDRA-3.1, Terms, App. A |
| 014 | Prevention before detection | IDRA-1.3, step E |
| 015 | Verification contract agreed at step C | IDRA-4.1, step C, D |
| 016 | Risk-appropriate challenge, no quotas | IDRA-3.2, IDRA-1.9 |
| 017 | Shared verification across invariants | IDRA-3.1 item 3 |
| 018 | Consequence-based findings, review boundary | IDRA-3.7 items 5–8 |
| 019 | Impact-based re-review | IDRA-3.7 item 11, step G |
| 020 | Finite closure, honest claims, continuations | IDRA-3.6, IDRA-3.3, IDRA-4.8 |

### DDR-013: The enforcement argument is the unit of assurance
- Every invariant has a mechanism and an argument for why it preserves the invariant within its approved scope and assumptions.
- The argument must account for every authoritative path that can affect the invariant. A shared boundary, such as the transition interface over encapsulated state, can cover many paths, but the premise that nothing bypasses it needs evidence.
- If coverage can't be established, the limitation is recorded and the claim stays unverified.
- The record is compact: `mechanism`, `argument`, `evidence`, `limitations`. `location`, `premises` and `bypass_paths` are added only where step C agrees the level or complexity needs them.
- IDRA-3.1 is retitled "Every invariant has a substantiated enforcement argument".

### DDR-014: Prevention before detection
- The order of preference is types and visibility, then exhaustive matching, then the transition boundary, then boundary validation, then runtime detection.
- A weaker form must say why a stronger one wasn't used.

### DDR-015: The verification contract is agreed at step C
- Cluster consensus covers each enforcement argument, its assumptions, and its verification obligations, including how critical premises of L2 and L3 arguments are challenged.
- Agreement on these authorizes implementation.
- A new record version may be reviewed on its diff. Unchanged parts keep their agreement and are not renegotiated.
- New information can still invalidate an agreed decision.

### DDR-016: Risk-appropriate challenge, without quotas
- IDRA-3.2 keeps its identifier and anchor; CVM cites it for fault injection. It is retitled "L2 and L3 enforcement arguments must withstand challenge".
- A deliberate violation is required where it substantiates a critical premise that stronger evidence does not already establish. Compiler-enforced visibility plus an architectural check, for example, needs no mutation harness.
- There is no number of mutants per invariant or per premise. Each challenge serves a stated purpose.
- The IDRA-1.9 level table is reworded to match. A level sets rigor, not a test count.

### DDR-017: Shared verification
- One mechanism or check may substantiate several invariants, provided it establishes the relevant part of each argument.
- No duplicate checks just because the identifiers differ.

### DDR-018: Consequence-based findings and the reviewer boundary
- Each finding has one of four consequences: `defect`, `unsupported_claim`, `improvement` or `uncertainty`.
- A BLOCK is a defect, or an unsupported claim: a mandatory premise lacks sufficient evidence, is contradicted by evidence, or depends on an enforcement path whose applicability isn't established. An unmet agreed obligation counts as an unsupported claim.
- An unsupported claim must name its premise. A surviving mutant alone is not one.
- A real defect always blocks, whether or not step C anticipated it.
- Reviewers evaluate the agreed arguments and mandatory evidence. Exploratory testing can't expand the agreed obligations unless it finds a defect or a material assurance gap.
- Claude and Codex classify findings. Only correctness disagreements, and matters reserved to the human, are escalated.

### DDR-019: Impact-based re-review
- Each remediation slice declares its impact, agreed at step D.
- The scope follows the impact:
  - evidence or documentation only: the affected evidence and claims;
  - local implementation with unchanged contracts: the affected invariants and their dependencies;
  - semantics, a security or trust boundary, or architecture: a full review of the affected architecture and the relevant integration.
- Both reviewers still review independently, each over the complete change since the last reviewed revision.
- If the impact is uncertain, the review is broadened.

### DDR-020: Finite closure, honest claims, continuation sessions
- Closure requires all of: passing ECs, satisfied agreed obligations, substantiated arguments (or recorded limitations, with the claim unverified and escalated), and no open BLOCK.
- Improvements are recorded as WARN test debt and do not block.
- Obligations are added to an open cluster only through a new cluster record version, driven by a defect, an unsupported claim or new information.
- IDRA-3.3: every claim states its assumptions and evidence limits, and is not presented as established beyond what its evidence shows.
- IDRA-4.8 gains item 5:
  - a continuation session keeps every recorded agreement, approval, finding and piece of evidence;
  - unrecorded artifacts from the interrupted session enter the records only verbatim, with their origin stated, as unreconciled inputs.

## Effect on active projects

| Project | State | Effect |
|---|---|---|
| vadrant | C001 open, on 1.1 | Pin move by continuation; C001 reconciled |
| ORTHOS | C002 closed | Pin move at next session start |
| idra-trial | C001 closed | Pin move at next session start |

- **Vadrant C001.** Round 3 (at 44aef8e) has not been recorded yet. A human-authorized continuation session records it first, under 1.1:
  - the two Claude BLOCKs: the veto never exercised with a record whose claim or commit differs, and precedence with several requirements;
  - Codex's overlapping BLOCK;
  - the empty-list completeness question, as an escalation;
  - the M010 uncertainty WARN.

  A copy is kept at `~/.claude/backups/2026-11-12-vadrant-C001-round3/` with `SHA256SUMS`. The reviewers' mutant logs under `/tmp` are already gone; each mutant is described exactly in the replies and can be reproduced. After the pin move:
  - each C001 detailed invariant gets an enforcement block, agreed by delta consensus;
  - every 1.1 finding keeps its original disposition, with the reassessment added beside it;
  - reclassifying any 1.1 BLOCK needs both reviewers **and** human authorization, recorded in `decisions.toml`. This applies to the migration only.
- **Methodology and tooling** must be reconciled before any project moves (Change control item 3): idra.md §4, §6, §7 and §9; the cluster, slice, implement-slice, commit-slice and close commands; the slice-implementer and idra-closure-reviewer agents; the templates; idra-lint and idra-export; the test fixtures; and the `~/.claude/CLAUDE.md` index, whose 3.1 and 3.2 titles change. The records and lint are extended; no new framework or register is added.
- **CVM 1.0** cites IDRA-3.1 and IDRA-3.2. Both identifiers and anchors remain, fault injection remains a recognized challenge method, and CVM-3.2's own requirement for fault-injection results is unaffected. CVM's header names IDRA 1.1, so a project adopting both needs an explicit compatibility decision under CVM's change control. Whether that decision updates the CVM header is for the human to decide; this request does not change CVM.

## Effective boundary

1.2 is installed as the master `IDRA Doctrine.md` (1.1 archived as `archive/IDRA_Doctrine_1.1.md`, `[doctrine]` in `idra-doctrine.toml` moved to 1.2) only after the Phase 2 tooling reconciliation is committed. Until then the master stays 1.1 and every project stays pinned to 1.1. After installation, 1.2 takes effect for a project when its pin moves, by a human decision recorded in its `decisions.toml`.
