---
description: Update docs/standards and docs/product with latest methodology (tech-agnostic)
agent: scaffold
model: anthropic/claude-sonnet-4-5
temperature: 0.3
---

# Update Standards & Product Documentation

## Step 1: Explore Repository Structure

@context analyze repository structure to understand the project.

Explore:

- Root configuration files (Cargo.toml, package.json, pyproject.toml, go.mod, etc.)
- Source code organization patterns
- Existing documentation structure
- Build and dependency management files

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

## Step 3: Create/Update docs/standards/

@context7 fetch latest standards and patterns for detected stack.

Fetch current documentation for:
- Latest coding standards and conventions
- Current architectural patterns for detected stack
- Modern testing strategies and best practices
- Updated security and performance guidelines

@scaffold create or update standards documentation using current best practices.

### Create Language-Specific Standards

- `docs/standards/{detected-language}/architecture.md`
- `docs/standards/{detected-language}/coding.md`

### Template Selection Strategy

1. **Exact Match**: If template exists for detected stack → Use directly
2. **Similar Match**: If template exists for same language, different framework → Adapt framework-specific parts
3. **Language Match**: If template exists for language but no stack match → Use language patterns, add stack-specific sections
4. **No Match**: Create generic standards based on language-agnostic principles

### Context7 Enhancement Integration

- Integrate latest framework patterns into standards
- Update architectural guidance with current best practices
- Enhance coding standards with modern conventions
- Refresh testing strategies with current ecosystem approaches

### Core Standards (Always Create/Update)

- `docs/standards/coding.md` - TDD implementation patterns and coding principles
- `docs/standards/architecture.md` - Architecture patterns and design principles

### Template Adaptation Process

When adapting templates:

- Preserve language-agnostic architecture principles (layered architecture, DDD, etc.)
- Preserve language-agnostic testing patterns (TDD, unit/integration tests)
- Update framework-specific examples and tooling
- Update dependency management and build system references
- Keep the same structure and organization patterns

## Step 4: Create/Update docs/product/

@scaffold create or update product documentation.

### Product Documentation Files

- `docs/product/product.md` - Product context and business requirements
- `docs/product/roadmap.md` - Development roadmap and priorities

### Content Updates

- Refresh with latest product documentation patterns
- Ensure business context is captured effectively
- Update roadmap templates with current best practices
- Maintain lightweight, implementation-focused documentation

## Step 5: Validate Standards Application

@context validate that standards are properly applied and complete.

Validation Checks:

- All required standards files exist
- Language-specific standards match detected stack
- Generic standards are present and up-to-date
- Product documentation follows latest patterns
- Cross-references between files are correct
- Template adaptations preserve core principles

## ✅ Standards & Product Documentation Updated

### Summary

- Technology stack detected agnostically
- Standards documentation created/updated from templates
- Product documentation refreshed with latest patterns
- Template adaptations preserve language-agnostic principles

### Update Results

- **Stack Detection**: Technology identified without hardcoded assumptions
- **Standards Creation**: Language-specific and generic standards applied
- **Template Adaptation**: Existing templates adapted for detected stack
- **Product Documentation**: Business context and roadmap updated

### Next Actions

1. Review updated standards documentation
2. Continue development: `/spec/implement [feature-name]`
3. Check product status: `/product/status`

### Standards Applied

- **Architecture**: Layered architecture patterns for detected stack
- **Coding**: Stub-driven TDD approach and language-specific conventions
- **Testing**: Unit and integration test strategies
- **Documentation**: Product context and roadmap management

