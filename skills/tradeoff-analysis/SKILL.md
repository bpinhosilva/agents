---
name: tradeoff-analysis
description: Guides a structured, collaborative trade-off analysis for technology and system-design decisions. Use when choosing between technology options or architectures (databases, protocols, cloud services, build-vs-buy, sync-vs-async, monolith-vs-services, etc.), writing an ADR/RFC, or when a defensible recommendation is needed — especially for decisions that are hard to reverse.
---

# Trade-off Analysis

## Overview

A structured technique for choosing between technology or architecture options. Produces a **weighted decision matrix** plus a short written recommendation that names assumptions, risks, reversibility, and non-goals — so reviewers can challenge the *inputs* rather than argue about the *conclusion*.

**Default posture:** act like a thoughtful colleague running a decision workshop with the user, not like an autonomous analyst delivering a finished answer. Keep the user involved at each step, make reasoning visible, and pause for confirmation before moving from framing → criteria → weights → scoring → recommendation.

**Core principle:** The quality of a technology decision is the quality of its *criteria and weights*, not the eloquence of the prose. Make criteria, weights, and assumptions explicit before scoring anything.

## When to Use

Use when:
- Choosing between 2+ concrete options (Postgres vs DynamoDB, REST vs gRPC, monolith vs services, AWS vs GCP, build vs buy)
- Evaluating architectural qualities against each other (consistency vs availability, latency vs cost, flexibility vs simplicity)
- Writing an ADR, RFC, design doc, or proposal section that needs a recommendation
- A stakeholder asks "which should we pick and why?"

Do NOT use for:
- Decisions that are clearly reversible and cheap (two-way doors with low blast radius) — just pick one and move on
- Pure preference/style choices with no measurable impact
- Problems where the real blocker is missing requirements, not missing analysis — go elicit requirements first

## Interaction Protocol

If the user does not provide enough context (e.g., missing constraints, unclear options, lack of specific goals), **do not invent them**. State what is missing and ask the user to provide the assumptions, non-goals, or specific options *before* generating the matrix. Only proceed to the Seven Steps once you have a clear understanding of the decision space.

Run this as a **guided process by default**:

1. Ask concise clarifying questions first.
2. Summarize what you heard in plain language.
3. Ask the user to confirm or correct the summary.
4. Work through the seven steps in order, usually one step per turn or one tightly related pair of steps per turn.
5. After each major step, show the current state and ask whether to continue or adjust.

Do **not** complete the full matrix in one pass unless the user explicitly asks for a fast draft or has already supplied enough confirmed detail to do so responsibly.

When guiding:
- Prefer questions over assumptions.
- Explain *why* each step matters so the user can learn from the process.
- Offer suggestions as proposals to react to, not as final decisions.
- If you supply provisional defaults to keep momentum, label them clearly and ask the user to approve or replace them.
- Keep questions focused; ask only the next few that unblock the process.

On the **first turn**, do only three things unless the user already supplied rich context:
- Propose a one-sentence framing of the decision.
- Name the most important missing constraints.
- Ask 3–6 high-leverage clarifying questions.

Facilitation principles (apply throughout):
- Be collaborative, not performative.
- Make disagreements explicit instead of smoothing them over.
- If the user seems unsure, switch into coaching mode: explain the trade-off, then ask a focused follow-up question.
- If the user requests speed, provide a **draft** answer with clearly labeled assumptions and invite correction.
- Never hide uncertainty; use it to decide where a spike, benchmark, or prototype would help.

## The Seven Steps (do them in order)

Skipping or reordering steps is the most common failure mode. In particular: **do not score options before weights are set**, and **do not set weights after seeing option scores** — that is reverse-engineering the answer you already wanted.

Unless the user explicitly asks for a compact mode, do not run all seven steps without interaction. Use the sequence below as a collaborative agenda and keep checking alignment.

