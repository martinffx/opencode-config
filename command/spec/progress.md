---
description: Check progress on specific feature or all active features
agent: product
model: opencode/glm-4.6
temperature: 0.4
---

# Feature Progress Report

## Step 1: Load Feature Data

@context retrieve feature progress information.

**If specific feature provided:**

- Load `docs/spec/$ARGUMENTS/tasks.md` (contains embedded progress tracking)
- Load `docs/spec/$ARGUMENTS/design.md` for context

**If no feature specified:**

- Load all `docs/spec/*/tasks.md` files
- Provide comprehensive project overview

Extract current progress data for analysis.

## Step 2: Analyze Progress

@product calculate metrics and generate comprehensive progress report.

**Progress Analysis:**

- Task completion percentages by phase
- Time spent vs original estimates
- Current development phase and status
- Identify any blockers or bottlenecks
- Code quality indicators (test coverage, passing tests)
- AI efficiency multiplier (actual vs estimated time)

**Strategic Assessment:**

- Feature readiness for next phase
- Resource allocation recommendations
- Timeline projections and risk factors
- Priority recommendations for optimal flow

Generate data-driven progress analysis with actionable insights.

## ✅ Progress Report Generated

### Feature Status Summary

- **Overall completion**: [X]% complete
- **Current phase**: [Phase name and details]
- **Tasks completed**: [X of Y] ([percentage]%)
- **Time analysis**: [Elapsed vs estimated with AI multiplier]
- **Quality metrics**: [Test coverage, code quality indicators]

### Development Flow Analysis

- **Completed phases**: [List with dates]
- **Active work**: [Current tasks and owners]
- **Ready to start**: [Next available tasks]
- **Blocked items**: [Any impediments identified]

### Strategic Recommendations

#### Immediate Actions (Today)

- **Next step**: [Specific command recommendation]
- **Priority focus**: [What to work on first]
- **Alternative paths**: [If blocked, what else to do]

#### Strategic Planning

- **Timeline projection**: [Expected completion date]
- **Resource needs**: [Any additional requirements]
- **Risk mitigation**: [Potential issues to address]

### Next Commands

- **Continue implementation**: `/spec/implement $ARGUMENTS`
- **Review completed work**: `/code/review $ARGUMENTS`
- **Update roadmap priorities**: `/roadmap-update`
- **Overall project status**: `/project/progress`
