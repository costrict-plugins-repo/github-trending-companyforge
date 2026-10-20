# CompanyForge Plugin

Claude Code plugin for generating domain-specific AI organizations.

Describe a professional domain and CompanyForge builds a complete organizational structure: capabilities, workflows, quality controls, ceremony calibration, and cost management. The output is a working Claude Code project you can drop into any directory.

## Install

Enable `companyforge` in your Claude Code plugin settings.

## Usage

```
/forge "finance"            # Generate a financial analysis firm
/forge "consulting"         # Generate a consulting firm
/forge "real-estate"        # Generate a real estate brokerage
/forge "custom"             # Define a custom domain interactively
```

## What It Generates

A self-contained domain directory with:

- **Capability pool** — Builder, Reviewer, Scout (3-4 types, not 12 job titles)
- **Workflows** — Domain-specific work types with three-pass review for medium+ tasks
- **Quality gates** — Checklists, adversarial prompts, conflict resolution rules
- **Ceremony calibration** — Trivial tasks get a solo practitioner; complex tasks get the full team
- **Domain context engine** — What to look up and where, scaled by task tier
- **Cost controls** — Model selection by judgment required, budget alerts, spend tracking

## How It Works

1. **Research** the domain (types of work, failure modes, AI-specific risks)
2. **Architect** the organization (capability types, workflows, infrastructure)
3. **Present** for your approval before building
4. **Generate** the files
5. **Adversarial review** of its own output
6. **Calibrate** — AI resolves defaults from published standards, then asks you about the rest
7. **Commit**

## Design Principles

- AI-native organizations, not human org charts
- Three capability types (build, review, research), not twelve job titles
- Ceremony scales with stakes — same types, more dispatches
- Natural language routing — you talk, the system figures out the work type
- Deterministic tools before agent opinions
- Infrastructure is conditional — only what the domain needs

## Reference Implementations

The [CompanyForge](https://github.com/aboynamedstacey/CompanyForge) repository includes complete reference domains (software development, R&D lab, competitive BBQ) that demonstrate what the forge generates and how practitioners refine it through use.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI

Optional but useful:
- claude-mem (cross-session memory)
- firecrawl (web research for domain context)

## License

Apache 2.0 — see [LICENSE](LICENSE) for details.
