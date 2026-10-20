# Error Resilience — Timeouts, Fallbacks, and Recovery

Shared across all domains. Domain-specific orchestrators inherit this.

---

## Failure Types and Responses

### 1. Subagent Timeout / No Response

If a subagent dispatch doesn't return within a reasonable time:

**Detection:** The Agent tool returns an error or the subagent output is empty/malformed.

**Response:**
1. Log the failure in the dispatch log (outcome: TIMEOUT)
2. Retry ONCE with the same prompt and model
3. If second attempt fails, try with a different model tier:
   - Haiku failed → retry with Sonnet
   - Sonnet failed → retry with Opus
   - Opus failed → escalate to human
4. If all retries fail:
   > "**Agent failure:** {capability} did not respond after 2 attempts.
   > Phase: {phase}, Workflow: {workflow}
   > Options:
   > 1. Retry with more context
   > 2. Skip this phase (if optional)
   > 3. Abort workflow"

### 2. Garbage Output / Format Mismatch

If a review capability returns output that doesn't contain the expected structured
format (e.g., missing VERDICT, missing checklist responses):

**Detection:** The orchestrator checks for the presence of the required format
elements defined in the domain's quality checklists.

**Response:**
1. Log the malformed output (outcome: ERROR)
2. Re-dispatch with an explicit format reminder prepended to the prompt:
   > "IMPORTANT: Your response MUST follow the structured format defined in
   > the checklist. Previous attempt did not follow the required format."
3. If second attempt also malformed, treat as FAIL with the note:
   > "Review returned malformed output after retry. Treating as FAIL — manual review needed."
   Route to human.

### 3. Partial Parallel Failure

When running parallel reviews and some succeed but others fail:

**Response:**
1. Collect all successful verdicts
2. For failed dispatches, apply the timeout/retry logic above
3. If a review can't be recovered:
   - If it's the domain's highest-priority review (e.g., security in software,
     privilege in law, physics in R&D): BLOCK. Escalate to human.
   - If it's a lower-priority review: proceed with available verdicts but flag:
     > "**Incomplete review:** {failed_capability} could not complete. Proceeding with
     > {N}/{total} reviews. The missing review should be performed manually or in the next session."

### 4. Context Window Overflow

For Large tasks with many phases, the orchestrator's context may fill up.

**Prevention:**
- Keep dispatch prompts focused (inject only what the capability needs, not full history)
- Don't accumulate raw subagent outputs — summarize and discard
- Use persistent state tracking (BEADS or equivalent) for state that needs to survive context compression

**Detection:** Claude's automatic context compression fires, or the orchestrator
notices it's lost track of earlier phases.

**Response:**
1. Save current state immediately (BEADS if available, otherwise document in session)
2. Log all completed phases and their outcomes
3. Tell the human:
   > "**Context limit approaching.** Saving state for session continuity.
   > Completed: {list of completed phases}
   > Remaining: {list of remaining phases}
   > You can continue in a new session — the workflow will resume from {next_phase}."

### 5. External Service Unavailable

If an external service (cross-model review, web search, domain-specific tool) is unavailable:

**Response:**
1. Log the failure
2. Proceed WITHOUT the service but flag it:
   > "**{service} unavailable** — did not respond.
   > Proceeding without. Consider running this check manually before finalizing."
3. Add a note to the deliverable:
   > "Note: {service} was skipped due to service unavailability."

Do NOT block the workflow for external service failure unless the domain's
conflict rules explicitly require it.

### 6. State Tracking Unavailable

If the persistent state system (BEADS or equivalent) is unavailable:

**Response:**
1. Log the failure
2. Continue without it — use in-session task tracking instead
3. Warn the human:
   > "**State tracking unavailable.** Using in-session tracking. Cross-session
   > continuity will be limited."

---

## Workflow Abort

The human can abort a workflow at any time by saying "abort" or "cancel" at any gate.

**Abort procedure:**
1. Log the abort with current state
2. If any work has been committed/delivered, preserve it (do NOT revert)
3. Save state for potential resumption (if state tracking available)
4. Persist the partial log to the memory layer
5. Report:
   > "**Workflow aborted.**
   > Completed phases: {list}
   > Committed work: {list, if any}
   > Cost incurred: ${total_cost}
   > State saved for potential resumption."

---

## Workflow Resume

When starting a session and state tracking shows an incomplete workflow:

1. Read the saved state to determine where the workflow stopped
2. Query the memory layer for the workflow log
3. Present to the human:
   > "**Incomplete workflow found:** {description}
   > Last completed phase: {phase}
   > Next phase: {phase}
   > Resume / Abort / Start fresh?"
4. If Resume: pick up from the next incomplete phase
5. If Start fresh: archive the old state and begin again