1. **Frame the decision** — one sentence: *"Choose X to achieve Y under constraints Z."*
2. **State assumptions and non-goals** — what you're taking as given, and what this decision is explicitly *not* optimizing for.
3. **Elicit criteria** — the axes that actually matter for *this* decision.
4. **Assign weights** — before looking at options.
5. **Score each option** against each criterion with a short justification.
6. **Analyze risk & reversibility** — one-way vs two-way door, top risks with mitigations.
7. **Recommend, with sensitivity check** — would the answer change if a weight or score shifted?

## Quick Reference

| Symptom                                       | Fix                                                                 |
|-----------------------------------------------|---------------------------------------------------------------------|
| Recommendation feels pre-baked                | Set weights *before* scoring; don't edit weights after scoring      |
| Everything scores 3–4; no signal              | Criteria too generic — re-derive from actual requirements           |
| 10+ criteria, analysis bloats                 | Merge overlapping criteria; drop any with weight < 5                |
| Prose recommendation without numbers          | Build the matrix; numbers force honesty                              |
| No mention of what could go wrong             | Add risk register for the leading option                            |
| "It depends" with no commitment               | Run the sensitivity check; state the trigger that would flip the call |
| Reversibility not discussed on a big decision | Explicitly label one-way vs two-way door                             |

## Step Details

### 1. Frame the decision
One sentence. If you cannot write it in one sentence, the decision isn't scoped yet — clarify before continuing.

Start by proposing a draft framing sentence and asking the user to confirm or edit it.

### 2. Assumptions & non-goals
- **Assumptions:** load, team size, timeline, budget, compliance constraints, existing stack — anything you're taking as given. If an assumption is wrong, the analysis is invalid; making them explicit lets reviewers catch this.
- **Non-goals:** what this decision is *not* trying to optimize (e.g., "not optimizing for analytics workloads", "not a general-purpose event store", "not solving multi-region yet"). Non-goals prevent scope creep from dragging the recommendation off-center.

If the user has not stated these, ask for them explicitly. When helpful, offer a short candidate list and ask which items are true.

### 3. Criteria
Derive from the *requirements and constraints*, not from generic checklists. Typical categories (pick the ones that apply; add domain-specific ones):

- **Fit-for-purpose:** meets functional requirements, performance targets (throughput, latency, p95/p99)
- **Operability:** on-call burden, observability, existing team expertise, hiring pool
- **Cost:** infra cost, licensing, migration cost, total cost of ownership
- **Evolvability:** flexibility for likely future requirements, lock-in, exit cost
- **Risk:** maturity, blast radius on failure, security/compliance posture
- **Time-to-value:** how fast can we ship the first version

5–8 criteria is usually right. More than 10 means criteria are too granular; fewer than 4 means the analysis is probably too shallow.

Do not silently pick the criteria yourself. Propose a shortlist, explain each in one line, and ask the user which ones to keep, remove, merge, or add.

### 4. Weights
Assign weights summing to 100 (or 1.0). Drive weights from the *framing and constraints*, not from gut feeling. Document the reasoning for each weight in one sentence.

**Pitfall:** If you find yourself adjusting weights after scoring to make a preferred option win, stop. Either your weights were wrong (fix them and rescore *all* options) or your preference is not well-supported (update your preference).

Guide the user through weighting instead of declaring the weights unilaterally. If they are unsure, suggest an initial weighting and ask them to react to it.

### 5. Score options
Use a consistent scale (1–5 recommended: 1=poor, 3=acceptable, 5=excellent). For each cell, one line of justification. **Eliminate dominated options early** — an option that loses or ties on every criterion can be cut with a note, saving analysis effort.

Weighted score = Σ (weight × score) per option.

Score collaboratively. For contested scores, call out the uncertainty and ask what evidence or operating experience should change the score.

### 6. Risk & reversibility
- **Reversibility:** Is this a **one-way door** (hard/expensive to reverse — e.g., datastore choice, core protocol, cloud vendor) or a **two-way door** (cheap to change — e.g., a library, an internal API shape)? Raise the bar for one-way doors: demand stronger evidence, prefer more conservative options, and prefer optionality-preserving choices when scores are close.
- **Risk register:** top 3–5 risks per leading option, with likelihood (L/M/H), impact (L/M/H), and mitigation. A high-score option with an un-mitigated high/high risk is not actually the winner.

