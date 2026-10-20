---
name: forge
description: Use when creating a new domain-specific AI organization — generates lean capability pool, workflows with three-pass review, document handling, and domain context engine
---

# Forge — Domain Organization Generator

Generates a complete AI organizational structure for a professional domain.

## RIGHT-SIZING GATE (ask before building)

Before generating a full domain, ask the user:

> "A full domain framework (capabilities, workflows, checklists, personas) is
> justified when you need: multiple users running the system, unattended
> execution, dozens of similar tasks per week, or handoff to someone who
> doesn't know the domain.
>
> For personal use on occasional tasks, a CLAUDE.md modification with the
> three-pass adversarial review pattern may be sufficient — it delivers
> ~70-80% of the value with ~5% of the infrastructure.
>
> Which fits your use case: full framework or CLAUDE.md?"

If the user says CLAUDE.md, help them write a lean project or global CLAUDE.md
with: domain context, the three-pass review pattern, relevant checklists, and
document handling instructions. Do not generate the full domain.

If the user says full framework (or needs scale/handoff/unattended), proceed
with the forge process below.

## Design Principles (learned from iteration)

1. **AI-native, not human-mimicking.** Agents don't need hierarchy, departments,
   or job titles. They need: specialized prompts, fresh instances for review,
   persistent knowledge, and conflict resolution rules. Design around *types of
   work* (building, reviewing, researching), not *human roles* (PM, architect,
   developer). If two "roles" are the same agent with a different prompt, they're
   one capability.

2. **Three functional types, not twelve job titles.** Most domains need:
   - **Builder** — produces artifacts (drafts, designs, code, documents)
   - **Reviewer** — adversarial critique (always a fresh instance, never the builder)
   - **Scout** — gathers context (research, prior art, codebase analysis)
   A domain might need a 4th type if it has genuinely distinct work that can't be
   a Builder with a different prompt. But the bar is high. "Uses Opus" is not
   enough — that's a model tier decision, not a capability boundary.

3. **The only hard organizational boundary is drafter ≠ reviewer.** Everything
   else is workflow logic with different prompts. The builder can write specs,
   designs, code, and docs — those are phases, not capabilities. The reviewer
   can check quality, security, and correctness — those are checklists, not roles.

4. **Natural language routing.** The user talks; the system routes. No rigid
   command taxonomy. `/{domain} analyze this contract` not `/orchestrate matter`.

5. **Three-pass flow for Medium+ deliverables.**
   Builder #1 drafts → Reviewer critiques → Builder #2 (fresh) synthesizes.
   The human sees one clean output, not two competing analyses.

6. **Deterministic verification before agent judgment.** If a tool can check it
   (lint, type check, test, math check, citation check), the tool runs first.
   Agent review is for things tools can't verify.

7. **Document handling is conditional.** Some domains are document-heavy (law,
   finance) and need a document handler. Others (BBQ, gardening) don't process
   documents at all. Only include a document handler if the domain actually
   processes documents as a core part of its work.

8. **Domain context engine is universal pattern.** Law has jurisdiction. Finance
   has regulatory regime. Consulting has industry context. Real estate has
   market/county. Every domain needs a context-aware lookup that scales with
   ceremony tier.

9. **Reviewer conciseness.** Max 5 issues, structured format, ~500 words.
   Value is in WHAT you find, not how much you write.

10. **Don't include what wasn't asked for.** Present research findings and let
    the human decide scope. Don't add compliance modules or ethics reviews
    unless the human says so.

11. **Ceremony tier = elastic composition.** Trivial = builder alone.
    Small = builder + one review pass. Medium = builder + adversarial reviewer +
    cross-model check. Large = full pipeline with scout, multiple review passes,
    and human gates. The same capability types scale — you don't add new types
    at higher tiers, you add more dispatches.

12. **Self-reinforcement isolation.** When generating multiple comparable
    outputs (personas, parallel analyses, independent reviews), each MUST
    be produced in its own dispatch with no visibility into the others.
    An LLM attending to its own prior output will converge — later outputs
    pattern-match against earlier ones. This is the architectural root cause
    of persona homogenization, reviewer anchoring, and "Claude voice"
    propagating across characters. The orchestrator owns the loop; each
    dispatch sees only its own input.

13. **Claude-ism suppression for character output.** When the Builder produces
    text that should sound like a specific person (personas, simulated
    responses, character-voiced content), apply the suppression list from
    `shared/claude-ism-suppression.md`. Claude has recognizable speech
    patterns that leak into character output. The suppression list is a
    living artifact that grows with use.

