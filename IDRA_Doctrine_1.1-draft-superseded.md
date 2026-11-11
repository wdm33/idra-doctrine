# IDRA Doctrine

**Invariant-Driven Reasoning Architecture**  
**Status:** Proposed authoritative specification — pending human approval  
**Version:** 1.1-draft  
**Scope:** Software engineering undertaken under IDRA, including work performed by human developers and autonomous engineering agents  
**Implementation language:** Rust

> **Doctrine:** Software shall be constructed as a system of explicitly constrained state transitions, governed by mechanically enforced, human-approved invariants, implemented through a deterministic functional core isolated from environmental effects by an imperative shell, and advanced only when reproducible evidence supports its correctness.

IDRA is the complete engineering doctrine. It is not a separate autonomous layer placed on another methodology. It governs the software, the engineering process, and the authority of the agents conducting that process.

**Authority note:** This document is a proposed replacement specification. Its status remains *draft* until the designated human authority explicitly approves it. Approval of the doctrine is distinct from approval of individual project invariants. Once approved, **this document alone** is the governing doctrine: no parallel legacy rulebook is required or invoked. Prior formulations are not normative dependencies.

**Changes in 1.1-draft.** Incorporates approved design decisions DDR-001 to DDR-006 and DDR-008 to DDR-012: invariant families (IDRA-1.7), functional domains (IDRA-1.8), invariant assurance levels (IDRA-1.9), Rust core-crate constraints (IDRA-2.1), TCB classification (IDRA-2.5), invariant identifiers (IDRA-3.5), independent cluster-closure review (IDRA-3.7), and generated grounding views (IDRA-3.8), with the matching schema, profile and example updates. DDR-007 (scope distinctions) was not approved and is not incorporated. The decision register is drafting material, not a source of authority.

---

## 0. How to read and apply this doctrine

### 0.1 Two layers, one source of truth

**Layer 1 — Foundational Rules** communicates the intent in four concise statements. **Layer 2 — Normative Requirements** supplies numbered, operational obligations under those statements. The two layers are parts of one specification, not competing documents.

1. **Invariant-Driven Correctness & Determinism** — Correctness is defined by human-approved invariants and deterministic state transitions.
2. **Isolated Functional Core & Versioned Boundaries** — Pure decisions are isolated from effects, with explicit and versioned interfaces and safe failure semantics.
3. **Mechanical Enforcement & Empirical Proof** — Claims of correctness and completion require independent, reproducible, machine-checkable evidence.
4. **Human-Gated Multi-Agent Governance & Compliance** — Agents collaborate within explicit limits; specified decisions require human authorization.

If a foundational summary and its normative requirements appear to differ, **the normative requirements control operational behavior**. A contradiction or ambiguity must not be silently resolved by an agent: it is an escalation event under IDRA-4.6.

### 0.2 Normative language

- **MUST / MUST NOT:** mandatory requirement or prohibition.
- **ONLY:** exclusive authority or exclusive permitted mechanism.
- **MAY:** permitted but not required; never grants authority to override a MUST.
- **SHOULD:** recommendation; deviations require an explicit rationale and cannot override a MUST.
- **Human authority:** the specifically designated person empowered to approve invariants, doctrine changes, assurance-profile reductions, and escalated design decisions. A tool, model, review vote, implied consent, or automated workflow is not that authority.
- **Mechanically enforced:** checked by a type boundary, compiler, executable test, lint/static analysis, or CI gate, with a verifiable result. A prose assertion alone is not enforcement.
- **Evidence:** a reproducible artifact or recorded check, linked to the precise version of the implementation or specification it evaluates.

### 0.3 Non-invention rule

An agent MUST NOT invent approvals, missing requirements, test outcomes, reviewer consensus, trust assumptions, profile exemptions, or evidence. It MUST distinguish **verified**, **unverified**, **blocked**, and **rejected** claims. Where the doctrine does not define an answer that affects authority, correctness, architecture, or verification, the agent MUST request the required decision rather than treating a plausible guess as permission.

### 0.4 Requirement records

Every normative requirement below has three elements:

- **Obligation** — what must be true or what action is required/prohibited.
- **Enforcement** — how a violation is prevented, detected, or blocked. Not every human authorization is compiler-checkable; those decisions must be recorded and checked by a workflow gate instead.
- **Evidence** — what a reviewer can inspect to establish compliance. A check marked *not run* is not a pass.

For project-specific implementation, a requirement record MAY point to named checks, policies, code locations, and evidence artifacts. Such mappings cannot reduce the force of the requirement.

---

# Layer 1 — The Four Foundational Rules

## Rule 1. Invariant-Driven Correctness & Determinism

**Define the conditions of correctness before writing features. Allow only valid states and explicitly constrained transitions. Ensure that the same ordered inputs always produce the same state.**

Human authority determines which invariants govern. Agents may reason about them and propose changes, but they cannot authorize changes themselves. Features are sequences of permitted state transitions, not independent sources of correctness.

## Rule 2. Isolated Functional Core & Versioned Boundaries

**The core decides; the shell interacts.** The functional core must have no hidden environmental effects. Its accepted inputs and change rules must be explicit, closed, and versioned. Failures that threaten state integrity, replay, or agreement must halt affected progress rather than be hidden or retried inside the core.

## Rule 3. Mechanical Enforcement & Empirical Proof

**A requirement that cannot be checked is not yet protected, and a claim without reproducible evidence is not established.** Tests for elevated and high-assurance invariants must demonstrate that they detect violations, and every cluster receives independent review before it closes. A failing check is a defect or a blocking discrepancy, not permission to lower an invariant, test, exit criterion, or expected result.

## Rule 4. Human-Gated Multi-Agent Governance & Compliance

**Autonomy operates within human-defined authority.** Claude and Codex review specifications before implementation, agreed decisions are documented, and specified conflicts or changes stop for the human. Assurance profiles determine applicable rigor but cannot silently waive core obligations. Agent conduct and execution boundaries are explicit.

---

# Layer 2 — Normative Requirements

## Rule 1 — Invariant-Driven Correctness & Determinism

### IDRA-1.1 — Invariants precede implementation

**Obligation.** Before implementing a feature or behavior change, its governing invariants MUST be written down. They MUST identify, as applicable, what always holds, what is forbidden, and what outputs or state results must be reproducible. A feature MUST be specified as allowed transitions consistent with those invariants. Work MUST NOT proceed on an unstated correctness assumption as though it were an approved invariant.

