# IDRA Design Decision Register

**Project:** Invariant-Driven Reasoning Architecture (IDRA)  
**Record status:** Design decisions approved in discussion; **not yet merged into or approved as the IDRA doctrine**  
**Captured:** 2026-11-09  
**Scope:** Missing Requirement #1 — Invariant Family Taxonomy and Organization  
**Next topic:** Missing Requirement #2 — Invariant Scope and Distinctions (not yet decided)

## Authority and interpretation

This register captures decisions explicitly agreed during the IDRA design review. It is a working decision record, **not an additional authoritative doctrine layer**. The intended destination is Layer 2 of the single IDRA doctrine, beneath its four foundational rules, with suitable supporting artifact schemas and explorer views. The current IDRA doctrine file has not been modified as part of this record. Where exact implementation details remain to be formulated, they are marked **To specify**, not silently treated as approved.

IDRA is the renamed/replacement doctrine, not a new layer on top of IDSE. The earlier IDSE text is historical comparison material, not a parallel source of normative authority. Since IDSE is not widely deployed, its identifier format need not be preserved for legacy compatibility.

## DDR-001 — Functional domains as the primary organizational dimension
**Status:** Agreed

- Organize invariants by **functional aspects/conceptual responsibilities of the software**, not by the location of modules or source files.
- Domains are **project-specific**, not a universal prescribed list. Examples like Payment Processing, Inventory Management, User Management, and Scheduling are illustrative only.
- Give each invariant **one primary domain** and permit **additional associated domains** where a guarantee spans responsibilities.
- The functional domain provides the primary navigation of the Invariant Explorer. The **family** is a distinct correctness classification, not a mandatory nested folder within the domain.
- Domain assignments can change as understanding of the project evolves. Domain changes are **versioned organizational metadata** and their history is retained.
- Reassigning a domain **does not** change the invariant's ID, approved statement, previously recorded evidence, clusters, or slices.
- A domain-only reorganization does not itself require reapproval of invariant semantics; if the change **actually modifies meaning, scope, assumptions or enforcement obligations**, it is a substantive change governed by existing human approval rules.
- The same authoritative records may be explored by functional domain, family, component/enforcement location, and verification/evidence status without duplicating definitions.

**Rationale:** Correctness governs architecture in IDD. Code locations can change without altering a correctness requirement; flat family-prefix lists are difficult to navigate at scale.

## DDR-002 — Eight invariant families replace the fourteen-family proposal
**Status:** Explicitly approved

The accepted family names and their classification and verification intent are:

| Abbreviation (for ID only) | Family (spelled out in the record) | Meaning | Mandatory minimum verification concern |
|---|---|---|---|
| SEC | Security | Protects against unauthorized actions, adversarial behavior, and violations of trust boundaries. | Demonstrate that unauthorized or adversarial actions cannot violate protected guarantees. |
| SAF | Safety | Prevents prohibited states and transitions, including violations of validity, structural constraints, conservation, and finality. | Demonstrate that prohibited states and transitions cannot occur. |
| LIV | Liveness | Ensures required progress and eventual completion under explicitly stated assumptions. | Demonstrate that required progress occurs under those assumptions. |
| DET | Determinism | Ensures reproducible execution, canonical ordering, and identical authoritative results from identical inputs. | Demonstrate that identical canonical inputs produce identical authoritative results. |
| RES | Resilience | Ensures committed information survives failures and valid operation can be restored. | Demonstrate required state/behavior survives failures or recovers correctly. |
| EVO | Evolution | Governs controlled changes to interfaces, protocols, rules, and system semantics. | Demonstrate that changes preserve required compatibility and obey version/activation boundaries. |
| OBS | Observability | Ensures trustworthy information is available to inspect, diagnose, and verify system behavior. | Demonstrate required records and diagnostics accurately represent behavior. |
| PRC | Process Assurance | Ensures engineering procedures, approval gates, verification obligations, and completion criteria are enforced. | Demonstrate required workflow gates and approvals cannot be bypassed. |

**Approved consolidation map:**

| New IDRA family | Former subject categories absorbed |
|---|---|
| Security | Security |
| Safety | Safety, Structural, Validity, Conservation, Finality |
| Liveness | Liveness |
| Determinism | Determinism, Ordering |
| Resilience | Durability, Recovery |
| Evolution | Evolution |
| Observability | Observability |
| Process Assurance | Process Assurance |