14. **Three-layer separation for domains that reason against real-world
    stimuli.** When a domain dispatches Scout to research real-world
    conditions, then Builder to generate responses or analysis against
    those conditions, then synthesizes findings — those are three distinct
    epistemic layers that must not collapse into one output stream:
    - **Fact layer** (Scout): what's announced, priced, confirmed, selling.
      Every empirical claim lives here, tagged with source confidence.
    - **Simulation/generation layer** (Builder): reactions, preferences,
      blocking variables, draft artifacts. Receives fact layer as read-only
      input. Does not generate empirical claims or market analysis.
    - **Analytical layer** (synthesis): pattern identification, concern
      clustering, recommendations. Explicitly labeled as interpretation.
      Magnitude claims are illustrative, not forecasts.
    The forge should inject this separation into any domain where Scout
    research feeds Builder generation. Without it, the tool generates
    quasi-empirical claims (fabricated statistics, invented details for
    grounding) that look like data but have no factual substrate.

15. **Epistemic labeling in analytical output.** When a domain produces
    analytical deliverables, every claim must be tagged: DIRECTIONAL
    (emerged from generation/simulation), ILLUSTRATIVE (magnitude estimate,
    not data), or EMPIRICAL (sourced from research, with confidence tag).
    This prevents downstream readers from treating synthetic-derived numbers
    as quantitative findings. A synthetic-derived "38% defection risk" is
    not a finding — it's a dramatization of "meaningful chunk will leave."
    Label it as such.

## How to Invoke

```
/forge "finance"        # Generate a financial analysis firm
/forge "consulting"     # Generate a consulting firm
/forge "real-estate"    # Generate a real estate brokerage
/forge "custom"         # Interactive: define a custom domain
```

## Process

### Phase 1: DOMAIN RESEARCH

Dispatch **Scout** capability (model: Sonnet):

