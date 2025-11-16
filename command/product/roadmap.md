---
description: Update product roadmap with next priorities (add 'update' to force refresh)
agent: product
model: openrouter/deepseek/deepseek-v3.1-terminus
temperature: 0.4
---

# Update Roadmap

## Step 1: Analyze Current State

@context gather current project state.

Retrieve:
- Current roadmap priorities from `docs/product/roadmap.md`
- Feature completion status from `docs/spec/*/status.md`
- Available features from `docs/product/product.md`

Load and analyze current state for strategic planning.

## Step 2: Calculate Progress Metrics

@product analyze progress and generate strategic recommendations.

Calculate:
- Feature completion percentages
- Development velocity metrics
- Timeline assessments
- Priority recommendations based on business value and dependencies

Provide data-driven strategic recommendations for next priorities.

## Step 3: Priority Interview

Based on current progress analysis, confirm your next priorities:

### Current Features Status
- [Feature status from product agent analysis]

### Available Features
- [Features from product.md not yet started]

### Recommended Next 3 Priorities
1. **[Product agent recommendation 1]** - [Reasoning]
2. **[Product agent recommendation 2]** - [Reasoning]
3. **[Product agent recommendation 3]** - [Reasoning]

**Confirm or adjust these priorities:** [Interactive confirmation needed]

## Step 4: Update Roadmap File

@scaffold update `docs/product/roadmap.md` with confirmed priorities.

Update roadmap file with:
- Confirmed next priorities
- Current status summary
- Updated timeline projections
- Risk assessments

## ✅ Roadmap Updated

### Summary:
- Progress analysis completed
- New priorities confirmed and documented
- Roadmap file updated with strategic rationale

### Next Actions:
1. Continue active feature: `/spec-implement [current-feature]`
2. Start next priority: `/spec-create [next-feature]`
3. Check overall progress: `/product-progress`
