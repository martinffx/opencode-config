# SDD - Spec-Driven Development Framework

A powerful development framework that accelerates feature delivery through
structured specifications and dependency-driven task execution, executed
by the **opencode** AI agent.

## 🚀 Overview

**SDD (Spec-Driven Development)** is a methodology that transforms AI from a
tool requiring constant prompting into a team member that understands your
standards, business context, and implementation approach. The **opencode** agent
is the specialized AI that executes SDD workflows.

## 🎯 Core Philosophy

- **Lightweight documentation over heavyweight planning** - Replace PRDs with
  minimal business context plus unified implementation specs
- **Dependency-driven over sprint-driven** - Order tasks by technical
  requirements via Beads (database → repository → service → API → UI)
- **AI-assisted implementation** - Structured specs enable AI agents to make
  informed technical decisions
- **Prevention over debugging** - Spot issues in design, not production
- **Progress over perfection** - Ship iteratively with enforced architectural patterns
- **Measurement over assumptions** - Profile before optimizing, test before shipping

## 📦 Dependencies

- **Beads** - Git-backed dependency-aware issue tracker for AI agents
  - Install: `npm install -g @beads/bd`
  - Initialize: `bd init`
  - Docs: https://github.com/steveyegge/beads

## 🛠️ Available Commands

SDD provides a comprehensive workflow through specialized commands,
executed by the opencode agent:

### Product Management

- `/product/init` - Initialize project with requirements and documentation
- `/product/status` - Track project status and completion metrics
- `/product/update` - Update project requirements and documentation

### Specification Workflow

- `/spec/init` - Create feature specification (greenfield or from existing code)
- `/spec/work` - Implement next ready task using layer boundary testing
- `/spec/sync` - Update spec from code (retroactive sync)
- `/spec/status` - Track feature progress via Beads

### Change Workflow

- `/change/propose` - Propose changes to existing features (brownfield)
- `/change/complete` - Merge delta into spec and close epic

### Code Management

- `/code/commit` - Commit changes with proper formatting
- `/code/review` - Conduct comprehensive code reviews
- `/code/validate` - Validate code quality and standards

### Advanced Thinking & Analysis

- `/zen/challenge` - Critical thinking to question assumptions and validate approaches
- `/zen/debug` - Systematic debugging with bisect methodology
- `/zen/thinkdeep` - Extended reasoning for deep analysis and complex decisions

## 🤖 Specialized Agents

SDD leverages a team of specialized AI agents, coordinated by the opencode agent:

- **📊 Analyst** - Requirements gathering and structured interviews
- **🏗️ Architect** - Technical design and architecture enforcement
- **💻 Coder** - Implementation with strict TDD methodology
- **📁 Scaffold** - Project structure and boilerplate generation
- **📚 Context** - Documentation retrieval and knowledge management
- **📈 Product** - Progress tracking and strategic recommendations
- **🎯 Strategist** - Alternative approaches and assumption challenging

## 📁 Project Structure

```
sdd-config/
├── agent/              # Specialized agent configurations for opencode
│   ├── analyst.md      # Requirements gathering (code-aware)
│   ├── architect.md    # Technical design (contextual layers)
│   ├── coder.md        # Implementation (stub→test→fix)
│   ├── context.md
│   ├── product.md
│   ├── scaffold.md
│   └── strategist.md
├── command/            # SDD workflow command definitions
│   ├── product/        # Product management commands
│   │   ├── init.md
│   │   ├── status.md
│   │   └── update.md   # Includes v1→v2 migration
│   ├── spec/           # Specification commands
│   │   ├── init.md     # Greenfield + code detection
│   │   ├── work.md     # Implementation via Beads
│   │   ├── sync.md     # Retroactive spec update
│   │   └── status.md   # Progress via Beads
│   ├── change/         # Change workflow commands
│   │   ├── propose.md  # Brownfield change proposals
│   │   └── complete.md # Merge delta, close epic
│   ├── code/           # Code management commands
│   │   ├── commit.md
│   │   ├── review.md
│   │   └── validate.md
│   └── zen/            # Advanced thinking and analysis commands
│       ├── challenge.md
│       ├── debug.md
│       └── thinkdeep.md
├── docs/
│   └── templates/
│       ├── changes/    # Change proposal templates (NEW)
│       │   ├── proposal.md
│       │   └── delta.md
│       ├── specs/      # Unified spec template
│       │   └── spec.md # Requirements + Technical Design
│       ├── product/
│       │   ├── product.md
│       │   └── roadmap.md
│       └── standards/  # Tech-specific architecture & coding
├── AGENTS.md          # Core opencode agent identity and guidelines
├── opencode.json      # opencode agent configuration and MCP settings
└── README.md          # This file
```

## ⚙️ Configuration

The `opencode.json` file configures the opencode agent:

- **Theme**: Visual theme (Catppuccin)
- **Model**: Default AI model (zen/GLM-4.6)
- **MCP Integration**: File system, sequential-thinking, memory, fetch, browser automation
- **Context7**: API documentation integration

## 🔧 MCP Integration

The opencode agent integrates with multiple Model Context Protocol (MCP) servers:

- **filesystem** - Local file system access
- **sequential-thinking** - Advanced reasoning and systematic analysis
- **memory** - Knowledge graph and entity management
- **fetch** - Web content retrieval
- **playwright** - Browser automation
- **context7** - Real-time documentation access

---

**Built for speed, quality, and consistency in AI-assisted development.**