**Enforcement.** The implementation-entry gate MUST require references to approved invariant identifiers. Changes to behavior must be checked against those invariants and any new or modified ones.

**Evidence.** Approved invariant definitions, links from the relevant cluster/slice specification, and recorded gate results.

### IDRA-1.2 — Only the human establishes invariant authority

**Obligation.** Agents MAY draft invariants. Claude and Codex MUST review proposed invariants and seek agreement on the draft. The designated human MUST then be able to **approve, reject, or amend each invariant individually, line by line**. Each line presented for approval MUST include the invariant's statement, family (IDRA-1.7), primary domain (IDRA-1.8), and proposed assurance level with its rationale (IDRA-1.9); approving the line approves all of these together. ONLY explicit human approval may create, modify, or retire an invariant. Silence, agent consensus, a passing test, and previous examples MUST NOT be interpreted as approval.

**Enforcement.** The invariant register MUST distinguish proposed, rejected, approved, superseded, and retired records. Only an authenticated, attributable human decision can move a proposal to approved status or change/retire an approved invariant. The implementation gate MUST reject references to unapproved invariants.

**Evidence.** Per-invariant review decisions, approved text/version, identity of the approver, and the active invariant register.

### IDRA-1.3 — Illegal states are unrepresentable wherever possible

**Obligation.** Rust types MUST encode legal distinctions and disallow invalid combinations wherever the type system can reasonably express them. An enum such as `Active | Consumed` is preferred to independent flags that can form contradictory combinations. When an invalid state cannot be ruled out at compile time, the system MUST validate it at a defined boundary and preserve the applicable invariant thereafter.

**Enforcement.** Type design, constructor visibility, validated smart constructors, compile-fail tests where useful, and invariant-focused tests for runtime-only constraints. An unchecked public constructor MUST NOT bypass a protected invariant.

**Evidence.** Type and constructor definitions, compiler/test results, and tests for prohibited states or invalid inputs.

### IDRA-1.4 — One controlled state-transition boundary

**Obligation.** Every authoritative state change MUST pass through the designated transition interface, conceptually:

```rust
transition(state, command, context) -> Result<(new_state, effects), rejection>
```

The interface MUST either produce a complete new state plus an explicit description of effects, or reject without changing authoritative state. It MUST NOT partially apply a change, perform hidden I/O, or create unreported effects. `context` MUST contain every environmental value needed by the decision. An implementation-specific Rust signature MAY vary in ownership and type names but MUST preserve these semantics.

**Enforcement.** Encapsulation of state mutation, review of all mutation entry points, core isolation checks, and atomic-rejection tests. The effect list MUST be data returned for shell execution, not effects performed by the core.

**Evidence.** Transition API, code-path inventory, tests asserting rejection leaves state unchanged, and effect-contract tests.

### IDRA-1.5 — The core is deterministic

**Obligation.** The core MUST be a pure function of its explicit inputs for observable state decisions. It MUST NOT implicitly consult wall-clock time, randomness, locale, OS state, network state, filesystem state, process scheduling, or unordered map iteration. If such information affects a decision, it MUST be provided through an explicit, replayable input with defined ordering and serialization.

**Enforcement.** Dependency checks, restricted imports/capabilities, deterministic collections or canonical ordering, canonical serialization tests, and lint/static-analysis rules appropriate to the crate.

**Evidence.** Core dependency manifest, lint and build output, code review of external-input handling, and deterministic test cases.

### IDRA-1.6 — Byte-identical replay is required

**Obligation.** Given the same initial state, ordered commands, explicit contexts, and active rule versions, replay MUST produce byte-identical canonical state output. Nondeterministic replay is a correctness failure even when high-level values appear equivalent. Canonical encoding and ordering MUST be defined for anything compared byte-for-byte.

**Enforcement.** Repeatable replay tests, canonical state serialization, cross-run comparisons, and—where relevant—cross-node or cross-platform comparison fixtures.

**Evidence.** Replay fixtures, exact byte comparisons, environment/version metadata, and commit-bound test results.

### IDRA-1.7 — Every invariant belongs to exactly one family

**Obligation.** Every invariant MUST be classified in exactly one of the following eight families. The three-letter abbreviation is used ONLY in the invariant identifier (IDRA-3.5); the record's family field MUST spell out the full name.

| Prefix | Family | Meaning | Minimum verification concern |
|---|---|---|---|
| SEC | Security | Protects against unauthorized actions, adversarial behavior, and violations of trust boundaries. | Demonstrate that unauthorized or adversarial actions cannot violate protected guarantees. |
| SAF | Safety | Prevents prohibited states and transitions, including violations of validity, structural constraints, conservation, and finality. | Demonstrate that prohibited states and transitions cannot occur. |
| LIV | Liveness | Ensures required progress and eventual completion under explicitly stated assumptions. | Demonstrate that required progress occurs under those assumptions. |
| DET | Determinism | Ensures reproducible execution, canonical ordering, and identical authoritative results from identical inputs. | Demonstrate that identical canonical inputs produce identical authoritative results. |
| RES | Resilience | Ensures committed information survives failures and valid operation can be restored. | Demonstrate that required state and behavior survive failures or recover correctly. |
| EVO | Evolution | Governs controlled changes to interfaces, protocols, rules, and system semantics. | Demonstrate that changes preserve required compatibility and obey version and activation boundaries. |
| OBS | Observability | Ensures trustworthy information is available to inspect, diagnose, and verify system behavior. | Demonstrate that required records and diagnostics accurately represent behavior. |
| PRC | Process Assurance | Ensures engineering procedures, approval gates, verification obligations, and completion criteria are enforced. | Demonstrate that required workflow gates and approvals cannot be bypassed. |

The families consolidate earlier subject categories: Safety absorbs Structural, Validity, Conservation and Finality; Determinism absorbs Ordering; Resilience absorbs Durability and Recovery. Consolidation MUST NOT eliminate any distinct correctness property or its verification obligation. A family specifies a **verification concern**, not a mandatory tool or test count; invariants in the same family MAY require different checks according to their statements, scope, risks, and assumptions. Illustrative methods, not mandates: authorization/adversarial checks (Security); type and state-machine/property checks (Safety); progress/temporal/fault scenarios (Liveness); replay/canonical/differential checks (Determinism); crash/restart/fault injection (Resilience); activation/migration/compatibility checks (Evolution); audit/trace/integrity checks (Observability); CI approval, artifact and workflow-gate validation (Process Assurance).

