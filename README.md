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
  minimal business context plus detailed implementation specs
- **Dependency-driven over sprint-driven** - Order tasks by technical
  requirements (database → repository → service → API → UI)
- **AI-assisted implementation** - Structured specs enable AI agents to make
  informed technical decisions
- **Prevention over debugging** - Spot issues in design, not production
- **Progress over perfection** - Ship iteratively with enforced architectural patterns
- **Measurement over assumptions** - Profile before optimizing, test before shipping

## 🛠️ Available Commands

SDD provides a comprehensive workflow through 8 specialized commands,
executed by the opencode agent:

### Product Management

- `/product-init` - Initialize project with requirements and documentation
- `/product-roadmap` - Create and manage feature roadmap
- `/product-progress` - Track project status and completion metrics

### Specification Workflow

- `/spec-create` - Create detailed feature specifications
- `/spec-design` - Generate technical designs following architectural patterns
- `/spec-plan` - Plan implementation with dependency-aware task ordering
- `/spec-implement` - Execute implementation with TDD approach
- `/spec-progress` - Track feature implementation status

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
│   ├── analyst.md
│   ├── architect.md
│   ├── coder.md
│   ├── context.md
│   ├── product.md
│   ├── scaffold.md
│   └── strategist.md
├── command/            # SDD workflow command definitions
│   ├── product-init.md
│   ├── product-roadmap.md
│   ├── product-progress.md
│   ├── spec-create.md
│   ├── spec-design.md
│   ├── spec-plan.md
│   ├── spec-implement.md
│   └── spec-progress.md
├── AGENTS.md          # Core opencode agent identity and guidelines
├── opencode.json      # opencode agent configuration and MCP settings
└── README.md          # This file
```

## ⚙️ Configuration

The `opencode.json` file configures the opencode agent:

- **Theme**: Visual theme (Catppuccin)
- **Model**: Default AI model (zen/GLM-4.6)
- **MCP Integration**: File system, thinking, memory, fetch, browser automation
- **Context7**: API documentation integration

## 🔧 MCP Integration

The opencode agent integrates with multiple Model Context Protocol (MCP) servers:

- **filesystem** - Local file system access
- **sequential-thinking** - Advanced reasoning capabilities
- **memory** - Knowledge graph and entity management
- **fetch** - Web content retrieval
- **playwright** - Browser automation
- **context7** - Real-time documentation access

---

**Built for speed, quality, and consistency in AI-assisted development.**
