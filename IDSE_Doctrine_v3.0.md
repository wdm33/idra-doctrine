# Invariant-Driven Software Engineering

## Doctrine v3.0: A Working Doctrine for Correctness-First, AI-Assisted Development

**Version:** 3.0 · **Status:** Working draft · **Date:** 7 November 2026

**Operational companion:** `~/.claude/methodology/idse.md` describes how agents carry out this doctrine. It cites this document and never restates its rules. If the two disagree, this doctrine governs.

**Purpose.** A doctrine for designing, implementing, reviewing, verifying and maintaining software, especially software that is mostly generated or maintained by AI agents.

**Status of claims.** This is a working doctrine. It does not claim that any particular system is formally verified.

### Foundational statement

> Software should be constructed as a system of explicitly constrained state transitions, governed by mechanically enforced invariants, implemented through a deterministic functional core, isolated from environmental effects by an imperative shell, and advanced only when reproducible evidence supports its correctness.

### Executive operating rule

> Requirements establish what a system must accomplish. Invariants define what must remain true. Transition contracts define how authoritative state may change. Implementations realize those transitions. Independently reproducible evidence determines which correctness claims may be promoted.

### Premise for AI-assisted work

An AI-generated implementation is a candidate for acceptance, not evidence of its own correctness.

### Contents

- Part I: Constitution and terms (§1–§3)
- Part II: Invariants (§4–§11)
- Part III: Architecture and state transitions (§12–§19)
- Part IV: Authority and agent governance (§20–§26)
- Part V: Verification and evidence (§27–§30)
- Part VI: Workflow (§31–§35)
- Part VII: Profiles and the protocol track (§36–§39)
- Part VIII: Artifacts, worked example, adoption (§40–§43)
- Appendix A: Reference implementation

---

# Part I: Constitution and terms

## §1 Constitution

These rules bind every project, subject to its profile (§36). They are written to be loaded word for word into agent context.

1. **Invariants come before features.** Before planning any work, name what must always be true, what must never be possible, what must stay identical across executions, and what must be replay-equivalent. A feature is a composition of legal transitions. If it can't be expressed as a pure transformation from canonical input to canonical output, it isn't understood yet.
2. **Invariants are defined by human approval.** The agent drafts, Claude and Codex review to consensus, and the human approves each invariant from a line-by-line batch. Approving a line defines that invariant. Only human approval can redefine, weaken or retire one.
3. **Three work units, one ID grammar.** Work is organized only as invariants, clusters and slices. Every project identifies them as `FAM-NNN`, `CNNN`, `CNNN-SNN` and `CNNN-CENN` (§6–§7).
4. **Illegal states must be unrepresentable.** Encode state machines in types. Replace booleans with sum types and nullable states with explicit variants.
5. **Transitions are explicit and total.** `transition(state, command, context) -> Result<(new_state, effects), rejection>`. No partial mutation. No ambient side effects.
6. **The authoritative core is deterministic.** No wall-clock reads, randomness, uncanonicalized floating point, unordered iteration, OS-dependent ordering or locale-dependent logic. Nondeterminism enters only as explicit context.
7. **Replay is the integration contract.** Replaying the same canonical inputs must reproduce the same authoritative bytes. Replay failure is a correctness failure.
8. **Closed surfaces, versioned evolution.** Authoritative boundaries are closed and enumerable. Semantic change is versioned and activates at a deterministic boundary. Mixed semantics execution is forbidden.
9. **Fail fast on invariant risk.** Errors that can affect authoritative state, replay or consensus halt deterministically. Retries live only in the shell.
10. **Functional core, imperative shell.** The core defines truth and the shell moves bytes. The core is a crate of its own with no dependencies, and the shell depends on it (§12). Dependencies flow inward.
11. **Mechanical enforcement over convention.** If an invariant cannot be checked by types, tests, lints or CI, it is not protected. The language, the layout and the tools are chosen to enforce the doctrine, and the doctrine never bends to fit them. Every project's software is written in Rust (§12).
12. **Consensus before implementation.** No cluster or slice proceeds until Claude and Codex agree on its document. Contested items go to the human (§21).
13. **Evidence decides promotion.** Only Supported claims may be represented as established. Evidence refers to an immutable revision, and residual uncertainty is always disclosed.
14. **Witnesses must bite.** A critical invariant's witness isn't strong just because correct code passes it. A representative violation must fail it, for the intended reason (§28).
15. **Controlled remediation.** Failed evidence permits repair. It never permits weakening the invariant, the witness, the cluster exit criterion or the expected outputs.
16. **Complete work only.** A cluster closes only when every cluster exit criterion passes mechanically. No carry-forward.
17. **Stop and escalate** when an invariant conflicts with a requirement or another invariant, a new trust or finality assumption appears, a boundary must move, verification can't run, or a refactor changes behavior (§23).
18. **Profiles set rigor.** A missing configuration or missing profile means `strict`. Only the human lowers a profile.
19. **Agent conduct.** No AI attribution in commits, pull requests, releases or issue comments. No AI-style source comments. No attribution-scrubbing scripts or hooks in repositories. No work deferred to agents scheduled to run later (§25).

## §2 Terms and distinctions

| Term | Meaning |
|---|---|
| Authoritative state | Information whose meaning governs valid future decisions. It is distinct from caches, logs, indexes and projections. |
| Invariant | A falsifiable property that must hold in every reachable authoritative state or across every permitted transition. It exists once defined (§5). |
| Cluster | A planning unit built around one primary invariant and closed by mechanical cluster exit criteria. |
| Slice | The smallest mergeable change against defined invariants. It leaves the system fully correct. |
| CE (cluster exit criterion) | A named test or CI check that must pass before its cluster can close. |
| Acceptance test | A named test or check that a slice must pass. It is never called a CE. |
| Transition | A rule mapping prior state, command and context to an accepted new state with declared effects, or to a rejection. |
| Context | Explicit values that would otherwise be ambient: logical time, rule version, supplied entropy, ordering keys. |
| Effect | A declarative description of work for the shell. It is data, never a closure. |
| TCB color | BLUE (deterministic, authoritative core), GREEN (deterministic glue, non-authoritative), RED (nondeterministic shell). |
| Determinism | Equivalent canonical state, inputs, rule version and ordering produce equivalent authoritative outcomes. |
| Finality | The condition under which an outcome can't be superseded under the stated protocol and trust model. |
| Witness | The assertion that claims to protect an invariant. |
| Evidence | A reproducible record that supports a bounded claim, with its assumptions and limits stated. |
| Model | A TLA+ formal model on the protocol track (§38). |

