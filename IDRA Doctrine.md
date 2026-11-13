Contents

- [Executive summary](#executive-summary)
- [Status and authority](#status-and-authority)
- [Terms and conventions](#terms-and-conventions)
  - [Normative language and definitions](#normative-language)
  - [How requirements are written](#how-requirements-are-written)
  - [Status terms](#status-terms)
  - [Non-invention](#non-invention)
- [The Four Rules](#the-four-rules)
  - [Rule 1. Invariant-Driven Correctness & Determinism](#rule-1-invariant-driven-correctness-determinism)
    - [1.1Invariants precede implementation](#idra-1-1)
    - [1.2Only the human establishes invariant authority](#idra-1-2)
    - [1.3Illegal states are unrepresentable wherever possible](#idra-1-3)
    - [1.4One controlled state-transition boundary](#idra-1-4)
    - [1.5The core is deterministic](#idra-1-5)
    - [1.6Byte-identical replay is required](#idra-1-6)
    - [1.7Every invariant belongs to exactly one family](#idra-1-7)
    - [1.8Invariants are organized by functional domain](#idra-1-8)
    - [1.9Every invariant has an approved assurance level](#idra-1-9)
  - [Rule 2. Isolated Functional Core & Versioned Boundaries](#rule-2-isolated-functional-core-versioned-bounda)
    - [2.1Functional Core / Imperative Shell is mandatory](#idra-2-1)
    - [2.2Interfaces are closed and explicit](#idra-2-2)
    - [2.3Semantic changes have explicit version and activation boundaries](#idra-2-3)
    - [2.4Correctness-threatening failures are fail-stop](#idra-2-4)
    - [2.5Every module has exactly one TCB color](#idra-2-5)
  - [Rule 3. Mechanical Enforcement & Empirical Proof](#rule-3-mechanical-enforcement-empirical-proof)
    - [3.1Every invariant has a substantiated enforcement argument](#idra-3-1)
    - [3.2L2 and L3 enforcement arguments must withstand challenge](#idra-3-2)
    - [3.3Evidence, not assertion, establishes status](#idra-3-3)
    - [3.4Do not weaken a failing requirement](#idra-3-4)
    - [3.5Work identifiers and hierarchy are mandatory](#idra-3-5)
    - [3.6Cluster completion is all-or-nothing](#idra-3-6)
    - [3.7Independent review at cluster closure](#idra-3-7)
    - [3.8Grounding views are generated from authoritative records](#idra-3-8)
  - [Rule 4. Human-Gated Multi-Agent Governance & Compliance](#rule-4-human-gated-multi-agent-governance-compli)
    - [4.1Consensus precedes code](#idra-4-1)
    - [4.2Human authority cannot be delegated to agents](#idra-4-2)
    - [4.3Assurance profiles are explicit](#idra-4-3)
    - [4.4Mandatory escalation conditions](#idra-4-4)
    - [4.5Agent conduct and repository hygiene](#idra-4-5)
    - [4.6Ambiguity, disagreement, and unknowns block invention](#idra-4-6)
    - [4.7The doctrine has one versioned authority](#idra-4-7)
    - [4.8Each cluster is carried out in a single session](#idra-4-8)
- [Operating under the Doctrine](#operating-under-the-doctrine)
  - [Operational protocol](#operational-protocol)
    - [A. Establish the project concept](#a-establish-the-project-concept)
    - [B. Plan the clusters and overall invariants](#b-plan-the-clusters-and-overall-invariants)
    - [C. Define each cluster and its detailed invariants](#c-define-each-cluster)
    - [D. Plan the slices](#d-plan-the-slices)
    - [E. Implement within boundaries](#e-implement-within-boundaries)
    - [F. Prove behavior](#f-prove-behavior)
    - [G. Close work](#g-close-work)
  - [Assurance profiles](#assurance-profiles)
  - [Change control](#change-control)
- [Appendix A — Record Schemas](#appendix-a-record-schemas)
  - [Invariant record](#invariant-record)
  - [Project concept record](#project-concept-record)
  - [Cluster plan record](#cluster-plan-record)
  - [Cluster record](#cluster-record)
  - [Slice record](#slice-record)
  - [Session record](#session-record)
  - [Exit criterion record](#exit-criterion-record)
  - [Module record](#module-record)
  - [Review finding record](#review-finding-record)
  - [Evidence manifest](#evidence-manifest)
- [Appendix B — Interpretation Examples](#appendix-b-interpretation-examples)
  - [Rule 1 — Invariant-Driven Correctness & Determinism](#examples-rule-1)
  - [Rule 2 — Isolated Functional Core & Versioned Boundaries](#examples-rule-2)
  - [Rule 3 — Mechanical Enforcement & Empirical Proof](#examples-rule-3)
  - [Rule 4 — Human-Gated Multi-Agent Governance & Compliance](#examples-rule-4)

# IDRA Doctrine

**Invariant-Driven Reasoning Architecture**\
**Status:** Approved authoritative doctrine — approved by the designated human authority on 2026-11-12\
**Version:** 1.2\
**Scope:** Software engineering undertaken under IDRA, including work performed by human developers and autonomous engineering agents\
**Implementation language:** Rust

> **Doctrine:** Software is built from human-approved invariants: statements of what must always be true. Nothing changes state unless those invariants still hold, and nothing advances without evidence that they do.

## Executive summary

IDRA (Invariant-Driven Reasoning Architecture) is a complete engineering doctrine for building software in Rust with human developers and autonomous agents working together. It governs the software itself, the process used to build it, and the limits on what agents may decide.

**The problem it addresses.** When agents write and review much of the code, a passing test, a plausible implementation, or two agents agreeing does not prove the software is correct. IDRA makes correctness something people define, machines check, and evidence demonstrates, so that no claim rests on anyone's word alone.

**How it works.** The doctrine rests on four rules.

- **Invariants define correctness.** Work moves from a project concept to a cluster plan carrying the overall invariants, then to clusters whose detailed invariants refine them, and finally to slices that implement those detailed invariants. A gap found at any level sends work back to the level above. Every invariant is approved one by one by a designated human. Each is a one-line, falsifiable statement with one of eight families, a functional domain, and an assurance level from L1 (baseline) to L3 (high assurance) that sets how rigorously it must be verified. The same ordered inputs must always produce byte-identical state.
- **A pure core decides; a shell acts.** Every authoritative decision is made in a deterministic, dependency-free `no_std` Rust crate that performs no I/O. Everything that touches the outside world lives in a separate shell. Each module is classified BLUE (core), GREEN (deterministic support) or RED (shell). Interfaces are closed and versioned, and any failure that threatens correctness halts processing instead of being hidden or retried.
- **Machines check; evidence proves.** Every invariant has an enforcement mechanism and an argument for why that mechanism preserves it on every authoritative path, substantiated by checks that actually run. Tests, mutations, static analysis and reviews exist to substantiate that argument, not to accumulate obligations. L2 and L3 arguments are challenged with methods suited to their risks, including deliberate violations where those establish a critical premise. Claims of completion require reproducible evidence tied to an exact commit. Work is organized into clusters and slices; the verification each cluster needs is agreed before implementation, and a cluster closes only when every exit criterion passes, those agreed obligations are met, and independent architecture and security reviews by Claude and Codex leave no blocking finding: an actual defect or a materially unsupported mandatory claim. One Explorer, generated from the project's records, provides the implementation map, traceability, navigation, the seams between clusters, and the changes since the last session. Each cluster is carried out in a single agent session that starts from these records, not from memory.
- **Humans hold authority.** Claude and Codex must agree on how each piece of work preserves its invariants before code is written. Agents may draft, propose, and implement, but only the designated human can approve the project concept, the cluster plan, and invariants, move the core boundary, lower assurance, resolve escalations, or change this doctrine. Silence and agent consensus never count as approval.

**What adoption requires.** A project names its human authority, pins the doctrine version it follows, declares an assurance profile (`prototype`, `app`, `strict`, or `protocol`; `strict` by default), and keeps the invariant register, work records, module classifications, and evidence the doctrine calls for. Each rule below opens with a short statement of intent, followed by numbered requirements with their enforcement and evidence.

## Status and authority

This document is the IDRA doctrine: the authoritative body of principles from which all IDRA authority, obligations, and practice derive. Its status remains *draft* until the designated human authority explicitly approves it. Approval of the doctrine is distinct from approval of individual project invariants. Once approved, **this document alone** is the governing doctrine.

- **Approval.** The designated human explicitly approves this document's exact version. Until then its status is `draft` and it must not be represented as an adopted doctrine.
- **Single authority.** Once approved, the approved IDRA Doctrine is the sole normative source for IDRA requirements.

---

# Terms and conventions

## Normative language and definitions

- **MUST / MUST NOT:** mandatory requirement or prohibition.
- **ONLY:** exclusive authority or exclusive permitted mechanism.
- **MAY:** permitted but not required; never grants authority to override a MUST.
- **SHOULD:** recommendation; deviations require an explicit rationale and cannot override a MUST.
- **Human authority:** the specifically designated person empowered to approve invariants, doctrine changes, assurance-profile reductions, and escalated design decisions. A tool, model, review vote, implied consent, or automated workflow is not that authority.
- **Mechanically enforced:** checked by a type boundary, compiler, executable test, lint/static analysis, or CI gate, with a verifiable result. A prose assertion alone is not enforcement.
- **Evidence:** a reproducible artifact or recorded check, linked to the precise version of the implementation or record it evaluates.
- **Cluster and slice records:** the plan for a bounded unit of work: which approved invariants it serves, the transitions it will add, its boundaries, and how it will be verified. A record never defines correctness. If a record conflicts with an approved invariant, the invariant controls and the conflict is escalated (IDRA-4.6).
- **Project concept:** the approved starting description of the project: its purpose, scope, main actors and state transitions, and trust assumptions. Claude and Codex review it and the designated human approves it. Everything in the cluster plan traces back to it. The concept does not itself define correctness; invariants do.
- **Cluster plan:** the project-level plan that carries the overall invariants and sets out which clusters exist, the order in which they are delivered, and the dependencies between them. Claude and Codex review the same version of each proposed plan, and it takes effect only when the designated human approves it. Apart from the invariants it carries, the plan never defines correctness: if it conflicts with an approved invariant, the invariant controls and the conflict is escalated (IDRA-4.6).
- **Session:** the single continuous working context in which agents carry out one cluster, from the definition of its detailed invariants through closure. A session starts from the authoritative records and leaves everything later sessions need in them (IDRA-4.8).
- **Overall invariant:** a project-wide invariant carried in the cluster plan. It is a full invariant, with an identifier, family, domain, and assurance level, approved line by line (IDRA-1.2).
- **Detailed invariant:** an invariant defined for a specific cluster. It refines one or more overall invariants and never weakens them. Slices implement detailed invariants; they do not create them.
- **Enforcement argument:** the recorded explanation of why an invariant's enforcement mechanism preserves it on every authoritative path, within its approved scope and stated assumptions (IDRA-3.1). It is the primary unit of assurance; checks and evidence substantiate it.
- **Critical premise:** a statement an enforcement argument depends on, such that if it were false the invariant could be violated. For example: every authoritative state change passes through the transition function; a constructor is private to the core crate.
- **Verification obligation:** a check or other piece of evidence agreed at step C (IDRA-4.1) as necessary to substantiate a stated part of an enforcement argument. The agreed obligations of a cluster are finite and form its verification contract.
- **Challenge:** a method that tests an enforcement argument rather than restating it: a deliberate violation (mutation, fault injection, compile-fail test, model counterexample or equivalent controlled violation), independent analysis, or an architectural check (IDRA-3.2).

## How requirements are written

Each of the four rules opens with a short statement of intent, followed by numbered requirements. Every numbered requirement has three elements:

- **Obligation** — what must be true or what action is required/prohibited.
- **Enforcement** — how a violation is prevented, detected, or blocked. Not every human authorization is compiler-checkable; those decisions must be recorded and checked by a workflow gate instead.
- **Evidence** — what a reviewer can inspect to establish compliance. A check marked *not run* is not a pass.

For project-specific implementation, a requirement record MAY point to named checks, policies, code locations, and evidence artifacts. Such mappings cannot reduce the force of the requirement.

If a rule's statement of intent and its numbered requirements appear to differ, **the numbered requirements control operational behavior**. A contradiction or ambiguity must not be silently resolved by an agent: it is an escalation event under IDRA-4.6.

## Status terms

| Term | Meaning | May work proceed? |
| --- | --- | --- |
| **Proposed** | Drafted but not approved | No, if approval is a prerequisite |
| **Approved** | Human explicitly approved the exact requirement/version | Yes, subject to other gates |
| **Ready** | All pre-implementation gates satisfied | Yes |
| **Verified** | Required checks passed with reproducible evidence for exact commit and record version | Yes, subject to remaining gates |
| **Unverified** | Claim lacks sufficient evidence | No claim of correctness/completion |
| **Failed** | Required check contradicted the expected result | Correct implementation or escalate conflict |
| **Blocked** | Mandatory decision/check cannot be completed | No affected progress until resolved |
| **Closed** | Every exit criterion passed with valid evidence | Cluster complete |

Neither `approved` nor agent consensus means code is `verified`; neither `verified` code nor passing tests grants authority to change an invariant.

## Non-invention

An agent MUST NOT invent approvals, missing requirements, test outcomes, reviewer consensus, trust assumptions, profile exemptions, or evidence. It MUST distinguish **verified**, **unverified**, **blocked**, and **rejected** claims. Where the doctrine does not define an answer that affects authority, correctness, architecture, or verification, the agent MUST request the required decision rather than treating a plausible guess as permission. IDRA-4.6 sets out the full requirement.

---

# The Four Rules

## Rule 1. Invariant-Driven Correctness & Determinism

**Define the conditions of correctness before writing features. Allow only valid states and explicitly constrained transitions. Ensure that the same ordered inputs always produce the same state.**

Human authority determines which invariants govern. Agents may reason about them and propose changes, but they cannot authorize changes themselves. Features are sequences of permitted state transitions, not independent sources of correctness.

### IDRA-1.1 Invariants precede implementation

**Obligation.** Invariants MUST be written down and approved before the work they govern, at two levels: **overall invariants** in the cluster plan before any cluster is defined, and **detailed invariants** for a cluster before its slices are planned. Slices implement approved detailed invariants and MUST NOT introduce new ones. Every detailed invariant MUST refine at least one overall invariant and MUST NOT weaken one. Invariants MUST identify, as applicable, what always holds, what is forbidden, and what outputs or state results must be reproducible. A feature MUST be defined as allowed transitions consistent with those invariants. Work MUST NOT proceed on an unstated correctness assumption as though it were an approved invariant.

**Enforcement.** The implementation-entry gate MUST require references to approved invariant identifiers. Changes to behavior must be checked against those invariants and any new or modified ones. Register validation MUST confirm that every detailed invariant names the overall invariants it refines.

**Evidence.** Approved invariant definitions, refinement links from detailed to overall invariants, links from the relevant cluster/slice record, and recorded gate results.

### IDRA-1.2 Only the human establishes invariant authority

**Obligation.** Agents MAY draft invariants. Claude and Codex MUST review proposed invariants and seek agreement on the draft. The designated human MUST then be able to **approve, reject, or amend each invariant individually, line by line**. Each line presented for approval MUST include the invariant's statement, family (IDRA-1.7), primary domain (IDRA-1.8), and proposed assurance level with its rationale (IDRA-1.9); approving the line approves all of these together. ONLY explicit human approval may create, modify, or retire an invariant. Silence, agent consensus, a passing test, and previous examples MUST NOT be interpreted as approval.

**Enforcement.** The invariant register MUST distinguish proposed, rejected, approved, superseded, and retired records. Only an authenticated, attributable human decision can move a proposal to approved status or change/retire an approved invariant. The implementation gate MUST reject references to unapproved invariants.

**Evidence.** Per-invariant review decisions, approved text/version, identity of the approver, and the active invariant register.

### IDRA-1.3 Illegal states are unrepresentable wherever possible

**Obligation.** Rust types MUST encode legal distinctions and disallow invalid combinations wherever the type system can reasonably express them. An enum such as `Active | Consumed` is preferred to independent flags that can form contradictory combinations. When an invalid state cannot be ruled out at compile time, the system MUST validate it at a defined boundary and preserve the applicable invariant thereafter.

Prevention takes priority over detection. When designing how an invariant is enforced, agents MUST consider these forms in order and use the strongest that the type system and architecture can reasonably express:

1. types, visibility and private or validated constructors that make the violation unrepresentable;
2. exhaustive matching over closed enums;
3. the single controlled transition boundary (IDRA-1.4) over encapsulated state, together with the deterministic core (IDRA-1.5, IDRA-2.1);
4. validation at a defined boundary, after which the invariant is preserved;
5. dynamic detection by runtime checks or tests.

Whenever a weaker form in this ordering is used, the enforcement argument (IDRA-3.1) MUST explain why each stronger applicable form was not used. Where enforcement is dynamic, the argument MUST identify what is checked dynamically.

**Enforcement.** Type design, constructor visibility, validated smart constructors, compile-fail tests where useful, and invariant-focused tests for runtime-only constraints. An unchecked public constructor MUST NOT bypass a protected invariant. Review at step C of the form each enforcement argument relies on.

**Evidence.** Type and constructor definitions, compiler/test results, tests for prohibited states or invalid inputs, and enforcement arguments naming the form used.

### IDRA-1.4 One controlled state-transition boundary

**Obligation.** Every authoritative state change MUST pass through the designated transition interface, conceptually:

```rust
transition(state, command, context) -> Result<(new_state, effects), rejection>
```

The interface MUST either produce a complete new state plus an explicit description of effects, or reject without changing authoritative state. It MUST NOT partially apply a change, perform hidden I/O, or create unreported effects. `context` MUST contain every environmental value needed by the decision. An implementation-specific Rust signature MAY vary in ownership and type names but MUST preserve these semantics.

**Enforcement.** Encapsulation of state mutation, review of all mutation entry points, core isolation checks, and atomic-rejection tests. The effect list MUST be data returned for shell execution, not effects performed by the core.

**Evidence.** Transition API, code-path inventory, tests asserting rejection leaves state unchanged, and effect-contract tests.

### IDRA-1.5 The core is deterministic

**Obligation.** The core MUST be a pure function of its explicit inputs for observable state decisions. It MUST NOT implicitly consult wall-clock time, randomness, locale, OS state, network state, filesystem state, process scheduling, or unordered map iteration. If such information affects a decision, it MUST be provided through an explicit, replayable input with defined ordering and serialization.

**Enforcement.** Dependency checks, restricted imports/capabilities, deterministic collections or canonical ordering, canonical serialization tests, and lint/static-analysis rules appropriate to the crate.

**Evidence.** Core dependency manifest, lint and build output, code review of external-input handling, and deterministic test cases.

### IDRA-1.6 Byte-identical replay is required

**Obligation.** Given the same initial state, ordered commands, explicit contexts, and active rule versions, replay MUST produce byte-identical canonical state output. Nondeterministic replay is a correctness failure even when high-level values appear equivalent. Canonical encoding and ordering MUST be defined for anything compared byte-for-byte.

**Enforcement.** Repeatable replay tests, canonical state serialization, cross-run comparisons, and—where relevant—cross-node or cross-platform comparison fixtures.

**Evidence.** Replay fixtures, exact byte comparisons, environment/version metadata, and commit-bound test results.

### IDRA-1.7 Every invariant belongs to exactly one family

**Obligation.** Every invariant MUST be classified in exactly one of the following eight families. The three-letter abbreviation is used ONLY in the invariant identifier (IDRA-3.5); the record's family field MUST spell out the full name.

| Prefix | Family | Meaning | Minimum verification concern |
| --- | --- | --- | --- |
| SEC | Security | Protects against unauthorized actions, adversarial behavior, and violations of trust boundaries. | Demonstrate that unauthorized or adversarial actions cannot violate protected guarantees. |
| SAF | Safety | Prevents prohibited states and transitions, including violations of validity, structural constraints, conservation, and finality. | Demonstrate that prohibited states and transitions cannot occur. |
| LIV | Liveness | Ensures required progress and eventual completion under explicitly stated assumptions. | Demonstrate that required progress occurs under those assumptions. |
| DET | Determinism | Ensures reproducible execution, canonical ordering, and identical authoritative results from identical inputs. | Demonstrate that identical canonical inputs produce identical authoritative results. |
| RES | Resilience | Ensures committed information survives failures and valid operation can be restored. | Demonstrate that required state and behavior survive failures or recover correctly. |
| EVO | Evolution | Governs controlled changes to interfaces, protocols, rules, and system semantics. | Demonstrate that changes preserve required compatibility and obey version and activation boundaries. |
| OBS | Observability | Ensures trustworthy information is available to inspect, diagnose, and verify system behavior. | Demonstrate that required records and diagnostics accurately represent behavior. |
| PRC | Process Assurance | Ensures engineering procedures, approval gates, verification obligations, and completion criteria are enforced. | Demonstrate that required workflow gates and approvals cannot be bypassed. |

Each family covers the full breadth of its subject: Safety includes structural, validity, conservation and finality properties; Determinism includes ordering; Resilience includes durability and recovery. Classification MUST NOT eliminate any distinct correctness property or its verification obligation. A family specifies a **verification concern**, not a mandatory tool or test count; invariants in the same family MAY require different checks according to their statements, scope, risks, and assumptions. Illustrative methods, not mandates: authorization/adversarial checks (Security); type and state-machine/property checks (Safety); progress/temporal/fault scenarios (Liveness); replay/canonical/differential checks (Determinism); crash/restart/fault injection (Resilience); activation/migration/compatibility checks (Evolution); audit/trace/integrity checks (Observability); CI approval, artifact and workflow-gate validation (Process Assurance).

**Enforcement.** Invariant-register validation that each record has exactly one listed family whose prefix matches its identifier; review at cluster/slice entry that each invariant's checks address its family's verification concern.

**Evidence.** Invariant records with family fields, register validation output, and the invariant-to-check mapping.

### IDRA-1.8 Invariants are organized by functional domain

**Obligation.** Invariants MUST be organized by the functional responsibilities of the software, not by module or file location. Domains are project-specific; IDRA does not prescribe a universal list. Each invariant MUST have exactly one **primary domain** and MAY have **associated domains** where a guarantee spans responsibilities. Family and domain are independent dimensions: the family is not a sub-folder of the domain. Domain assignments are versioned organizational metadata whose history MUST be retained. Reassigning a domain MUST NOT change the invariant's identifier, approved statement, recorded evidence, clusters, or slices, and does not by itself require reapproval. A change that alters an invariant's meaning, scope, assumptions, or enforcement obligations is a substantive change governed by IDRA-1.2, whatever it is labeled.

**Enforcement.** Register validation that each invariant has one primary domain; retained history of domain assignments; review of domain changes for hidden semantic change.

**Evidence.** Domain list, invariant records with domain fields, and domain-assignment history.

### IDRA-1.9 Every invariant has an approved assurance level

**Obligation.** Every invariant MUST carry one of three cumulative assurance levels. A level sets the **minimum verification rigor**; it never exempts an invariant from correctness or evidence obligations, and a higher level does not imply that any check has passed.

| Level | Name | Intended use | Minimum verification obligations |
| --- | --- | --- | --- |
| L1 | Baseline | Assessed, ordinary, limited-consequence guarantees | Human-approved invariant; an identified enforcement mechanism and argument (IDRA-3.1); reproducible, revision-bound evidence that it behaves as expected; applicable assumptions and limits stated. |
| L2 | Elevated | Significant consequences or substantial verification difficulty | All L1 obligations, plus evidence, structural or dynamic as appropriate, that important invalid inputs, transitions and failure paths cannot violate the invariant; and a challenge of the argument's critical premises where stronger evidence does not already establish them (IDRA-3.2). |
| L3 | High Assurance | Severe, irreversible, or system-wide consequences, or exceptional verification difficulty | All L2 obligations, plus independent challenge of the enforcement argument, its assumptions and its coverage, and stronger risk-appropriate substantiation where the consequences or technical difficulty demand it, with explicit analysis of assumptions and limitations. Formal modeling, fault injection or extensive analysis is used when needed to substantiate the specific claim, not automatically. |

A level sets how rigorously an invariant's enforcement argument must be substantiated. It does not set a number of tests, mutants or other checks.

Classification rules:

1. The level is determined by the **consequences of a violation** (security, safety, operational or financial integrity, data integrity and recovery) and by **verification difficulty** (concurrency, distributed or adversarial interaction, and similar challenges).
2. **Highest trigger wins.** Any applicable L3 criterion means L3; otherwise any L2 criterion means L2; otherwise L1. A severe exposure MUST NOT be averaged down because other dimensions are mild.
3. L1 is the default **only after assessment** finds no L2 or L3 criterion. If consequences or verification difficulty cannot be adequately assessed, the level MUST be marked `Unresolved` and a human decision sought; it MUST NOT silently default to L1.
4. Each level MUST carry a one-line **assurance rationale** explaining the consequence and difficulty judgment.
5. Agents propose the level with each drafted invariant; the human approves it as part of the line-by-line approval (IDRA-1.2). Agents MAY strengthen verification but MUST NOT lower an approved level without explicit human authorization.
6. Assurance levels are distinct from the project assurance profile (IDRA-4.3) and from verification status. A profile MUST NOT silently negate a level's obligations.

**Overall invariants.** An overall invariant (one carried in the cluster plan) is enforced by its own project-wide checks, through the detailed invariants that refine it, or both. The choice depends on its level:

- **L3:** it MUST have its own project-wide check, in addition to whatever its detailed invariants provide.
- **L2:** it MUST have either its own project-wide check or a recorded **coverage argument**: an enforcement argument (IDRA-3.1) explaining why its detailed invariants together cover the whole guarantee. Claude and Codex review the argument, and the human approves it with the cluster plan.
- **L1:** the project chooses, and records the choice.

A project-wide check is subject to the same level obligations as any other check, including IDRA-3.2 for L2 and L3. If a coverage argument is shown to be incomplete, for example because a new cluster adds behavior that no detailed invariant covers, the gap is escalated and work returns to the cluster plan.

No numeric thresholds are defined for the boundary between significant and severe consequences. Projects MAY adopt approved examples or indicators; agents MUST NOT invent them.

**Enforcement.** Register validation that each approved invariant has a level of L1, L2 or L3 and a rationale; approval gate rejecting `Unresolved` levels as implementation references; diff checks on level changes requiring a human approval record; closure checks that the level's obligations were met.

**Evidence.** Invariant records with level and rationale, approval records, level-change history, and level-specific verification evidence.

---

## Rule 2. Isolated Functional Core & Versioned Boundaries

**The core decides; the shell interacts.** The functional core must have no hidden environmental effects. Its accepted inputs and change rules must be explicit, closed, and versioned. Failures that threaten state integrity, replay, or agreement must halt affected progress rather than be hidden or retried inside the core.

### IDRA-2.1 Functional Core / Imperative Shell is mandatory

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

### IDRA-2.2 Interfaces are closed and explicit

**Obligation.** The accepted command/message variants, fields, validation rules, and rejection behaviors MUST be enumerated and documented. Unknown, malformed, or unsupported inputs MUST NOT be silently interpreted as another command or accepted through an undocumented fallback.

**Enforcement.** Closed Rust enums and validated decoding boundaries, exhaustive matching, compatibility tests, and rejection tests for unknown or invalid messages.

**Evidence.** Message definitions, schema/decoder tests, and documented rejection outcomes.

### IDRA-2.3 Semantic changes have explicit version and activation boundaries

**Obligation.** A change to the meaning of a command, state transition, validation rule, or protocol MUST be assigned an explicit version and a defined activation point. An execution/replay segment MUST apply the rules specified for that segment; incompatible old and new semantics MUST NOT be silently mixed. Migration behavior, if required, MUST be explicit and testable.

**Enforcement.** Version dispatch, activation-boundary checks, compatibility/replay tests on both sides of the boundary, and rejection of unknown versions.

**Evidence.** Versioned command, transition, and protocol definitions, activation records, migration rules where applicable, and boundary test results.

### IDRA-2.4 Correctness-threatening failures are fail-stop

**Obligation.** If a failure could compromise authoritative state, deterministic replay, or agreement between nodes, the affected processing MUST halt instead of continuing under an assumption of safety. The core MUST return an explicit rejection/error; it MUST NOT conceal or internally retry failures. **Only the shell** MAY implement retries, and only when the retry policy preserves correctness and is explicit.

**Enforcement.** Classified error contracts, fail-stop paths, negative tests for corruption and replay disagreement, and shell retry policy tests. Retry logic inside the core is prohibited.

**Evidence.** Error classification, failure-injection results, halt/rejection behavior, and documented shell retry behavior.

### IDRA-2.5 Every module has exactly one TCB color

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

## Rule 3. Mechanical Enforcement & Empirical Proof

**A requirement that cannot be checked is not yet protected, and a claim without reproducible evidence is not established.** Tests for elevated and high-assurance invariants must demonstrate that they detect violations, and every cluster receives independent review before it closes. A failing check is a defect or a blocking discrepancy, not permission to lower an invariant, test, exit criterion, or expected result.

### IDRA-3.1 Every invariant has a substantiated enforcement argument

**Obligation.** Invariant enforcement is the basis of assurance. Tests, mutations, static analysis and independent reviews exist to substantiate enforcement arguments, not to create an unlimited collection of verification obligations; every mandatory check MUST establish a specific assurance claim.

*Invariant enforcement sufficiency.* Every approved invariant MUST have an identified enforcement mechanism and a substantiated **enforcement argument** explaining why that mechanism preserves the invariant within its approved scope and stated assumptions. The mechanism MUST be effective: Rust type or visibility constraints, compiler checks, the controlled transition boundary, executable tests, lint/static analysis, or CI. A prose claim or reviewer belief alone does not protect an invariant.

1. **Coverage.** An enforcement argument MUST account for every authoritative path capable of affecting the invariant. Where complete coverage cannot be established, the limitation MUST be recorded and the affected assurance claim MUST remain unverified. A shared architectural boundary, such as the single transition interface over encapsulated state, MAY establish coverage for every path that passes through it; the premise that no authoritative path bypasses it then requires evidence like any other premise. Paths need not be documented one by one.
2. **Substantiation.** Verification MUST establish the validity and coverage of the argument, address the verification concern of the invariant's family (IDRA-1.7), and meet the obligations of its assurance level (IDRA-1.9). The verification obligations are agreed before implementation (IDRA-4.1, step C). Every critical premise MUST be stated in the argument or in the records it references, whether or not a separate `premises` field is recorded. A necessary premise that is omitted, or left out of the step-C agreement, is not thereby exempt from substantiation; a finding may identify it as an unsupported claim (IDRA-3.7 item 7).
3. **Shared verification.** One mechanism or check MAY substantiate several invariants, provided its evidence establishes the relevant part of each argument that cites it. Duplicate checks MUST NOT be required merely because the invariants have different identifiers.
4. **Finite obligations.** Additional tests or deliberate violations MUST NOT be required solely because another hypothetical regression can be constructed. Additional verification becomes mandatory only through a new cluster record version, when a defect, an unsupported claim as defined in IDRA-3.7 item 7, or new information shows that the agreed obligations are insufficient.
5. **No overstatement.** A claim MUST NOT be represented as proven beyond what its evidence establishes. Where an invariant cannot be enforced or checked mechanically, that gap MUST be disclosed as unverified and escalated rather than called satisfied (IDRA-4.4).

The argument is recorded compactly in the invariant record (Appendix A): the mechanism, the argument, the evidence it rests on, and its limitations. Further detail is recorded only where the invariant's level or complexity requires it. Records point to existing checks and evidence instead of duplicating them.

**Enforcement.** Enforcement arguments and verification obligations agreed at step C (IDRA-4.1); register validation that every approved invariant has a mechanism, an argument and evidence references; CI gate ensuring named checks exist, execute, and report results; closure review of each argument (IDRA-3.7).

**Evidence.** Invariant records with enforcement arguments and evidence references, the cluster's agreed verification obligations, executed check reports, recorded limitations, and explicit unresolved gaps.

### IDRA-3.2 L2 and L3 enforcement arguments must withstand challenge

**Obligation.** For every L2 and L3 invariant (IDRA-1.9), a check passing on correct code is not by itself sufficient. Its enforcement argument MUST be challenged using methods appropriate to the invariant's risks. Both closure reviewers examine every argument independently (IDRA-3.7); L3 arguments additionally receive independent challenge during verification (IDRA-1.9). Methods include deliberate violations (mutation, fault injection, compile-fail tests, model counterexamples, or equivalent controlled violations), architectural or static checks, independent analysis, or other evidence that tests the argument rather than restating it.

1. Deliberate violations are required where they substantiate a critical premise that is not otherwise established by stronger evidence. Compiler-enforced types, visibility and exhaustiveness, together with an architectural check that the protected path cannot be bypassed, are stronger evidence for what they enforce and do not additionally require a mutation harness.
2. A deliberate violation MUST make the relevant check fail **for the intended violation of the premise it challenges**, against a passing unmodified baseline. A failure for unrelated build or environment reasons does not satisfy this rule, and neither does a violation that challenges no stated premise.
3. No number of tests, mutants or violations is prescribed per invariant or per premise. Each challenge MUST serve a stated assurance purpose. One challenge MAY serve several invariants when it targets a premise each of their arguments relies on (IDRA-3.1 item 3).
4. Which premises are critical, and how each is challenged, is agreed with the verification obligations at step C (IDRA-4.1).

**Enforcement.** Challenge plan agreed at step C; deliberate-violation procedure with an expected-failure assertion and a clean baseline; review of failure cause; review of the stronger evidence relied on where no deliberate violation is used.

**Evidence.** For each challenge: the premise challenged, the method, and its result. For a deliberate violation: the introduced defect or mutation, failing check output and reason, passing unmodified baseline, and associated commit or fixture IDs. For a premise established structurally: the compiler, type or architectural evidence.

### IDRA-3.3 Evidence, not assertion, establishes status

**Obligation.** Claims such as *implemented*, *verified*, *compatible*, *deterministic*, and *complete* MUST be supported by reproducible evidence tied to a specific source commit and relevant record version. Each claim MUST state the assumptions it depends on and the limits of its evidence, and MUST NOT be represented as established beyond what that evidence shows. Remaining uncertainty MUST be stated plainly. A check that was skipped, blocked, or impossible to run MUST NOT be reported as passed.

**Enforcement.** Evidence manifest and status gates; claims must link to run commands/configuration, results, and commit IDs.

**Evidence.** Commit hash, toolchain and command details, fixtures/inputs, check outputs, assumptions and evidence limits, and an uncertainty/blocker record.

### IDRA-3.4 Do not weaken a failing requirement

**Obligation.** When a check fails, agents MUST correct the implementation or identify a genuine conflict and seek human resolution. Agents MUST NOT weaken or delete an invariant, test, exit criterion, or expected output to turn failure into success. They MUST NOT relabel a failing check as optional or move it outside the cluster merely to permit closure. Any legitimate requirement change requires the appropriate human approval before implementation proceeds under the revised requirement.

**Enforcement.** Review of changed test expectations and records, diff checks on invariants and exit criteria, and explicit approval gates for authorized changes.

**Evidence.** Failure record, corrective code diff, unchanged check criteria or approved change record, and passing rerun.

### IDRA-3.5 Work identifiers and hierarchy are mandatory

**Obligation.** Work MUST use these identifier forms:

| Item | Format | Example | Meaning |
| --- | --- | --- | --- |
| Invariant | `<FAM>-<number>` | `SAF-001` | Approved correctness requirement; `<FAM>` is one of the eight family prefixes (IDRA-1.7) |
| Cluster | `C<number>` | `C001` | Bounded delivery unit |
| Slice | `C<number>-S<number>` | `C001-S01` | Implementable part of its parent cluster |
| Exit criterion (EC) | `C<number>-EC<number>` | `C001-EC01` | Objectively verifiable, cluster-level condition required for closure |

Identifier sequences and widths MUST be stable within the project and MUST NOT be reused to disguise changes in meaning. Invariant identifiers MUST NOT embed domains. No separate CamelCase or descriptive name is required; instead every invariant MUST carry a **one-line, precise, falsifiable statement** of what remains true or what is forbidden. Identifiers required by external tools (for example `SAF_001` for a model checker) MUST be derived mechanically from the invariant identifier rather than maintained as manual aliases. Every slice MUST identify its parent cluster and relevant invariants. Every cluster MUST state its exit criteria before work begins. Exit criteria belong to the cluster, and MAY depend on evidence from multiple slices.

**Enforcement.** Schema/format validation in project tooling, referential-integrity checks between artifacts, and record review gates.

**Evidence.** Work register, cluster and slice records, identifier validation, and exit-criterion mappings.

### IDRA-3.6 Cluster completion is all-or-nothing

**Obligation.** A cluster MUST NOT be marked closed until, for the closing revision:

1. **every** specified exit criterion passes with required evidence;
2. every verification obligation agreed for the cluster (IDRA-3.1, IDRA-4.1) is satisfied;
3. the enforcement argument of each detailed invariant is substantiated. Recording a limitation or requesting escalation does not resolve a BLOCK or authorize closure: where a limitation leaves a mandatory premise unsupported, the claim remains unverified and closure remains blocked until evidence substantiates it or an explicit human resolution authorizes the applicable change under IDRA-3.4 and Change control;
4. the independent closure review (IDRA-3.7) applies to the closing revision with every BLOCK finding resolved and verified.

These obligations are finite: they are the closure obligation, not an open-ended search for further hypothetical regressions. No unfinished check or unresolved correctness obligation may be silently deferred to later work. A verification obligation is added to an open cluster only through a new cluster record version (IDRA-4.1), and only when a defect, an unsupported claim (IDRA-3.7 item 7), or new information shows that the agreed obligations are insufficient. A possible improvement that shows neither a defect nor an unsupported claim is recorded as a WARN finding of consequence `improvement`; it is not an unresolved correctness obligation and does not block closure. Real defects and missing mandatory evidence always block. If closure is blocked, the cluster MUST remain open or blocked, with the unmet checks and uncertainty listed. Scope may only change through an explicit authorized decision; the agent may not redefine success to match what was finished.

**Enforcement.** Exit-criterion ledger and closure gate requiring every exit criterion to be passed, every agreed verification obligation to be satisfied, every enforcement argument to be substantiated, and no open BLOCK, plus approval records for changes to scope. Optional exploratory checks do not by themselves gate closure; any defect or unsupported claim they reveal does.

**Evidence.** Exit-criterion list, agreed verification obligations, per-check result and proof, commit IDs, unresolved-blocker state, and final closure record.

### IDRA-3.7 Independent review at cluster closure

**Obligation.** Before a cluster closes, Claude and Codex MUST each independently review the **complete cluster implementation**, including interactions among its slices.

*Timing and independence*

1. The review occurs at cluster closure, not at each slice boundary. Each slice MUST still pass its own mechanical verification and acceptance obligations before promotion; the closure review does not permit carrying known violations forward.
2. Both reviewers MUST examine the **same exact implementation revision** together with the approved cluster record, invariants, and verification evidence.
3. Each reviewer MUST produce an initial assessment before seeing the other's conclusions. Findings are then reconciled. Unresolved correctness disagreements MUST be documented and escalated to the human. Agreement cannot override an evidenced violation.

*Coverage*

4. Each reviewer MUST cover both concerns: - **Architecture:** BLUE/GREEN/RED constraints, the governed core/shell boundary, authoritative transition paths, determinism, replay obligations, invariant preservation, and correctness-threatening failures. - **Security:** authorization and trust boundaries, untrusted-input handling, secrets and information exposure, adversarial and failure behavior, and vulnerabilities arising from cross-slice interactions.
5. Review depth and required corroboration follow the assurance levels of the affected invariants. This does not waive required coverage. Reviewers MUST evaluate the agreed enforcement arguments and the mandatory evidence: whether each mechanism actually preserves its invariant, whether every authoritative path is covered, and whether the assumptions are justified. Additional exploratory testing is permitted, but it MUST NOT expand the agreed verification obligations unless it identifies a real defect or a material gap in the required assurance (an unsupported claim, item 7).

*Findings*

6. Every finding MUST have exactly one disposition, **BLOCK** or **WARN**, and exactly one consequence: **defect**, **unsupported claim**, **improvement**, or **uncertainty**. It MUST identify the affected code, the affected invariant or requirement, the consequence, its rationale, and its supporting evidence. Dispositions are distinct from invariant assurance levels.
7. **BLOCK:** a finding whose consequence is either
   - a **defect**: an evidenced violation, in the reviewed revision, of a governing invariant or of an architectural or security requirement. A defect blocks whether or not it was anticipated at step C; or
   - an **unsupported claim**: a mandatory premise of an invariant's enforcement argument lacks sufficient evidence, is contradicted by evidence, or depends on an enforcement path whose applicability has not been established. An agreed verification obligation that is not satisfied is an unsupported claim, and so is an argument that does not account for every authoritative path capable of affecting its invariant (IDRA-3.1 item 1). The finding MUST name the specific premise or obligation. A surviving mutant or other constructed regression is not by itself an unsupported claim.

   A BLOCK blocks closure until corrected and verified. A known BLOCK MUST NOT be relabeled WARN to permit closure.
8. **WARN:** a nonblocking finding whose consequence is an **improvement** (additional verification or a hypothetical regression that shows neither a defect nor an unsupported claim; recorded as test debt and not a correctness obligation) or an **uncertainty** (residual uncertainty, recorded with rationale). Claude and Codex classify findings under these rules; a disputed finding that affects correctness MUST be resolved with evidence or escalated to the human.

*Remediation*

9. Multiple BLOCK findings MAY be corrected in one coherent remediation slice within the same open cluster, provided each finding traces individually to its correction and is independently verified. Findings that do not form a coherent slice MUST be split into separate remediation slices.
10. Remediation MUST NOT weaken invariants, tests, or exit criteria (IDRA-3.4). A finding that requires a human-controlled decision MUST be escalated (IDRA-4.4), not handled as ordinary remediation.
11. After corrections, affected verification and review obligations MUST be satisfied for the closing revision. Each remediation slice record declares the impact of its change, and Claude and Codex agree on it with the slice (IDRA-4.1). The re-review is scoped by that impact:
    - **evidence or documentation only:** the affected evidence and the claims it supports;
    - **local implementation with unchanged contracts:** the affected invariants and their dependencies;
    - **authoritative semantics, a security or trust boundary, or architecture:** a full review of the affected architecture and the relevant integration.

    Both reviewers re-review independently, as in item 3, and each examines the complete change since the previously reviewed revision. If the impact cannot be established reliably, the review MUST be broadened. A reviewer who finds the declared impact understated MUST review at the broader scope and record the understatement as a finding. Earlier review conclusions stand only for what the change cannot affect; a change that invalidates earlier review evidence requires that part to be reviewed again.

*Release review*

12. No separate PR or release security review is mandated as a duplicate of the closure review. The review evidence MUST identify the exact reviewed revision; changes made after the review, and multi-cluster integration, MUST still satisfy all applicable invariant and verification obligations. A prior review does not certify unreviewed changes.

**Enforcement.** Closure gate requiring two recorded independent reviews of the closing revision, a findings register with no open BLOCK findings, register validation that every BLOCK has consequence defect or unsupported claim and every unsupported claim names its premise or obligation, a check of each remediation slice's declared impact against its change, and traceability from each BLOCK to its correction and verification.

**Evidence.** Both initial assessments, the reviewed revision, the findings register with consequences, remediation slices with their declared impact and their verification, escalation records, and the reconciled review outcome.

### IDRA-3.8 Grounding views are generated from authoritative records

**Obligation.** Every applicable project MUST provide five grounding capabilities through **one local Explorer** with multiple views:

- **Implementation Map:** modules, responsibilities, dependencies, TCB colors (IDRA-2.5), and linked invariants.
- **Traceability:** invariant → cluster/slice → implementation → verification check → evidence.
- **Navigation:** the same facts organized primarily as `Project → Functional Domain → Invariant → Cluster(s)/Slice(s) → Implementation/Verification Evidence`, with family, component, and verification-status perspectives as alternatives. This is a navigation path, not a rule that an invariant has only one cluster, slice, or location. Each invariant is shown with its identifier, full family name, statement, and assurance level.
- **Seams:** the interfaces, shared types, and dependencies between clusters, so that a session can see what its cluster must not break elsewhere.
- **Changes since:** what changed in the records and code since a given commit, by default the closing commit of the previous session (IDRA-4.8).

Rules for the views:

1. The **single authoritative input** is the existing project records: approved invariants and domains, cluster and slice records, transition and verification contracts, module classifications, source and commit information, verification checks, and evidence records.
2. Derived views and indexes MUST NOT become sources of correctness authority, MUST NOT require manual synchronization of duplicated facts, and MUST NOT assert unsupported relationships. A semantic link that cannot be reliably inferred MUST be recorded once, explicitly, in an authoritative record.
3. Views MUST be regenerated from authoritative data, never patched by hand. Missing links, inconsistencies, and stale data MUST be surfaced explicitly.
4. Updates SHOULD be incremental, refreshing only affected records using revisions, hashes, and dependency information where reliable. Opening the Explorer SHOULD load the current generated index rather than trigger full reanalysis.
5. **Incrementality is an optimization, not a correctness exemption.** If affected items cannot be reliably determined, the necessary portion MUST be regenerated or marked stale/incomplete; an unverified index MUST NOT be presented as current.
6. Storage format and implementation MAY vary by project.

**Enforcement.** Generation from authoritative records only, staleness detection against source revisions and hashes, validation that every displayed link resolves to an authoritative record, and rejection of hand-edited derived output.

**Evidence.** Generator configuration, index revision and hash metadata, staleness reports, and link-validation output.

---

## Rule 4. Human-Gated Multi-Agent Governance & Compliance

**Autonomy operates within human-defined authority.** Claude and Codex review each cluster and slice record before implementation, agreed decisions are documented, and specified conflicts or changes stop for the human. Assurance profiles determine applicable rigor but cannot silently waive core obligations. Agent conduct and execution boundaries are explicit.

### IDRA-4.1 Consensus precedes code

**Obligation.** Claude and Codex MUST independently review each cluster and slice record and reach explicit agreement on the same version **before implementation of that work starts**. Agreement MUST cover relevant invariants, intended transitions, interfaces/boundaries, acceptance checks, and known uncertainties. For a cluster record it MUST also cover each detailed invariant's enforcement argument, its assumptions, and the verification obligations that will substantiate it, including how critical premises of L2 and L3 arguments are challenged (IDRA-3.1, IDRA-3.2). Together with the other pre-implementation gates, agreement on these authorizes implementation, and once agreed they are the cluster's verification contract. Reviewer agreement governs the verification plan within existing approved requirements. It does not authorize changes to an invariant's approved meaning, scope, assumptions, assurance level, or enforcement obligations; those remain subject to IDRA-1.2 and IDRA-1.8. Silence, vague approval, or agreement on different versions is not consensus. If they disagree, ONLY the designated human may resolve the decision.

A new version of an agreed record MAY be reviewed on its difference from the agreed version. Parts the change does not affect keep their agreement and MUST NOT be renegotiated. New information MAY still invalidate an agreed decision; the affected part is then revised as a new version and agreed again.

**Enforcement.** Pre-implementation gate keyed to immutable record version, with recorded approvals from both reviewers or a recorded human resolution; for a delta review, the agreed base version and the difference reviewed.

**Evidence.** Both review decisions, exact record hash/version (and base version for a delta review), disagreements and resolutions, and gate outcome.

### IDRA-4.2 Human authority cannot be delegated to agents

**Obligation.** ONLY the designated human authority may approve, amend, or retire invariants; resolve escalated invariant/design conflicts; authorize required trust-boundary decisions; approve the project concept, the cluster plan, and any change to either; reduce the assurance profile; and approve amendments to this doctrine. Agents MAY recommend options with consequences but MUST NOT treat their own consensus as human authorization.

**Enforcement.** Authenticated approval records, permission separation, and required human decision gates.

**Evidence.** Named decisions, approved exact text, decision identity, version, and time/order of authorization.

### IDRA-4.3 Assurance profiles are explicit

**Obligation.** Projects MUST operate under one declared profile: `prototype`, `app`, `strict`, or `protocol`. If none is explicitly selected, `strict` applies. ONLY the designated human MAY lower a project's profile. Profile choice MUST NOT silently remove a foundational obligation. Profile-specific requirements or exceptions MUST be written and approved; agents MUST NOT invent thresholds, exemptions, or alternate interpretations from a profile label.

**Enforcement.** Profile declaration in project configuration, default-to-strict validation, authorized profile-change record, and gates that reject undocumented exemptions.

**Evidence.** Active profile, approved profile-specific controls, profile-change history, and executed gates.

### IDRA-4.4 Mandatory escalation conditions

**Obligation.** Agents MUST STOP affected work and request a human decision when any of the following occurs:

1. Two invariants conflict, or an invariant conflicts with a requirement.
2. A new trust assumption is needed.
3. The functional core / imperative shell boundary would have to move.
4. Required verification cannot run or its evidence cannot be established.
5. A refactor changes observable behavior.

Work MUST NOT resume on the affected decision until the human resolution is explicit, documented, and incorporated into the affected records. Unaffected independent work MAY continue only if it cannot prejudge or conceal the blocked decision.

**Enforcement.** Blocked-state workflow, escalation register, and prevention of merge/closure for affected work without the required decision.

**Evidence.** Trigger description, blocked work identifiers, alternatives and risks presented, human decision, and resumption record.

### IDRA-4.5 Agent conduct and repository hygiene

**Obligation.** Agents MUST NOT add AI attribution to commits, pull requests, releases, or issue comments. They MUST NOT introduce AI-style source comments. They MUST NOT add scripts or hooks whose purpose is to remove attribution. They MUST NOT hand off work to agents scheduled to execute later or create unattended deferred agent work as part of the development workflow. These constraints do not authorize falsifying mandatory third-party notices, provenance, or legal acknowledgments; any conflict MUST be escalated.

**Enforcement.** Repository review and policy checks for generated metadata/comments and prohibited cleanup mechanisms; workflow review for deferred agent handoffs. Automated checks may supplement but cannot replace human review for context-sensitive violations.

**Evidence.** Commit/PR/release/issue review, repository scans, workflow configuration, and escalation records for conflicting obligations.

### IDRA-4.6 Ambiguity, disagreement, and unknowns block invention

**Obligation.** If a requirement is ambiguous in a way that affects correctness, authority, architectural boundaries, or evidence, agents MUST surface the ambiguity and seek an explicit decision. They MUST NOT silently choose a convenient interpretation, fabricate consensus, infer approval from inaction, invent test results, or claim unknown behavior is established. Conflicting normative clauses MUST be escalated for human resolution and a versioned amendment.

**Enforcement.** Explicit `blocked`/`unverified` status in workflow artifacts, validation of approval/evidence references, and record-change gating.

**Evidence.** Open question or conflict record, resolution decision, amended record or doctrine, as applicable, and successful rerun of relevant checks.

### IDRA-4.7 The doctrine has one versioned authority

**Obligation.** Once human-approved, this document (including its normative clauses) MUST be the single authoritative IDRA doctrine. No other rule list is a source of IDRA obligations. Any change MUST receive explicit human approval, a new doctrine version, and a recorded effective point. A project MUST identify the doctrine version under which it operates. Changes MUST NOT be silently applied halfway through work; affected records and checks MUST be reconciled before proceeding.

**Enforcement.** Pinned doctrine version in project metadata, human-controlled changes, version diff review, and consistency checks against current project records.

**Evidence.** Approved doctrine version, change record, effective point, project pin, and affected-artifact reconciliation.

### IDRA-4.8 Each cluster is carried out in a single session

**Obligation.** Each cluster MUST be carried out in one session, from the definition of its detailed invariants through closure, except for continuation sessions explicitly authorized under item 4. Every original or continuation session MUST work on exactly one cluster.

1. **Start from records.** A session MUST begin only from the authoritative records: the approved project concept, the approved cluster plan, the applicable approved invariants, its cluster record, and Explorer views regenerated at the starting commit, including Seams and Changes since the previous session's closing commit (IDRA-3.8). Context remembered from an earlier session MUST NOT be treated as a source of facts, approvals, or decisions.
2. **Stay within the cluster.** A session MUST NOT carry out work on another cluster or revise the cluster plan or project concept. If it finds a gap at a higher level, it records and escalates the gap and stops; the higher level is revised outside the session (operational protocol).
3. **End with records.** Before a session ends, everything later sessions need MUST be in the authoritative records: cluster and slice records, invariant and evidence records, review findings, open questions, and escalations. Anything not recorded does not carry forward.
4. **Interrupted sessions.** If a session ends before its cluster closes, the cluster MUST be marked blocked. ONLY the designated human MAY authorize a new session to continue it.
5. **Continuation sessions.** A continuation session works on the same cluster and starts from the records under item 1, with Changes since the interrupted session's end commit; agents MUST NOT reconstruct the earlier session from memory. Recorded approvals, agreements, evidence and findings remain in force and are not renegotiated unless the records show them invalidated. Artifacts the interrupted session produced but did not record, such as review outputs, MAY be brought into the records only verbatim, with their origin stated; they are then unreconciled inputs, not established facts, until reconciled under this doctrine.

**Enforcement.** A session record bound to one cluster identifier with start and end commits; a start gate confirming the Explorer views were regenerated at the starting commit; a session-end check that the required records are complete; blocked status for interrupted sessions; a human authorization record for any continuation; and origin statements for artifacts brought into the records by a continuation.

**Evidence.** Session records, the regenerated views used at the start, the cluster's closing or blocked status, any human authorization to continue, and the origin of any artifacts a continuation recorded.

---

# Operating under the Doctrine

## Operational protocol

This protocol sets out the workflow implied by the four rules; it does not introduce another independent set of foundational rules.

Work moves from the general to the specific: the project concept, then the cluster plan with the overall invariants, then each cluster with its detailed invariants, then the slices that implement them. Each level traces to the one above it. Each cluster, from step C through closure, is carried out in a single session (IDRA-4.8).

**Returning to an earlier level.** If work at any level reveals that the level above is missing something or is wrong, work at that level stops and returns to the level above. That level is revised as a new version and goes through its review and approval again before the work continues. A gap found while planning clusters returns to the project concept (A); a gap found while defining a cluster returns to the cluster plan (B); a gap found while planning slices returns to the cluster (C). Unaffected work MAY continue only if it cannot prejudge or conceal the revision (IDRA-4.4).

### A. Establish the project concept

1. Describe what the project is for, its scope, its main actors and state transitions, and its trust assumptions.
2. Have Claude and Codex review the **same immutable version** of the concept. Record both decisions and any disagreement.
3. Obtain the human's approval of that exact version. Any later change to the concept is a new version and requires renewed review and approval.

**Gate A: No cluster plan before an approved project concept.**

### B. Plan the clusters and overall invariants

1. Draft the overall invariants that follow from the concept, each with a one-line falsifiable statement, a family and identifier (IDRA-1.7, IDRA-3.5), a primary domain (IDRA-1.8), and a proposed assurance level with rationale (IDRA-1.9). Mark the level `Unresolved` if it cannot be assessed.
2. Draft the cluster plan: the clusters the project needs, the purpose of each, the overall invariants each serves, the order of delivery, and the dependencies between clusters.
3. Have Claude and Codex review the **same immutable version** of the plan and of each overall invariant draft. Record both decisions and any disagreement.
4. Obtain human line-by-line approval of each overall invariant, including its family, domain, and assurance level, and approval of the exact plan version. Record rejected and amended proposals without treating them as active rules. Any later change to the overall invariants, to which clusters exist, or to their order or dependencies is a new plan version and requires renewed review and approval.
5. For each approved overall invariant, record how it will be enforced, with its enforcement argument: by its own project-wide checks, through the detailed invariants that will refine it, or both (IDRA-3.1). An L3 overall invariant requires its own project-wide check; an L2 overall invariant requires its own check or an approved coverage argument (IDRA-1.9).

**Gate B: No work on a cluster before an approved cluster plan that includes it, with its overall invariants approved.**

### C. Define each cluster and its detailed invariants

1. Draft the cluster's detailed invariants in the same form as overall invariants. Each MUST refine at least one approved overall invariant and MUST NOT weaken one. A detailed invariant that cannot be traced to an overall invariant shows a gap in the cluster plan: return to B.
2. Create the cluster record `C###`, as included in the approved cluster plan, with purpose, detailed invariants, boundaries, assumptions, expected transitions, and `C###-EC##` exit criteria.
3. Have Claude and Codex review the **same immutable version** of the cluster record and of each detailed invariant draft.
4. Obtain human line-by-line approval of each detailed invariant. Record agreement on the cluster record or escalate disagreement to the human. Update the record version after any substantive change and obtain renewed agreement.
5. For each approved detailed invariant, record its enforcement mechanism and argument (IDRA-3.1), choosing the strongest available form (IDRA-1.3) and addressing its family's verification concern. Record the verification obligations that will substantiate the argument at its level, including how critical premises of L2 and L3 arguments are challenged (IDRA-3.2). Claude and Codex agree on these with the cluster record (IDRA-4.1); they form the cluster's verification contract. If an argument cannot cover every authoritative path, record the limitation; the affected claim stays unverified. If verification is not possible, mark blocked and escalate.

**Gate C: No slice before the cluster's detailed invariants are approved and Claude and Codex agree on the same version of the cluster record, or a documented human resolution.**

### D. Plan the slices

1. Break the cluster into slices `C###-S##`. Each slice names the approved detailed invariants of its cluster that it implements, with precise expected behavior, constraints, the verification obligations it discharges, and dependencies. A remediation slice also declares the impact of its change (IDRA-3.7 item 11).
2. Slices MUST NOT introduce invariants. If a slice reveals a missing or inadequate invariant, return to C.
3. Have Claude and Codex review the **same immutable version** of each slice record. Record agreement or escalate disagreement to the human. Update the record version after any substantive change and obtain renewed agreement.

**Gate D: No coding for a slice before Claude and Codex agree on the same version of its record, or a documented human resolution.**

### E. Implement within boundaries

1. Enforce invariants in the strongest available form (IDRA-1.3), beginning with encoding legal states in Rust types.
2. Implement pure transition logic in the dependency-free `no_std` core crate, and classify every module BLUE, GREEN, or RED (IDRA-2.1, IDRA-2.5).
3. Return explicit effects for the imperative shell to execute.
4. Keep environmental inputs explicit; do not leak clock, randomness, I/O, locale, or iteration-order dependencies into core decisions.
5. Halt and escalate the five mandatory stop conditions. Do not transform the implementation into a different design merely because doing so is convenient.

**Gate E: No bypass of state-transition, architectural, or versioning constraints.**

### F. Prove behavior

1. Run type/compiler, lint, and test/CI checks associated with affected invariants.
2. Run deterministic replay and byte-for-byte state comparisons as applicable.
3. For L2 and L3 invariants, challenge the enforcement argument as agreed at step C (IDRA-3.2). Where a critical premise is not established by stronger evidence, deliberately introduce a targeted violation of it and confirm that the intended check fails for the correct reason; confirm the clean version passes. For L3 invariants, also obtain independent challenge of the argument, its assumptions and its coverage, and record the analysis of assumptions and limitations.
4. Record the exact code commit, active record versions, commands, inputs, environment/toolchain, outputs, assumptions and evidence limits, and remaining uncertainty.
5. Repair failing implementation code. Do not modify pass criteria to manufacture success.

**Gate F: No verified status without reproducible, commit-bound evidence.**

### G. Close work

1. Evaluate **every** cluster exit criterion and every agreed verification obligation against its evidence.
2. Obtain the independent architecture and security review of the complete cluster from Claude and Codex, evaluating the agreed enforcement arguments and mandatory evidence (IDRA-3.7). Correct BLOCK findings in remediation slices within the open cluster, re-verify, and re-review the closing revision to the extent the corrections can affect (IDRA-3.7 item 11).
3. If any exit criterion or mandatory check is failed, unrun, blocked, or unverified, any agreed verification obligation is unmet, any enforcement argument is unsubstantiated, or any BLOCK finding is open, retain an open/blocked cluster status. Optional exploratory checks do not by themselves gate closure; any defect or unsupported claim they reveal does.
4. Close only when the entire exit set is passed, the agreed verification obligations are satisfied, and no BLOCK finding remains, under the approved scope. Improvement findings are recorded as WARN and do not block closure.
5. Record closure against the exact commit, doctrine version, invariant versions, review findings, and evidence manifest.
6. Regenerate the affected grounding views (IDRA-3.8).
7. End the session only when everything later sessions need is in the records (IDRA-4.8).

**Gate G: No partial or deferred closure disguised as completion.**

## Assurance profiles

IDRA-4.3 defines the four profiles (`prototype`, `app`, `strict`, and `protocol`), makes `strict` the default, and reserves any reduction to the human. This section describes how a project applies and records its profile. The profile determines the level of approved project-specific assurance detail, *not* whether an agent may ignore core IDRA obligations. The doctrine does **not** invent fixed numeric thresholds or waive requirements based on the names alone.

Project profiles and invariant assurance levels (IDRA-1.9) are separate dimensions. A profile governs project-wide rigor; an assurance level governs the minimum verification of one invariant. A profile MUST NOT silently negate a level's obligations, and no profile waives the core/shell separation of IDRA-2.1.

A project's profile record MUST identify:

- The active profile and human-approved project-specific control matrix, if any.
- Which checks are mandatory for that project's scope, including the checks required by each invariant's assurance level.
- Any explicitly approved difference in verification depth, with a rationale and effective boundary.
- The human decision authorizing any **reduction** from the current profile.

When controls for the declared profile are missing or ambiguous, the agent MUST NOT manufacture exemptions; it MUST use the applicable default obligations and escalate any required deviation.

## Change control

1. **Version pinning.** Every project records the approved IDRA doctrine version it follows.
2. **Changes.** A change request states the exact text to add, modify, or retire, why it is necessary, its effects on active projects, and an effective boundary. Only the human may approve it.
3. **Consistency.** The active project records, mechanical checks, and recorded evidence must be reconciled to any approved doctrine change before affected work proceeds.
4. **No silent exceptions.** A waiver or deviation that changes a normative requirement is itself a governed doctrine/project decision; it cannot be inferred by agents from convenience, urgency, or prior practice.

---

# Appendix A — Record Schemas

The following schemas describe the **minimum information** that an IDRA-compliant project record needs. Repositories MAY use Markdown, structured data, or a database, provided these fields remain machine- or workflow-verifiable.

## Invariant record

```text
id: SAF-001
status: proposed | approved | rejected | superseded | retired
level: overall | detailed
cluster: <C001, for a detailed invariant>
refines: [<overall invariant id>, ...]  (detailed invariants only)
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
enforcement:
  mechanism: <type_constraint | private_constructor | exhaustive_match | controlled_transition | boundary_validation | static_check | runtime_check | ...>
  argument: <why the mechanism preserves the invariant on every authoritative path, within its scope and assumptions>
  evidence: [<check identifiers: test/type/lint/CI>]
  limitations: [<coverage or evidence limits; empty if none>]
  # only where the level or complexity requires it (agreed at step C):
  location: <where the mechanism is enforced>
  premises: <critical premises the argument depends on>
  bypass_paths: <paths considered and why they cannot bypass the mechanism>
review:
  claude: <decision + invariant draft version>
  codex: <decision + invariant draft version>
human_decision: <approver, exact decision, exact approved text/version>
```

An example identifier does not itself imply approval. The `enforcement` block points to existing checks and evidence; it does not copy their results.

## Project concept record

```text
concept_version: <immutable version/hash>
status: proposed | approved | rejected | superseded
purpose: <what the project is for>
scope: <what is in and out of scope>
actors: <who and what interacts with the system>
principal_transitions: <the main state changes the system performs>
trust_assumptions: <explicit trust boundaries and assumptions>
review:
  claude: <decision + concept version>
  codex: <decision + concept version>
human_decision: <approver, exact decision, approved concept version>
```

## Cluster plan record

```text
plan_version: <immutable version/hash>
status: proposed | approved | rejected | superseded
project_concept_version: <approved concept version>
overall_invariants: [SAF-001, ...]
overall_enforcement:
  - invariant: SAF-001
    method: own_check | refinement | both
    project_wide_checks: [<check identifiers>]
    coverage_argument: <why the detailed invariants cover the whole guarantee, for L2 without its own check>
clusters:
  - id: C001
    purpose: <bounded engineering outcome>
    serves: [<overall invariant id>, ...]
    depends_on: [<cluster id>, ...]
delivery_order: [C001, C002, ...]
review:
  claude: <decision + plan version>
  codex: <decision + plan version>
human_decision: <approver, exact decision, approved plan version>
```

## Cluster record

```text
id: C001
status: draft | awaiting_consensus | ready | active | blocked | closed
cluster_plan_version: <approved plan version that includes this cluster>
session: <session id>
purpose: <bounded engineering outcome>
detailed_invariants: [SAF-010, ...]
architecture_and_trust_assumptions: <explicit constraints>
inputs_and_transitions: <defined behavior>
slices: [C001-S01, ...]
exit_criteria: [C001-EC01, ...]
verification_obligations:
  - invariant: <detailed invariant id, or several for a shared obligation>
    substantiates: <the part of the enforcement argument or the critical premise it establishes>
    method: <check | deliberate violation | static/architectural check | independent analysis | model>
    check: <check identifier>
record_version: <immutable version/hash>
review_consensus: <Claude decision, Codex decision, or human resolution>
closure_review: <reviewed revision, Claude and Codex initial assessments, findings, open BLOCK count>
closure_evidence: <commit + per-check results>
```

## Slice record

```text
id: C001-S01
parent_cluster: C001
status: draft | awaiting_consensus | ready | active | blocked | verified
implements: [<detailed invariant id of the parent cluster>, ...]
transition_and_effect_contract: <exact change>
core_shell_boundary: <explicit placement>
verification_plan: <checks and evidence>
discharges: [<verification obligations of the parent cluster>, ...]
impact: evidence_only | local | semantic_boundary_or_architecture  (remediation slices)
remediates: [<finding ids>, ...]  (remediation slices)
record_version: <immutable version/hash>
review_consensus: <two recorded decisions or human resolution>
implementation_commit: <hash, when implemented>
```

## Session record

```text
id: <session identifier>
cluster: C001
status: active | ended_closed | ended_blocked
start_commit: <full commit hash>
views_at_start: <identifiers or digests of the Explorer views regenerated at start_commit>
changes_since: <closing commit of the previous session>
end_commit: <full commit hash>
records_updated: [<record ids>, ...]
open_items: [<open questions and escalations>, ...]
continuation_of: <earlier session id, with the human authorization, if continuing an interrupted session>
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
consequence: defect | unsupported_claim | improvement | uncertainty
affected_code: <paths/symbols>
requirement_or_invariant: <IDRA-x.y or invariant id>
premise: <for unsupported_claim: the premise or verification obligation that lacks or contradicts evidence>
rationale: <why this is a finding>
evidence: <supporting evidence>
remediation_slice: <C001-Snn, when corrected>
status: open | resolved_verified | escalated
```

## Evidence manifest

```text
doctrine_version: <pinned doctrine version>
record_versions: <invariant/cluster/slice versions>
source_commit: <full commit hash>
toolchain: <Rust/tool versions and relevant environment>
commands: <exact commands/configuration>
inputs: <fixtures, seeds, context and ordering>
outputs: <result files, digests, logs>
challenges: <L2/L3: premise challenged, method; for a deliberate violation, the violation, expected failure and reason, clean baseline>
assumptions_and_limits: <assumptions the claims depend on and limits of this evidence>
uncertainties: <unresolved or unverified claims>
status: verified | failed | blocked | unverified
```

A record with no real command execution or missing outputs MUST NOT assert `verified`.

---

# Appendix B — Interpretation Examples

These examples are non-negotiable: each shows the response the doctrine requires in a situation where a convenient shortcut might seem reasonable.

## Rule 1 — Invariant-Driven Correctness & Determinism

| Situation | Required IDRA response |
| --- | --- |
| Claude and Codex agree an invariant should change | Draft a change; human must explicitly approve it before it becomes active |
| An agent thinks an approved L2 invariant should be L1 | Propose the change; the level stays L2 until the human approves the lowering |
| An invariant's consequences cannot be assessed | Mark the level `Unresolved` and seek a decision; do not default to L1 |
| An invariant is reassigned to another domain | Record it as versioned metadata; the ID, statement and evidence are unchanged, and no reapproval is needed unless meaning changes |

## Rule 2 — Isolated Functional Core & Versioned Boundaries

| Situation | Required IDRA response |
| --- | --- |
| The core needs current time | Shell supplies explicit time/context; core cannot read the clock |
| A failure risks replay divergence | Fail-stop; do not retry inside the core |
| A command's meaning changes | Version it, define the activation boundary, and test replay across the boundary |
| A module mixes authoritative decisions with I/O | Split it into BLUE and RED modules; do not classify it as either, and do not invent a fourth color |
| Logic is moved out of the core to avoid BLUE restrictions | Prohibited; moving the BLUE boundary requires a human decision |

## Rule 3 — Mechanical Enforcement & Empirical Proof

| Situation | Required IDRA response |
| --- | --- |
| A test fails but a smaller expected value would make it pass | Do not change the expected value to obtain a pass; fix the code or escalate a genuine conflict between the record and an invariant |
| A slice works but one cluster exit criterion remains unrun | Cluster remains open or blocked |
| An agent cannot run verification in its environment | Mark unverified/blocked and escalate; do not report success |
| A reviewer proposes relabeling a BLOCK as WARN so the cluster can close | Not permitted; correct and verify, or escalate a genuine dispute |
| The Explorer index may be out of date | Regenerate the affected portion or show it as stale; never present it as current |
| A veto must affect a decision only when its claim and commit match the decision being evaluated | Make the matching rule part of one decision function; test matching and mismatching identities; establish by architectural check that every authoritative decision path uses it. Add checks only where they establish what these cannot |
| A guarantee rests on a constructor private to the core crate | Cite the compiler's visibility enforcement and an architectural check that no other path constructs the type; no mutation harness is required for that premise |
| An enforcement argument covers the transition function, but another code path can modify the protected state directly | The argument does not cover every authoritative path: an unsupported claim (BLOCK), or a defect if the path is reachable in the reviewed revision |
| A reviewer constructs a mutant that survives, but cannot name a premise or agreed obligation it shows unsupported | Record a WARN with consequence `improvement`; it does not block closure |
| A reviewer finds a reachable violation that step C did not anticipate | BLOCK as a defect; correct and verify it in a remediation slice |
| A remediation slice changes only tests and records | Re-review the affected evidence and the claims it supports, not the complete cluster |

## Rule 4 — Human-Gated Multi-Agent Governance & Compliance

| Situation | Required IDRA response |
| --- | --- |
| Two agents reviewed different record versions | No consensus; obtain review of the same version |
| The human has not answered an approval request | No approval exists; do not infer consent |
| A refactor alters observed behavior | Stop affected work and obtain human decision before proceeding |
| A new cluster record version adds a remediation slice | Review the difference from the agreed version; unchanged parts keep their agreement |
| Claude and Codex agree a finding is an improvement, not an unsupported claim | Record it as WARN under the approved rules; no human decision is needed unless they disagree on correctness or the matter is reserved to the human |