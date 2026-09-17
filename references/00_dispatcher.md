# 3Part Proposal Engine — Technical Operational Dispatcher

**Framework Author**: William Fitzpatrick (*Writer Science*)  
**Source Lecture**: [20 Years of Writing Advice in 52 mins](https://www.youtube.com/watch?v=G-Sl0-PZv2Q)  
**Parent Collection**: [Master Collection](../../kirby-fitzpatrick-writers-collection/SKILL.md) | [Global Help](../../../kirby-help/SKILL.md)  

---

## 1. Cognitive Foundation: The Destabilized Status Quo

Every engineering proposal — an RFC, a PR body, a review comment, a dependency bump — is a persuasion artifact competing against the strongest opponent in the room: **doing nothing**. Inaction carries zero migration cost, zero review risk, and zero blame surface. It is free to reject and expensive to approve. Therefore the reviewer's default output is `NO`, and any author who does not engineer the reader's path to `YES` is silently defaulting to it.

The failure mode is almost never weak evidence. It is **sequence**. The author has lived inside the problem for weeks; the problem has become self-evident, so it gets compressed into half a sentence — or deleted entirely — and the document opens with the solution. The reviewer receives a *conclusion without the pressure that produced it*.

**The Three-Load Model.** A reviewer must load three things before a decision is physically possible:

1. **A shared model of the current state** — what the system actually is right now, agreed on by both parties.
2. **A felt cost of that state continuing** — the trajectory must be framed as a loss already underway, not a future benefit.
3. **A bounded path out** — scoped, reversible, executable.

Skip (1) and the discussion derails into definitional argument ("that isn't how our auth works"). Skip (2) and the proposal reads as churn, preference, or résumé-driven development. Skip (3) and it reads as a complaint. Any single omission collapses the reader back to the status quo.

**Why the 3-part sequence works.** It converts the proposal from an assertion into a *forced logical relay*: the common ground is something the reviewer already believes; the destabilizing problem is an unavoidable consequence of that belief; the solution is the only remaining exit. The reviewer experiences the change as their own inference rather than your request.

```text
[ANTI-PATTERN: Solution-First Proposal]      →  Reviewer must reconstruct the missing phases
+--------------------------------------------------------------------------+
| "We should migrate the monorepo to pnpm workspaces and adopt Turbo."     |
|         ^^^^ PHASE 3 in sentence 1; phases 1-2 absent                    |
+--------------------------------------------------------------------------+
Reviewer's internal state: Why? What is broken? What does this cost us?
Result: NO, or an indefinite "let's revisit next quarter."

[3-PART SEQUENCE]                            →  Reviewer walks a prepared path
+-- 1. COMMON GROUND (Status Quo) ----------------------------------------+
| "Today 9 packages share one root node_modules. CI installs 1.4 GB in     |
|  11m20s at p50. Every package pins its own transitive versions."         |
+-- 2. DESTABILIZING PROBLEM (Cost of Inaction) ---------------------------+
| "That install is paid per package per job: +90s each. At the current     |
|  merge rate the p50 is 14 min by Q3. 3 of the last 5 broken releases     |
|  were phantom-dependency failures that local builds could not see."      |
+-- 3. TECHNICAL SOLUTION (Resolution) ------------------------------------+
| "Adopt pnpm workspaces. Scope: root + 9 packages, no Turbo, one PR,      |
|  revert = single lockfile rollback. Week 1: CI parity spike on a flag."  |
+--------------------------------------------------------------------------+
Reviewer's internal state: That IS how our repo works -> that trajectory is
unacceptable -> yes, and the first step is cheap. Decision reached.
```

**Phase contract table.**

| Phase | Reviewer question it answers | Author obligation | Evidence class | Failure symptom when missing |
|---|---|---|---|---|
| 1. Common Ground (Status Quo) | "Do we agree on reality?" | Describe the current system neutrally, as a third party would | Verifiable artifact: metric, config path, commit SHA, incident ID | Definitional derailment; "that's not accurate" threads |
| 2. Destabilizing Problem (Cost of Inaction) | "Why can't this stay as it is?" | Name one causal failure mode and its trajectory | Quantified cost in the approver's unit; date or compounding rate | "Feels like churn / preference / resume-driven development" |
| 3. Technical Solution (Resolution) | "What exactly am I approving?" | Bound scope, rollback, and blast radius | Concrete first step, executable inside one sprint | Indefinite deferral; "let's revisit next quarter" |

---

## 2. Core Transformation Protocols

1. **Write backwards, deliver forwards.** Draft the solution first. Then ask "what problem is this the answer to?" Then ask "what must both of us already believe for that problem to be real?" Reverse-engineer the argument, then present it in forward order: Common Ground → Problem → Solution. Never publish the reverse-engineered order.
2. **Common Ground must be falsifiable.** One sentence, present tense, third-person, no opinion, no adjectives, no proposal language. It must contain something the reviewer can open, run, or link: a metric, a config path, a line of code, an incident ID. "Our CI is slow" is opinion; "CI p50 is 11m20s, 61% of that is dependency install" is common ground.
3. **Exactly one destabilizing condition.** A laundry list of grievances reads as a grievance. Name one causal mechanism — the thing that *breaks the equilibrium* — and let it carry the entire proposal.
4. **Quantify the cost of inaction in the approver's currency.** Engineer-weeks for a tech lead, p99 and incident count for an SRE, dollars and runway for a VP, support tickets for a PM. Ambiguity here is the most common reason a technically correct RFC dies.
5. **Never transport urgency with adjectives.** `critical`, `urgent`, `ASAP`, `unacceptable`, `massive` are all zero-information tokens that signal *personal* discomfort rather than *systemic* cost. Delete them; the cost curve supplies the urgency and does it more persuasively.
6. **Frame cost as a loss underway, not a benefit foregone.** "We are currently paying 90s per package per job" beats "we could save 90s per package per job." Loss aversion is a lever; use it honestly by showing work already being spent.
7. **The Solution must be bounded, reversible, and staged.** State all three explicitly: a scope ceiling (*what is NOT included*), a rollback path (*how to undo in one operation*), and a blast radius (*what breaks if this is wrong*). Unbounded proposals are rejected for risk reasons that the author never hears out loud.
8. **Answer the objection before it is raised.** Add one sentence per foreseeable objection: the "do nothing" option, the competing alternative, and the migration cliff. An unanswered objection becomes a blocking comment.
9. **Phase-ratio discipline.** Aim for roughly **20% Common Ground / 30% Problem / 50% Solution** by length — but the Problem paragraph carries the highest information density per sentence. If Common Ground exceeds 25%, you are stalling; if the Problem is under 20%, you are asserting.
10. **Run the phase test on every paragraph.** Label each paragraph `CG`, `DP`, or `TS`. Any paragraph you cannot label is either merged or deleted. Any `TS` appearing before the first `DP` is a structural violation and must move.

**Transformation table: anti-patterns and clean replacements.**

| Phase | Anti-Pattern (what it looks like) | Why it fails | Clean Replacement |
|---|---|---|---|
| 1. Common Ground | "Our current architecture is a mess." | Judgement, not observation; invites dispute instead of agreement | "8 services share one database schema; the `orders` table is written by 5 of them." |
| 1. Common Ground | "As everyone knows, we have technical debt." | Unfalsifiable consensus claim; the reviewer can decline the premise | "The `billing` module has 412 lines of duplicated retry logic across 7 call sites." |
| 1. Common Ground | Three paragraphs of background history | Stalling; reviewer disengages before reaching the problem | One paragraph, max 3 sentences, ends on a measurable state |
| 2. Problem | "This is critical and must be fixed ASAP." | Zero information; urgency without cost; reads as personal frustration | "Payment reconciliation is silently dropping ~0.4% of refunds; 12 support tickets in 3 weeks." |
| 2. Problem | A list of 9 grievances | Diffuses causality; each item is individually deniable | One destabilizing condition, one causal chain, one cost trajectory |
| 2. Problem | "We could save 2 engineer-weeks." | Future benefit, easily deferred; no pressure today | "We are spending 2 engineer-weeks per quarter on manual reconciliation, rising each quarter." |
| 2. Problem | Cost expressed in your unit ("code smells") | Approver cannot price it; cannot defend the budget decision upward | Cost expressed in their unit: engineer-weeks, p99 ms, incidents, dollars, tickets |
| 3. Solution | "We should refactor to a clean hexagonal architecture." | Unbounded scope; no first step; maximum perceived risk | "Extract `PaymentRetry` into one module. Scope: 7 call sites, no API change, 1 PR. Revert = revert 1 commit." |
| 3. Solution | "Option A is clearly best." | States preference; skips the trade-off the reviewer must defend | "A costs 3 engineer-weeks and adds a network hop; B costs 1 week and blocks tenant isolation until Q4. Recommending B." |
| 3. Solution | No rollback, no blast radius, no "what's excluded" | Reviewer rejects on unstated risk; author never learns why | Explicit scope ceiling + rollback path + blast radius, each one sentence |
| Any phase | Proposal language inside Common Ground ("so we should…") | Breaks the agreement phase; reviewer negotiates the solution before accepting reality | Move every prescriptive sentence into Phase 3 |

**Failure diagnostics.**

| Symptom in review | Structural diagnosis | Fix |
|---|---|---|
| "Let's discuss this offline / revisit next quarter" | Deferral, usually a missing or unpriced Cost of Inaction | Add a trajectory with a date and an approver-owned unit |
| "Can you explain why we need this?" | Solution presented before Common Ground | Move the current-state paragraph to the top |
| "This is a lot of churn for little gain" | Cost of inaction framed as future benefit, not active loss | Reframe as an ongoing expenditure with a compounding rate |
| "What's the actual scope here?" | Unbounded Solution | Add scope ceiling, rollback path, blast radius |
| Long thread arguing about the current design | Common Ground loaded with judgement | Strip opinions; keep only linkable, measurable facts |

**Related dispatchers.** Front-load the subject-verb nexus in each phase-opening sentence with [Locomotive Syntax Engine](../../kirby-fitzpatrick-locomotive-syntax/SKILL.md); strip zero-information tokens with the [Lexical Anti-Bloat Filter](../../kirby-fitzpatrick-lexical-anti-bloat-filter/SKILL.md); replace abstract verbs of state with measurable ones via the [Empty Verb Extractor](../../kirby-fitzpatrick-empty-verb-extractor/SKILL.md).

---

## 3. Engineering Application Scenarios

### 3.1 Code Reviews — Framing a Requested Change So It Gets Accepted

Review comments fail for the same reason RFCs do: they ship phase 3 only. "Use the repository pattern here" is a solution with no problem attached, and the author — who is inside a deadline — reasonably responds "why?". Apply the sequence to a single comment.

**Before (solution-first, adversarial reading):**
> This abstraction is wrong. Please use the repository pattern and stop querying from the handler.

**After (3-part comment, ~45 words):**

```markdown
Agreed on keeping tenant scoping in one place (CG: we both do this in `TenantRepo`).
DP: this handler now queries `orders` directly, so tenant filtering is duplicated at line 88
and the next divergence drops it silently — that is the shape of incident INC-4412.
TS: move the query into `TenantRepo.findOrders()`; no behavior change, 1 commit.
```

Operational rules: **CG must be a genuine agreement**, not flattery — quote the existing shared pattern or convention by name so the author can verify it. **DP must cite an observable consequence**, ideally a prior incident, test gap, or duplicated call site with line numbers. **TS must be the smallest change that resolves the destabilizing condition**, and must say whether the reviewer will do it. Never stack three requests in one comment; one destabilizing condition per comment, or the author will fix only the cheapest and consider the thread closed.

### 3.2 PR Descriptions — The Three-Block Body

The PR description is where a decision-that-already-happened gets audited six months later by someone with no context. Structure it so the *decision* is recoverable, not just the diff.

```markdown
## Context (Common Ground)
`orders-api` retries failed charges inline, in 7 call sites. p50 retry path = 240 ms; the
handler has no retry budget. (See `src/orders/handler.ts:88`, `src/billing/*.ts`.)

## Problem (Cost of Inaction)
Retry semantics diverge per call site: 3 backoff to a cap, 4 retry instantly. The instant
ones caused 2 of 4 charge-duplication incidents since March (INC-4412, INC-4501), each
~6 support hours. Every new endpoint re-implements the decision, so the divergence rate
scales with the endpoint count, not with the incident rate.

## Change (Technical Solution)
Extract `RetryPolicy` used by all 7 call sites. Backoff = capped exponential, matching the
3 correct implementations.
- Scope ceiling: no signature changes, no new dependency, no endpoint added or removed.
- Rollback: revert this single commit; no schema or config migration.
- Blast radius: charge path only; refunds and payouts untouched.
- Verification: existing retry tests + 3 new duplication-regression tests.
```

**Ordering rule:** Context → Problem → Change, always. A PR body that opens with "What changed" forces every future reader to reconstruct the problem from the diff. **Ratio rule:** Context ≤ 3 sentences, Problem ≤ 5, Change = the rest. If the Context block exceeds the Problem block, the author is journaling rather than proposing.

### 3.3 Architecture RFCs / ADRs — Including Dependency Upgrade and Tech-Debt Pitches

ADR templates already contain all three phases, which is exactly why they get filled in wrongly: authors put the *solution* into "Context", leaving "Decision" as a restatement and "Consequences" as a formality. The mapping is:

| 3-Part Phase | RFC section | ADR section | Content requirement |
|---|---|---|---|
| Common Ground (Status Quo) | Context / Current State | **Context** | The system as it is, with version numbers, call sites, metrics |
| Destabilizing Problem (Cost of Inaction) | Motivation / "Why now" | **Consequences — split into "Cost of Inaction"** | One causal mechanism + quantified trajectory + trigger date |
| Technical Solution (Resolution) | Proposal / Migration Plan | **Decision** + **Consequences of the Decision** | Scope, rollback, blast radius, staged plan, rejected alternatives |

**Worked example — major dependency upgrade proposal (framework v3 → v5):**

```markdown
### Context
`billing-svc` pins `framework@3.9`. v3 reached upstream EOL on 2026-04-01 and receives
security patches only for v5. 41 direct call sites use the v3 middleware chain
(`src/**/middleware.ts`). CI currently builds and passes on v3.9.2.

### Problem — Cost of Inaction
We are on a dead support branch while running a PCI-scoped workload: any CVE in
`framework@3` now requires a vendor backport we do not control, at unknown latency.
Second, migration cost is not constant — it compounds with every new middleware we add.
The v3→v5 diff grew from 18 to 41 call sites in 2 quarters; at the current merge rate the
upgrade window closes in ~2 quarters, after which it becomes a rewrite rather than a bump.

| Cost axis | Now (v3) | +2 quarters (v5) |
|---|---|---|
| Security patch latency | vendor backport, unbounded | upstream release day |
| Engineer-weeks to migrate | 3 (est. from spike, PR #812) | 7+ (call sites are accelerating) |
| Audit exposure | EOL dependency in PCI scope | current, documented |

### Decision
Upgrade to `framework@5` in three staged PRs. Scope ceiling: middleware chain + config,
no API surface change, no new dependency. Rollback: each stage is independently revertible;
no data migration. Blast radius: `billing-svc` only; `ledger` stays on v3 until Q3.

1. PR 1 — bump with the v3 compatibility shim, CI parity only (no runtime change).
2. PR 2 — migrate 41 call sites to the v5 middleware chain, behind a feature flag.
3. PR 3 — remove the shim, delete flag, close the ADR.

Rejected alternative: stay on v3 + vendor backport — accepts unbounded patch latency in a
PCI scope to save 3 engineer-weeks now. Rejected.
```

**Tech-debt pitch rule:** tech debt is a *cost curve*, not a moral category. Never write "the code is bad." Write the ongoing expenditure, the compounding rate, and the closing window. A proposal that names a date after which the work gets more expensive converts an unbounded backlog item into a scheduled decision — which is the only form a budget committee can act on.

---

## 4. Verification Checklist

- [ ] **Common Ground is falsifiable and opinion-free.** The first block states the current system state in present tense with ≥1 verifiable artifact (metric, file path + line, commit SHA, incident ID) and contains no adjectives of judgement and no proposal language.
- [ ] **Exactly one destabilizing condition, priced in the approver's unit.** A single causal mechanism is named, its cost of inaction is expressed in a unit the approver owns (engineer-weeks, p99 ms, incidents, dollars, tickets), and it includes either a trajectory, a compounding rate, or a trigger date.
- [ ] **Solution is bounded, reversible, and staged.** The proposal states a scope ceiling (what is excluded), a rollback path (the single operation that undoes it), a blast radius, and a first step executable inside one sprint.
- [ ] **Phase test passes.** Every paragraph is labelable `CG` / `DP` / `TS`; no paragraph is unlabeled; no `TS` appears before the first `DP`; no urgency adjective (`critical`, `urgent`, `ASAP`, `unacceptable`) survives in the final text.
- [ ] **Skeleton test passes.** Reading only the three phase headings and the first sentence of each still yields the same decision the full document yields — a reviewer skimming on a phone does not reach a different conclusion than one reading in full.