Use this step to surface blind spots. Ask the user what would make the decision painful to reverse and what failure modes worry them most.

### 7. Recommendation & sensitivity
- State the recommendation and the *decisive* reasons (usually 2–3 criteria where it clearly wins, or where alternatives clearly fail).
- **Sensitivity check:** Would the recommendation flip if:
  - The top-weighted criterion were weighted 10 points lower?
  - Any single score moved by ±1?
  - A key assumption (load, team size, timeline) changed by 2×?
- If the answer flips easily, say so — the decision is close and deserves a lighter commitment or a spike/prototype before committing.

Before presenting the recommendation as final, summarize the decisive factors and ask whether the user wants to challenge any weight, score, or assumption.

## Output Template

Before generating the Markdown template below, **you must output a brief paragraph explicitly stating your current understanding of the core constraints**. This ensures you clarify the problem space before assigning weights.

If the process is still interactive, treat the template as the evolving shared artifact: fill only the sections supported by confirmed information, then ask the next blocking question instead of fabricating the rest. Do not force the full template into the first response unless the user explicitly wants a fast draft.

Once you have enough confirmed information to move beyond questioning, produce exactly this structure. Keep prose tight; the matrix does the heavy lifting.

```markdown
## Decision
<one-sentence framing>

## Assumptions
- <assumption 1>
- <assumption 2>

## Non-goals
- <what this decision is not optimizing for>

## Criteria & Weights
| # | Criterion | Weight | Rationale |
|---|-----------|--------|-----------|
| 1 | <name>    | 25     | <why>     |
| … | …         | …      | …         |
|   | **Total** | **100**|           |

## Options
- **A:** <one-line description>
- **B:** <one-line description>
- **C:** <one-line description>  (eliminated: dominated — see note)

## Scoring (1–5)
| Criterion (weight) | A | B | C |
|--------------------|---|---|---|
| Fit-for-purpose (25) | 4 — <why> | 5 — <why> | 2 — <why> |
| Operability (20)     | 5 — <why> | 3 — <why> | 2 — <why> |
| …                    | … | … | … |
| **Weighted total**   | **X** | **Y** | **Z** |

## Reversibility
One-way door / Two-way door — <brief reasoning, e.g. "datastore choice; migration cost ~2 quarters">

## Top Risks (leading option)
| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| …    | M          | H      | …          |

## Sensitivity
- If weight on <X> drops 10 points → <result>
- If <assumption> changes → <result>
- Close call? <yes/no, and what would change it>

## Recommendation
**Choose <option>.** Decisive reasons: <2–3 bullets>. Revisit if <trigger conditions>.
```

## Guard Rails

**Anti-patterns to avoid:**
- **Scoring before weighting.** Guarantees confirmation bias. Weights first, always.
- **Adjusting weights until your favorite wins.** This is reverse-engineering, not analysis. If it happens, your preference is the hypothesis — go falsify it, don't launder it.
- **Generic criteria copy-pasted across decisions.** Criteria must be derived from *this* decision's requirements and constraints.
- **Ignoring dominated options instead of eliminating them.** Note and cut; don't pad the matrix.
- **Treating one-way doors like two-way doors.** The cost of being wrong is asymmetric; the analysis depth should reflect that.
- **Missing non-goals.** Without them, every criterion feels relevant and weights get diluted.
- **No sensitivity analysis.** A recommendation that quietly depends on a single shaky assumption is a landmine.
- **Risk register as an afterthought.** A high-score option with unmitigated high/high risks is not the winner.

**Stop and redo if:**
- You wrote the recommendation before you built the matrix
- Weights were adjusted after seeing scores
- Every option scores within 5% of every other — criteria aren't discriminating
- No assumption or non-goal was ever stated
- The leading option has a high-likelihood, high-impact risk with no mitigation
- The decision is one-way and you haven't said so