Reducing categories **must not eliminate the underlying distinct correctness properties or their verification obligations**. Family classification is meaningful for mechanical verification, not merely labeling. Families specify the relevant **verification concern**, not a universal compulsory test tool or a test-count target. Different invariants in the same family may require different tests, depending on their statements, scope, risks, and assumptions.

**Illustrative verification methods, not mandatory tool mandates:** Security: authorization/adversarial checks; Safety: type and state-machine/property checks; Liveness: progress/temporal/fault scenarios; Determinism: replay/canonical/differential checks; Resilience: crash/restart/fault injection; Evolution: activation/migration/compatibility checks; Observability: audit/trace/integrity checks; Process Assurance: CI approval, artifact, and workflow-gate validation.

## DDR-003 — Invariant identifiers and human-readable records
**Status:** Agreed

- Invariant title/ID uses a **three-letter family prefix plus a numeric identifier**, e.g. `SAF-001`.
- The separate **Family** field always spells out the full name, e.g. `Safety`; the abbreviation is used **only in the invariant ID/title**.
- Do **not** embed domains into permanent invariant IDs.
- **Do not require a separate CamelCase/PascalCase name** such as `SinglePaymentCapture`.
- Every invariant carries a **mandatory one-line, precise, falsifiable correctness statement**. It must say what remains true or what is forbidden; vague aspirations are insufficient.
- The concise statement is visible directly in the explorer, while full records preserve assumptions, counterexamples, enforcement and evidence.
- Tool-specific identifiers, if required by a modeling system, may be derived mechanically from the invariant ID (e.g. `SAF_001`) rather than requiring manually maintained descriptive aliases.

**Illustrative explorer record:**

- **Title:** `SAF-001`
- **Domain:** Inventory Management
- **Family:** Safety
- **Statement:** Available inventory must never become negative.
- **Assurance Level:** L2 — Elevated (illustration; not a mandated classification for this example)
- **Enforcement and evidence:** Linked from the record

**To specify:** exact identifier width/sequence/uniqueness rules and machine-readable schema validation. Do not infer that those format details were separately approved.

## DDR-004 — Three cumulative invariant assurance levels
**Status:** Explicitly approved

Assurance levels replace the earlier `Critical`/`Standard` labels **at the invariant level**. They are separate from project-level assurance profiles and independent of the invariant's *verification status*.

| Level | Name | Intended use | Minimum verification obligations |
|---|---|---|---|
| L1 | Baseline | Assessed ordinary, limited-consequence correctness guarantees | Human-approved invariant; effective mechanical enforcement and reproducible, revision-bound verification; applicable assumptions and limits stated. |
| L2 | Elevated | Significant consequences or substantial verification difficulty | All L1 obligations plus negative/boundary checks, failure-path verification, and discriminating mutation verification or appropriate equivalent fault injection demonstrating that the intended check catches an actual violation. |
| L3 | High Assurance | Severe/irreversible/system-wide consequences or exceptional verification difficulty | All L2 obligations plus independent verification and stronger, risk-appropriate substantiation, including explicit analysis of assumptions and limitations. Formal modeling is used when necessary to substantiate the particular claim; not automatically for every L3 invariant. |

All levels still require correctness and evidence; levels are **minimum verification rigor**, not exemptions from invariants. Higher levels inherit lower-level obligations. Methods must substantiate the claim, not satisfy a numeric test count. A higher assigned level does not imply tests have passed.

## DDR-005 — Assurance classification, default, approvals and uncertainty
**Status:** Explicitly approved

