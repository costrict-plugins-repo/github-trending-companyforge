# Cost Control — Budgets, Alerts, and Caps

Shared across all domains. Domain-specific orchestrators inherit this.

---

## Budget Parameter

Every workflow invocation accepts an optional budget:

```
/{domain} {request} --budget $N
```

If no budget is specified, the orchestrator uses the domain's tier defaults
(defined in each domain's `ceremony-signals.md`).

The budget is a SOFT cap — the orchestrator warns at 80% and pauses at 100%,
but the human can override and continue.

## Cost Tracking

The orchestrator estimates cost for each dispatch based on:
- Model tier (Haiku ~$0.001/1K tokens, Sonnet ~$0.015/1K tokens, Opus ~$0.075/1K tokens)
- Estimated prompt size (from context being injected)
- Estimated response size (based on capability type)

These are rough estimates, not exact billing. They're directionally correct
for budget enforcement.

### Running Total

After each dispatch, update the running total:
```
cost_so_far += dispatch_estimated_cost
```

### Budget Alerts

At three thresholds:

**50% — Info:**
> "Budget update: ${cost_so_far} of ${budget} used ({percent}%). {remaining_phases} phases remaining."

**80% — Warning:**
> "**Budget warning:** ${cost_so_far} of ${budget} used ({percent}%). Remaining phases may exceed budget. Options:
> 1. Continue (may exceed budget)
> 2. Reduce ceremony (skip optional phases)
> 3. Abort workflow"

**100% — Pause:**
> "**Budget reached:** ${cost_so_far} of ${budget}. Workflow paused.
> Remaining work: {list of remaining phases}
> Estimated cost to complete: ${estimate}
> Options:
> 1. Increase budget to ${suggested_new_budget}
> 2. Skip remaining optional phases and proceed to delivery
> 3. Abort workflow"

The human must respond before the workflow continues.

## Review Loop Cost Cap

Review loops (fix → re-review) are capped:
- **Per-review cap:** 3 iterations, then escalate to human
- **Cost cap:** If a review loop has consumed >30% of the total budget, escalate:
  > "Review loop for {capability} has consumed ${loop_cost} ({percent}% of budget) over {N} iterations. The {capability} keeps finding issues. Options:
  > 1. Continue iterating
  > 2. Accept with known issues (list them)
  > 3. Escalate to a different approach"

## Cross-Model Review Cost Optimization

Cross-model reviews (Gemini/GPT via external-tools) are expensive dispatches.
Cost optimization rules:
- Skip cross-model review if budget is <$5 (not worth the cost)
- Near budget limit, run cross-model for the domain's highest-priority review only
- For Large tasks, always run all cross-model reviews (the cost of missing a blind spot exceeds the review cost)

## Post-Workflow Cost Report

At workflow end, report:
```
COST REPORT:
  Budget: ${budget}
  Actual: ${total_cost}
  Variance: {under/over by ${amount} ({percent}%)}

  Breakdown:
    {Phase 1}:  ${cost} ({percent}%)
    {Phase 2}:  ${cost} ({percent}%)
    Reviews:    ${cost} ({percent}%)  [N iterations]
    ...

  Compared to tier estimate: {within range | X% over | X% under}
```

This feeds into the learning system for calibrating future estimates.
