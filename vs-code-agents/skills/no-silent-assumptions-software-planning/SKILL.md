---
name: no-silent-assumptions.software-planning
description: Prevents silent scope assumptions during software planning. Forces explicit Proposed Contracts, backwards-compatibility decisions, test-scope confirmation, and clearly labeled assumptions using low-friction, default-friendly questioning. Designed for Planner-first use and plan-wide consumption by other agents.
license: MIT
metadata:
  author: aricd + chatgpt
  version: "1.1"
---

# No Silent Assumptions (Software Planning)

## Purpose

This skill prevents agents—especially the Planner—from silently inferring software scope, contracts, or definition-of-done from ambiguous user input.

Its goals are to:
- Eliminate "helpful guessing" that leads to incorrect plans
- Keep high-impact decisions explicit and reviewable
- Minimize user typing via defaults and batch questions
- Produce plans that function as **contracts**, not narratives

Wrong plans due to hidden assumptions are worse than asking questions.

---

## When to Load

Load this skill when:
- Producing or reviewing a **software development plan or spec**
- Translating ambiguous intent into tasks, files, or acceptance criteria
- Any time **interfaces/contracts**, **backwards compatibility**, or **test depth** could be interpreted multiple ways

This skill applies **strongly** to the Planner and **informationally** to all other agents that consume plans.

---

## Core Rule (Non-Negotiable)

**Never treat inferred scope as explicit requirements.**

If something affects:
- system boundaries
- interfaces or contracts
- backwards compatibility
- definition of done

…and the user did not state it explicitly, the agent must:
1. ask, or  
2. present a clearly labeled Proposed Contract with alternatives, or  
3. proceed only with explicitly labeled ASSUMPTION-* items (where allowed)

No silent assumptions.

---

## Decision Handling — Proposed Defaults vs. Applied Requirements

This section governs how the agent handles any situation where a requirement is **not explicitly provided**.

### The Four Rules

1. **Do not finalize a requirement using a default.** A proposed default is a suggestion, not a decision.
2. **Propose the default, but mark it clearly.** Use the exact label `Suggested default (not applied)` so the user can confirm quickly—never treat a proposed default as confirmed.
3. **Do not convert a proposed default into a requirement, task, risk, or backcompat decision until the user explicitly confirms.**
4. **Do not re-open a question the user has already answered**, unless there is a genuine conflict.

### Required Response Format for Unresolved Items

Use this exact pattern for every unresolved soft-default decision:

```md
OPENQ-###: <question>
Suggested default (not applied): <default>
Status: Awaiting user confirmation
```

### Required Summary Statement

When surfacing unresolved items to the user, the agent MUST say:

> "I identified N unresolved decisions and proposed defaults, but **I did not apply them**. Please confirm each item before I finalize requirements."

Do **not** say:
- "I've integrated defaults into the plan"
- "Confirm defaults or override"
- "Default is X" (without "not applied")
- "I've added these defaults and called them out"

### Prohibited Phrasing

| ❌ Prohibited | ✅ Required |
|---|---|
| "I added defaults to the plan" | "I proposed defaults but did not apply them" |
| "Confirm defaults or override" | "Please confirm each item; none are applied yet" |
| "Default is X" | "Suggested default (not applied): X" |
| "I've now integrated it… with defaults called out" | "I surfaced N unresolved decisions; none finalized" |
| "Defaults included in requirements section" | "Defaults are PROPOSED only; not in requirements" |

This applies even when using the Batch Question UX pattern. Offering options in that format does not grant permission to pre-apply defaults.

---

## Hard-Gate Ambiguity Domains

If **any** of the following are ambiguous, the Planner **must stop and ask** before proceeding:

### 1. CONTRACTS (Interfaces / Boundaries)
- APIs (REST/RPC/CLI)
- Event topics
- File formats
- Database tables/schemas
- Library boundaries
- External integrations

### 2. BACKWARDS COMPATIBILITY
- Shims or adapters
- Versioned endpoints
- Data migrations
- Dual-read / dual-write
- Deprecation strategy

Wrong assumptions here invalidate the entire plan.

---

## Proposed Contract (Canonical Concept)

A **Proposed Contract** is a **minimal, concrete, intentionally provisional** description of a software interface or boundary.

It is:
- specific enough to reason about scope, dependencies, testing, and risk
- explicitly **not a final design**
- easy to approve, reject, or modify

Its purpose is to surface ambiguity early and prevent silent invention by implementers.

### Required Contents

Every Proposed Contract must include:
1. **Boundary & direction** (producer → consumer)
2. **Interaction shape**  
   (REST / event / file / DB / in-process / CLI)
3. **High-level inputs & outputs**  
   (names + rough types only)
4. **Success & failure shape**
5. **Versioning posture** (if relevant)

### Explicit Exclusions

A Proposed Contract must **not** include:
- performance guarantees
- scaling strategies
- auth/z details (unless explicitly requested)
- full schemas or OpenAPI specs
- implementation details
- backwards-compatibility mechanisms

These are handled elsewhere.

### Labeling Requirement

All such contracts must be labeled explicitly:

```md
CONTRACT-001 (Proposed)
```

Variants are allowed but must remain explicit:

```md
CONTRACT-002 (Proposed – Option B)
```

---

## Soft-Default Domains (Ask, but Defaults Allowed)

### TEST SCOPE (Always Present)

Plans must always include a TEST-SCOPE section.

Rules:

* **Unit tests:** always required (non-negotiable)
* **Integration tests:** optional (ask with default)
* **E2E tests:** optional (ask with default)