## §3 Normative language

MUST, MUST NOT, SHOULD and MAY are used as in RFC 2119. A rule without a profile qualifier applies under `strict`, and §36 says what each other profile relaxes. The conduct rules (§25) and the identifier rules (§6–§7) apply in every profile.

---

# Part II: Invariants

## §4 What an invariant is

An invariant is falsifiable. It names a property and admits a concrete counterexample that would violate it. "After an authority instance is consumed, no later accepted transition can consume that same instance again" is an invariant. "The system is secure" is an aspiration, and that isn't enough.

Requirements establish the capabilities a system is meant to have. Invariants set the bounds within which those capabilities are legal. Every feature preserves every defined invariant. If a feature would weaken an invariant, either a human redefines the invariant or the feature is rejected.

Design documents describe what a system is for and how it should work. They inform Define but don't bind. Where one disagrees with a defined invariant, the invariant holds until a redefinition changes it.

Each invariant is recorded in the project's invariant registry, which is append-only: no entry is ever removed or renumbered, and no approved text is ever lost. A redefinition replaces an entry's fields and keeps the text it replaces in the entry's record of that redefinition (§5). An assumption that several invariants rely on is recorded once in the registry, and each of them cites it. An entry records:
- ID and name (§7)
- statement and counterexample
- primary family and secondary families (§6)
- scope, assumptions and dependencies
- enforcement locality (§8)
- criticality (§9)
- enforcement: compile-time, runtime, tests and CI checks
- definition record: the approving revision, plus the revision and nature of each redefinition
- lifecycle: the introducing cluster, the clusters that strengthened enforcement, and deprecation
- claim status and evidence records (§10)

An entry's tests, CI checks and evidence only grow. Removing one requires a recorded justification.

## §5 Defining and redefining

- An invariant exists only once it has been defined, and only human approval of an agent's draft defines it.
- The agent drafts, Claude and Codex review to consensus (§21), and the human approves, rejects or amends each line of the resulting batch (§32). Approving a line defines that invariant.
- Redefinition follows the same path. That covers strengthening the meaning, weakening, retiring or materially reinterpreting an invariant. Agents may propose a redefinition, analyze conflicts, show counterexamples and estimate consequences. They may not apply one.
- Changing or retiring an assumption is a redefinition of every invariant that relies on it. The human approves it once, and each of those invariants records it.
- Evidence recorded before a redefinition stays in the entry, but no longer supports the claim. The claim is Unresolved until evidence for the redefined meaning is recorded (§10, §11).
- There are two kinds of change:
  - **Enforcement strengthening** adds a test, CI check or evidence record to an invariant without changing what it says. Agents do this on their own.
  - **Semantic change** alters what the invariant requires. It is a redefinition, and the registry entry records the revision that approved it.
- A retired invariant keeps its ID and its entry. It is marked deprecated, with the revision that retired it.

## §6 Invariant family taxonomy

These 14 families are the closed, global classification for every IDSE project.

| Code | Family | Definition |
|---|---|---|
| SEC | Security | Authentication, authorization, isolation, secrets, cryptographic verification, trust boundaries, and abuse prevention. |
| STR | Structural | Required system structure, component relationships, architectural boundaries, and structural constraints. |
| SAF | Safety | Prevention of prohibited states, unsafe outcomes, and violations of fail-closed behavior. |
| LIV | Liveness | Required progress and eventual completion under explicitly stated assumptions. |
| VAL | Validity | Rules governing acceptable inputs, objects, states, and transitions. |
| ORD | Ordering | Required sequencing, precedence, and ordering of events or transitions. |
| FIN | Finality | Conditions under which authoritative decisions or transitions become final. |
| CON | Conservation | Preservation and accounting of quantities, resources, balances, and other conserved properties. |
| DET | Determinism | Reproducible computations, deterministic transitions, and replay consistency. |
| DUR | Durability | Preservation of committed state and required evidence across failures. |
| REC | Recovery | Restoration of valid system state and behavior following failures or interruptions. |
| EVO | Evolution | Controlled changes to governing rules, interfaces, permitted behavior, and closed extension surfaces. |
| OBS | Observability | Availability of trustworthy information needed to inspect behavior, diagnose failures, and support verification. |
| PRC | Process Assurance | Mechanically enforced engineering procedures, evidence requirements, and promotion conditions. |

**Governing rules**

1. Every invariant belongs to exactly one primary family.
2. Additional family classifications are permitted as metadata.
3. Family definitions are identical across all IDSE projects.
4. Invariant IDs are permanent and must never be reused.
5. Numbering is maintained independently for each family within each project.
6. Cross-project references include the project namespace, such as MIRAGE:SEC-001.
7. Adding, removing, or redefining a family requires an approved IDSE doctrine revision.
8. Existing identifiers must be preserved through an explicit migration and traceability record.

**Choosing the primary family**

- **Security first.** Every security-related invariant takes `SEC` as its primary family, even when another family would also fit. An invariant is security-related when it exists to defend against an adversary. Invariants that must hold with no adversary present, such as conservation, finality and determinism, keep their own family, even where a violation would be exploitable. Other families that fit are recorded as secondary families (rule 2).
- Otherwise, the primary family is the one whose definition the invariant's statement most directly meets. The subsystem the invariant concerns goes in its scope, never in its family.
- `SEC`, `VAL`, `ORD` and `FIN` are separate families because authorization, validity, ordering and finality are independent properties (§8).

## §7 Identifiers

| Thing | Format | Example |
|---|---|---|
| Invariant | `FAM-NNN` plus a CamelCase name | `FIN-001 SingleConsumption` |
| Cluster | `CNNN` | `C003` |
| Slice | `CNNN-SNN` | `C003-S02` |
| Cluster exit criterion | `CNNN-CENN` | `C003-CE01` |
| Cross-project reference | `PROJECT:ID` | `MIRAGE:SEC-001` |

Validation patterns:

```text
invariant  ^(SEC|STR|SAF|LIV|VAL|ORD|FIN|CON|DET|DUR|REC|EVO|OBS|PRC)-[0-9]{3}$
name       ^[A-Z][A-Za-z0-9]+$
cluster    ^C[0-9]{3}$
slice      ^C[0-9]{3}-S[0-9]{2}$
CE         ^C[0-9]{3}-CE[0-9]{2}$
```

