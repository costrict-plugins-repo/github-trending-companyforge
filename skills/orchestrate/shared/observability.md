# Observability — Dispatch Logging & Audit Trail

Shared across all domains. Domain-specific orchestrators inherit this.

---

## Dispatch Log

For every capability dispatch, the orchestrator appends an entry to the workflow's
log (held in memory during the session, persisted to the memory layer at workflow end):

```
DISPATCH LOG ENTRY:
  timestamp: {ISO 8601}
  workflow: {workflow type}
  phase: {phase name}
  capability: {capability name}
  model: {haiku|sonnet|opus|gemini|gpt}
  prompt_summary: {first 100 chars of the dispatch prompt}
  duration: {seconds}
  outcome: {PASS|FAIL|ERROR|TIMEOUT}
  cost_estimate: {estimated tokens × rate}
  output_summary: {first 200 chars of the return value}
  issues_found: {count, if review capability}
```

## Session Log Structure

The orchestrator maintains a running session log as a structured list:

```
WORKFLOW LOG: {workflow_type} — "{description}"
  ceremony_tier: {tier}
  budget: ${budget or "unlimited"}
  started: {timestamp}

  [1] DISPATCH: {Capability} ({Model}) — Phase: {PHASE}
      duration: Xs | outcome: PASS | cost: ~$X.XX
  [2] DISPATCH: {Capability} ({Model}) — Phase: {PHASE}
      duration: Xs | outcome: PASS | cost: ~$X.XX
  ...
  [N] WORKFLOW COMPLETE
      total_cost: $X.XX
      total_duration: Xm Xs
      phases_completed: N/N
      human_gates: N
      review_iterations: N
      issues_found: N (N critical, N important, N minor)
```

## Decision Audit Trail

For every decision point (human gate, conflict resolution, verdict merge),
the orchestrator logs:

```
DECISION: {description}
  trigger: {what caused this decision point}
  options: {what options were available}
  chosen: {what was decided}
  reason: {why — rule applied, human chose, or default}
  who: {human|rule-N|orchestrator}
```

This enables postmortems: "Why did the system approve this deliverable?" →
check the decision log → trace the chain of reviews, verdicts, and human gates.

## Persistence

At workflow end (success or failure), the orchestrator:

1. **Persists the log to the memory layer** as a session observation tagged with:
   - project name
   - workflow type
   - task description
   - total cost
   - outcome (success/failure/aborted)

2. **Writes a summary to the terminal** for the human:
   ```
   Workflow complete: {description}
   Cost: $X.XX (estimated: $X-X)
   Duration: Xm Xs
   Phases: N/N complete
   Reviews: N iterations, N issues found
   ```

3. **On failure/abort**, captures:
   - Which phase failed
   - What the error was
   - What was tried before escalation
   - State at time of failure (for resumption)

## Querying Past Workflows

Use the memory layer's search to find past workflow logs by description, cost,
or outcome. The exact query syntax depends on which memory system the domain uses
(claude-mem, auto-memory, etc.).