**Enforcement.** Invariant-register validation that each record has exactly one listed family whose prefix matches its identifier; review at cluster/slice entry that each invariant's checks address its family's verification concern.

**Evidence.** Invariant records with family fields, register validation output, and the invariant-to-check mapping.

### IDRA-1.8 — Invariants are organized by functional domain

**Obligation.** Invariants MUST be organized by the functional responsibilities of the software, not by module or file location. Domains are project-specific; IDRA does not prescribe a universal list. Each invariant MUST have exactly one **primary domain** and MAY have **associated domains** where a guarantee spans responsibilities. Family and domain are independent dimensions: the family is not a sub-folder of the domain. Domain assignments are versioned organizational metadata whose history MUST be retained. Reassigning a domain MUST NOT change the invariant's identifier, approved statement, recorded evidence, clusters, or slices, and does not by itself require reapproval. A change that alters an invariant's meaning, scope, assumptions, or enforcement obligations is a substantive change governed by IDRA-1.2, whatever it is labeled.

**Enforcement.** Register validation that each invariant has one primary domain; retained history of domain assignments; review of domain changes for hidden semantic change.

**Evidence.** Domain list, invariant records with domain fields, and domain-assignment history.

### IDRA-1.9 — Every invariant has an approved assurance level

**Obligation.** Every invariant MUST carry one of three cumulative assurance levels. A level sets the **minimum verification rigor**; it never exempts an invariant from correctness or evidence obligations, and a higher level does not imply that any check has passed.

| Level | Name | Intended use | Minimum verification obligations |
|---|---|---|---|
| L1 | Baseline | Assessed, ordinary, limited-consequence guarantees | Human-approved invariant; effective mechanical enforcement; reproducible, revision-bound verification; applicable assumptions and limits stated. |
| L2 | Elevated | Significant consequences or substantial verification difficulty | All L1 obligations, plus negative/boundary checks, failure-path verification, and discriminating mutation verification or equivalent fault injection showing that the intended check catches an actual violation (IDRA-3.2). |
| L3 | High Assurance | Severe, irreversible, or system-wide consequences, or exceptional verification difficulty | All L2 obligations, plus independent verification and stronger risk-appropriate substantiation, including explicit analysis of assumptions and limitations. Formal modeling is used when needed to substantiate the specific claim, not automatically. |

Classification rules:

1. The level is determined by the **consequences of a violation** (security, safety, operational or financial integrity, data integrity and recovery) and by **verification difficulty** (concurrency, distributed or adversarial interaction, and similar challenges).
2. **Highest trigger wins.** Any applicable L3 criterion means L3; otherwise any L2 criterion means L2; otherwise L1. A severe exposure MUST NOT be averaged down because other dimensions are mild.
3. L1 is the default **only after assessment** finds no L2 or L3 criterion. If consequences or verification difficulty cannot be adequately assessed, the level MUST be marked `Unresolved` and a human decision sought; it MUST NOT silently default to L1.
4. Each level MUST carry a one-line **assurance rationale** explaining the consequence and difficulty judgment.
5. Agents propose the level with each drafted invariant; the human approves it as part of the line-by-line approval (IDRA-1.2). Agents MAY strengthen verification but MUST NOT lower an approved level without explicit human authorization.
6. Assurance levels are distinct from the project assurance profile (IDRA-4.3) and from verification status. A profile MUST NOT silently negate a level's obligations.

No numeric thresholds are defined for the boundary between significant and severe consequences. Projects MAY adopt approved examples or indicators; agents MUST NOT invent them.

**Enforcement.** Register validation that each approved invariant has a level of L1, L2 or L3 and a rationale; approval gate rejecting `Unresolved` levels as implementation references; diff checks on level changes requiring a human approval record; closure checks that the level's obligations were met.

**Evidence.** Invariant records with level and rationale, approval records, level-change history, and level-specific verification evidence.

---

## Rule 2 — Isolated Functional Core & Versioned Boundaries

### IDRA-2.1 — Functional Core / Imperative Shell is mandatory

**Obligation.** The decision-making core MUST be an independent Rust crate with **no external crate dependencies** under the doctrine's baseline constraint. It MUST NOT perform I/O or depend on the imperative shell. The shell MUST handle network, filesystem, clocks, randomness acquisition, persistence, process coordination, and effect execution, and MAY depend on the core. Dependency direction MUST never point from core to shell.

The following Rust constraints make this separation mechanical:

1. Project software MUST be organized as a **Cargo workspace from the first cluster**.
2. The authoritative core MUST be a **separate crate** declaring `#![no_std]` (it MAY use `alloc`) and `#![forbid(unsafe_code)]`.
3. The core crate MUST have **no external crate dependencies** and **no build script**. Test-only dev-dependencies MAY be used but MUST NOT become runtime core dependencies.
4. The shell MAY depend on the core by path. No dependency MAY point from the core to the shell.
5. The core MUST NOT use networking, filesystem, async runtimes, clocks, randomness, or OS/environment facilities to determine authoritative results.
6. CI/static checks MUST cover prohibited constructs that the compiler and crate layout do not fully exclude on authoritative core paths, including floating point, global state, panics, and other sources of nondeterminism.
7. These structural restrictions apply under **every** software profile, including `prototype`. Profiles MAY vary additional checking rigor; they MUST NOT waive the core/shell separation.
8. Under the `protocol` profile the principal deliverable MAY be a model rather than production code; the purity and boundary constraints then apply to the modeled actions. No further protocol exceptions are defined.
9. The shell MAY use environmental libraries, services, concurrency and I/O, but MUST NOT redefine core semantics or bypass the controlled transitions of IDRA-1.4.

**Enforcement.** Workspace dependency graph checks, core manifest validation (no dependencies, no `build.rs`), crate-attribute checks, isolation and lint checks for prohibited constructs, and tests that run core logic without environmental services.

**Evidence.** Cargo manifests, dependency graph output, crate attributes, static-check output, and standalone core test results.

### IDRA-2.2 — Interfaces are closed and explicit

