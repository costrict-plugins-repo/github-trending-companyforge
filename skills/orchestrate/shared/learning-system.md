# Learning System — Cross-Project Knowledge Accumulation

Shared across all domains. Domain-specific orchestrators inherit this.

---

## Two Categories of Knowledge

**Project-specific:** Tagged with project name. Context that only matters for this project.
**Generalizable expertise:** Tagged with domain concepts. Knowledge that makes the
organization smarter across all future projects in this domain.

## When to Capture

After every Medium+ workflow completion, dispatch Knowledge Extraction capability:
1. What worked — capture for reuse
2. What failed — propose checklist and rule updates
3. What should change — propose CLAUDE.md updates (human approves)
4. Bias incidents — track where adversarial review caught something the drafter missed

## Cross-Domain Learning

Some lessons transfer across domains:
- Process improvements (ceremony calibration adjustments)
- Quality gate effectiveness (which checklists catch real issues)
- Cost patterns (which phases cost more than expected)

Tag these with "cross-domain" for retrieval in any domain context.

## Retrospective

After large engagements, run a structured retrospective via Knowledge Extraction.
Human reviews and approves any changes to rules, checklists, or CLAUDE.md.

## Known Gap: The Backward Loop

Knowledge flows forward reliably: completed workflows → memory observations →
tagged for future retrieval. But knowledge flowing backward — from accumulated
observations into config file updates (ceremony-signals.md, domain-context-engine.md,
quality-checklists.md) — requires human intervention. There is no automated
mechanism for "10 studies have shown this default is wrong; update it."

This matters because:
- Generated defaults marked `[GENERATED DEFAULT — refine through use]` stay
  as-is until a human explicitly edits the config file
- Systematic cost estimation errors in ceremony-signals.md persist even when
  observability logs show the pattern
- Domain-context-engine staleness thresholds may drift from actual category
  velocity without a recalibration trigger

**Manual trigger:** After every 5th Medium+ workflow in a domain, the
orchestrator should surface a prompt:

> "This domain has completed {N} Medium+ workflows. Review memory for
> patterns that suggest config defaults should change. Check:
> - Ceremony cost estimates vs. actual costs (observability logs)
> - Source staleness thresholds vs. actual source freshness encountered
> - Panel/artifact size minimums vs. actual quality outcomes
> - Confidence modifiers vs. actual accuracy (if ground truth available)"

The human decides whether to update configs. The system surfaces the prompt.
