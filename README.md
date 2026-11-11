# IDRA Doctrine

**Invariant-Driven Reasoning Architecture (IDRA)** is an engineering doctrine for building Rust software with human developers and autonomous agents working together. It governs the software itself, the process used to build it, and the limits on what agents may decide.

> Software is built from human-approved invariants: statements of what must always be true. Nothing changes state unless those invariants still hold, and nothing advances without evidence that they do.

The doctrine itself is in [`IDRA Doctrine.md`](IDRA%20Doctrine.md). This README is a short introduction to it. Where the two differ, the doctrine controls.

## Why IDRA

When agents write and review much of the code, a passing test, a plausible implementation or two agents agreeing does not prove the software is correct. IDRA makes correctness something people define, machines check and evidence demonstrates, so that no claim rests on anyone's word alone.

## The four rules

| Rule | In short |
|---|---|
| **1. Invariant-Driven Correctness & Determinism** | Correctness is defined before features are written, as one-line, falsifiable invariants that a designated human approves one by one. Each has a family, a functional domain and an assurance level from L1 (baseline) to L3 (high assurance). The same ordered inputs always produce byte-identical state. |
| **2. Isolated Functional Core & Versioned Boundaries** | Every authoritative decision is made in a deterministic `no_std` Rust core that performs no I/O. A separate shell handles everything that touches the outside world. Each module is BLUE (core), GREEN (deterministic support) or RED (shell). Interfaces are closed and versioned, and a failure that threatens correctness halts processing instead of being hidden or retried. |
| **3. Mechanical Enforcement & Empirical Proof** | Every invariant has a check that actually runs. For L2 and L3 invariants, the check must be shown to fail on a deliberate violation. Completion claims need reproducible evidence tied to an exact commit, and a cluster of work closes only after independent architecture and security reviews by Claude and Codex leave no blocking finding. |
| **4. Human-Gated Multi-Agent Governance & Compliance** | Claude and Codex must agree on how each piece of work preserves its invariants before code is written. Agents may draft, propose and implement. Only the designated human approves the concept, the plan and the invariants, moves the core boundary, lowers assurance, resolves escalations or changes the doctrine. Silence and agent consensus never count as approval. |

## How work flows

Work moves from the general to the specific, and each level traces to the one above it:

1. **Project concept:** purpose, scope, actors, state transitions and trust assumptions.
2. **Cluster plan:** the clusters, their order and dependencies, and the project-wide (overall) invariants.
3. **Cluster:** a bounded unit of work with detailed invariants that refine the overall ones. Each cluster is carried out in one agent session, from definition through closure.
4. **Slices:** the steps that implement a cluster's detailed invariants, each mechanically verified and committed with its evidence.

A gap found at any level sends the work back to the level above, which is revised and approved again before the work continues.

## Adopting IDRA

A project names its human authority, pins the doctrine version and sha256 it follows, and declares an assurance profile: `prototype`, `app`, `strict` or `protocol`, with `strict` as the default. It keeps the invariant register, work records, module classifications and evidence that the doctrine calls for. The doctrine's record formats (invariant, cluster, slice, session, evidence manifest and others) are in Appendix A, and Appendix B gives worked examples of each rule.

## Current version

| | |
|---|---|
| File | `IDRA Doctrine.md` |
| Version | 1.1 |
| Status | Approved by the designated human authority on 2026-11-10 |
| sha256 | `b367ad21498038ac616a75fb8324dcd765265de803da020e4cf8ba18488789c4` |

`IDRA Doctrine.md` is the single authority for IDRA obligations (IDRA-4.7). Nothing else in this repository adds to, removes from or reinterprets it. The approval and change record is `~/.claude/methodology/idra-doctrine.toml`, and projects pin the version and sha256 in their `.idra-config.json`.

## Changing the doctrine

Every change is a new version: edit `IDRA Doctrine.md`, get human approval of the exact text, record the approval in `idra-doctrine.toml`, commit it here, and move the superseded text into `archive/`.
