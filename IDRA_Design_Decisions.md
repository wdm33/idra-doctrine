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