If the user replies "defaults" (or equivalent), defaults are treated as **explicit confirmation**, not assumptions.

### PERFORMANCE POSTURE (Anti-Over-Engineering)

Plans must include an explicit performance posture statement:

> Default posture: optimize for clarity and maintainability; no performance optimization unless requirements demand it.

Then ask once:

* "Any specific performance or SLA constraints?"

Do not escalate complexity without confirmation.

### ALTERNATIVES (Simplicity Exploration)

Do not automatically expand alternatives.

Ask:

* "Do you want me to explore simpler alternatives?"

Only include ALT-* sections if the user opts in or if ambiguity is severe.

---

## Batch Question UX (Low Friction)

When clarification is required, ask **one compact batch** of questions with defaults.

### Question Format

```md
OPENQ-001 (Contracts)
Is this Proposed Contract acceptable?
a) Yes, proceed with this contract (default)
b) Modify this contract
c) Use an alternate contract shape
```

### Response Format

Reply using:

```
Open Questions: 1:a, 2:y, 3:c
```

To accept all defaults:

```
Open Questions: defaults
```

This is treated as explicit confirmation.

### Rules

* Prefer one batch over multiple turns
* 2–4 options per question max
* Each question must have a default unless truly unknown

---

## Required Plan Sections (Planner Mode)

Plans must include the following sections **in this order**, even if empty:

1. CONTRACT-* (Proposed Contracts first)
2. BACKCOMPAT-* (explicit compatibility decision)
3. TEST-SCOPE-*
4. TASK-*
5. FILE-*
6. DEP-*
7. ALT-* (only if requested or required)
8. RISK-*
9. ASSUMPTION-*
10. OPENQ-* (remaining unanswered items)

### Prefix Conventions

Use:

* CONTRACT-001
* BACKCOMPAT-001
* TEST-SCOPE-001
* TASK-001
* FILE-001
* DEP-001
* ALT-001
* RISK-001
* ASSUMPTION-001
* OPENQ-001

---

## Assumption Hygiene

ASSUMPTION-* is allowed **only** for non-gated domains.

Each ASSUMPTION-* must include:

* what is assumed
* why it was ambiguous
* risk if wrong
* how to confirm quickly

No buried assumptions in prose.

---

## OPENQ-* Handling & Handoff

* All unresolved OPENQ-* items must be listed prominently before handoff.
* Every OPENQ-* must carry `Status: Awaiting user confirmation` until the user replies.
* Once confirmed, update to `Status: Confirmed — <answer>` and move values into the appropriate plan section.
* OPENQ-* related to CONTRACTS or BACKCOMPAT are **hard-blocking** — the plan must not advance until resolved.
* OPENQ-* related to TEST or PERFORMANCE are **soft-blocking** — they may be non-blocking **only if defaults were explicitly accepted** by the user.

### Correct Checkpoint Phrasing

Use this pattern for the Performance/Backcompat checkpoint:

```md
**Performance/Backcompat checkpoint (unresolved):**
I need your input before finalizing requirements. I can suggest defaults, but I will not apply them unless you confirm.

OPENQ-008 (Performance/SLA)
Are there specific performance or SLA constraints?
Suggested default (not applied): Optimize for clarity; no special performance constraints.

OPENQ-009 (Backcompat/Consumers)
Does this affect existing interfaces or consumers requiring backwards compatibility?
Suggested default (not applied): No backwards compatibility required.

Reply format: `Open Questions: 8:a, 9:a ...` or `Open Questions: defaults`
```

Do **not** pre-insert backcompat statements, default requirement text, or ASSUMPTION-* entries based on unconfirmed items.

---

## Forbidden Anti-Patterns

* Producing a definitive plan when contracts or compatibility are ambiguous
* Letting implementers invent interfaces
* Burying assumptions in narrative text
* Assuming performance requirements imply complexity
* Treating test depth as implied rather than confirmed
* Writing a proposed default into a REQ-*, BACKCOMPAT-*, or TASK-* section before the user confirms it
* Saying "default is X" without the phrase "not applied"
* Updating plan sections first, then asking for confirmation — confirmation must precede updates
* Treating no-reply as implicit approval of proposed defaults
* Marking a plan "Ready for Critic" or handing off while any OPENQ-* item still has `Status: Awaiting user confirmation`

---

## Finalization Gate

Before marking a plan "Done", "Ready for Critic", or handing off to Implementer, the agent MUST emit a **Finalization Status block**.

### Required Format

```md
## Finalization Status

### Resolved Decisions
- OPENQ-001: <question> → <answer> [RESOLVED]

### Proposed Defaults Awaiting Confirmation
- OPENQ-002: <question>
  Suggested default (not applied): <default>
  Status: Awaiting user confirmation

### Open Questions (no default)
- OPENQ-003: <question>
  Status: Awaiting user confirmation

Plan status: Draft (blocked on confirmation)
```

### Gating Rules

* If **any** OPENQ-* has `Status: Awaiting user confirmation`, the plan status MUST be:
  `Plan status: Draft (blocked on confirmation)`
* The agent MUST NOT hand off to Critic while blocked.
* Once all items are resolved, update and emit:
  `Plan status: Ready for Critic`

### What Counts as Resolution

* User explicitly confirms a default (including replying `Open Questions: defaults`)
* User provides an explicit alternative answer
* User explicitly states "skip this" or "not applicable"

Silence, no-reply, or implicit continuation does **not** count as resolution.