**Obligation.** The accepted command/message variants, fields, validation rules, and rejection behaviors MUST be enumerated and documented. Unknown, malformed, or unsupported inputs MUST NOT be silently interpreted as another command or accepted through an undocumented fallback.

**Enforcement.** Closed Rust enums and validated decoding boundaries, exhaustive matching, compatibility tests, and rejection tests for unknown or invalid messages.

**Evidence.** Message definitions, schema/decoder tests, and documented rejection outcomes.

### IDRA-2.3 — Semantic changes have explicit version and activation boundaries

**Obligation.** A change to the meaning of a command, state transition, validation rule, or protocol MUST be assigned an explicit version and a defined activation point. An execution/replay segment MUST apply the rules specified for that segment; incompatible old and new semantics MUST NOT be silently mixed. Migration behavior, if required, MUST be explicit and testable.

**Enforcement.** Version dispatch, activation-boundary checks, compatibility/replay tests on both sides of the boundary, and rejection of unknown versions.

**Evidence.** Versioned specifications, activation records, migration rules where applicable, and boundary test results.

### IDRA-2.4 — Correctness-threatening failures are fail-stop

**Obligation.** If a failure could compromise authoritative state, deterministic replay, or agreement between nodes, the affected processing MUST halt instead of continuing under an assumption of safety. The core MUST return an explicit rejection/error; it MUST NOT conceal or internally retry failures. **Only the shell** MAY implement retries, and only when the retry policy preserves correctness and is explicit.

**Enforcement.** Classified error contracts, fail-stop paths, negative tests for corruption and replay disagreement, and shell retry policy tests. Retry logic inside the core is prohibited.

**Evidence.** Error classification, failure-injection results, halt/rejection behavior, and documented shell retry behavior.

### IDRA-2.5 — Every module has exactly one TCB color

**Obligation.** Every module MUST be classified by its responsibility into exactly one of three colors:

- **BLUE: deterministic, authoritative functional core.** Owns canonical state meaning, authoritative validation, transition decisions, and declarative effects. Resides in the core crate and MUST satisfy IDRA-2.1.
- **GREEN: deterministic, non-authoritative support.** Support computations, test and verification tooling, fixtures, and toolchain orchestration. MUST NOT define, modify, or reinterpret authoritative semantics or outputs. GREEN is a responsibility, not an exception zone.
- **RED: imperative shell.** Owns I/O, networking, storage, clocks, processes, external APIs, effect execution, retries, and environmental orchestration. MUST NOT independently determine authoritative meaning, mutate core state outside the transition contract, or reinterpret core results.

There is no fourth color. **YELLOW or any other intermediate classification MUST NOT be introduced**, and no module MAY remain uncategorized. A module MUST conform to every restriction of its color; a module that mixes BLUE decisions with RED effects MUST be split. An ambiguous classification MUST be resolved before affected implementation proceeds. A functional domain (IDRA-1.8) MAY span modules of all three colors.

Boundary semantics:

1. There is **one governed core/shell boundary**. The boundary itself has no color.
2. **RED → BLUE** is allowed: the shell invokes the core's public transition interfaces with explicit canonical inputs.
3. **BLUE → RED** invocation or dependency is prohibited. BLUE returns declarative effects as data and RED executes them; returning effects is not a dependency.
4. **BLUE → GREEN → RED** is not a permissible bypass. GREEN is not an intermediary in the runtime flow and MUST NOT bridge the boundary.
5. The shell MUST NOT directly mutate authoritative state, construct semantic types unsafely, bypass canonical validation, invent variants, or reinterpret core outputs.
6. ONLY the designated human MAY authorize moving the BLUE boundary (IDRA-4.4). Agents MUST NOT reclassify authoritative logic as GREEN or RED to avoid BLUE restrictions.

**Enforcement.** Crate separation, dependency-graph constraints, state encapsulation, explicit transition interfaces, a module classification record validated for completeness, architecture review (IDRA-3.7), and human boundary-change records.

**Evidence.** Module records with color and responsibility, dependency graph output, classification validation, review findings, and boundary-change approvals.

---

## Rule 3 — Mechanical Enforcement & Empirical Proof

### IDRA-3.1 — Every invariant has an effective check

**Obligation.** Every approved invariant MUST have at least one effective enforcement or verification mechanism: Rust type/visibility constraints, compiler checks, executable tests, lint/static analysis, or CI. A prose claim or reviewer belief alone does not protect an invariant. The checks MUST address the verification concern of the invariant's family (IDRA-1.7) and meet the obligations of its assurance level (IDRA-1.9). Where an invariant cannot be mechanically checked, that gap MUST be disclosed as unverified and escalated rather than called satisfied.

**Enforcement.** Invariant-to-check mapping reviewed at cluster/slice entry and exit; CI gate ensuring named checks exist, execute, and report results.

**Evidence.** Invariant register entries with check identifiers, executed check reports, and explicit unresolved gaps.

### IDRA-3.2 — L2 and L3 checks must fail on deliberate violations

**Obligation.** For every L2 and L3 invariant (IDRA-1.9), a test passing on correct code is insufficient. A deliberately broken implementation (mutation, fault injection, or equivalent controlled violation) MUST make the relevant check fail **for the intended invariant violation**. A failure for unrelated build or environment reasons does not satisfy this rule.

**Enforcement.** Mutation/fault-injection procedure with an expected-failure assertion and a clean baseline; review of failure cause.

**Evidence.** The introduced defect or mutation, failing check output and reason, passing unmodified baseline, and associated commit or fixture IDs.

### IDRA-3.3 — Evidence, not assertion, establishes status

**Obligation.** Claims such as *implemented*, *verified*, *compatible*, *deterministic*, and *complete* MUST be supported by reproducible evidence tied to a specific source commit and relevant specification version. Remaining uncertainty MUST be stated plainly. A check that was skipped, blocked, or impossible to run MUST NOT be reported as passed.

**Enforcement.** Evidence manifest and status gates; claims must link to run commands/configuration, results, and commit IDs.

**Evidence.** Commit hash, toolchain and command details, fixtures/inputs, check outputs, and an uncertainty/blocker record.

### IDRA-3.4 — Do not weaken a failing requirement

**Obligation.** When a check fails, agents MUST correct the implementation or identify a genuine conflict and seek human resolution. Agents MUST NOT weaken or delete an invariant, test, exit criterion, or expected output to turn failure into success. They MUST NOT relabel a failing check as optional or move it outside the cluster merely to permit closure. Any legitimate requirement change requires the appropriate human approval before implementation proceeds under the revised requirement.