1. **L1 Baseline is the default after assessment**, provided no L2 or L3 escalation criterion has been identified. Unassessed or insufficiently understood risk is *not* permission to declare L1.
2. Determine level using **consequences of violation** and **verification difficulty**. Evaluate impacts on security, safety, operational/financial integrity, data integrity and recovery, plus concurrency, distributed/adversarial interaction and other verification challenges.
3. **Highest-trigger rule:** Any applicable L3 criterion means L3; otherwise any L2 criterion means L2; otherwise L1. Do not average a severe exposure down because other dimensions are mild.
4. L1 = localized/limited and straightforward; L2 = significant consequences or substantial verification complexity; L3 = severe, irreversible or system-wide consequences, or exceptional verification complexity.
5. Each classification carries a **mandatory one-line assurance rationale** explaining the impact/difficulty judgment.
6. Agents **propose** the assurance level alongside each drafted invariant. The designated human **approves the level with the invariant in the existing line-by-line approval process**, without a separate L1 downgrade workflow.
7. Agents may strengthen verification but may **not lower an approved assurance level without explicit human authorization**.
8. When impact or verification difficulty cannot be assessed adequately, mark classification **Unresolved** and seek a decision; do not silently default to L1.
9. A project assurance profile (`prototype`, `app`, `strict`, `protocol`) is a separate dimension and must not silently negate invariant-level obligations.

**To specify:** operational examples and precise, automatable indicators for significant versus severe risk, as needed during normative drafting. No numeric thresholds were approved.

## DDR-006 — Explorer and traceability presentation
**Status:** Agreed

The primary navigational path is:

`Project → Functional Domain → Invariant → Cluster(s) / Slice(s) → Implementation / Verification Evidence`

This is a navigation/traceability view, **not** a rule that every invariant has only one cluster, slice, or physical implementation location. The explorer should support alternative perspectives such as family, implementation component, and verification evidence using the **same invariant records**. It should show the invariant title (`SAF-001`), full family name (`Safety`), concise invariant statement, and assurance level.

## Out of scope / non-decisions

- **Monitoring priority, scheduling, alerting and monitoring dashboards** are explicitly out of scope for the present design. Do not add them to the doctrine based on the exploratory conversation about possible future capability.
- The old IDSE taxonomy is **not** a second source of authority after IDRA adoption. Historical migration compatibility is not a current requirement because IDSE was not widely deployed.
- The names of particular functional domains are **not standardized globally**.
- Missing Requirement #2 (scope distinctions: authorization/validity/ordering/finality, and local/transactional/distributed enforcement) was introduced but **not decided**. Do not include it among the decisions approved for Requirement #1.

## Decision completion summary

**Missing Requirement #1:** Discussion concluded and approved at design level: project-specific functional domains; eight families; mandatory family-related verification concerns; abbreviated invariant ID only in title; full family name in the record; no mandatory CamelCase name; one-line invariant correctness statement; three cumulative assurance levels and their default/classification/approval rules; primary functional explorer navigation.

**Implementation status:** Not yet incorporated into the IDRA doctrine file. The single IDRA doctrine should be updated later through an explicitly approved, versioned amendment. This decision record supports that drafting; it is not an additional normative doctrine layer.

---

# Subsequent requirement reviews — 2026-11-09

This section extends the existing register. It does not supersede DDR-001 through DDR-006 or amend the IDRA doctrine. The distinction between an explicit approval and a discussion remains binding for interpreting this working record.

## DDR-007 — Requirement #2: Scope and correctness distinctions
**Status:** Discussed; not explicitly approved

The IDSE distinctions **authorization** (who may act), **validity** (whether an operation is acceptable), **ordering** (what must precede what), and **finality** (what cannot be superseded) were explained. They establish independent proof obligations: verification of one does not by itself establish another. The original local/transactional/distributed scope taxonomy was also explained, including the example of inventory checks that pass in isolation but fail under concurrent purchases.

After the user questioned the value of additional labels, the recommendation was **not to require a new mandatory scope classification field**. Instead, an invariant's verification contract would identify the conditions under which its guarantee must hold, whether concurrency/distribution affects it, and the evidence required at that scope. The four correctness distinctions would guide drafting and review without becoming extra families or mandatory categorization fields.

**Decision status:** The user said “ok what's next” after this explanation, but did not explicitly approve the recommendation. Treat it as a proposed direction, not a locked normative requirement, unless later confirmed.

## DDR-008 — Requirement #3: Rust enforcement constraints
**Status:** Explicitly approved
**Intended destination:** Layer 2, Rule 2 — Isolated Functional Core & Versioned Boundaries

The user asked to preserve the older IDD/IDSE approach and explicitly agreed. For ordinary software implementation projects:

1. Project software is implemented in **Rust** and organized as a **Cargo workspace from the first cluster**.
2. The authoritative functional core is a **separate Rust crate**.
3. The core uses `#![no_std]`, may use `alloc`, and has `#![forbid(unsafe_code)]`.
4. The core has **no external crate dependencies** and **no build script**. The historical rule allowed test-only dev-dependencies, which must not become runtime core dependencies.
5. The imperative shell/root package may depend on the core by path; dependencies **never point from the core to the shell**.
6. The core does not perform environmental I/O or use networking, filesystem, async-runtime, clock, randomness, OS/environment facilities to determine authoritative results.
7. CI/static checks cover prohibited constructs the compiler and crate layout do not fully exclude, including floating point, global state, panics and sources of nondeterminism as applicable to authoritative core paths.
8. These structural core restrictions apply to normal software profiles, including prototypes; profiles may vary additional checking rigor but do not waive the architectural separation.
9. The earlier **protocol-track distinction** remains: its principal deliverable may be a model rather than production code; purity and boundary constraints apply to modeled actions. The full protocol track remains to be reviewed separately in Requirement #9, so do not silently invent further exceptions here.
10. The shell may use necessary environmental libraries, services, concurrency and I/O, but may not redefine core semantics or bypass its controlled transitions.

**Rationale:** Make Functional Core / Imperative Shell mechanically enforceable rather than dependent on agent adherence to prose.

## DDR-009 — Requirement #4: TCB classification and governed boundary
**Status:** Explicitly approved after follow-up discussion
**Intended destination:** Layer 2, Rule 2 — Isolated Functional Core & Versioned Boundaries

### The three classifications

- **BLUE — Deterministic authoritative functional core.** Owns canonical state meaning, authoritative validation, transition decisions and declarative effects. Must satisfy the Rust/core restrictions in DDR-008.
- **GREEN — Deterministic, non-authoritative supporting code.** May provide support computations, test and verification tooling, fixtures and toolchain orchestration, but cannot define, modify or reinterpret authoritative semantics or outputs. The classification describes responsibility; it is not an exception zone.
- **RED — Imperative shell / environmental interaction.** Owns I/O, networking, storage, clocks, processes, external APIs, effect execution, retries and environmental orchestration. Cannot independently determine authoritative meaning, mutate core state outside the transition contract, or reinterpret core results.

**YELLOW is explicitly excluded.** The user explained that it was removed historically because it allowed code that did not conform to GREEN or RED to occupy a loophole. Do not restore it as an intermediate classification or permit uncategorized code to hide in an ambiguous fourth zone.

### Module and domain rules

1. **Every module has exactly one TCB color** defined by its responsibility; colors are not optional decorations or freely chosen labels.
2. A module must conform to every restriction of its assigned color. If a module mixes BLUE decisions with RED environmental effects, split responsibilities rather than treating the mixed module as compliant.
3. **BLUE** authoritative modules reside within the isolated core; **RED** environmental modules are outside it. **GREEN** supporting modules may exist where architecturally appropriate but may not change authoritative outputs or bridge past the boundary.
4. A **functional domain can span modules of all three colors**. Domain organization is conceptual, whereas TCB color is a code responsibility/boundary classification.
5. Ambiguous module classifications must be resolved before affected implementation proceeds; ambiguity does not authorize a fourth classification.

### Boundary and dependency semantics

1. There is **one governed Functional Core / Imperative Shell boundary**; the boundary itself has **no TCB color** and is not a fourth category.
2. **RED → BLUE** is allowed: shell code may invoke the core's defined public transition interfaces with explicit canonical inputs.
3. **BLUE → RED direct invocation/dependency is prohibited**: the core cannot call shell functions or use external/environmental effects.
4. **BLUE returns declarative effects as data; RED executes them**. Returning effects does not constitute a BLUE-to-RED code dependency.
5. **BLUE → GREEN → RED is not a permissible bypass**. GREEN is not a required or permissive intermediary in the runtime flow.
6. The shell cannot directly mutate authoritative core state, construct semantic types unsafely, bypass canonical validation, invent variants or reinterpret core outputs.
7. The designated **human controls changes that move the authoritative BLUE/core boundary**. Agents cannot reclassify authoritative logic into GREEN or RED merely to avoid BLUE restrictions.
8. Crate separation, dependency graph constraints, state encapsulation, explicit transition interfaces, checks/reviews, and human boundary authorization provide the enforcement and evidence mechanisms.

