# Conflict Resolution — Base Rules

Shared across all domains. Each domain adds domain-specific priority rules
in its own `gates/conflict-rules.md`.

---

## Universal Rules (apply to every domain)

### Rule 1: Deterministic verification overrides agent opinions.
If a tool can verify it (math check, citation check, spell check, format validation,
lint, type check, test suite), the tool's answer is authoritative regardless of
what any agent says. Tools have no model bias.

### Rule 2: The brief/spec/scope defines "what."
Capabilities don't debate requirements — they execute them. If a capability believes
the scope is wrong, it escalates to the human, not to other capabilities.

### Rule 3: When capabilities disagree on "how":
1. Apply domain-specific priority rules first (defined per domain)
2. If domain rules don't resolve: prefer the simpler approach (fewer moving parts,
   fewer abstractions, less coupling)
3. If simplicity is genuinely ambiguous: escalate to human with both positions
   and a recommendation

### Rule 4: Adversarial reviewer disagreeing with drafter triggers human review.
When an adversarial review finds issues the drafter missed, the human sees both
perspectives before the deliverable is finalized. The drafter is NEVER the same
instance as the reviewer.

### Rule 5: Cross-model disagreement = human escalation.
If a cross-model reviewer (Gemini/GPT via external-tools) disagrees with the
Claude reviewer on the same artifact, this likely indicates a genuine blind spot.
Automatically escalate to human with both verdicts and the specific point of
disagreement.

---

## Escalation Protocol

When escalating to the human:

1. **State the question** — one sentence, what decision is needed
2. **Present the context** — what's been decided so far, what triggered the disagreement
3. **Show both sides** — each position with its reasoning
4. **Give a recommendation** — the orchestrator's best judgment
5. **State the consequences** — what happens with each choice
6. **Wait** — do not proceed until the human responds

---

## Verdict Merging (for parallel reviews)

When multiple reviews run in parallel:

```
Any FAIL with critical issues  → FAIL
  Action: Route ALL critical issues for fixing.
  Re-run failed reviews after fixes.

All PASS                       → PASS
  Action: Proceed to next workflow phase.

Mixed (some FAIL non-critical) → CONDITIONAL PASS
  Action: Proceed, but present non-critical issues at the next human gate.
  Log issues for the learning system.
```

---

## Review Verdict Format

Every review capability MUST return this structure:

```
VERDICT: PASS | FAIL

CHECKLIST:
- [item]: PASS (evidence) | FAIL (file:line — what, why, fix)

ISSUES:
- [severity: critical|important|minor] [reference] description — why — fix

BIAS DISCLOSURE:
Areas where this reviewer has low confidence or known blind spots for this
specific review.
```

The orchestrator checks for this format. Malformed responses trigger the
error-resilience garbage output handling.
