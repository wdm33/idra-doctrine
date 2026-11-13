# IDRA 1.2 draft: Claude ↔ Codex review record

Codex reviewed read-only through the codex-companion runner. Each round's prompt carried no Claude assessment. Claude's round-1 assessment was written before Codex's reply was read.

| Round | Draft sha256 | Codex received | Outcome |
|---|---|---|---|
| 1 | `407f978a44c8dc6014179b7ec229c5245b46c7a54f87417d5e3d796257a07c18` | match | changes requested (4 must-fix, 4 should-fix, 1 note) |
| 2 | `3ac0caaa60b4764101722acce8cac3340030f6e751781b05079116e2704a4b2d` | match | **agree**; items 1–9 accepted, new note 10 |

## Round 1: Claude's independent assessment


1. must-fix: IDRA-3.6 item 3. It lets a cluster close with a claim recorded as unverified-and-escalated, but step G item 3 keeps any unverified check open. Fix: an unverified claim blocks closure until the human resolves the escalation.
2. should-fix: IDRA-3.7 item 7. An argument that leaves out an authoritative path is unsupported only by implication. State it: an argument that does not account for every authoritative path (IDRA-3.1 item 1) is an unsupported claim. This closes the "vague argument with no premises" loophole.
3. should-fix: IDRA-3.7 item 11. The impact is declared by agents. Add: a reviewer who finds the declared impact understated reviews at the broader scope, and records the understatement as a finding.
4. should-fix: IDRA-4.1. "Agreement on these is sufficient to authorize implementation" reads as if it bypasses Gate C (human approval of the detailed invariants). Fix: "together with the other gates".
5. should-fix: IDRA-3.2. "challenged independently" for L2 conflicts with IDRA-1.9, which adds "independent challenge" only at L3. Fix: L2 and L3 arguments are challenged with appropriate methods, and examined independently at closure (IDRA-3.7). L3 additionally requires independent challenge during verification (IDRA-1.9).
6. note: Rule 3's intent sentence ("Tests ... must demonstrate that they detect violations") stays verbatim. Read as applying wherever a test is the evidence, it is consistent with 3.2 item 1, where structural evidence is not a test. This should be reported to the human, not silently resolved.

## Round 1: Codex reply (verbatim)

received: 407f978a44c8dc6014179b7ec229c5245b46c7a54f87417d5e3d796257a07c18

1. **must-fix — IDRA-3.6 enforcement; operational protocol G.3.** “All check states” and “any check” retain 1.1’s unbounded closure gate. An optional exploratory test or mutant recorded as an improvement can remain failed or unrun and still prevent closure, contradicting DDR-020 and IDRA-3.7 item 8.

   Proposed replacement: “Closure requires passing exit criteria, satisfied agreed verification obligations, the enforcement-argument status required by IDRA-3.6 item 3, and no open BLOCK. Optional exploratory checks do not independently gate closure; any defect or unsupported claim they reveal does.”

2. **must-fix — IDRA-3.6 item 3; IDRA-3.7 item 7; protocol G.** Item 3 permits an unverified claim with a recorded limitation and escalation. But item 7 makes insufficient evidence for a mandatory premise a BLOCK, and IDRA-4.4 prohibits affected closure without a human decision. The draft does not distinguish *recording an escalation* from *obtaining its resolution*. Agents could interpret item 3 as permission to close after merely filing the escalation.

   Add: “Recording a limitation or requesting escalation does not resolve a BLOCK or authorize closure. Where a limitation leaves a mandatory premise unsupported, closure remains blocked until evidence substantiates it or an explicit human resolution authorizes the applicable change under IDRA-3.4 and Change control. The unsupported claim remains unverified.”

3. **must-fix — IDRA-3.1 item 4.** Additional verification becomes mandatory only when it demonstrates a defect, *invalidates* a necessary premise, or exposes an unmet agreed obligation. This excludes discovering that a necessary premise lacks evidence without proving it false. IDRA-3.7 item 7 correctly treats that as an unsupported claim, while IDRA-3.6 also permits additions driven by new information. These rules give different boundaries.

   Replace the second sentence with: “Additional verification becomes mandatory only through a new cluster record version when a defect, an unsupported claim as defined in IDRA-3.7 item 7, or new information shows that the agreed obligations are insufficient.”