- Numbers are assigned in sequence within a family at the moment of human approval, so rejected drafts use up no number.
- An invariant's ID, primary family and name never change once it is defined. IDs are never renumbered or reused, and a deprecated invariant stays listed.
- An invariant's name is unique within its project and is used unchanged as its property name in any formal model.
- Variants of a property are separate invariants or separate model configurations, never ID suffixes.
- The project namespace is the project's name in uppercase.
- Clusters are numbered in sequence within the project. A cluster's CEs and slices are numbered within that cluster.

## §8 Scope and the four distinctions

Authorization (who may act), validity (whether conditions hold), ordering (which event comes first) and finality (which outcome is irreversible) are distinct. None of them implies the others. A review or proof that establishes one establishes nothing about the rest.

Each invariant's enforcement is local (one transition), transactional (one atomic commit) or distributed (across processes or replicas). A local guarantee doesn't establish a system-wide one, and it doesn't establish a distributed one either (§16).

## §9 Criticality

Each invariant is either `critical` or `standard`, and criticality is part of its definition. Critical invariants carry discriminating mutation witnesses (§28) and need fresh evidence at every promotion (§11). `SEC` invariants are critical by default.

Surfaces that usually warrant `critical`:
- canonical serialization and commitment folds
- finality, authority and signature verification
- ordering semantics
- replay and restart equivalence
- durability barriers
- one-terminal-outcome rules
- history uniqueness
- scope and governance gates
- fail-closed resource rules

## §10 Claim status and evidence

Every invariant carries a claim status:

| Status | Meaning |
|---|---|
| Supported | Reproducible evidence meets the declared acceptance obligations within the stated assumptions. |
| Partial | Some obligations are met; others are not yet established. |
| Falsified | A counterexample demonstrates a violation. |
| Unresolved | The evidence isn't enough to decide. |

An invariant's declared acceptance obligations are the ones the cluster enforcing it declares for it: its cluster exit criteria and the acceptance tests of every slice planned for it, plus a discriminating witness if it is critical (§28). Its claim can't be Supported until every slice planned for it has merged.

Only Supported claims may be represented as established. A placeholder, a successful compile or an unverified assertion is not evidence.

Evidence is recorded on the invariant itself, as append-only records. Each record holds:
- the revision (an immutable commit)
- the command and environment
- the expected and observed result
- digests of any artifacts
- the limits of the claim

Agents append evidence and update claim status from results. They never alter the defined meaning of the invariant. Failed evidence is kept as a diagnostic record and is never deleted.

Enforcement status and claim status are separate axes. Enforcement status (enforced, partial, declared, deprecated) says whether a mechanism exists. Claim status says whether evidence supports the invariant. An invariant counts as enforced only when its claim is Supported at the current revision.

## §11 Evidence freshness and change impact

Evidence from an earlier revision isn't automatically valid for the current one. Every promotion works out which invariants the change could affect and re-establishes their evidence at the new revision. A refactor must also show behavioral preservation against the most recent Supported baseline (§19).

---

# Part III: Architecture and state transitions

## §12 Functional core and imperative shell

The deterministic functional core owns authoritative decisions: canonical types, validation, authority evaluation, transition guards, canonical ordering inputs, next-state computation and declarative effects. The imperative shell owns external interaction: network, storage, clocks, processes, environmental observation, effect execution, retries and recovery orchestration.

- The core defines truth. The shell moves bytes. It does not define meaning.
- Dependencies point inward, and there is no circular dependency between core and shell. The core knows nothing of its runtime environment and can be tested without a live database, clock or network.
- The core MUST NOT import networking, filesystem, async-runtime, clock or randomness facilities.
- **Rust, laid out for the compiler.** Every project's software is written in Rust. From its first cluster, in every profile, a project is a Cargo workspace laid out so the compiler holds the boundary:
  - The core is a crate of its own: `#![no_std]` with `alloc`, `#![forbid(unsafe_code)]`, no dependencies other than its tests' dev-dependencies, and no build script. Networking, files, processes, threads, clocks, the environment and unordered collections then don't exist in the core, however they are named, and the build fails on them.
  - The shell is the root package. It depends on the core by path, even while it does nothing else, and Cargo rejects a dependency back. It sees only what the core makes public, so private fields and constructors keep it from building or changing core state except through the transition.
  - What the compiler can't rule out, such as floating point, global state and panics, is checked by a scan of the core in the project's CI.
  - Scripts and harnesses that only drive the toolchain, such as a CI entry point or a mutation harness, are GREEN and may be written in another language.
- The shell MUST NOT:
  - modify core state directly
  - construct semantic types unsafely
  - bypass canonical validation
  - introduce new semantic state variants
  - reinterpret core outputs
- Every module carries a TCB color. No RED behavior may appear in BLUE code, and GREEN code must not affect authoritative outputs. If a module's color is ambiguous, it is resolved before work on that module begins.
- Which paths are BLUE is a boundary decision, and the human owns it (§20).

## §13 The transition contract

```text
transition(state, command, context) -> Result<(new_state, effects), rejection>
```

- `state` is an immutable canonical value and `command` is canonical input. `context` carries every value that would otherwise be ambient. `effects` are declarative, and `rejection` is explicit, structured and deterministic.
- Transitions are total: every input yields either a new state with effects or a rejection. No partial mutation. No ambient side effects.
- Illegal states must be unrepresentable. Encode state machines in types, use sum types instead of booleans, explicit variants instead of nullable states, and markers for phase distinctions. Invalid states should not compile.
- A transition contract states:
  - valid and invalid commands, preconditions and guards
  - authorization, conflict handling and ordering
  - which changes commit atomically, what is reversible and what is final
  - rejection semantics and effects
  - idempotency and deduplication under retries and duplicates
  - recovery after restarts, interrupted writes and migrations
- Canonical authoritative state is identified separately from derived caches and projections.
- The contract lives in the slice document that introduces or changes the transition (§34). The transition's types and tests enforce it.

## §14 Determinism

Any component that defines semantic truth is deterministic. The authoritative core forbids:
- wall-clock reads
- randomness
- floating point, unless strictly canonicalized
- collections with nondeterministic iteration order
- OS-dependent ordering
- locale-dependent logic
- unsafe global mutation

Every source of nondeterminism is either removed or supplied as explicit context.

The requirement is observable determinism. Internal mutation and efficient imperative algorithms are allowed as long as the core's observable semantics stay deterministic. Under `strict`, tripwires reject the forbidden constructs in BLUE paths mechanically, failing the edit or the build. Banning them outright is cheaper and more reliable than proving each use unobservable. A path that genuinely needs a forbidden construct is not BLUE. Reclassifying it is a boundary decision for the human, never a workaround for an agent.

