# Claude-ism Suppression List

A living artifact of constructions, phrases, and patterns that mark text as
AI-generated. Any domain where the Builder produces text that should sound
like it came from a specific person (or just not sound like Claude) should
reference this list.

This list grows as patterns are identified in actual output. Version it
alongside the skill.

## Banned Constructions

### Metaphorical clichés
- "does real work" / "doing work" as a metaphor for rhetorical effectiveness
- "loadbearing" as metaphor outside engineering context
- "the thing that'll actually matter" as topic-pivot construction

### Intensifiers and hedges
- "genuinely" as intensifier (Claude's favorite)
- "that's not nothing" as understatement
- "I say that without [qualifier]" as preemptive defense
- "to be fair" at the start of a concession

### Structural tics
- "the X isn't Y — it's Z" inversion pattern (Claude uses this constantly)
- "that's a [noun] problem, not a [noun] problem" reframing
- em-dash parentheticals more than once per response
- triple-structure lists ("it's X, Y, and Z") more than once per response
- "here's the thing" or "here's what matters" as topic introduction

### Over-articulation markers
- Clean cause-and-effect reasoning where a real person would be vague
- Self-aware meta-commentary on one's own decision process
- Framework language from someone who wouldn't use frameworks
- Every concern expressed with equal clarity and precision

## How to Apply

**In persona/character generation:** Include the suppression list in the
Builder's dispatch prompt. Instruct: "Do NOT use any construction from
the suppression list. If you catch yourself writing one, rephrase."

**In review:** The Reviewer should check output against this list. Any
match is a flag — not necessarily a failure, but evidence of Claude voice
leaking through.

**Maintenance:** After every study or generation run, scan output for new
Claude-isms. Add them to this list. The list is never complete — Claude
develops new tics as it's prompted in new ways.

## When NOT to Apply

- Analysis reports and recommendations (Claude voice is fine here — the
  output is the AI's analysis, not a simulation of someone else)
- Technical documentation
- Any output where the AI is speaking as itself, not as a character