**Enforcement.** Review of changed test expectations and specifications, diff checks on invariants and exit criteria, and explicit approval gates for authorized changes.

**Evidence.** Failure record, corrective code diff, unchanged check criteria or approved change record, and passing rerun.

### IDRA-3.5 — Work identifiers and hierarchy are mandatory

**Obligation.** Work MUST use these identifier forms:

| Item | Format | Example | Meaning |
|---|---|---|---|
| Invariant | `<FAM>-<number>` | `SAF-001` | Approved correctness requirement; `<FAM>` is one of the eight family prefixes (IDRA-1.7) |
| Cluster | `C<number>` | `C001` | Bounded delivery unit |
| Slice | `C<number>-S<number>` | `C001-S01` | Implementable part of its parent cluster |
| Exit criterion (EC) | `C<number>-EC<number>` | `C001-EC01` | Objectively verifiable, cluster-level condition required for closure |

Identifier sequences and widths MUST be stable within the project and MUST NOT be reused to disguise changes in meaning. Invariant identifiers MUST NOT embed domains. No separate CamelCase or descriptive name is required; instead every invariant MUST carry a **one-line, precise, falsifiable statement** of what remains true or what is forbidden. Identifiers required by external tools (for example `SAF_001` for a model checker) MUST be derived mechanically from the invariant identifier rather than maintained as manual aliases. Every slice MUST identify its parent cluster and relevant invariants. Every cluster MUST state its exit criteria before work begins. Exit criteria belong to the cluster, and MAY depend on evidence from multiple slices.

**Enforcement.** Schema/format validation in project tooling, referential-integrity checks between artifacts, and specification review gates.

**Evidence.** Work register, cluster and slice specifications, identifier validation, and exit-criterion mappings.

### IDRA-3.6 — Cluster completion is all-or-nothing

**Obligation.** A cluster MUST NOT be marked closed until **every** specified exit criterion passes with required evidence and the independent closure review (IDRA-3.7) applies to the closing revision with every BLOCK finding resolved and verified. No unfinished check or unresolved correctness obligation may be silently deferred to later work. If closure is blocked, the cluster MUST remain open or blocked, with the unmet checks and uncertainty listed. Scope may only change through an explicit authorized decision; the agent may not redefine success to match what was finished.

**Enforcement.** Exit-criterion ledger and closure gate requiring all check states to be passed, plus approval records for changes to scope.

**Evidence.** Exit-criterion list, per-check result and proof, commit IDs, unresolved-blocker state, and final closure record.

### IDRA-3.7 — Independent review at cluster closure

**Obligation.** Before a cluster closes, Claude and Codex MUST each independently review the **complete cluster implementation**, including interactions among its slices.

*Timing and independence*

1. The review occurs at cluster closure, not at each slice boundary. Each slice MUST still pass its own mechanical verification and acceptance obligations before promotion; the closure review does not permit carrying known violations forward.
2. Both reviewers MUST examine the **same exact implementation revision** together with the approved cluster specification, invariants, and verification evidence.
3. Each reviewer MUST produce an initial assessment before seeing the other's conclusions. Findings are then reconciled. Unresolved correctness disagreements MUST be documented and escalated to the human. Agreement cannot override an evidenced violation.

*Coverage*

4. Each reviewer MUST cover both concerns:
   - **Architecture:** BLUE/GREEN/RED constraints, the governed core/shell boundary, authoritative transition paths, determinism, replay obligations, invariant preservation, and correctness-threatening failures.
   - **Security:** authorization and trust boundaries, untrusted-input handling, secrets and information exposure, adversarial and failure behavior, and vulnerabilities arising from cross-slice interactions.
5. Review depth and required corroboration follow the assurance levels of the affected invariants. This does not waive required coverage.

*Findings*

6. Every finding MUST have exactly one disposition, **BLOCK** or **WARN**, and MUST identify the affected code, the relevant requirement or invariant, its rationale, and its supporting evidence. Dispositions are distinct from invariant assurance levels.
7. **BLOCK:** an evidenced violation of a governing invariant, an architectural or security requirement, or a mandatory verification obligation. It blocks closure until corrected and verified. A known BLOCK MUST NOT be relabeled WARN to permit closure.
8. **WARN:** a nonblocking concern or residual uncertainty, recorded with rationale and remaining uncertainty. A disputed finding that affects correctness MUST be resolved with evidence or escalated to the human.

*Remediation*

9. Multiple BLOCK findings MAY be corrected in one coherent remediation slice within the same open cluster, provided each finding traces individually to its correction and is independently verified. Findings that do not form a coherent slice MUST be split into separate remediation slices.
10. Remediation MUST NOT weaken invariants, tests, or exit criteria (IDRA-3.4). A finding that requires a human-controlled decision MUST be escalated (IDRA-4.4), not handled as ordinary remediation.
11. After corrections, affected verification and review obligations MUST be satisfied for the closing revision.

*Release review*

12. No separate PR or release security review is mandated as a duplicate of the closure review. The review evidence MUST identify the exact reviewed revision; changes made after the review, and multi-cluster integration, MUST still satisfy all applicable invariant and verification obligations. A prior review does not certify unreviewed changes.

**Enforcement.** Closure gate requiring two recorded independent reviews of the closing revision, a findings register with no open BLOCK findings, and traceability from each BLOCK to its correction and verification.

**Evidence.** Both initial assessments, the reviewed revision, the findings register, remediation slices and their verification, escalation records, and the reconciled review outcome.

### IDRA-3.8 — Grounding views are generated from authoritative records

**Obligation.** Every applicable project MUST provide three grounding capabilities through **one local Explorer** with multiple views, not three separately maintained documents or applications:

- **Implementation Map:** modules, responsibilities, dependencies, TCB colors (IDRA-2.5), and linked invariants.
- **Traceability:** invariant → cluster/slice → implementation → verification check → evidence.
- **Navigation:** the same facts organized primarily as `Project → Functional Domain → Invariant → Cluster(s)/Slice(s) → Implementation/Verification Evidence`, with family, component, and verification-status perspectives as alternatives. This is a navigation path, not a rule that an invariant has only one cluster, slice, or location. Each invariant is shown with its identifier, full family name, statement, and assurance level.