## §15 Effects and delivery

Effects are declarative data, not closures, and contain no embedded logic. Examples: persist these bytes, broadcast this message, schedule this timeout, emit this event.

The canonical interaction loop:

1. The shell receives external input and canonicalizes it.
2. The shell calls the core transition.
3. The core returns the new state and effects.
4. The shell executes the effects and feeds their results back as canonical input where needed.
5. The transcript is persisted for replay.

For every effect, the shell defines execution timing, retries, deduplication, handling of delivery failures and crash boundaries. Where needed it uses durable outboxes, idempotency keys or equivalent protocols. Committing state and delivering an effect are separate steps. A transition may be committed while its effect is still pending, and the system must account for that state explicitly.

## §16 Replay, durability and distributed limits

Replay is a correctness contract. The same canonical starting state, ordered accepted inputs, versioned rules and recorded environmental facts must reconstruct the required authoritative result. Replay failure is a correctness failure. Replay covers canonical input bytes, version-gating state and deterministic transitions. The shell may persist transcripts but may not alter replay semantics.

Equivalence is byte-identical by default, and always for canonical committed or persisted bytes. Semantic equivalence is allowed only where a slice contract declares it in advance and states what is compared. Integer rounding, serialization, iteration order, time interpretation and migrations are all fixed explicitly.

Local determinism does not prove distributed consensus. For a distributed system, state the mechanism and assumptions that establish ordering and convergence, including safety and liveness under failure. Matching progress counters or a successful startup is not evidence of matching authoritative state.

## §17 Closed semantic surfaces and versioned evolution

Authoritative boundaries are closed and enumerable:
- versioned message enums
- explicit activation boundaries
- no runtime negotiation of meaning
- no plugin-style extension in authority paths

Adapters reduce external inputs to canonical forms before the core processes them.

Semantic change carries a version identifier and activates at a deterministic, replay-derivable boundary that is canonically recorded. Upgrades preserve replay compatibility. Mixed semantics execution is forbidden. The shell may coordinate a rollout but cannot soften version rules.

## §18 Failure model

Core errors are deterministic, explicit and structured. Shell failures may retry, back off or reconnect, but must not alter authoritative results. An error that could affect authoritative state, replay equivalence or a consensus outcome halts deterministically. Retries and adaptive recovery belong in the shell unless their behavior is canonically captured.

A runtime halt is a design rule for the running system. A blocked promotion (§30) is a rule for the engineering process. Both apply.

## §19 Behavioral preservation

A change that is not an approved semantic change must preserve established authoritative behavior. Refactors and optimizations are carried out as maintenance slices. A maintenance slice compares the old and new implementations on canonical inputs and states, against the most recent Supported baseline. It uses regression tests, differential execution, deterministic replay or stronger methods, in proportion to the contract.

Contract-required canonical bytes must match exactly, and any permitted semantic equivalence is declared in advance. A discovered difference is a potential redefinition, not an acceptable refactoring. The slice stops and escalates.

---

# Part IV: Authority and agent governance

## §20 Human authority and bounded autonomy

| Agents may act on their own | Human approval is required |
|---|---|
| Draft invariants, cluster documents and slice documents | Define or redefine an invariant (§5) |
| Implement agreed slices and refactor within them | Change the established semantics of authoritative state or of a promoted transition |
| Optimize algorithms while preserving semantics | Move the core/shell boundary (which paths are BLUE) or change the profile |
| Add justified internal abstractions | Relax a cluster exit criterion or an acceptance obligation after consensus has set it |
| Create and run tests, record evidence, update claim status | Resolve an item that ended without consensus (§21) |
| Diagnose and fix defects | — |

Nobody promotes an unverified change, and no agent claims completion that the evidence doesn't support. An agent that finds a genuine contradiction or an unimplementable requirement reports it and proposes options. It never rewrites the invariant, the cluster exit criteria or the slice's acceptance tests to fit its implementation.

Human authority doesn't mean a human supervises every action. Routine engineering within defined invariants and agreed documents goes ahead without human sign-off. Whether commits also need human confirmation is a project policy.

## §21 Consensus review

Consensus review gates three documents: the Define batch (§32), each cluster document (§33) and each slice document (§34).

- Claude drafts and Codex reviews. The two reviewers come from different model families, so that one family's blind spots aren't mistaken for agreement.
- Each critique is resolved either by a revision or by a rebuttal the other reviewer accepts. Consensus is reached when neither reviewer has an open critique.
- Rounds are bounded, five by default. The bound counts the rounds since the document last reached consensus, so revising an agreed document starts a new count. An item still contested at the limit goes to the human marked "no consensus", with both positions stated. Disagreement is never hidden, averaged or quietly dropped.
- Each round is recorded alongside the document it concerns, so consensus is a checkable fact, not an assertion.
- Consensus is not human approval. It gates cluster and slice documents and prepares the Define batch. Only human approval defines an invariant.

## §22 Controlled remediation

An agent may diagnose failed checks, amend the implementation, add regression tests and rerun verification. Failed required evidence blocks promotion and any work that depends on it.

Evidence cannot be made green by removing the relevant assertion, loosening the invariant, relaxing a cluster exit criterion, or modifying expected outputs, golden files or replay corpora. Changing any of those needs consensus review, plus human approval if the change alters an invariant's meaning. Counterexamples are kept.

Unrelated work may continue only where dependency and integration boundaries make the failed claim irrelevant to it.

## §23 Stop and escalate

Work stops and goes to the human when:
- a defined invariant appears to conflict with a requirement or with another invariant
- correctness would depend on a new trust, ordering or finality assumption
- the work would change authoritative state semantics, the core/shell boundary or the profile
- a slice needs an invariant that isn't defined yet, or a change to one (return to Define)
- verification that is needed can't be run, or keeps failing
- a maintenance slice changes observable authoritative behavior
- the agent can't tell a defect from an ambiguity in the defined invariants
- consensus review ends without consensus
- a security finding can't be fixed locally and turns out to be a design issue

If a requirement is ambiguous, stop and ask. If an invariant can't be enforced mechanically, don't approximate it.

## §24 Complexity governance

Prefer direct, inspectable transition logic in which each authoritative decision has a single owner. Reject redundant state, duplicated decision paths, unnecessary frameworks and speculative extension points. An added abstraction, dependency or indirection must show a concrete benefit to correctness, operations, performance or maintainability.

