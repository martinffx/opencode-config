---
description: Systematic debugging investigation with bisect methodology
agent: coder
model: opencode/kimi-k2.5
temperature: 0.3
---

# Debug: Systematic Investigation & Bisect Debugging

## Step 1: Parse Debug Request & Initialize Todo List

@coder analyze the debug request: $ARGUMENTS

@todowrite create debugging todo list.

Initialize todo structure:
- Main task: Debug $ARGUMENTS
- Sub-tasks: Problem classification, investigation, solution
- Tracking: Evidence collection, hypothesis evolution

**Debug Context Extraction:**
- **Error type**: [runtime, logic, integration, performance]
- **Symptoms**: [specific error messages, unexpected behavior]
- **Environment**: [runtime versions, configuration, deployment context]
- **Reproduction**: [when, where, how the issue manifests]
- **Previous attempts**: [debugging steps already tried]

**Parameter Validation:**
- **Error type**: Validate category is one of runtime/logic/integration/performance
- **Environment**: Check if environment details are sufficient
- **Reproduction**: Validate reproducibility description
- **File references**: Ensure referenced files exist, are accessible, and within allowed directories
- **Input sanitization**: Remove potentially malicious characters, validate argument length limits
- **Security validation**: Prevent path traversal attacks, validate against whitelist [docs/, command/, agent/]

**Problem Classification:**
- **Category**: [runtime/logic/integration/performance]
- **Symptoms**: [specific manifestations]
- **Context**: [environmental factors]
- **Reproducibility**: [consistent/intermittent/rare]

@todowrite update todo list with problem classification results.

## Step 2: Strategy Selection & Todo Planning

@coder determine the optimal debugging strategy.

@todowrite update todo list with selected strategy and investigation plan.

**Bisect Debugging Criteria:**
- ✅ Clear boundary between working and broken states
- ✅ Testable midpoint available
- ✅ Linear or temporal problem space
- ✅ Binary outcome testing possible

**Strategy Decision:**

**Use Bisect Debugging if:**
- Git bisect: "When did this start breaking?" (commit history)
- Code bisect: "Which section contains the bug?" (code regions)
- Data bisect: "What input triggers the issue?" (test data)
- Time bisect: "When did the issue start?" (timeline)

**Use Systematic Investigation if:**
- Complex multi-factor issues
- No clear boundaries
- Requires comprehensive code analysis
- Integration or environmental problems

**Selected Strategy**: [bisect/systematic] based on problem characteristics

**Todo List Updates:**
- If bisect: Add boundary identification, midpoint testing, recursive narrowing tasks
- If systematic: Add evidence collection, hypothesis evolution, code examination tasks
- Mark strategy selection as completed

## Step 3: Bisect Debugging (when applicable) & Todo Tracking

@coder use sequential thinking to guide bisect debugging.

@todowrite track bisect debugging progress in todo list.

**Error Handling**: 
- If sequential-thinking MCP server is unavailable: use manual framework below
- If MCP server returns error: log error and fallback with user notification
- If timeout occurs: retry once, then fallback to manual framework

**Bisect Framework:**
```
sequential-thinking_sequentialthinking with:
- Initial thought: "Identify the boundary between working and broken states"
- Process: Test midpoint → Discard working half → Repeat recursively
- Continue: Until issue isolated to specific point
- Confidence: Track hypothesis evolution
```

**Sequential Thinking Configuration:**
- **Timeout**: 30 seconds per thought
- **Max thoughts**: 8 (configurable via complexity)
- **Performance monitoring**: Track response times
- **Error handling**: Log errors and fallback to manual framework

**Todo List Tracking:**
- Mark boundary identification as in_progress
- Track each midpoint test iteration
- Update boundary narrowing progress
- Record confidence level changes
- Mark bisect completion when issue isolated

**Fallback Manual Framework (if MCP unavailable):**
1. **Boundary Identification**: Clearly define working vs broken states
2. **Midpoint Selection**: Choose testable midpoint between boundaries
3. **Test Execution**: Run test at midpoint
4. **Result Analysis**: Determine if issue persists
5. **Boundary Update**: Discard working half, keep problematic half
6. **Recursive Repeat**: Continue until specific issue isolated

**Git Bisect Process:**
1. **Boundary Identification**: Last known good commit, first bad commit
2. **Midpoint Testing**: Test commit at midpoint between boundaries
3. **Result Analysis**: Working or broken?
4. **Boundary Update**: Discard working half, keep problematic half
5. **Recursive Repeat**: Continue until single problematic commit isolated

**Code Bisect Process:**
1. **Region Identification**: Working vs problematic code sections
2. **Midpoint Testing**: Comment out/test half the code
3. **Result Analysis**: Issue persists or resolved?
4. **Region Update**: Focus on problematic half
5. **Recursive Repeat**: Isolate specific problematic code section

**Data Bisect Process:**
1. **Dataset Identification**: Working vs problematic input data
2. **Midpoint Testing**: Test with half the dataset
3. **Result Analysis**: Issue occurs or not?
4. **Dataset Update**: Focus on problematic data subset
5. **Recursive Repeat**: Isolate specific problematic input

**Time Bisect Process:**
1. **Timeline Identification**: When issue started
2. **Midpoint Testing**: Test state at midpoint time
3. **Result Analysis**: Working or broken?
4. **Timeline Update**: Focus on problematic time period
5. **Recursive Repeat**: Isolate specific change/time