**Rationale:** A clear single-owner authority boundary is essential to invariant-driven correctness. The color scheme communicates constraints at module granularity and cannot dilute the strict core/shell contract.

## Review checkpoint

- **Requirement #1:** Approved and documented (DDR-001 through DDR-006).
- **Requirement #2:** Discussed, not explicitly locked (DDR-007).
- **Requirement #3:** Approved and documented (DDR-008).
- **Requirement #4:** Approved and documented (DDR-009).
- **Next topic:** Requirement #5 — Effect Delivery and Durability.
- **IDRA doctrine status:** No normative doctrine amendment made through this design register. The doctrine remains a separate, single authoritative specification once approved and amended.

---

# Subsequent approved decisions — Requirements #8 and #11 (2026-11-09)

These entries amend the **design-decision register only**. They do not modify or approve the IDRA doctrine, and they do not authorize implementation or testing before incorporation into the doctrine.

## DDR-010 — Scope of the remaining restoration review
**Status:** Explicitly approved

Following the review of candidate gaps, the human selected **only Requirement #8 (Independent Architecture and Security Reviews) and Requirement #11 (Project Grounding Artifacts)** for further design and restoration. Requirements #5, #6, #7, #9 and #10 are excluded from this restoration exercise. In particular, effect-delivery details are to be governed by existing core/shell constraints, project invariants and transition contracts rather than introduced as another universal doctrine requirement. This does not erase any existing normative requirement; it limits proposed additions and restorations.

## DDR-011 — Requirement #8: Independent review at cluster closure
**Status:** Explicitly approved across sequential decisions
**Intended destination:** Layer 2, Rule 3 — Mechanical Enforcement & Empirical Proof; cross-reference Rule 4 agent governance

### Timing and independent reviewers

1. **Independent implementation review occurs at cluster closure, not at each slice boundary.** Each slice must still pass its existing mandatory mechanical verification and acceptance obligations before promotion. The review is not permission to carry known violations into cluster closure.
2. **Claude and Codex are the two mandatory independent reviewers.** Both examine the **same exact implementation revision** and the approved cluster specification/invariants and relevant verification evidence.
3. Each reviewer produces an **independent initial assessment** before seeing the other's conclusions. Findings are then reconciled; unresolved correctness disagreements are documented and escalated to the designated human. Agreement cannot override an evidenced violation.

### Review coverage

4. **Each of Claude and Codex examines both architecture and security** across the **complete cluster implementation**, including interactions among slices. These are two review concerns, not necessarily separate review teams or workflow stages.
5. Architecture review covers the applicable BLUE/GREEN/RED constraints, governed core/shell boundary, authoritative transition paths, determinism, replay obligations, invariant preservation, and correctness-threatening failures.
6. Security review covers applicable authorization and trust boundaries, untrusted-input handling, secrets/information exposure, adversarial and failure behavior, and vulnerabilities arising from cross-slice interactions.
7. Depth of review and required corroboration are informed by the applicable approved invariants and their **L1/L2/L3 assurance levels**; this does not waive required review coverage.

### Findings, remediation and closure

8. Findings have exactly two review dispositions: **BLOCK** and **WARN**. These are **review-finding dispositions**, distinct from invariant assurance levels.
9. Every finding identifies affected code, relevant requirement or invariant, rationale, and supporting evidence.
10. **BLOCK:** an evidenced violation of a governing invariant, architectural or security requirement, or mandatory verification obligation. It blocks cluster closure until corrected and verified. Reviewers cannot relabel a known BLOCK as WARN to manufacture closure.
11. **WARN:** a nonblocking concern or residual uncertainty that does not establish a blocking violation. It is recorded with rationale and remaining uncertainty and does not automatically block closure. A disputed finding affecting correctness must be resolved with evidence or escalated to the human.
12. **Consolidated remediation is permitted:** multiple BLOCK findings MAY be corrected in **one coherent remediation slice within the same open cluster**, provided every finding is individually traceable to a correction and independently verified. Findings that do not form a coherent slice must be split into separate remediation slices. There is no universal one-slice-per-finding rule, nor a requirement to force all findings into one slice.
13. Corrections may not weaken approved invariants, tests, or exit criteria to pass. Findings requiring a human-controlled invariant, boundary, or other governed decision are escalated rather than silently handled as ordinary remediation.
14. Cluster closure requires all BLOCK findings to be resolved and verified and **all cluster exit criteria** to pass under the existing evidence rules. After corrections, affected verification and review obligations must be satisfied for the closing revision.