A slice implements exactly what it states: no helpful extra refactors, no conveniences, no preparation for future behavior. Complexity metrics are diagnostic signals, not a substitute for judging the contract.

## §25 Agent conduct

These rules apply in every profile.

- No AI attribution in commits, pull requests, releases or issue comments. That means no co-author trailers naming an AI, no "generated with" lines and no AI vendor addresses.
- No AI-style source comments, such as "added by", "AI:", "generated implementation" or "note: this fixes the bug from earlier". Comment only where the reason isn't obvious.
- Attribution enforcement lives outside the repository. Repositories carry no attribution-scrubbing scripts or hooks.
- Work is never deferred to agents scheduled to run later.

## §26 Handoff

A replacement agent must be able to recover the current contracts without relying on chat history. The repository carries:
- the invariant registry, with claim statuses and evidence
- the cluster and slice documents
- the grounding documents (§40), with TRACEABILITY as the view of which claims are Supported, Partial, Falsified or Unresolved
- reproduction commands
- the next slice

The codebase, not the agent's narrative, must carry the durable contract.

---

# Part V: Verification and evidence

## §27 Mechanism selection and the verification portfolio

For each invariant:
1. First check whether the compiler can enforce it: Rust's types, visibility and exhaustive matching, and the core crate's lack of a standard library and dependencies (§12).
2. Use runtime guards where the relevant facts only arise at execution or cross a trust boundary.
3. Verify the chosen mechanism independently.

The layers complement each other. They are not interchangeable.

| Layer | Examples | Limits |
|---|---|---|
| Compile time | Private constructors, sum types, ownership, exhaustive matching, a core crate without std or dependencies | Doesn't establish semantic correctness, crash safety or global finality |
| Runtime | Preconditions, conservation checks, admission guards, idempotency | The guard can be wrong, and infrastructure or concurrency can violate its assumptions |
| Verification | Properties, state machines, replay, fault injection, differential tests, model checking, proofs | Coverage and assumptions must be explicit; passing tests don't prove every case |

Choose the verification portfolio from the invariant's risk, scope and criticality, never from a test-count target. The strength of the evidence should match the claim's complexity, risk and cost of failure. The portfolio can include:
- review and type constraints
- unit examples
- property-based and state-machine tests
- negative and adversarial cases
- replay and differential comparison
- fault injection
- model checking, and formal proofs where practical

At a minimum, CI enforces:
- no I/O or environment access in core modules
- dependency direction
- canonical serialization coverage
- replay equivalence
- deterministic test runs

If an invariant cannot be checked by types, tests, lints or CI, it is not protected.

## §28 Witness strength and mutation accountability

A load-bearing invariant witness is not strong merely because correct code passes it. For a critical invariant, show that a representative violating implementation fails the witness, and fails it for the intended reason.

The obligation, in order:

```text
invariant
  → witness                     the assertion that claims to protect it
  → applicability proof         the mutated program still reaches that check
  → representative mutation     a violation a real implementation could contain
  → mutation-applied assertion  proven changed on disk, not assumed
  → expected witness failure    the named witness fails, for its own reason
  → restore byte-exact          the tree is bit-identical afterwards
```

Witnesses come in three classes:
- **basic**: the correct implementation passes
- **discriminating**: a representative violation fails
- **independently discriminating**: the intended assertion itself distinguishes the mutation, not an earlier guard or a redundant assertion elsewhere

When two witnesses catch the same mutation, record the overlap; never assume independence. Where a layer is deliberate defense in depth and no mutation can exercise it on its own, declare that redundancy at the site.

Mutation coverage follows invariant criticality, not line coverage.
- For load-bearing invariants, a slice SHOULD include at least one representative violating mutation.
- Critical invariants MUST have discriminating mutation coverage, unless the project records why no meaningful mutation can exercise that enforcement layer on its own. "On its own" means with that witness run alone: the record holds only if no representative mutation that builds makes that witness fail for its intended reason. Another check that also catches the mutation is an overlap to record, not a reason. A mutation the harness can't apply is a harness defect, not a reason.
- A mutation result counts only when the mutation is proven applied, the intended enforcement path is actually reached, the expected witness fails for the intended reason, and the repository is restored byte-for-byte afterwards.

The mutation harness is governed infrastructure. It must:
- identify its pre-image by content
- prove the bytes changed
- keep mutants valid where validity matters
- confirm the intended check actually executes
- report unexpected failures instead of filtering them out
- restore the original bytes exactly
- verify cleanup independently of the step that performed it

Some false results look exactly like healthy runs, and each one is a harness defect:
- a malformed mutant that fails on configuration
- a textual mutation that matched nothing
- a failure that comes from an earlier layer
- a witness that never executes
- scratch files escaping into the repository
- a test that hangs on open input

A harness that cannot prove it mutated anything cannot prove anything at all.

## §29 Review

Each slice, and each cluster at close, goes through one review step carried out by two independent reviewers:

- **Architecture review**: core/shell direction and TCB colors, determinism, replay equivalence, closed surfaces, fail-fast behavior, type-level enforcement.
- **Security review**, anchored on the `SEC` invariants:
  - unsafe assumptions about input, identity, trust, ownership or ordering
  - abuse cases
  - injection and unchecked deserialization
  - secrets in source and unsafe code
  - panics on attacker-controlled paths
  - at cluster scope: one slice violating another's invariant, authorization bypass, malformed input crossing slices, races and stale state, denial of service, data leakage, missing negative tests

Before a pull request or release, a further security review runs on the full pending diff.

Findings are either BLOCK (high or critical) or WARN. A BLOCK finding halts promotion. At slice level the fix goes into the same slice; at cluster level it becomes a new slice. A finding that can't be fixed locally is a design issue and is escalated (§23).

Severity is graded consistently from round to round. A re-review sees the earlier rounds' findings, and raises a WARN to BLOCK only on new evidence or a reason it states. For a gap in what a witness covers, the test is realism. If a violation a real implementation could plausibly contain would pass every check, the gap is BLOCK. If only contrived variants would get through, it is WARN, disclosed as residual uncertainty.

The two reviews complement each other. A clean architecture review does not imply a clean security review, and vice versa.

Both reviewers also apply the review standard:
- Is each invariant falsifiable?
- Are state and inputs canonical?
- Are authorization, validity, ordering and finality kept apart?
- Are effects declarative?
- Do retries and failures preserve commitments?
- Does recovery reproduce the required state?
- Does the evidence support the actual claim?
- Is the complexity warranted?

Clean design and many passing tests do not override unsupported evidence.

## §30 Promotion

