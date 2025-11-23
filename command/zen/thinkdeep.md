---
description: Extended reasoning partner for deep analysis using sequential thinking
agent: strategist
model: opencode/gemini-3-pro
temperature: 0.7
---

# ThinkDeep: Extended Reasoning Analysis

## Step 1: Parse Request Parameters

@strategist analyze the thinkdeep request: $ARGUMENTS

**Parameter Extraction:**
- **Main prompt**: [extract core thinking request]
- **Thinking mode**: [parse "minimal|low|medium|high|max" - default high]
- **Model preference**: [parse "auto|pro|flash|o3|gpt4" etc - default auto]
- **Focus areas**: [extract "architecture, performance, security" etc]
- **File references**: [extract "reference to file.py" patterns]
- **Image references**: [extract image paths for visual analysis]
- **Problem context**: [extract additional context provided]

**Parameter Validation:**
- **Thinking mode**: Validate "minimal|low|medium|high|max" (default: high)
- **Model preference**: Validate model options (default: auto)
- **Focus areas**: Ensure valid focus categories
- **File references**: Check if referenced files exist, are accessible, and within allowed directories
- **Problem context**: Validate context completeness
- **Input sanitization**: Remove potentially malicious characters, validate argument length limits
- **Security validation**: Prevent path traversal attacks, validate against whitelist [docs/, command/, agent/]

**Parsed Configuration:**
```json
{
  "prompt": "[core request]",
  "thinking_mode": "[high/medium/low/max/minimal]",
  "focus_areas": ["architecture", "performance"],
  "file_references": ["api/routes.py"],
  "problem_context": "[additional context]"
}
```

## Step 2: Deep Sequential Analysis

**Error Handling**: 
- If sequential-thinking MCP server is unavailable: use manual framework below
- If MCP server returns error: log error and fallback with user notification
- If timeout occurs: retry once, then fallback to manual framework

@strategist use sequential-thinking to perform extended reasoning on the parsed request.

**Sequential Thinking Configuration:**
- **Initial depth**: [determine based on thinking_mode]
- **Focus areas**: [from parsed parameters]
- **Session context**: [automatically available - current conversation]
- **File integration**: [read referenced files if needed]
- **Timeout**: 30 seconds per thought
- **Max thoughts**: 8 (configurable via thinking_mode)
- **Performance monitoring**: Track response times
- **Error handling**: Log errors and fallback to manual framework

**Sequential Thinking Process:**

### Thought 1: Context Understanding
Analyze the current session context to understand:
- What has been discussed so far
- Previous decisions and analyses made
- User's specific constraints and goals
- Existing solutions or approaches tried

### Thought 2: Problem Decomposition
Break down the core issue into:
- Primary problem statement
- Key sub-problems or components
- Underlying assumptions and constraints
- Success criteria and desired outcomes

### Thought 3: Assumption Challenge
Question the underlying assumptions:
- What assumptions are we making?
- Are these assumptions valid in this context?
- What if the opposite were true?
- Alternative perspectives to consider

### Thought 4: Alternative Approaches Exploration
Explore different solution paths:
- Conventional approaches and their pros/cons
- Unconventional or innovative solutions
- Trade-offs between different options
- Hybrid or combined approaches

### Thought 5: Context Integration
Incorporate relevant context:
- File references and code patterns
- Previous session insights
- User's specific technical stack
- Business or project constraints

### Thought 6: Edge Case Analysis
Identify potential issues:
- What could go wrong with each approach?
- Edge cases and failure modes
- Scalability and performance concerns
- Security and maintenance implications

### Thought 7: Risk Assessment
Evaluate risks for each option:
- Implementation complexity
- Technical debt potential
- Team skill requirements
- Timeline and resource impacts

### Thought 8: Solution Synthesis
Combine insights into recommendations:
- Primary recommended approach
- Backup or alternative options
- Implementation considerations
- Next steps and validation points

**Continue sequential thinking** until satisfaction, using:
- `is_revision`: true to refine previous thoughts
- `branchFromThought`: to explore alternative reasoning paths
- `needsMoreThoughts`: true to extend analysis if needed

**Fallback Manual Framework (if MCP unavailable):**
1. **Context Review**: Analyze current session for relevant background
2. **Problem Decomposition**: Break down core issue into components
3. **Assumption Challenge**: Question underlying assumptions systematically
4. **Alternative Exploration**: Consider multiple solution paths
5. **Context Integration**: Incorporate file references and constraints
6. **Risk Analysis**: Identify potential issues and edge cases
7. **Solution Synthesis**: Combine insights into recommendations

## Step 3: Critical Evaluation & Synthesis

@strategist perform critical evaluation of the sequential thinking results.

**Self-Critique Questions:**
- Does the analysis address the user's specific context?
- Are the recommendations practical and implementable?
- Have we considered the most important constraints?
- Are there any blind spots or missing perspectives?

**Contextual Validation:**
- Fit with user's technical stack and preferences
- Alignment with previous decisions in session
- Practicality given team skills and resources
- Integration with existing systems or patterns

**Final Synthesis:**
- **Primary Recommendation**: [best approach with rationale]
- **Alternative Options**: [backup plans with trade-offs]
- **Implementation Guidance**: [key considerations and next steps]
- **Risk Mitigation**: [how to address identified concerns]

## Step 4: Integration Recommendations

**Workflow Integration:**
- When to use this vs other commands
- How this fits with spec-driven development
- Follow-up commands or next steps

**Context Preservation:**
- Key insights to remember for future sessions
- Decision rationale for documentation
- Dependencies and prerequisites identified

---

## Usage Examples

**Basic deep analysis:**
```
/zen/thinkdeep "Analyze my authentication architecture"
```

**With specific thinking mode:**
```
/zen/thinkdeep "Review my API design using max thinking mode"
```

**With file references:**
```
/zen/thinkdeep "Evaluate my database schema with reference to models/user.py"
```

**With focus areas:**
```
/zen/thinkdeep "Assess my microservices design focusing on performance and security"
```

**Complex architectural decisions:**
```
/zen/thinkdeep "Should I use GraphQL or REST for my API considering team skills and scalability needs"
```

## When to Use ThinkDeep

- **Architecture decisions**: When choosing between major design approaches
- **Complex problem solving**: Multi-faceted issues requiring deep analysis
- **Assumption validation**: Challenging conventional wisdom or previous decisions
- **Alternative exploration**: When stuck and need fresh perspectives
- **Risk assessment**: Before major implementation decisions

## Integration with Spec-Driven Development

**Workflow**: Before /spec-create → During /spec/design → After /spec/implement → Before commits
**Purpose**: Validate approaches, challenge decisions, review implementations, ensure sound choices

ThinkDeep builds on session context for personalized, relevant analysis within your specific situation.