4. **must-fix — IDRA-3.1; IDRA-3.2 items 2 and 4; Appendix A invariant record.** The dedicated `premises` field is intentionally optional, but nothing explicitly requires the compact argument to state every critical dependency when that field is absent. Combined with “no stated premise” and reviewer agreement on which premises are critical, this permits an omitted premise to shield a needed challenge from review.

   Add: “Every critical premise MUST be stated in the argument or its referenced records, whether or not a separate `premises` field is required. Omission or absence from the step-C agreement does not exempt a necessary premise from substantiation. A finding may identify an omitted necessary premise as an unsupported claim.”

5. **should-fix — IDRA-1.3, prevention hierarchy.** DDR-014 requires an explanation whenever a weaker form is chosen. The draft requires that explanation only when a guarantee “cannot be enforced structurally.” Choosing a weaker structural form—for example, a transition-boundary check instead of private construction—escapes the explicit rationale requirement.

   Replace that paragraph with: “Whenever a weaker form in this ordering is used, the enforcement argument MUST explain why each stronger applicable form was not used. Where enforcement is dynamic, it MUST identify what is checked dynamically.”

6. **should-fix — IDRA-1.8 versus IDRA-4.1 and step C.** The unchanged IDRA-1.8 says any change to an invariant’s “enforcement obligations” is substantive and governed by human approval under IDRA-1.2. The new text lets reviewers agree verification obligations and revise them by record version. It does not distinguish changes to human-approved invariant obligations from changes to the evidence plan. This leaves approval authority ambiguous.

   Add to IDRA-4.1: “Reviewer agreement governs the verification plan within existing approved requirements. It does not authorize changes to an invariant’s approved meaning, scope, assumptions, assurance level, or enforcement obligations; those remain subject to IDRA-1.2 and IDRA-1.8.”

7. **should-fix — IDRA-4.8 opening obligation and item 5.** “Exactly one session” now directly conflicts with the expressly authorized continuation session. DDR-020 requires continuations to preserve prior records, but an agent still has to invent an exception to the opening MUST.

   Proposed replacement: “Each cluster MUST be carried out in one session, except for continuation sessions explicitly authorized under item 4. Every original or continuation session MUST work on exactly one cluster.”

8. **should-fix — IDRA-3.1 and IDRA-3.2 section anchors.** Clause identifiers and contents-link fragments remain, but neither heading has an explicit anchor. Under ordinary GitHub-style Markdown, the generated fragment includes the heading’s title; changing the titles changes those generated anchors, and `#idra-3-1` and `#idra-3-2` do not identify them. DDR-016 expressly requires anchor preservation.

   Add explicit stable anchors immediately before the headings: `<a id="idra-3-1"></a>` and `<a id="idra-3-2"></a>`. Preserve any previously published title-derived fragments as aliases where applicable.

9. **note — DDR-019; CVM compatibility.** The impact-based re-review provisions implement DDR-019 well: both reviewers agree the declared impact, examine the complete change, broaden uncertain impact, and retain earlier conclusions only where unaffected. CVM-3.1’s mapping to IDRA-3.1 remains meaningful, and CVM-3.2 still explicitly requires fault-injection results; the new IDRA-3.2 does not waive that companion requirement. CVM’s header still names IDRA 1.1, so adopting 1.2 requires an explicit compatibility/pinning decision rather than assuming that reference has moved.

verdict: changes requested

## Round 2: prompt with answers (verbatim)

Your round-1 items, reviewed at 407f978a…, and the answers:

1. (must-fix) IDRA-3.6 enforcement and step G.3 gated closure on "all check states"/"any check". Answer: revised. 3.6 Enforcement and G.3 now name exit criteria, mandatory checks, agreed obligations, substantiated arguments and open BLOCKs, and say "Optional exploratory checks do not by themselves gate closure; any defect or unsupported claim they reveal does."
2. (must-fix) 3.6 item 3 let an escalation stand in for resolution. Answer: revised with your text. Recording a limitation or requesting escalation does not resolve a BLOCK or authorize closure; closure stays blocked until evidence or an explicit human resolution under IDRA-3.4 and Change control.
3. (must-fix) 3.1 item 4 boundary. Answer: revised with your text, referencing IDRA-3.7 item 7.
4. (must-fix) omitted premises. Answer: added to 3.1 item 2, with your text adapted. In addition, 3.7 item 7 now states that an argument not accounting for every authoritative path is an unsupported claim.
5. (should-fix) 1.3 rationale for any weaker form. Answer: revised with your text.
6. (should-fix) 1.8 versus 4.1 authority. Answer: your sentence added to 4.1. "Agreement on these is sufficient to authorize implementation" also became "Together with the other pre-implementation gates, agreement on these authorizes implementation".
7. (should-fix) 4.8 opening versus continuation. Answer: revised with your text.
8. (should-fix) explicit anchors for 3.1 and 3.2. Answer: rebuttal. The Markdown has no explicit anchors for any heading, in 1.1 or the draft; the Contents fragments (#idra-3-1 etc.) are the ids of the published reading page, which derives them from clause numbers, not titles. Retitling does not change them. Adding explicit anchors to two headings only would be inconsistent, and adding them to all headings is outside this change. Mark accepted or keep open with a reason.
9. (note) CVM header names IDRA 1.1. Answer: recorded in the change request's effects section as a human decision under CVM's change control; this request does not change CVM.

Also revised from Claude's own independent review: 3.2 no longer says L2 arguments are "challenged independently" (1.9 adds independent challenge only at L3); it says both closure reviewers examine every argument independently, and L3 arguments also get independent challenge during verification. 3.7 item 11 now says a reviewer who finds the declared impact understated reviews at the broader scope and records a finding.

## Round 2: Codex reply (verbatim)

received: 3ac0caaa60b4764101722acce8cac3340030f6e751781b05079116e2704a4b2d

1. **accepted** — IDRA-3.6 Enforcement and G.3 distinguish mandatory closure obligations from optional exploration. Discovered defects and unsupported claims still block.
2. **accepted** — IDRA-3.6 item 3 explicitly prevents recording a limitation or requesting escalation from authorizing closure.
3. **accepted** — IDRA-3.1 item 4 ties additional mandatory verification to a revised cluster record and the substantive grounds defined in IDRA-3.7 item 7.
4. **accepted** — IDRA-3.1 item 2 covers omitted premises; IDRA-3.7 item 7 explicitly covers incomplete authoritative-path coverage.
5. **accepted** — IDRA-1.3 requires a rationale for every stronger applicable form not used.
6. **accepted** — IDRA-4.1 preserves human authority over substantive invariant changes and makes implementation authorization conditional on the other gates.
7. **accepted** — IDRA-4.8 expressly accommodates human-authorized continuation sessions while retaining one cluster per session.
8. **accepted** — The clause-derived published anchors remain unchanged under the stated publishing convention. No selective explicit anchors are needed.
9. **accepted** — The change request records CVM’s IDRA 1.1 pin as requiring an explicit compatibility decision under CVM change control. CVM-3.2’s own fault-injection evidence requirement remains applicable.

The revised draft implements DDR-013–020 consistently. The finite verification contract does not exempt omitted premises, uncovered paths, real defects, or mandatory evidence. Impact-based re-review preserves independent review and requires broader coverage when impact is uncertain or understated. The L2/L3 independence correction and the understated-impact provision are satisfactory.

One new nonblocking item:

10. **severity:** note  
    **clause:** Rule 3 statement of intent  
    **problem:** “Tests for elevated and high-assurance invariants must demonstrate that they detect violations” retains the broader 1.1 formulation, while revised IDRA-3.2 permits structural substantiation without a mutation harness. The express rule that numbered requirements control prevents this from becoming an operational contradiction; the change request also explicitly preserves intent statements.  
    **proposed text:** In a future editorial amendment, replace that sentence with: “Enforcement arguments for elevated and high-assurance invariants must withstand risk-appropriate challenge, and every cluster receives independent review before it closes.”

No new must-fix or should-fix items. No files modified.

verdict: agree

## Outcome

Consensus on draft sha256 `3ac0caaa60b4764101722acce8cac3340030f6e751781b05079116e2704a4b2d`. Open for the human: approval of that exact version; Codex note 10 / Claude note 6 (Rule 3 intent sentence); the CVM compatibility decision.