Promotion means merging a slice or closing a cluster. It requires:
- direct evidence for the targeted invariants
- regression coverage for the affected contracts
- disclosure of residual uncertainty

Promotion refers to an immutable revision and exact verification records, not to a green dashboard indicator. Dependent work doesn't begin against behavior that hasn't been promoted.

---

# Part VI: Workflow

## §31 The four stages

**Define → Cluster → Slice → Close**

| Stage | What it establishes | Gate |
|---|---|---|
| Define | Defined invariants (§4) | Consensus review, then human approval of a line-by-line batch |
| Cluster | One primary invariant, its cluster exit criteria, ordered slices, TCB map and forbidden patterns | Consensus review |
| Slice | One mergeable change, with its transition contract and acceptance tests | Consensus review before implementation; review step and evidence before merge |
| Close | Every slice merged and every cluster exit criterion passing at one revision | Cross-slice review, claim statuses updated, grounding documents refreshed |

The stages are applied iteratively. Whenever a slice needs an invariant that isn't defined yet, work goes back to Define.

## §32 Define

1. **Draft.** The agent drafts every invariant the concept depends on: what must always be true, what must never be possible, what must stay identical across executions, and what must be replay-equivalent. Each draft carries the §4 fields that exist before approval. The draft set also lists the transitions in scope and a TCB color hypothesis, and names the design documents it draws on. Open questions are listed, not guessed at.
2. **Consensus.** Claude and Codex review the drafts until they agree. They check:
   - that each draft is falsifiable
   - that each counterexample actually violates its statement
   - for missing invariants, overlaps and contradictions
   - against the design documents the draft names: whatever they require that no draft captures, and whatever a draft contradicts
   - scope and assumptions
   - that authorization, validity, ordering and finality are kept apart

   They also settle each open question: by drafting a line for it, by deferring it to a named later cluster or concept, or by excluding it with a stated reason. A question still contested at the round limit goes to the human, as does any question whose answer §20 reserves to the human.
3. **Batch.** The agreed set goes to the human as one line-by-line batch: one numbered line per invariant with its name, one-line statement and criticality, and the full draft underneath. Items without consensus appear as marked lines with both positions. A redefinition's line names the existing invariant or assumption and the nature of the change, with the current and the proposed text of each field it changes, and an assumption's line also lists every invariant that relies on it. The settled open questions are listed under the lines, one line each, along with any questions left for the human.
4. **Approval.** The human answers the whole batch, approving, rejecting or amending each line, or approving all of them, and answers each question left for them. Approving a line defines that invariant, and its ID is assigned at that moment. Approving a redefinition's line applies it, and the ID doesn't change. A settled question stands unless the human reopens it.
5. **Not yet understood.** If a concept can't be expressed as a pure transformation from canonical input to canonical output, or it brings nondeterminism into an authoritative path, Define says so plainly. The concept then needs refining before any planning.

## §33 Cluster

A cluster document contains:
- one primary defined invariant
- its anchors: the invariant IDs it relies on and strengthens
- entry conditions: what earlier clusters already enforce
- the cluster exit criteria, each a named test or CI check
- an ordered slice list, one line per slice
- the TCB map
- patterns forbidden while the cluster is open
- its consensus record

Consensus review checks that every cluster exit criterion is mechanical and reachable by the planned slices, that the slice order respects dependencies, and that the TCB map is complete.

Clusters are ordered by dependency and safety, not by delivery priority. A cluster never depends on invariants that only a later cluster enforces. Only one primary cluster is worked on at a time.

## §34 Slice

A slice is the smallest mergeable change. It preserves every defined invariant and leaves the system fully correct. It does one of three things:
- strengthens enforcement of an invariant
- delivers behavior within the defined invariants
- as a maintenance slice, preserves behavior, with the evidence §19 requires

A change that would temporarily weaken an invariant is not a slice.

The slice document has nine sections, plus its consensus record:

1. **Header**: the cluster, the cluster exit criteria addressed, dependencies and status.
2. **Intent**: the invariant IDs preserved, and those whose enforcement this slice strengthens.
3. **Contract**: the transition contract (§13), with guards, rejection, effects, recovery and failure modes.
4. **Boundary**: every module touched, with its TCB color.
5. **Assumptions**: trust, ordering, durability and environment.
6. **Acceptance tests and counterexamples**: named before implementation, each a concrete executable test or check.
7. **Prohibitions and non-goals**: inherited cluster prohibitions plus slice-specific ones, and what the slice must not do.
8. **Preservation baseline**: for maintenance slices only, the baseline revision and the preservation evidence (§19).
9. **Result**: tests run, revision, claim status for each affected invariant, and residual uncertainty.

The steps:
1. **Document.** The slice document is drafted and reviewed to consensus. Implementation starts only after consensus.
2. **Implement.** The agent implements exactly what the document states, including the acceptance tests and counterexample checks it names.
3. **Verify and remediate.** Static checks, tests, replay and any other required evidence run. Failures are fixed under §22.
4. **Review** (§29).
5. **Promote.** Commit, record evidence on each affected invariant at the commit's revision, update claim statuses, and state the residual uncertainty in the slice's result section.

## §35 Close and the acceptance gate

A cluster closes when every slice has merged and every cluster exit criterion passes mechanically at a single revision. Where the project has a canonical CI, that CI confirms the result on that exact revision. Close runs the cross-slice review (§29), updates claim statuses and refreshes the grounding documents. Closing changes documentation only. A failure found at close becomes a new slice.

Every cluster exit criterion must pass with what that cluster ships. No carry-forward: work is either in scope now or not claimed. No human review substitutes for a cluster exit criterion, and no status changes before its evidence exists.

A cluster exit criterion found unmet after close, because a check or a record it relied on turns out to be false, doesn't reopen the cluster. The gap is recorded as evidence on each affected invariant, their claim statuses are updated from it, and the fix is work in a later cluster. The closed cluster's documents are not rewritten.

The acceptance gate has seven questions, and each must be answered yes:

| Question | Standard |
|---|---|
| Defined contract? | The invariants are defined, unchanged, and referenced by ID. |
| Correct boundary? | Core/shell allocation and authorized state mutation are established. |
| Suitable enforcement? | Static and runtime mechanisms are documented, along with their limits. |
| Required evidence? | Reproducible commands and records demonstrate the claim at this revision. |
| No silent drift? | Authoritative behavior is preserved, or its change was approved. |
| Known uncertainty? | Partial, falsified and unresolved claims are openly identified. |
| Ready to promote? | Every required gate is satisfied for the target revision. |

