---
description: Track overall product development progress (use 'verbose' for detailed analysis)
agent: product
model: openrouter/deepseek/deepseek-v3.1-terminus
temperature: 0.4
---

# Product Development Progress

## Step 1: Load Project State

@context gather comprehensive project data.

Retrieve:
- Current roadmap from `docs/product/roadmap.md`
- All feature specifications from `docs/spec/*/`
- Feature completion status from `docs/spec/*/status.md`

Load all project state data for analysis.

## Step 2: Analyze Progress & Calculate Metrics

@product perform comprehensive progress analysis using the context data.

Calculate:
- Feature completion percentages across all specs
- Development velocity metrics (tasks/day, features/week)
- Progress against current roadmap priorities
- Code quality metrics and test coverage
- Timeline projections and risk assessment

Generate comprehensive progress report with strategic insights.

## Step 3: Strategic Recommendations

Based on progress analysis, provide strategic recommendations:

### Current Status Summary
- Overall roadmap progress: [X]% complete
- Features in progress: [Count and status]
- Completed this period: [List]
- Blockers identified: [Any issues]

### Velocity & Quality Metrics
- Development velocity: [Tasks per day with AI assistance]
- Code quality indicators: [Test coverage, passing tests]
- AI efficiency multiplier: [Actual vs estimated time]

### Recommended Actions
#### Immediate (Today)
- [Specific next command to run]
- [Any urgent items to address]

#### This Week
- [Strategic priorities for the week]
- [Process improvements to consider]

### Roadmap Recommendations
- [Should priorities be updated?]
- [New features ready to start?]
- [Any timeline adjustments needed?]

## ✅ Progress Analysis Complete

**Next Action**: [Specific command recommendation]
**Alternative**: [If blocked or complete, what to do next]
