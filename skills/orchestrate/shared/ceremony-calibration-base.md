# Ceremony Calibration — Base Logic

Shared across all domains. Each domain provides its own SIZE SIGNALS and TIER
DEFAULTS. This file defines the universal calibration process.

---

## Process

### Step 1: Estimate Size

Score the request against domain-specific signals (defined in each domain's
`ceremony-signals.md`). Take the highest tier matching 2+ signals.

Four tiers: TRIVIAL, SMALL, MEDIUM, LARGE.

### Step 2: Present Estimate

Tell the human:
> "I'm sizing this as **{TIER}**:
> - Capabilities: {list}
> - Human gates: {list or 'none'}
> - Estimated cost: {range}
> Override? (trivial / small / medium / large / proceed)"

### Step 3: Compose Capabilities

Each domain defines which capabilities activate at each tier in its
`ceremony-signals.md`. Universal rules:

- TRIVIAL: Single capability, tool verification only, no review, no human gates.
- SMALL: Primary capability + one combined review.
- MEDIUM: Full pipeline with parallel specialist reviews and human gates.
- LARGE: Full pipeline + mandatory research + additional human checkpoints.

### Step 4: Set Budget

Use domain-specific tier defaults or human-specified `--budget $N`.
Apply cost-control.md rules throughout.

### Step 5: Domain Research Trigger

If the task involves domain knowledge beyond routine work, set
`deep_research = true`. Each domain defines its own triggers.