---

# Part VII: Profiles and the protocol track

## §36 Profiles

Each project declares one of four profiles: `prototype`, `app`, `strict` or `protocol`. A missing configuration or missing profile means `strict`. An unrecognized value is an error, never a fallback.

| | `prototype` | `app` | `strict` | `protocol` |
|---|---|---|---|---|
| Purpose | Answer a question fast; the code may be thrown away | Software other people depend on | Authoritative state, consensus, money, replay-critical systems | Designing distributed or decentralized protocols |
| Define | Short form: always true, never possible, core/shell split; approved in conversation | Full; determinism and replay items only where replay is configured | Full | Full, plus a threat model, a safety/liveness split and explicit assumptions (§38) |
| Consensus review | Optional | Required | Required | Optional |
| Clusters and slices | Short form | Full | Full | Not used |
| Functional core / imperative shell | Crate layout required (§12); putting decisions in the core encouraged | Required | Required | Required for modelled actions only |
| Determinism tripwires | Off | Off | On | Purity check on modelled actions |
| Replay | Off | Where configured | On | — |
| Review step (§29) | Off; no secrets in source, no real user data | On | On | Off |
| Mutation accountability | Off | Critical invariants only | As written | Balanced experiments (§38) |
| Claim status and evidence | Optional | Required | Required | Model results only |
| Grounding documents at close | — | CODEMAP, TRACEABILITY | CODEMAP, TRACEABILITY | — |
| Explorer (§40) | Required | Required | Required | Required |
| Commits | Ordinary messages | IDSE format, replay lines omitted | IDSE format | Ordinary; predictions committed before results |

Identifiers (§6–§7) and agent conduct (§25) apply in every profile. SEAMS and HEAD_DELTAS are optional in every profile.

**Short form (`prototype`).**
- The cluster document states the primary invariant, entry conditions, cluster exit criteria, slice list, TCB map and forbidden list briefly.
- The slice document fills in the header, intent, boundary, acceptance tests, prohibitions and result, one line each where possible.
- Close checks the slices and cluster exit criteria, without the review step or a grounding-document refresh.

## §37 Profile rules

- Only the human lowers a profile (`strict` → `app`, `app` → `prototype`), in a commit that changes nothing else. Agents never lower a profile.
- A prototype repository says so in its README: "PROTOTYPE — not for production."
- A prototype graduates to `app` or `strict` when any of these becomes true:
  - someone other than its author depends on it
  - it touches real user data or money
  - it is about to be deployed
  - work shifts from testing an idea to building on it
- Graduating means changing the profile, running Define with the prototype as evidence, then planning clusters. The prototype is a reference, not a foundation: harden or rewrite it slice by slice.

## §38 The protocol track

The protocol track is for distributed or decentralized protocols, where the hard questions are about interleavings, faults and adversaries. Its durable output is the model, its predictions and its classified counterexamples; the code may be thrown away. The main workflow never runs model checking. The protocol track is the only place it happens.

**Held to full rigor:**
- **Define additions.** Define adds:
  - a threat model: what the network may do (drop, delay, reorder, duplicate, partition), which participants may be malicious, what they can sign, which keys can be lost or stolen
  - a safety/liveness split, including fairness
  - every assumption the design relies on, so each one can be run both ways

  Invariants use standard IDs and names from the first definition, and the names become model property names.
- **Local knowledge only.** Honest parties act only on their own state and delivered evidence. The adversary's full view exists only as the checking oracle. Faults are transitions, not fixed sets.
- **Predictions first.** Predictions are written before any run, never back-filled, and committed before the results they predict.
- **Shakedown gate.** Only type-correctness runs come first. Then the human's approval freezes the baseline. No safety, reachability or liveness run happens before that.
- **Never tune to match.** Any change to the model, fixtures, configurations or invariants is a new revision with new predictions. Original traces are kept.
- **Balanced experiments.**
  - Every safety claim has a mutant configuration that could falsify it.
  - Every interesting outcome has a reachability configuration that shows it isn't vacuous.
  - Every assumption is run both ways.
- **Bounded runs.** Runs are time-limited and fail closed. Hitting a limit is a finding, never a pass.
- **Labels and classes.** Every property is labelled substantive, definitional, implied, vacuous or configuration-limited. Every counterexample is classified as a mechanism flaw, assumption exceeded, unrealistic assumption, abstraction limit or genuine impossibility, and indexed with its output hash.
- **Unresolved is a valid result.** A genuine limitation is kept and marked Unresolved, never patched over with an invented mechanism.
- **Claim boundary.** A passing model shows the property under the declared assumptions, on a finite model with ideal cryptography, and nothing more. Cryptographic claims need dedicated tools such as Tamarin or ProVerif.
- **Modelled actions are pure.** Code that implements an action from a model is a pure transition (§13). It names the action it implements and is checked mechanically for I/O, clock, randomness and async.

**Loose:**
- no clusters, slices, cluster exit criteria, core-path tripwires, replay gates or review step
- the functional core / imperative shell split is encouraged, except for modelled actions, where it is required
- tests that reproduce counterexamples are welcome but not required

**Git discipline:**
- Every idea lives on its own branch.
- The main branch holds ideas that reached a conclusion, positive or negative. Conclusive negative results merge just like positive ones, so later ideas run into them.
- Once a prediction is committed, the branch is never squashed, rebased, amended or force-pushed, because commit order is the evidence that nothing was back-filled.
- An idea dropped without a conclusion is tagged and closed, keeping its history.

## §39 Graduation from the protocol track

Graduation is the only bridge from the protocol track into Define → Cluster → Slice → Close.

- The model's invariants enter Define as drafts, keeping their IDs and names. They go through consensus review and are defined by human approval.
- Model results count as evidence for the model claim only. The implementation claim starts Unresolved until slice evidence supports it.
- Every counterexample becomes an acceptance test in a slice, or is marked model-internal.
- The model baseline re-check becomes a CI check.
- After graduation, slices proceed as in any other project and never run model checking. Changing the model is protocol-track work.

---

# Part VIII: Artifacts, worked example, adoption

## §40 Artifact set