### Separate release/PR review

15. **No separately mandated PR/release security review** is required as a general duplicate of the completed cluster review. The cluster-review evidence must refer to the exact reviewed revision. Changes since review and multi-cluster integration still have to satisfy all applicable invariant and verification obligations; a prior review does not certify unreviewed changes.

**Rationale:** Preserve independent scrutiny of implemented code and cross-slice interactions, while avoiding redundant per-slice and PR/release review loops. Slice correctness remains mechanically gated; cluster closure is the integrated independent-review boundary.

## DDR-012 — Requirement #11: Single-record, incrementally generated grounding views
**Status:** Explicitly approved in principle; testing explicitly deferred
**Intended destination:** Layer 2, Rule 3 — Mechanical Enforcement & Empirical Proof, and supporting artifact schema / explorer specification

### Mandatory capabilities, not three independently maintained documents

1. Every applicable IDRA project provides three grounding **capabilities**: an **Implementation Map**, **Traceability**, and an **Invariant Explorer**. Their storage format and concrete implementation may vary by project.
2. Implement them as **one lightweight local Explorer with multiple views**, not three separately maintained applications or independent documentation sources.
3. The **single authoritative input** consists of existing project records: approved invariants and domains; cluster/slice specifications; transition/verification contracts; module TCB classifications and responsibilities; source/commit information; verification checks; and evidence records.
4. **Implementation Map view:** identifies modules, responsibilities, dependencies, BLUE/GREEN/RED classification and linked invariants. **Traceability view:** connects invariant → cluster/slice → implementation → verification check → evidence. **Explorer/navigation view:** organizes those same facts by functional domain (primary), with family, component and verification perspectives as alternative navigations.
5. Derived views and indexes **must not become independent sources of correctness authority**, must not require manual synchronization of duplicated facts, and must not silently assert unsupported relationships. Semantic links that cannot be reliably inferred from specifications/code must be recorded explicitly once in an authoritative record.

### Low-overhead operation and correctness of generated views

6. Prefer **incremental updates from changes to authoritative records**, using source revisions, file hashes and dependency/relationship information where reliable. Only affected records and index entries should be refreshed, instead of rescanning the entire repository indiscriminately.
7. The Explorer **loads a generated local index on demand**. Opening the Explorer should not by itself trigger a full repository reanalysis when a current index already exists.
8. **Incrementality is an optimization, not a correctness exemption.** If affected items cannot be determined reliably, regenerate the necessary portion or mark it stale/incomplete; do not present an unverified index as current.
9. Derived map, traceability and Explorer views are **regenerated from authoritative data**, not manually patched. Missing links, inconsistencies and stale data are surfaced explicitly.
10. **No separate mandatory SEAMS or HEAD_DELTAS files** are introduced. Their useful information can be available from existing specifications and version history as appropriate.

### Approved sequencing and testing constraint

11. **Document these decisions now; do not build or test a proof of concept yet.** Incorporate all agreed design decisions into the single authoritative IDRA doctrine first. **Only after incorporation** will the generated-view approach be implemented/tested and its results evaluated. This explicitly supersedes the briefly suggested immediate prototype/test plan.

**Rationale:** Maintain navigability, code-to-invariant traceability and durable evidence with minimal agent/document-maintenance overhead; one authoritative record avoids drift, while deterministic/incremental generated views reduce work without weakening correctness.

## Latest review checkpoint

- Requirement #1: approved, documented in DDR-001–DDR-006.
- Requirement #2: discussed, not explicitly approved, DDR-007.
- Requirement #3: approved, DDR-008.
- Requirement #4: approved, DDR-009.
- Requirement #8: approved, DDR-011.
- Requirement #11: approved as a design approach, DDR-012; **no implementation or testing until after doctrine incorporation**.
- Requirements #5, #6, #7, #9 and #10: excluded from further restoration review by DDR-010.
- The **IDRA doctrine file remains unchanged**. The design-decision register is a drafting reference and does not constitute a third doctrine layer.
