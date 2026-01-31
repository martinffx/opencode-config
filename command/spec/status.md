---
description: Show feature progress via Beads (replaces /spec/progress)
agent: product
model: opencode/glm-4.7
temperature: 0.4
---

# Feature Status: $ARGUMENTS

Optional: specify feature name, or leave empty for all features

## Step 1: Check Beads Installation

@context verify Beads available.

Check Beads:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Query Beads for Tasks

@context retrieve task data from Beads.

**If feature specified:**
```bash
bd list --label $ARGUMENTS --json
```

**If no feature (show all):**
```bash
bd list --json
```

Parse results to extract:
- Task IDs, titles, status
- Epic associations
- Dependencies (blockers, blocked)
- Priority levels
- Labels

## Step 3: Calculate Progress Metrics

@product analyze task data for actionable insights.

### Task Status Breakdown

Count tasks by status:
- **Open**: Not started
- **In Progress**: Currently being worked on
- **Closed**: Completed

Calculate:
- Completion percentage: `closed / total * 100`
- Open task count
- In-progress task count

### Dependency Analysis

Identify:
- **Ready tasks**: Open tasks with no open blockers
- **Blocked tasks**: Open tasks with open blockers
- **Blocker tasks**: Tasks blocking other tasks

### Epic Status

If epics present:
- List epics with completion status
- Group tasks by epic
- Calculate per-epic completion

### Priority Distribution

Count tasks by priority:
- P0 (critical)
- P1 (high)
- P2 (medium)
- P3 (low)

## Step 4: Generate Progress Report

@product create data-driven status report.

### Feature Status Summary

**If single feature:**
```
Feature: $ARGUMENTS
Progress: {{completion_percentage}}% complete
Tasks: {{closed_count}}/{{total_count}} completed

Status Breakdown:
  - Open: {{open_count}}
  - In Progress: {{in_progress_count}}
  - Closed: {{closed_count}}

Ready to Work: {{ready_count}} tasks
Blocked: {{blocked_count}} tasks
```

**If all features:**
```
Overall Status
Active Features: {{feature_count}}
Total Tasks: {{total_count}}
Overall Progress: {{completion_percentage}}%

By Feature:
{{#each features}}
  - {{name}}: {{completion}}% ({{closed}}/{{total}})
{{/each}}
```

### Ready Tasks (Next Actions)

List ready tasks (no open blockers):
```
Ready Tasks:
{{#each ready_tasks}}
  - [{{id}}] {{title}} (P{{priority}})
{{/each}}
```

If no ready tasks:
```
No ready tasks. All work is blocked or complete.
```

### Blocked Tasks

List blocked tasks with their blockers:
```
Blocked Tasks:
{{#each blocked_tasks}}
  - [{{id}}] {{title}}
    Blocked by: {{#each blockers}}[{{id}}]{{/each}}
{{/each}}
```

### Recent Activity

Show recently closed tasks:
```
Recently Completed:
{{#each recent_closed limit=5}}
  - [{{id}}] {{title}} (closed {{time_ago}})
{{/each}}
```

## Step 5: Strategic Recommendations

@product provide actionable next steps.

### Immediate Actions

Based on current state:

**If ready tasks exist:**
```
Recommended: Start next task
  /spec/work $ARGUMENTS
  
Next task: [{{top_ready_task}}]
```

**If all tasks blocked:**
```
Recommended: Resolve blockers first
  
Top blocker: [{{top_blocker}}] (blocking {{blocked_count}} tasks)
  /spec/work <blocker-feature>
```

**If all tasks complete:**
```
Feature complete! Options:
  - Propose changes: /change/propose $ARGUMENTS <change>
  - Check product status: /product/status
```

### Timeline Projection

If tasks remain:
```
Estimated completion:
  - {{ready_count}} tasks ready ({{estimated_time}})
  - {{blocked_count}} tasks blocked (depends on blockers)
  
Critical path: {{critical_path_description}}
```

## ✅ Status Report Generated

Progress: {{completion_percentage}}% complete ({{closed}}/{{total}})
Ready tasks: {{ready_count}}
Blocked tasks: {{blocked_count}}

**Next commands:**

- **Start work**: `/spec/work $ARGUMENTS`
- **View task details**: `bd show <task-id>`
- **Check blockers**: `bd show <blocker-id>`
- **Complete change**: `/change/complete $ARGUMENTS <change>` (when all tasks done)
- **Overall progress**: `/product/status`