| Artifact | Purpose |
|---|---|
| Invariant registry | The canonical, append-only list of defined invariants (§4) |
| Cluster documents | One per cluster (§33) |
| Slice documents | One per slice (§34) |
| CODEMAP | Per-module authority: purpose, what it creates and interprets, what it must not do, dependencies, entry points, TCB color and attachment boundaries |
| TRACEABILITY | Invariant → source → code → tests → CI, with enforcement status and claim status. This is the handoff view of the project's claims. |
| SEAMS (optional) | Where new work can attach: closed versus extensible registries, version-gated contracts |
| HEAD_DELTAS (optional) | What has shipped since a baseline |
| Project configuration | Profile, core paths, test and replay commands, project namespace, document locations |
| Explorer | A local view, generated from the project's records, of how each invariant maps onto the slices that deliver it, the checks that enforce it and the code it lives in. Every project with invariants has one, from the Define that starts its registry. It is never edited, committed or published. |

Grounding documents and the explorer are regenerated, not edited by hand. A stale one is regenerated, not patched. Invariants are kept separate enough from the implementation that an implementing agent can't quietly redefine the target of its own verification.

Invariants are written independently of any language's syntax, so they state what must hold, not how the code holds it. State which properties follow from Rust's compiler and type system and which still need transition guards, durability protocols or adversarial verification. Appendix A shows this.

## §41 Worked example: single-use authority

**Concept:** for an indivisible resource, at most one accepted exercise consumes a given live authority instance. The model covers the resource ID, current owner, live/consumed state and version. The command is an owner-authorized exercise that names the expected live instance.

**Define** produces this batch, which the human approves:

1. `FIN-001 SingleConsumption`: at most one accepted exercise consumes a given live authority instance. Critical. Counterexample: two accepted exercises of the same instance.
2. `SEC-001 OwnerAuthorizesExercise`: an exercise is accepted only with valid authorization from the current owner. Critical (the `SEC` default). Counterexample: an accepted exercise authorized by someone who isn't the owner.
3. `VAL-001 ExpectedLiveInstance`: an exercise that names a stale, unknown or already-consumed instance is rejected. Critical. Counterexample: an accepted exercise naming a superseded instance.
4. `STR-001 ExclusiveResourceState`: the state is Active or Consumed, never both. Standard. Counterexample: a value carrying both an owner and a receipt.
5. `DUR-001 CommittedConsumptionSurvives`: a committed consumption survives a crash at any point after commitment. Critical. Counterexample: a restart that finds the instance live again.
6. `REC-001 ReplayRestoresState`: reconstructing from durable history yields identical authoritative state. Critical. Counterexample: replay producing a different owner or state.

`SEC-001` is classified `SEC` even though it also concerns validity, because it exists to defend against an adversary. `VAL` is recorded as a secondary family.

**Cluster `C001`** has primary invariant `FIN-001`. Its exit criteria:
- `C001-CE01`: a duplicate-submission property test, in which the second exercise is rejected.
- `C001-CE02`: of two conflicting authorized commands, exactly one is accepted.
- `C001-CE03`: crashes before and after commitment, after which recovery reproduces the state.
- `C001-CE04`: replay of durable history is byte-identical.

Its slices are ordered so that each leaves the system fully correct (§34). Owner authorization lands with the consume transition, because a slice that accepted a non-owner's exercise, even briefly, would weaken `SEC-001`.
- `C001-S01`: a pure consume transition over `ExclusiveResourceState` types, with owner authorization in its guard (`STR-001`, `VAL-001`, `SEC-001`).
- `C001-S02`: a discriminating mutation witness for owner authorization (`SEC-001`).
- `C001-S03`: duplicate, conflicting, stale and reordered command tests (`FIN-001`), addressing `C001-CE01` and `C001-CE02`.
- `C001-S04`: durable commitment and replay in the shell (`DUR-001`, `REC-001`), addressing `C001-CE04`.
- `C001-S05`: fault injection for crash before and after commitment, and for timeout and retry, addressing `C001-CE03`.

**The key limitation.** The deterministic core rejects reuse relative to the canonical state it observes. On its own, that doesn't stop two disconnected replicas from each accepting conflicting commands. If the system is replicated, shared finality is a separate invariant. Its claim stays Unresolved until an ordering and convergence design, worked out on the protocol track if needed, and adversarial evidence establish it.

## §42 Adoption checklist

- Declare the profile and the project namespace.
- Identify which state is authoritative and who may change it.
- Run Define (drafts, consensus, line-by-line approval) and start the registry with standard IDs.
- Locate the core/shell boundary and assign TCB colors.
- Lay the project out in Rust: the core crate and the shell package (§12).
- Choose the compiler's enforcement first, then add the runtime checks that are still needed.
- Plan the first cluster and its exit criteria, and reach consensus on it.
- Draft, agree and implement the smallest coherent slice, with its evidence.
- Record evidence and claim status at every promotion.
- Use controlled remediation for failures, and keep counterexamples.
- Show behavioral preservation for maintenance slices.
- Keep CODEMAP and TRACEABILITY current, so a replacement agent can resume from the repository.
- When entering an existing project, read its configuration and project instructions, skim CODEMAP and TRACEABILITY, and open the most recent cluster document.

## §43 Non-goals and continuing refinement

IDSE does not claim:
- that every property can be decided
- that every test constitutes proof
- that local determinism creates distributed consensus
- that formal proofs are mandatory everywhere

It doesn't ban concurrency, optimization, side effects or iterative discovery. Future revisions may refine evidence strength, liveness and operability gates, triggers for formal modelling, and exceptions to strict core/shell arrangements, without quietly weakening defined guarantees.

---

**Working principle.** Design from constraints. Isolate effects. Make state changes explicit. Challenge assumptions. Promote only what evidence establishes.

The core defines what is true. The shell handles the world.

---

# Appendix A: Reference implementation

**`STR-001 ExclusiveResourceState`**: an authoritative resource-state value is either Active(owner) or Consumed(receipt), and it can't structurally represent both variants at once. This does not establish exclusive consumption across disconnected replicas.

The structural guarantee is strong. But the code doesn't prove that the caller is authorized, that an instance can't be consumed twice in two different process snapshots, or that a committed outcome survives a crash. Those claims need transition guards, canonical ordering and finality design, and evidence.

### Rust: enum and encapsulation

```rust
enum ResourceState {
    Active { owner: OwnerId },
    Consumed { receipt: ReceiptId },
}

fn consume(s: ResourceState, receipt: ReceiptId) -> Result<ResourceState, Error> {
    match s {
        ResourceState::Active { .. } => Ok(ResourceState::Consumed { receipt }),
        ResourceState::Consumed { .. } => Err(Error::AlreadyConsumed),
    }
}
```

Rust makes the alternatives exclusive and can restrict mutation through ownership and private constructors. In a real system, the consume function still needs domain authorization and a durable, ordered commitment protocol.
