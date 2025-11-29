---
description: Initialize project by gathering requirements and creating product documentation
agent: analyst
model: opencode/glm-4.6
temperature: 0.3
---

# Initialize Project: $ARGUMENTS

## Step 1: Gather Project Information

@analyst conduct structured requirements interview for $ARGUMENTS.

Gather:

1. Product definition (what are you building - one sentence)
2. Target users (who will use it - be specific)
3. Core features (3-5 main features)
4. Tech stack preferences (frontend, backend, database)

Structure requirements for documentation.

## Step 2: Detect Technology Stack (Agnostic)

@context analyze project to detect technology stack without assumptions.

Detection Strategy:

- **Rust**: Look for `Cargo.toml`
- **Node.js/TypeScript/React**: Look for `package.json`
- **Python**: Look for `pyproject.toml`, `requirements.txt`, `setup.py`
- **Go**: Look for `go.mod`
- **Java**: Look for `pom.xml`, `build.gradle`
- **C#**: Look for `.csproj`, `packages.config`
- **Ruby**: Look for `Gemfile`
- **PHP**: Look for `composer.json`
- **Other**: Analyze file patterns and directory structure

Stack Analysis:

- Primary programming language
- Web framework (if any)
- Database technology (if detectable)
- Testing framework (if detectable)
- Build system and package manager

@context7 validate stack capabilities and current best practices.

Validate detected stack against:
- Current framework versions and capabilities
- Latest ecosystem trends and maturity
- Current best practices for detected technologies
- Framework-specific architectural patterns

## Step 3: Create Project Structure

@scaffold create project directory structure and documentation files.

Create:

- `docs/product/` directory with product.md and roadmap.md
- `docs/spec/` directory for feature specifications
- `docs/standards/` directory with language-specific and generic standards
- Root CLAUDE.md file with project overview

Apply templates with project data from Step 1 and detected stack from Step 2 using template adaptation strategy.

## Step 4: Files Created

The following files are created using the gathered requirements and detected technology stack:

### 📄 docs/product/product.md

- Product definition and target users
- Core features list
- Success metrics

### 📄 docs/product/roadmap.md

- Next 3 features in priority order
- Implementation strategy
- Current status

### 📄 docs/standards/{language}/

- **architecture.md**: Language-specific layer patterns (adapted from templates)
- **coding.md**: Language-specific TDD patterns and conventions (adapted from templates)

### 📄 docs/standards/ (core)

- **coding.md**: TDD implementation patterns and coding principles
- **architecture.md**: Architecture patterns and design principles

### 📄 CLAUDE.md

- Project overview and available commands
- Quick start guide

## Step 5: Template-Based Standards Applied

Based on detected technology stack, standards are created using template adaptation strategy:

### Template Selection Strategy

1. **Exact Match**: If template exists for detected stack → Use directly
2. **Similar Match**: If template exists for same language, different framework → Adapt framework-specific parts
3. **Language Match**: If template exists for language but no stack match → Use language patterns, add stack-specific sections
4. **No Match**: Create generic standards based on language-agnostic principles

### Standards Applied

- **Architecture Patterns**: Layered architecture adapted for detected stack
- **Coding Standards**: Stub-driven TDD approach with language-specific conventions
- **Testing Strategy**: Unit and integration test patterns for detected stack
- **Database Integration**: Database patterns adapted for detected database technology
- **Development Workflow**: Build, test, and deployment patterns for detected stack

### Language-Agnostic Principles Preserved

- Layered architecture (Router → Service → Repository → Entity → Database)
- Stub-driven TDD workflow (Stub → Test → Implement → Refactor)
- Domain-driven design patterns
- Dependency injection principles
- Error handling patterns

## ✅ Project Initialized

Created documentation structure with:

- Product definition and roadmap
- Technology-agnostic stack detection
- Template-based standards with adaptation strategy
- Language-specific and generic technical standards
- TDD workflow and testing strategy
- AI-friendly command reference
- Multi-stack support with unified product documentation

**Next action**: `/spec/create authentication` to build your first feature

## Multi-Stack Support

If multiple technology stacks are detected:

- Separate standards directories created for each detected stack
- Cross-stack project structure with shared product documentation
- Stack-specific standards applied independently
- Unified `docs/product/` documentation with stack-specific implementation notes
- Generic standards in `docs/standards/` apply across all stacks
- Template adaptation applied per stack independently
