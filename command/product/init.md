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

## Step 2: Detect Technology Stack

@context analyze project structure to detect technology stack.

Detection priority:
1. **React**: package.json with React + Vite dependencies
2. **TypeScript**: package.json with Fastify + Drizzle dependencies
3. **Rust**: Cargo.toml with Axum + SQLx dependencies
4. **Python**: pyproject.yml/requirements.txt with FastAPI + SQLAlchemy

## Step 3: Create Project Structure

@scaffold create project directory structure and documentation files.

Create:
- `docs/product/` directory with product.md and roadmap.md
- `docs/spec/` directory for feature specifications
- `docs/standards/` directory with language-specific standards
- Root CLAUDE.md file with project overview

Apply templates with project data from Step 1 and detected stack from Step 2.

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
- **architecture.md**: Language-specific layer patterns
- **coding.md**: Language-specific TDD patterns and conventions

### 📄 CLAUDE.md
- Project overview and available commands
- Quick start guide

## Step 5: Language-Specific Standards Applied

Based on detected technology stack:

### Python (FastAPI + SQLAlchemy + pytest + VCR.py)
- Async patterns with FastAPI
- Database integration with SQLAlchemy
- Testing with pytest-asyncio and VCR.py
- Docker test database setup

### Rust (Axum + SQLx + tokio-test)
- Async patterns with Axum
- Database integration with SQLx
- Testing with tokio-test and mockall
- Docker test database setup

### TypeScript (Fastify + Drizzle + bun + undici)
- Async patterns with Fastify
- Database integration with Drizzle
- Testing with bun test and undici mock
- Docker test database setup

### React (Vite + shadcn + zustand + MSW + Vitest)
- Component architecture patterns
- State management with Zustand
- Testing with Vitest and MSW
- API integration with openapi-react-query

## ✅ Project Initialized

Created documentation structure with:
- Product definition and roadmap
- Language-specific technical standards
- TDD workflow and testing strategy
- AI-friendly command reference
- Technology stack detection and template application

**Next action**: `/spec/create authentication` to build your first feature

## Multi-Stack Support

If multiple technology stacks are detected:
- Separate standards directories created for each stack
- Cross-stack project structure supported
- Language-specific commands and workflows applied
- Unified documentation with stack-specific sections