Rules for the views:

1. The **single authoritative input** is the existing project records: approved invariants and domains, cluster and slice specifications, transition and verification contracts, module classifications, source and commit information, verification checks, and evidence records.
2. Derived views and indexes MUST NOT become sources of correctness authority, MUST NOT require manual synchronization of duplicated facts, and MUST NOT assert unsupported relationships. A semantic link that cannot be reliably inferred MUST be recorded once, explicitly, in an authoritative record.
3. Views MUST be regenerated from authoritative data, never patched by hand. Missing links, inconsistencies, and stale data MUST be surfaced explicitly.
4. Updates SHOULD be incremental, refreshing only affected records using revisions, hashes, and dependency information where reliable. Opening the Explorer SHOULD load the current generated index rather than trigger full reanalysis.
5. **Incrementality is an optimization, not a correctness exemption.** If affected items cannot be reliably determined, the necessary portion MUST be regenerated or marked stale/incomplete; an unverified index MUST NOT be presented as current.
6. Storage format and implementation MAY vary by project. No separate SEAMS or HEAD_DELTAS documents are required.

**Enforcement.** Generation from authoritative records only, staleness detection against source revisions and hashes, validation that every displayed link resolves to an authoritative record, and rejection of hand-edited derived output.

**Evidence.** Generator configuration, index revision and hash metadata, staleness reports, and link-validation output.

---

## Rule 4 — Human-Gated Multi-Agent Governance & Compliance

### IDRA-4.1 — Consensus precedes code

**Obligation.** Claude and Codex MUST independently review each cluster and slice specification and reach explicit agreement on the same version **before implementation of that work starts**. Agreement MUST cover relevant invariants, intended transitions, interfaces/boundaries, acceptance checks, and known uncertainties. Silence, vague approval, or agreement on different versions is not consensus. If they disagree, ONLY the designated human may resolve the decision.

**Enforcement.** Pre-implementation gate keyed to immutable specification version, with recorded approvals from both reviewers or a recorded human resolution.

**Evidence.** Both review decisions, exact specification hash/version, disagreements and resolutions, and gate outcome.

### IDRA-4.2 — Human authority cannot be delegated to agents

**Obligation.** ONLY the designated human authority may approve, amend, or retire invariants; resolve escalated invariant/design conflicts; authorize required trust-boundary decisions; reduce the assurance profile; and approve amendments to this doctrine. Agents MAY recommend options with consequences but MUST NOT treat their own consensus as human authorization.

**Enforcement.** Authenticated approval records, permission separation, and required human decision gates.

**Evidence.** Named decisions, approved exact text, decision identity, version, and time/order of authorization.

### IDRA-4.3 — Assurance profiles are explicit

**Obligation.** Projects MUST operate under one declared profile: `prototype`, `app`, `strict`, or `protocol`. If none is explicitly selected, `strict` applies. ONLY the designated human MAY lower a project's profile. Profile choice MUST NOT silently remove a foundational obligation. Profile-specific requirements or exceptions MUST be written and approved; agents MUST NOT invent thresholds, exemptions, or alternate interpretations from a profile label.

**Enforcement.** Profile declaration in project configuration, default-to-strict validation, authorized profile-change record, and gates that reject undocumented exemptions.

**Evidence.** Active profile, approved profile-specific controls, profile-change history, and executed gates.

### IDRA-4.4 — Mandatory escalation conditions

**Obligation.** Agents MUST STOP affected work and request a human decision when any of the following occurs:

1. Two invariants conflict, or an invariant conflicts with a requirement.
2. A new trust assumption is needed.
3. The functional core / imperative shell boundary would have to move.
4. Required verification cannot run or its evidence cannot be established.
5. A refactor changes observable behavior.

Work MUST NOT resume on the affected decision until the human resolution is explicit, documented, and incorporated into the active specification. Unaffected independent work MAY continue only if it cannot prejudge or conceal the blocked decision.

**Enforcement.** Blocked-state workflow, escalation register, and prevention of merge/closure for affected work without the required decision.

**Evidence.** Trigger description, blocked work identifiers, alternatives and risks presented, human decision, and resumption record.

### IDRA-4.5 — Agent conduct and repository hygiene

**Obligation.** Agents MUST NOT add AI attribution to commits, pull requests, releases, or issue comments. They MUST NOT introduce AI-style source comments. They MUST NOT add scripts or hooks whose purpose is to remove attribution. They MUST NOT hand off work to agents scheduled to execute later or create unattended deferred agent work as part of the development workflow. These constraints do not authorize falsifying mandatory third-party notices, provenance, or legal acknowledgments; any conflict MUST be escalated.

**Enforcement.** Repository review and policy checks for generated metadata/comments and prohibited cleanup mechanisms; workflow review for deferred agent handoffs. Automated checks may supplement but cannot replace human review for context-sensitive violations.

**Evidence.** Commit/PR/release/issue review, repository scans, workflow configuration, and escalation records for conflicting obligations.

### IDRA-4.6 — Ambiguity, disagreement, and unknowns block invention

**Obligation.** If a requirement is ambiguous in a way that affects correctness, authority, architectural boundaries, or evidence, agents MUST surface the ambiguity and seek an explicit decision. They MUST NOT silently choose a convenient interpretation, fabricate consensus, infer approval from inaction, invent test results, or claim unknown behavior is established. Conflicting normative clauses MUST be escalated for human resolution and a versioned amendment.

**Enforcement.** Explicit `blocked`/`unverified` status in workflow artifacts, validation of approval/evidence references, and specification-change gating.

**Evidence.** Open question or conflict record, resolution decision, amended specification if applicable, and successful rerun of relevant checks.

### IDRA-4.7 — The doctrine has one versioned authority

**Obligation.** Once human-approved, this document (including its normative clauses) MUST be the single authoritative IDRA doctrine. Agents MUST NOT consult an earlier rule list as a second source of obligations. Any change MUST receive explicit human approval, a new doctrine version, and a recorded effective point. A project MUST identify the doctrine version under which it operates. Changes MUST NOT be silently applied halfway through work; affected specifications and checks MUST be reconciled before proceeding.

**Enforcement.** Pinned doctrine version in project metadata, human-controlled changes, version diff review, and consistency checks against current project specifications.

**Evidence.** Approved doctrine version, change record, effective point, project pin, and affected-artifact reconciliation.

---

