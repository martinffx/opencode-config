---
description: Initialize project by gathering requirements and creating product documentation
agent: oracle
model: opencode/glm-4.7
temperature: 0.3
tools: Read, Glob, Grep, Write, Edit, Bash
---

# Initialize Project: $ARGUMENTS

## Step 1: Gather Project Information

@oracle conduct project interview.

**What is this project about?**
Project name and purpose:
[Wait for response]

**Target users:**
Who will use this product?
[Wait for response]

**Core value propositions:**
What makes this unique?
[Wait for response]

## Step 2: Create Project Structure

@clerk create initial project directories.

Create directories:
```bash
mkdir -p docs/product
mkdir -p docs/spec
mkdir -p docs/changes
mkdir -p docs/templates/specs
mkdir -p docs/templates/changes
mkdir -p docs/templates/product
mkdir -p docs/templates/standards
mkdir -p docs/templates/standards/{language}
```

## Step 3: Generate Product Documentation

@clerk create initial product files from templates.

Create `docs/product/product.md`:
- Product name
- Mission statement
- Core features
- Target users
- Value propositions

Create `docs/product/roadmap.md`:
- Initial roadmap items
- Priorities
- Timeline

Templates applied from: `docs/templates/product/`

## Step 4: Initialize Technology Standards

@architect create initial coding standards for the chosen technology stack.

**What is the primary technology stack?**
- Language options:
  - TypeScript (Node.js)
  - Python
  - Rust
  - React (frontend)
  - [Other]

[Wait for response]

Write `docs/templates/standards/{language}/architecture.md`:
- Layered architecture patterns
- Component responsibilities
- Data flow patterns

Write `docs/templates/standards/{language}/coding.md`:
- Code style conventions
- Testing patterns
- Error handling

## Step 5: Initialize Beads

@clerk ensure Beads is initialized.

```bash
bd --version
```
If fails:
```bash
npm install -g @beads/bd
bd init
```

## ✅ Project Initialized

Created:
- `docs/product/product.md` - Product vision and objectives
- `docs/product/roadmap.md` - Roadmap with initial features
- `docs/templates/standards/{language}/` - Technical standards
- `.beads/` - Task tracking initialized

**Next steps:**

1. Define first feature: `/spec/init <feature-name>`
2. Gather product backlog items
3. Start implementing features