> Research the work of a {domain} practice. Focus on TYPES OF WORK, not roles.
>
> **What types of work exist? (3-5 max)**
> - What are the fundamental loops? (not every possible task — the 3-5 categories
>   that cover 90% of work)
> - For each: what phases does the work go through? Where can phases run in parallel?
> - What's the output of each work type? (document, code, analysis, decision)
>
> **What can go wrong? (the domain's "OWASP")**
> - Top 10 errors practitioners make in this domain
> - Which errors are detectable by tools (deterministic verification)?
> - Which errors require human/adversarial judgment to catch?
> - What does a skilled adversarial reviewer look for? (opposing counsel in law,
>   security auditor in software, physics reviewer in R&D)
>
> **What goes wrong when AI does this work specifically?**
> - Known LLM failure modes for this domain (e.g., hallucinated citations in law,
>   persona convergence in synthetic audiences, over-articulation in simulated
>   human responses, rationality bias in consumer modeling)
> - Where does LLM training data skew the output? (demographic, cultural, linguistic)
> - Where will the AI be confidently wrong in ways a human practitioner wouldn't be?
> - What contamination risks exist when multiple AI dispatches produce related outputs?
>
> **What context does the work depend on?**
> - What varies by environment? (jurisdiction, regulatory regime, market, codebase)
> - Where does a practitioner look it up? (source priority hierarchy)
> - At what workflow stages must context be checked?
>
> **What artifacts does the domain produce and consume?**
> - Input document types and what's domain-specific about processing them
> - Output artifact types and their quality standards
> - Does this domain even involve documents, or is the work primarily
>   experiential/physical?
>
> **When does a practitioner actually consult this system?**
> - Before the work? During? After? All three?
> - Is the work desk-based (law, finance) or physical (cooking, construction)?
> - Can the practitioner interact with the system while doing the work, or
>   only before/after?
> - This determines which infrastructure components are needed. A domain where
>   the practitioner can't consult the system mid-work doesn't need execution
>   modes or iteration protocols — it needs good planning artifacts and
>   post-work analysis.
>
> **IMPORTANT: Do NOT research "roles" or "team structure." Research the WORK.
> The organizational structure will be derived from the work, not from how
> humans have traditionally staffed it.**

### Phase 2: DOMAIN ARCHITECTURE

Dispatch **Domain Architect** capability (model: Opus) with research:

> Design an AI-NATIVE organizational structure for a {domain} practice.
>
> **CRITICAL CONSTRAINT: Design around types of work, not human roles.**
>
> Every domain needs at most 3-4 capability TYPES:
>
> **Builder** (Sonnet) — produces artifacts. The same capability, dispatched with
> different prompts, can write specs, designs, implementations, and documents.
> If two "roles" are a Builder with different instructions, they are ONE capability.
>
> **Reviewer** (Opus, always fresh instance) — adversarial critique. Dispatched
> with different checklists for different concerns (quality, security, correctness,
> domain-specific risks). Never the same instance that built the artifact.
>
> **Scout** (Haiku→Sonnet) — gathers context before building begins. Research,
> prior art, codebase analysis, domain-specific lookups. Cheap model first,
> escalate if depth needed.
>
> **Verifier** (deterministic tools, not an agent) — if a tool can check it,
> the tool checks it. Agent review is only for what tools can't verify.
>
> A 4th agent type is allowed ONLY if the domain has genuinely distinct work that
> cannot be a Builder/Reviewer/Scout with a different prompt. Justify it explicitly.
>
> **Test: If you find yourself naming a capability after a human job title
> (PM, architect, QA engineer, security auditor), you're copying a human org.
> Name it after what it DOES (build, review, research).**
>
> **Workflows — with three-pass flow built in:**
> For each work type from research, design phases that use the capability types:
> - Builder (prompt A) drafts → Reviewer critiques → Builder (prompt B, FRESH) synthesizes
> - Scout gathers context BEFORE building, not as a separate "role"
> - Verifier runs deterministic checks BEFORE reviewer offers opinions
> - Phases that can run in parallel MUST be dispatched in parallel
> Skip three-pass for Trivial/Small (not worth the cost).
>
> **Execution mechanics — think about HOW dispatches work, not just WHAT they do:**
> For each workflow phase that involves multiple independent units (multiple
> personas, multiple documents, multiple test conditions, multiple categories):
> - Must the orchestrator dispatch them independently? (prevents contamination
>   between units — each dispatch sees only its own input, not other units' output)
> - Or can a single Builder dispatch handle the batch? (cheaper but risks one
>   unit's output influencing the next)
> - Rule of thumb: if seeing unit A's output would change how you produce unit B,
>   they MUST be independent dispatches. The orchestrator owns the loop.
> - Specify this in the workflow design. Don't leave it to the file generator.
>
> **Interface:**
> `/{domain-short-name} {natural language}`.
> The orchestrator determines work type, ceremony tier, and context automatically.
>
> **Infrastructure — include only what the domain needs:**
>
> Always include:
> - Ceremony signals (tier definitions and staffing)
> - Quality checklists (domain-specific items, adversarial prompts, known AI limitations)
> - Conflict rules (base rules + domain-specific priority rules)
> - Domain context engine (what to look up and where — complexity should match the domain)
>
> Include only if the domain needs it:
> - Document handler — only if the domain processes documents as core work (law, finance). Skip for experiential/physical domains.
> - Execution modes — only if the domain has real unattended use cases. Skip for domains where the practitioner interacts before/after but not during.
> - Iteration protocol — only if review feedback loops are complex enough to warrant formal rules. Skip for domains where iteration is informal ("do it again next time").
>
> **The architect must justify each optional component.** If the domain doesn't
> process documents, don't generate a document handler just because the template
> has one. Fewer files that match the domain beats more files for completeness.
>
> **Ceremony tier = elastic composition (same types, more dispatches):**
> | Tier | Composition | Review | Human gates |
> |---|---|---|---|
> | Trivial | Builder alone | Verifier only (tools) | None |
> | Small | Builder + one review pass | Reviewer (combined checklist) | None |
> | Medium | Scout + Builder + Reviewer + cross-model | Three-pass + parallel checklists | One gate |
> | Large | Full pipeline, parallel work units | Everything + multiple review passes | Two+ gates |

### Phase 3: HUMAN GATE — Architecture Review

Present the proposed architecture CONCISELY:
> **Domain: {domain}**
>
> Capabilities: {count} — {name (model)} for each
> Workflows: {count} — {name} for each
> Interface: `/{command} {natural language}`
> Domain context: {what context engine tracks}
>
> Key design decisions:
> - {decision 1}
> - {decision 2}
>
> Approve / Revise / Reject?

**Critical: Let the human cut scope.** If they say "drop compliance" or
"we don't need 6 roles," do it. Don't argue for comprehensiveness.

### Phase 4: GENERATE FILES

Generate the domain structure. Only include files the domain actually needs.

```
domains/{domain-name}/
├── CLAUDE.md                              # Handbook (<100 lines, natural language focus)
├── README.md                              # Usage guide
└── .claude/skills/orchestrate/
    ├── SKILL.md                           # Orchestrator (natural language routing)
    ├── ceremony-signals.md                # Tier definitions and staffing
    ├── domain-context-engine.md           # What to look up and where (complexity matches domain)
    ├── [document-handler.md]              # ONLY if domain processes documents as core work
    ├── [execution-modes.md]               # ONLY if domain has real unattended use cases
    ├── [iteration-protocol.md]            # ONLY if review loops need formal rules
    ├── workflows/
    │   ├── {workflow-1}.md                # Each with three-pass flow for Medium+
    │   └── ...
    ├── gates/
    │   ├── quality-checklists.md          # Domain-specific, with adversarial prompts
    │   └── conflict-rules.md              # Base + domain priority rules
    └── personas/
        ├── builder.md                     # Produces artifacts (specs, designs, code, docs)
        ├── reviewer.md                    # Adversarial critique (fresh instance, never the builder)
        ├── scout.md                       # Context gathering (research, prior art, lookups)
        └── [{domain-specific}.md]         # ONLY if a 4th type is justified
```

**Files in [brackets] are conditional.** The Phase 2 architect decides which
optional components the domain needs. If the architect didn't include it,
don't generate it. A domain with 10 files that all earn their place is better
than 17 files where 4 are filler.

#### Depth of Generated Files

Generated files have two layers. The model can produce the first layer cold.
The second layer requires human input or iteration.

**Layer 1: Structure (model generates reliably)**
- Workflow phases with dispatch prompts
- Ceremony tier composition
- Checklist items and adversarial prompts
- Conflict resolution rules and priority ordering
- Escalation language at gates
- Output format templates

Generate these at operational depth appropriate to the domain. A finance
domain's regulatory engine needs checkpoint ceremony and depth-by-tier
matrices. A BBQ domain's context engine needs a simple reference doc. Match
the formality of the infrastructure to the formality of the work.

**Layer 2: Practitioner knowledge (model generates best-effort, human refines)**
- Source hierarchies (which databases/tools, in what order, why)
- Effort calibration per tier (how many sources, how deep, specific thresholds)
- Document processing procedures (file-type-specific extraction and handling)
- Verification procedures (specific checks in specific order with specific tools)
- Domain-specific non-negotiable rules with precise boundaries

For Layer 2: generate the best defaults you can from Phase 1 research. Mark
everything with:
```
[GENERATED DEFAULT — refine through use]
```

These markers serve two purposes: (1) Phase 6 calibration uses them to show
the user what to correct, and (2) they tell future users where the domain
will improve with iteration. A domain with generated defaults is functional.
A domain where the user has replaced every default with real working knowledge
is expert-grade. The forge produces the first; use produces the second.

The shared base components in `shared/` provide the universal operational specs for
cost control, error resilience, observability, and conflict resolution. Generated
domains inherit these automatically. Domain-specific files should ADD to the base
specs (domain-specific priority rules, domain-specific checklists, domain-specific
failure modes), not duplicate them.

If a generated file would just restate what the shared component already says,
don't generate it — reference the shared component instead.

### Phase 5: ADVERSARIAL REVIEW

Dispatch a fresh reviewer (Opus):

> You are a senior {domain} professional AND an AI systems architect.
> Review this AI org structure against BOTH domain correctness and AI-native principles.
> Max 5 issues, concise format:
>
> **Domain correctness:**
> 1. Workflows that don't match how real work flows?
> 2. Checklists that miss real quality issues in this domain?
> 3. Domain context engine missing critical context types?
>
> **AI-native integrity:**
> 4. Any capability that's really a human job title instead of a functional type?
>    (Test: could this be a Builder/Reviewer/Scout with a different prompt?)
> 5. Any sequential phase that agents could run in parallel?
> 6. Any agent review that a deterministic tool could handle instead?
> 7. Any workflow phase where a single dispatch handles multiple independent units
>    that should be dispatched separately? (e.g., generating 10 persona responses
>    in one call contaminates later responses with earlier ones. The orchestrator
>    must own the loop for independent units.)
> 8. Any workflow where Scout research feeds Builder generation without a clean
>    layer boundary? (Test: can you tell which claims in the final output came
>    from research vs. were generated by the Builder? If not, the layers have
>    collapsed.)
>
> If a "capability" is just Builder with a different prompt, flag it.
> If a workflow has agents doing work that tools could verify, flag it.
> If a dispatch batches independent units that could contaminate each other, flag it.
> If research and generation are blended in the output, flag it.

Incorporate fixes. Don't add scope the human didn't ask for.

### Phase 6: CALIBRATION PASS

Two sub-phases. The AI resolves what it can; the human resolves what it can't.

#### Phase 6A: AI SELF-CALIBRATION

Review every `[GENERATED DEFAULT — refine through use]` marker in the generated
files. For each, assess whether the AI can set the correct value from established,
well-documented knowledge. Categories:

**Resolve automatically** (remove marker, set value):
- Published standards (USDA safe temps, legal filing rules, industry benchmarks)
- Well-documented domain conventions (3-2-1 is the standard rib template, KCBS
  uses 6 judges on a 2-9 scale)
- Facts verifiable from authoritative sources (boiling point at altitude,
  specific competition rules)
- Thresholds with established consensus in the literature

**Leave for human** (keep marker):
- Practitioner-specific preferences (buffer times, salt balance approaches,
  workflow ordering that varies by individual)
- Values that depend on the user's specific context (equipment, location,
  client base, personal experience)
- Judgment calls where reasonable practitioners disagree
- Anything where the AI's confidence comes from training data patterns rather
  than citable sources

For each default resolved, briefly note the source ("USDA standard", "KCBS
rulebook", "established food science"). If you're not confident enough to cite
a source, leave it for the human.

**The goal is to cut the human's calibration list in half or more.** Don't
waste the human's expertise on things the AI already knows.

#### Phase 6B: HUMAN CALIBRATION

Present only the items the AI couldn't resolve — the genuinely uncertain
defaults that require practitioner knowledge.

> **The domain is generated. I've already set {N} defaults from established
> knowledge (published standards, documented conventions). Here are the {M}
> remaining items where I need your input.**

For each item, show the current default and why you're unsure:

> **{Item}** ({file}):
> I have: {current value}
> Why I'm unsure: {what makes this practitioner-specific rather than
> established knowledge}

Rules:
- Maximum 8 items — prioritize the ones most likely to be wrong
- If the user corrects a default, update the generated file immediately
- If the user confirms a default, remove the `[GENERATED DEFAULT]` marker
- If the user skips an item, leave the marker — it'll get refined through use
- If the human's correction reveals that the AI got a self-calibrated item
  wrong too, fix it — but this should be rare if 6A was done well

After incorporating corrections, proceed to commit.

### Phase 7: COMMIT AND REPORT

> **Domain generated: {domain}**
> Command: `/{short-name} {natural language}`
> Capabilities: {count}
> Workflows: {count}
> Test it: `/{short-name} {example task for this domain}`

## Pre-Built Domain Hints

### Software Development
- Command: `/dev`
- Minimum team: Developer (Sonnet) + Combined Reviewer (Sonnet)
- Context engine: codebase patterns, dependencies, prior project experience
- Adversarial: code quality reviewer, security auditor, cross-model reviewer
- Domain-specific: TDD mandatory, deterministic tools before agent judgment,
  fresh instance for every review, parallel specialist reviews at Medium+
- Workflows: feature, bugfix, techdebt, research, greenfield
- Key principle: AI-native organization — flat capability pool, no hierarchy
  where rules suffice, no departments where dispatch logic is better

### Finance
- Command: `/finance`
- Minimum team: Analyst (Sonnet) + Data Support (Haiku)
- Context engine: regulatory regime (SEC, FINRA, state, international)
- Adversarial: auditor, regulator
- Domain-specific docs: Excel models, financial statements, regulatory filings

### Consulting
- Command: `/consult`
- Minimum team: Consultant (Sonnet) + Research Support (Haiku)
- Context engine: industry context (client's sector, competitive landscape)
- Adversarial: skeptical client, board member
- Domain-specific docs: slide decks, data reports, interview transcripts

### Real Estate
- Command: `/realestate`
- Minimum team: Agent/Analyst (Sonnet) + Transaction Support (Haiku)
- Context engine: market + jurisdiction (county rules, zoning, recording requirements)
- Adversarial: opposing broker, inspector, lender underwriter
- Domain-specific docs: contracts, plats, aerials, inspection reports, title docs