# Operational Protocol — How IDRA work advances

This section specifies the workflow implied by the four rules; it does not introduce another independent set of foundational rules.

## A. Define correctness

1. Identify feature/behavior and its state transitions.
2. Draft invariants, each with a one-line falsifiable statement, a family and identifier (IDRA-1.7, IDRA-3.5), a primary domain (IDRA-1.8), and a proposed assurance level with rationale (IDRA-1.9). Mark the level `Unresolved` if it cannot be assessed.
3. Obtain Claude and Codex review of each invariant draft.
4. Obtain human line-by-line approval for each invariant, including its family, domain, and assurance level. Record rejected and amended proposals without treating them as active rules.
5. Associate each approved invariant with at least one mechanism that prevents or detects violation, addressing its family's verification concern and its level's obligations. If verification is not possible, mark blocked and escalate.

**Gate A: No implementation without the applicable approved invariants.**

## B. Specify bounded work

1. Create a cluster `C###` with purpose, referenced invariants, boundaries, assumptions, expected transitions, and `C###-EC##` exit criteria.
2. Break it into slices `C###-S##`, each with precise expected behavior, constraints, evidence plan, and dependencies.
3. Have Claude and Codex review the **same immutable version** of each cluster and slice specification.
4. Record agreement or escalate disagreement to the human. Update the specification version after any substantive change and obtain renewed agreement.

**Gate B: No coding for a cluster or slice before the required specification consensus or documented human resolution.**

## C. Implement within boundaries

1. Encode legal states in Rust types.
2. Implement pure transition logic in the dependency-free `no_std` core crate, and classify every module BLUE, GREEN, or RED (IDRA-2.1, IDRA-2.5).
3. Return explicit effects for the imperative shell to execute.
4. Keep environmental inputs explicit; do not leak clock, randomness, I/O, locale, or iteration-order dependencies into core decisions.
5. Halt and escalate the five mandatory stop conditions. Do not transform the implementation into a different design merely because doing so is convenient.

**Gate C: No bypass of state-transition, architectural, or versioning constraints.**

## D. Prove behavior

1. Run type/compiler, lint, and test/CI checks associated with affected invariants.
2. Run deterministic replay and byte-for-byte state comparisons as applicable.
3. For L2 and L3 invariants, deliberately introduce a targeted violation and confirm that the intended check fails for the correct reason; confirm the clean version passes. For L3 invariants, also obtain independent verification and record the analysis of assumptions and limitations.
4. Record the exact code commit, active specifications, commands, inputs, environment/toolchain, outputs, and remaining uncertainty.
5. Repair failing implementation code. Do not modify pass criteria to manufacture success.

**Gate D: No verified status without reproducible, commit-bound evidence.**

## E. Close work

1. Evaluate **every** cluster exit criterion against its evidence.
2. Obtain the independent architecture and security review of the complete cluster from Claude and Codex (IDRA-3.7). Correct BLOCK findings in remediation slices within the open cluster, re-verify, and re-review the closing revision as needed.
3. If any check is failed, unrun, blocked, or unverified, or any BLOCK finding is open, retain an open/blocked cluster status.
4. Close only when the entire exit set is passed and no BLOCK finding remains, under the approved scope.
5. Record closure against the exact commit, doctrine version, invariant versions, review findings, and evidence manifest.
6. Regenerate the affected grounding views (IDRA-3.8).

**Gate E: No partial or deferred closure disguised as completion.**

---

# Required Artifact Schemas

The following schemas describe the **minimum information** that an IDRA-compliant project record needs. Repositories MAY use Markdown, structured data, or a database, provided these fields remain machine- or workflow-verifiable.

## Invariant record

```text
id: SAF-001
status: proposed | approved | rejected | superseded | retired
family: <full family name, e.g. Safety>
primary_domain: <project-specific functional domain>
associated_domains: [<domain>, ...]
domain_history: <versioned record of domain assignments>
statement: <one-line, precise, falsifiable correctness statement>
assurance_level: L1 | L2 | L3 | Unresolved
assurance_rationale: <one line: consequence and verification-difficulty judgment>
rationale: <why the invariant exists>
assumptions: <conditions under which the guarantee must hold>
forbidden_states_or_transitions: <explicit cases>
counterexamples: <representative violations>
applicability: <affected components/transitions>
enforcement_checks: <test/type/lint/CI identifiers>
review:
  claude: <decision + spec version>
  codex: <decision + spec version>
human_decision: <approver, exact decision, exact approved text/version>
```

An example identifier does not itself imply approval.

## Cluster record

```text
id: C001
status: draft | awaiting_consensus | ready | active | blocked | closed
purpose: <bounded engineering outcome>
invariants: [SAF-001, ...]
architecture_and_trust_assumptions: <explicit constraints>
inputs_and_transitions: <defined behavior>
slices: [C001-S01, ...]
exit_criteria: [C001-EC01, ...]
spec_version: <immutable version/hash>
review_consensus: <Claude decision, Codex decision, or human resolution>
closure_review: <reviewed revision, Claude and Codex initial assessments, findings, open BLOCK count>
closure_evidence: <commit + per-check results>
```

## Slice record

```text
id: C001-S01
parent_cluster: C001
status: draft | awaiting_consensus | ready | active | blocked | verified
invariants: [SAF-001, ...]
transition_and_effect_contract: <exact change>
core_shell_boundary: <explicit placement>
verification_plan: <checks and evidence>
spec_version: <immutable version/hash>
review_consensus: <two recorded decisions or human resolution>
implementation_commit: <hash, when implemented>
```

## Exit criterion record

```text
id: C001-EC01
criterion: <unambiguous acceptance condition>
method: <exact check and command>
expected_result: <pass condition>
status: not_run | passed | failed | blocked
actual_result: <recorded output>
evidence: <commit, environment, logs/fixture/artifact>
```

## Module record

```text
module: <crate/module path>
tcb_color: BLUE | GREEN | RED
responsibility: <what the module owns>
dependencies: [<module>, ...]
invariants: [SAF-001, ...]
```

## Review finding record

```text
id: <cluster-scoped finding identifier>
cluster: C001
reviewed_revision: <full commit hash>
reviewer: claude | codex
concern: architecture | security
disposition: BLOCK | WARN
affected_code: <paths/symbols>
requirement_or_invariant: <IDRA-x.y or invariant id>
rationale: <why this is a finding>
evidence: <supporting evidence>
remediation_slice: <C001-Snn, when corrected>
status: open | resolved_verified | escalated
```

