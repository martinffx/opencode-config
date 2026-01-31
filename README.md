# Atelier - Spec-Driven Development Framework

A powerful development framework that accelerates feature delivery through
structured specifications and dependency-driven task execution, executed
by the **opencode** AI agent.

## 🚀 Overview

**Atelier** is your development workshop - a methodology that transforms AI from a
tool requiring constant prompting into a team member that understands YOUR
standards, business context, and implementation approach. The **opencode** agent
is the specialized AI that executes Atelier workflows.

## 🎯 Core Philosophy

- **Lightweight documentation over heavyweight planning** - Replace PRDs with
  minimal business context plus unified implementation specs
- **Dependency-driven over sprint-driven** - Order tasks by technical
  requirements via Beads (database → repository → service → API → UI)
- **Your standards, enforced** - Atelier adapts to your patterns and architecture
- **Prevention over debugging** - Spot issues in design, not production
- **Progress over perfection** - Ship iteratively with enforced architectural patterns
- **Measurement over assumptions** - Profile before optimizing, test before shipping

## 🎨 Customization

Atelier doesn't dictate how you build - it learns YOUR way. Define your standards once, 
and the opencode agent enforces them across all features.

### What You Can Customize

**Architecture Patterns** (`docs/templates/standards/`)
- Layer structure (e.g., Router → Service → Repository → Entity)
- Dependency injection approach
- Error handling patterns
- Validation strategies
- Testing methodology (TDD, contract testing, integration patterns)

**Tech Stack Conventions**
- Naming conventions (files, functions, classes)
- Code organization (folder structure, module boundaries)
- Framework-specific patterns (Next.js, Express, FastAPI, etc.)
- Database patterns (ORMs, query builders, migrations)
- API design (REST, GraphQL, RPC styles)

**Development Workflow**
- Git commit message format
- Branch naming conventions
- PR/review requirements
- CI/CD expectations
- Documentation requirements

### How It Works

1. **Define your standards** in `docs/templates/standards/`
```
   docs/templates/standards/
   ├── architecture.md      # Your layer structure
   ├── typescript.md        # TS-specific patterns
   ├── testing.md          # TDD approach
   ├── api-design.md       # REST/GraphQL patterns
   └── database.md         # Schema, migrations, queries
```

2. **Opencode reads and enforces** - Every spec, design, and implementation follows YOUR patterns

3. **Consistency guaranteed** - Same patterns across all features, all developers, all AI-generated code

### Example: Custom Architecture
```markdown
# docs/templates/standards/architecture.md

## Layer Structure
1. **Router** - Route definitions only, delegate to services
2. **Service** - Business logic, orchestration, transactions
3. **Repository** - Data access, query building
4. **Entity** - Domain models with validation
5. **Database** - Schema definitions

## Rules
- Services never import routers
- Repositories only query, never contain business logic
- Entities validate themselves
- All async operations use explicit error handling
```

Once defined, opencode generates specs that follow this structure:
```typescript
// Generated code follows YOUR architecture
export class UserService {
  constructor(private userRepo: UserRepository) {}
  
  async createUser(data: CreateUserDTO): Promise<User> {
    // YOUR service patterns
  }
}
```

### Migration & Evolution

Standards evolve. When you update your patterns:
- `/spec/sync` updates existing specs to match new standards
- `/code/validate` checks conformance
- Brownfield changes via `/change/propose` respect current standards

### Changing Methodologies

Atelier's defaults (stub-driven TDD, layered architecture, DDD entities) are just that - defaults. 
To use different approaches:

| Want to change... | Update these files |
|-------------------|-------------------|
| **Testing methodology** (no stubs, test-after, integration-first) | `agent/coder.md` - modify the implementation pattern |
| **Architecture layers** (flat, MVC, no repositories) | `docs/templates/standards/{lang}/architecture.md` |
| **Entity patterns** (Active Record, no entities, functional) | `docs/templates/standards/{lang}/architecture.md` |
| **Coding conventions** (naming, structure, imports) | `docs/templates/standards/{lang}/coding.md` |
| **Agent behaviors** (analyst interview style, architect patterns) | `agent/*.md` files |
| **Spec templates** (different sections, lighter/heavier) | `docs/templates/specs/spec.md` |

**Example**: To remove stub-driven TDD, edit `agent/coder.md`:
- Remove the "Stub Phase" requirement
- Change "Stub→Test→Fix" to your preferred workflow (e.g., "Implement→Test→Refactor")
- Update the "Boundaries" section to match your methodology

The opencode agent reads these files at runtime - your changes apply immediately.

## 📦 Dependencies

- **Beads** - Git-backed dependency-aware issue tracker for AI agents
  - Install: `npm install -g @beads/bd`
  - Initialize: `bd init`
  - Docs: https://github.com/steveyegge/beads

## 🛠️ Available Commands

Atelier provides a comprehensive workflow through specialized commands,
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

Atelier leverages a team of specialized AI agents, coordinated by the opencode agent:

- **📊 Analyst** - Requirements gathering and structured interviews
- **🏗️ Architect** - Technical design and architecture enforcement
- **💻 Coder** - Implementation with strict TDD methodology
- **📁 Scaffold** - Project structure and boilerplate generation
- **📚 Context** - Documentation retrieval and knowledge management
- **📈 Product** - Progress tracking and strategic recommendations
- **🎯 Strategist** - Alternative approaches and assumption challenging

## 📁 Project Structure
```
atelier/
├── agent/              # Specialized agent configurations for opencode
│   ├── analyst.md      # Requirements gathering (code-aware)
│   ├── architect.md    # Technical design (contextual layers)
│   ├── coder.md        # Implementation (stub→test→fix)
│   ├── context.md
│   ├── product.md
│   ├── scaffold.md
│   └── strategist.md
├── command/            # Atelier workflow command definitions
│   ├── product/        # Product management commands
│   │   ├── init.md
│   │   ├── status.md
│   │   └── update.md
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
│       ├── changes/    # Change proposal templates
│       │   ├── proposal.md
│       │   └── delta.md
│       ├── specs/      # Unified spec template
│       │   └── spec.md # Requirements + Technical Design
│       ├── product/
│       │   ├── product.md
│       │   └── roadmap.md
│       └── standards/  # YOUR architecture & coding patterns
│           ├── architecture.md
│           ├── testing.md
│           ├── [language].md
│           └── [framework].md
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
**Your workshop. Your standards. Your way.**