## Step 4: Systematic Investigation (when bisect not applicable) & Todo Tracking

@coder use sequential thinking for comprehensive debugging analysis.

@todowrite track systematic investigation progress in todo list.

**Systematic Investigation Framework:**
```
sequential-thinking_sequentialthinking with:
- Thought 1: "Describe the issue and initial hypotheses"
- Thought 2: "Examine relevant code and identify suspicious patterns"
- Thought 3: "Collect evidence and track findings"
- Thought 4: "Evolve hypotheses with confidence assessment"
- Thought 5+: "Refine analysis with new insights"
- Continue: Until sufficient evidence gathered
```

**Sequential Thinking Configuration:**
- **Timeout**: 30 seconds per thought
- **Max thoughts**: 10 (configurable via complexity)
- **Performance monitoring**: Track response times
- **Error handling**: Log errors and fallback to manual framework

**Todo List Tracking:**
- Mark problem description as in_progress
- Track evidence collection findings
- Record hypothesis evolution with confidence levels
- Monitor code examination progress
- Update context integration status

**Fallback Manual Framework (if MCP unavailable):**
1. **Problem Description**: Detailed symptom analysis and documentation
2. **Code Review**: Systematic examination of relevant files
3. **Evidence Collection**: Track all findings and suspicious patterns
4. **Hypothesis Development**: Create and refine working theories
5. **Testing**: Validate hypotheses with targeted tests
6. **Conclusion**: Draw final conclusions based on evidence

**Investigation Process:**
1. **Problem Description**: Detailed symptom analysis
2. **Code Examination**: Systematic file and method review
3. **Evidence Collection**: Track findings and suspicious patterns
4. **Hypothesis Evolution**: Develop and refine theories with confidence levels
5. **Context Integration**: Consider environmental and integration factors

@todowrite update investigation phases as they complete.

## Step 5: Complete Analysis & Recommendations & Finalize Todo List

@coder provide final debugging analysis and solutions.

@todowrite complete debugging todo list with solution and validation.

**Root Cause Analysis:**
- **Isolated Issue**: [specific problem identified]
- **Contributing Factors**: [related issues or conditions]
- **Impact Assessment**: [scope and severity of problem]

**Solution Recommendations:**
- **Immediate Fix**: [specific code change or configuration]
- **Implementation Steps**: [how to apply the fix]
- **Testing Strategy**: [how to validate the solution]
- **Rollback Plan**: [if fix causes other issues]

**Prevention Strategies:**
- **Code Improvements**: [prevent similar future issues]
- **Testing Enhancements**: [catch issues earlier]
- **Process Changes**: [improve development workflow]
- **Monitoring Additions**: [detect issues proactively]

**Todo List Completion:**
- Mark root cause identification as completed
- Update solution implementation status
- Track validation testing progress
- Record prevention strategy implementation
- Mark main debugging task as completed
- Archive todo list for future reference

---

## Usage Examples

**Runtime Error Debugging:**
```
/zen/debug "TypeError: 'NoneType' object has no attribute 'split' in parser.py"
```

**Git Bisect for Regression:**
```
/zen/debug "Use git bisect to find when the authentication started failing"
```

**Performance Issue Investigation:**
```
/zen/debug "App is consuming excessive memory during bulk operations"
```

**Integration Problem Analysis:**
```
/zen/debug "API returns 500 errors randomly with this stack trace: [traceback]"
```

**Code Bisect for Complex Issues:**
```
/zen/debug "Use code bisect to find which section contains the memory leak"
```

## Debugging Categories

**Runtime Errors:** Exceptions, null references, type errors, memory leaks
**Logic Errors:** Algorithm bugs, boundary conditions, state issues, race conditions  
**Integration Issues:** API failures, database problems, third-party services, configuration
**Performance Problems:** Slow responses, memory spikes, CPU bottlenecks, I/O issues

## Bisect Debugging Types

**Git Bisect:** `git bisect start → git bisect bad HEAD → git bisect good v1.0.0`
**Code Bisect:** Comment out half code → test → narrow down recursively
**Data Bisect:** Test with half dataset → identify problematic subset → repeat
**Time Bisect:** Check system states → identify when issue started → isolate change

## When to Use Each Strategy

**Use Bisect Debugging:** Clear boundaries, linear problems, testable midpoints, binary outcomes
**Use Systematic Investigation:** Complex issues, no clear boundaries, integration problems, multiple causes

## Integration with Spec-Driven Development

**During Implementation:**
- Debug issues that arise during `/spec/implement`
- Validate fixes with `/code/validate`
- Ensure solutions follow architectural patterns

**Quality Gates:**
- Use before commits to prevent introducing bugs
- Validate fixes don't break existing functionality
- Maintain code quality and consistency

**Workflow Integration:**
```bash
/spec/implement feature-name    # Implementation
/zen/debug "issue description"   # Debug problems
/code/validate                  # Quality gates
/commit                         # Commit fixes
```

**Example with SDD Integration:**
```bash
/zen/debug "Authentication service failing during /spec/implement with TypeError: NoneType object has no attribute 'user_id'"
```

## Expected Outcomes

**Results:** Root cause identified, solution implemented, validation completed, prevention established
**Benefits:** O(log n) bisect efficiency, comprehensive analysis, methodical approach, workflow integration

The debug command provides efficient bisect debugging for linear problems and systematic investigation for complex issues, guided by sequential thinking for methodical analysis.