## Evidence manifest

```text
doctrine_version: <pinned doctrine version>
spec_versions: <invariant/cluster/slice versions>
source_commit: <full commit hash>
toolchain: <Rust/tool versions and relevant environment>
commands: <exact commands/configuration>
inputs: <fixtures, seeds, context and ordering>
outputs: <result files, digests, logs>
mutation_proof: <L2/L3 invariant violation and expected failure>
uncertainties: <unresolved or unverified claims>
status: verified | failed | blocked | unverified
```

A record with no real command execution or missing outputs MUST NOT assert `verified`.

---

# Decision and Status Semantics

| Term | Meaning | May work proceed? |
|---|---|---|
| **Proposed** | Drafted but not approved | No, if approval is a prerequisite |
| **Approved** | Human explicitly approved the exact requirement/version | Yes, subject to other gates |
| **Ready** | All pre-implementation gates satisfied | Yes |
| **Verified** | Required checks passed with reproducible evidence for exact commit/spec | Yes, subject to remaining gates |
| **Unverified** | Claim lacks sufficient evidence | No claim of correctness/completion |
| **Failed** | Required check contradicted the expected result | Correct implementation or escalate conflict |
| **Blocked** | Mandatory decision/check cannot be completed | No affected progress until resolved |
| **Closed** | Every exit criterion passed with valid evidence | Cluster complete |

Neither `approved` nor agent consensus means code is `verified`; neither `verified` code nor passing tests grants authority to change an invariant.

---

# Assurance Profiles

The recognized profile names are **prototype**, **app**, **strict**, and **protocol**. **Strict is the default**. The profile determines the level of approved project-specific assurance detail, *not* whether an agent may ignore core IDRA obligations. The doctrine does **not** invent fixed numeric thresholds or waive requirements based on the names alone.

Project profiles and invariant assurance levels (IDRA-1.9) are separate dimensions. A profile governs project-wide rigor; an assurance level governs the minimum verification of one invariant. A profile MUST NOT silently negate a level's obligations, and no profile waives the core/shell separation of IDRA-2.1.

A project's profile record MUST identify:

- The active profile and human-approved project-specific control matrix, if any.
- Which checks are mandatory for that project's scope, including the checks required by each invariant's assurance level.
- Any explicitly approved difference in verification depth, with a rationale and effective boundary.
- The human decision authorizing any **reduction** from the current profile.

When controls for the declared profile are missing or ambiguous, the agent MUST NOT manufacture exemptions; it MUST use the applicable default obligations and escalate any required deviation.

---

# Non-negotiable Interpretation Examples

| Situation | Required IDRA response |
|---|---|
| Claude and Codex agree an invariant should change | Draft a change; human must explicitly approve it before it becomes active |
| A test fails but a smaller expected value would make it pass | Do not change the expected value to obtain a pass; fix the code or escalate a genuine spec conflict |
| A slice works but one cluster exit criterion remains unrun | Cluster remains open or blocked |
| An agent cannot run verification in its environment | Mark unverified/blocked and escalate; do not report success |
| A refactor alters observed behavior | Stop affected work and obtain human decision before proceeding |
| The core needs current time | Shell supplies explicit time/context; core cannot read the clock |
| A failure risks replay divergence | Fail-stop; do not retry inside the core |
| Two agents reviewed different specification revisions | No consensus; obtain review of the same version |
| A command's meaning changes | Version it, define the activation boundary, and test replay across the boundary |
| The human has not answered an approval request | No approval exists; do not infer consent |
| An agent thinks an approved L2 invariant should be L1 | Propose the change; the level stays L2 until the human approves the lowering |
| An invariant's consequences cannot be assessed | Mark the level `Unresolved` and seek a decision; do not default to L1 |
| A module mixes authoritative decisions with I/O | Split it into BLUE and RED modules; do not classify it as either, and do not invent a fourth color |
| Logic is moved out of the core to avoid BLUE restrictions | Prohibited; moving the BLUE boundary requires a human decision |
| A reviewer proposes relabeling a BLOCK as WARN so the cluster can close | Not permitted; correct and verify, or escalate a genuine dispute |
| An invariant is reassigned to another domain | Record it as versioned metadata; the ID, statement and evidence are unchanged, and no reapproval is needed unless meaning changes |
| The Explorer index may be out of date | Regenerate the affected portion or show it as stale; never present it as current |

---

# Adoption and Change Control

1. **Approval.** The designated human explicitly approves this document's exact version. Until then its status is `draft` and it must not be represented as an adopted doctrine.
2. **Single authority.** Once approved, the approved IDRA Doctrine is the sole normative source for IDRA requirements. Older formulations are not required reading and are not alternate authorities.
3. **Version pinning.** Every project records the approved IDRA doctrine version it follows.
4. **Changes.** A change request states the exact text to add, modify, or retire, why it is necessary, its effects on active projects, and an effective boundary. Only the human may approve it.
5. **Consistency.** The active project specifications, mechanical checks, and recorded evidence must be reconciled to any approved doctrine change before affected work proceeds.
6. **No silent exceptions.** A waiver or deviation that changes a normative requirement is itself a governed doctrine/project decision; it cannot be inferred by agents from convenience, urgency, or prior practice.

---

## IDRA in one paragraph

**Invariant-Driven Reasoning Architecture (IDRA)** is a human-governed, invariant-first software engineering doctrine. It specifies software as constrained state transitions implemented in a deterministic, dependency-free `no_std` Rust core crate, with every module classified BLUE, GREEN, or RED and environmental effects isolated in an imperative shell. Each invariant has one of eight families, a project-specific functional domain, a falsifiable one-line statement, and a human-approved assurance level (L1–L3) that sets its minimum verification rigor. Autonomous agents collaborate to specify and implement bounded clusters and slices, but they cannot authorize invariant changes or bypass required human decisions. Every correctness claim requires effective mechanical checks and reproducible, commit-bound evidence; every cluster remains open until all exit criteria pass and an independent architecture and security review by Claude and Codex leaves no BLOCK finding. One Explorer, generated from the authoritative records, provides the implementation map, traceability, and navigation. Four foundational rules provide the readable philosophy, and the numbered normative requirements provide the enforceable contract.
