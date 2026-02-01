---
description: Analyze product-wide gaps between product definition, specs, beads tracking, and implementation
agent: architect
subtask: true
---

# Product Sync: Analyze Implementation Gaps

Perform product-wide analysis comparing product definition against actual implementation. Identify gaps between what should exist, what's planned, what's tracked, and what actually exists.

## Step 1: Gather Project State

@clerk load all project artifacts.

Collect:
- `docs/product/product.md` - Product definition
- All specs: `docs/spec/**/spec.md`
- Beads status: `bd list --format=json`
- Implementation files (source code structure)
- Test files and coverage

## Step 2: Analyze Four-Way Alignment

@architect compare product definition against reality.

Create comparison matrix:

| Feature | In Product.md? | Has Spec? | Beads Status | Code Exists? | Gap |
|---------|---------------|-----------|--------------|--------------|-----|
| Feature A | Yes | Yes | 3/5 complete | Partial | Implementation behind beads |
| Feature B | Yes | No | N/A | Yes | Code exists, no spec |
| Feature C | Yes | Yes | 0% | None | Spec but no work started |
| Feature D | No | Yes | Complete | Complete | Spec not in product definition |

## Step 3: Identify Gap Categories

@architect categorize findings.

**Type A: Missing Specifications**
- Features in product.md without specs
- Suggests creating specs

**Type B: Orphaned Code**
- Code exists but no spec documents it
- Suggests retroactive spec creation

**Type C: Implementation Gaps**
- Spec exists but beads show incomplete work
- Suggests continuing implementation

**Type D: Product Doc Out of Sync**
- Specs/code exist but not mentioned in product.md
- Suggests updating product definition

**Type E: Beads/Code Mismatch**
- Beads say complete but code/tests missing
- Suggests review needed

## Step 4: Generate Gap Report

@clerk create structured summary.

### Product Sync Report - $(date +%Y-%m-%d)

#### Executive Summary
- Total features in product definition: [N]
- Features with specs: [N] ([N]% coverage)
- Features with implementation: [N]
- Overall alignment score: [N]%

#### Detailed Gaps

**🔴 Critical (Block Release)**
- Feature X: Spec says complete, tests failing
- Feature Y: Core feature, no implementation started

**🟡 Warning (Should Address)**
- Feature A: Code exists, no spec (maintenance risk)
- Feature B: Beads out of date with actual progress

**🟢 Info (Nice to Have)**
- Feature C: Product.md missing completed feature
- Feature D: Spec could use more detail

#### Recommended Actions

**Immediate:**
1. Create spec for orphaned code: `/spec/init auth-module`
2. Update beads for completed work: `/spec/work user-profile` → mark done
3. Update product.md to include completed features

**This Week:**
1. Resume stalled implementation: `/spec/work payment-gateway`
2. Sync out-of-date spec: `/spec/sync notification-service`

#### Commands to Run

```bash
# Create missing specs
/spec/init feature-name

# Sync existing code to spec
/spec/sync feature-name

# Continue implementation
/spec/work feature-name

# Update product definition
# (Manual edit of docs/product/product.md)
```

## Step 5: Save Report (Optional)

@clerk save analysis for reference.

If user requests:
```bash
mkdir -p docs/product/sync-reports
cat > docs/product/sync-reports/$(date +%Y-%m-%d).md << 'EOF'
[Full report content]
EOF
```

## ✅ Sync Analysis Complete

**Summary:** [N] gaps identified across [N] features
**Next step:** Choose an action above or run `/product/sync save` to persist this report

---

### Usage

- `/product/sync` - Show current gap analysis
- `/product/sync save` - Save report to docs/product/sync-reports/