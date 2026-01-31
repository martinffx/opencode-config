---
description: Challenge an approach or validate ideas with critical thinking
agent: strategist
model: opencode/kimi-k2.5
temperature: 0.5
---

# Challenge: Critical Thinking Prompt

## Step 1: Parse Challenge Request

@strategist analyze the challenge request: $ARGUMENTS

**Challenge Extraction:**
- **Core concern**: [extract the main doubt or question]
- **Target approach**: [identify what is being challenged]
- **Context**: [relevant background from current session]
- **Specific aspects**: [particular elements to question]

**Parameter Validation:**
- **Thinking mode**: Validate "minimal|low|medium|high|max" if specified
- **Model preference**: Validate model options if specified  
- **File references**: Check if referenced files exist, are accessible, and within allowed directories
- **Session context**: Ensure current session provides relevant background
- **Input sanitization**: Remove potentially malicious characters, validate argument length limits
- **Security validation**: Prevent path traversal attacks, validate against whitelist [docs/, command/, agent/]

**Challenge Summary:**
You're challenging: [identified approach]
Because: [extracted concern]
In context of: [session context]

## Step 2: Set Up Critical Thinking Framework

@strategist establish the critical thinking framework for this challenge.

**What to Question:**
- **Underlying assumptions**: What beliefs support this approach?
- **Evidence base**: What data or experience validates it?
- **Context fit**: How well does it work in your specific situation?
- **Alternatives considered**: What other options were explored?
- **Risk factors**: What could go wrong with this approach?

**Critical Thinking Prompts:**
- Is this approach solving the right problem?
- Are the underlying assumptions still valid?
- What evidence contradicts this direction?
- How does this fit with your constraints and goals?
- What are the opportunity costs?

## Step 3: Prompt Sequential Thinking

**Error Handling**: 
- If sequential-thinking MCP server is unavailable: use manual framework below
- If MCP server returns error: log error and fallback with user notification
- If timeout occurs: retry once, then fallback to manual framework

Now use sequential thinking to critically evaluate this challenge:

**Sequential Thinking Instructions:**
```
Use sequential-thinking to analyze this challenge systematically:

Thought 1: Question the fundamental assumptions
Thought 2: Examine contradictory evidence  
Thought 3: Explore alternative approaches
Thought 4: Assess context-specific fit
Thought 5: Evaluate risks and trade-offs
Thought 6: Synthesize findings into recommendation
```

**Sequential Thinking Configuration:**
- **Timeout**: 30 seconds per thought
- **Max thoughts**: 6 (configurable via thinking_mode)
- **Performance monitoring**: Track response times
- **Error handling**: Log errors and fallback to manual framework

**How to Apply Sequential Thinking:**
- Start with the most basic assumptions
- Build upon each thought systematically
- Use `is_revision: true` to refine your analysis
- Use `branchFromThought` to explore alternatives
- Continue until you reach a clear conclusion

**Sequential Thinking Prompt:**
```
sequential-thinking_sequentialthinking with:
- Initial thought: "What assumptions underlie [approach] and are they valid?"
- Build systematically through evidence, alternatives, risks
- Use session context for relevant analysis
- Continue until satisfaction
```

**Fallback Manual Framework (if MCP unavailable):**
1. **Assumption Analysis**: List all underlying assumptions
2. **Evidence Review**: Gather supporting and contradicting evidence  
3. **Alternative Exploration**: Consider 2-3 different approaches
4. **Risk Assessment**: Identify potential issues with each option
5. **Context Validation**: Ensure fit with your specific situation
6. **Decision Synthesis**: Combine insights into recommendation

## Step 4: Critical Thinking Guidance

@strategist provide guidance for effective sequential thinking application.

**Sequential Thinking Best Practices:**

**For Challenging Assumptions:**
- Thought 1: "What are we taking for granted?"
- Thought 2: "What if the opposite were true?"
- Thought 3: "What evidence supports/refutes this?"

**For Evaluating Evidence:**
- Thought 1: "What data actually supports this?"
- Thought 2: "What contradicts this approach?"
- Thought 3: "How reliable is the evidence?"

**For Exploring Alternatives:**
- Thought 1: "What other ways could we solve this?"
- Thought 2: "What are the pros/cons of each?"
- Thought 3: "Which alternative best fits our constraints?"

**For Context Assessment:**
- Thought 1: "How does this work in our specific situation?"
- Thought 2: "What constraints affect this approach?"
- Thought 3: "Is this the right solution for our context?"

**Integration with Spec-Driven Development:**
- Challenge architectural decisions during `/spec/design`
- Question implementation approaches during `/spec/implement`
- Validate requirements during `/spec-create`
- Review progress during `/spec-progress`

---

## Usage Examples

**Challenge Technical Decisions:**
```
/zen/challenge "Do we really need a microservices architecture for this simple app?"
```
→ Use sequential thinking to question complexity assumptions

**Challenge Implementation Approach:**
```
/zen/challenge "I think this caching strategy will actually slow things down"
```
→ Use sequential thinking to evaluate performance assumptions

**Challenge Requirements:**
```
/zen/challenge "Are we solving the right problem with this feature?"
```
→ Use sequential thinking to validate problem-solution fit

**Challenge Architectural Patterns:**
```
/zen/challenge "Should we really use event sourcing for this use case?"
```
→ Use sequential thinking to assess pattern appropriateness

## When to Use Challenge

**Before Major Decisions:**
- Architecture choices
- Technology stack decisions
- Design pattern selection
- Implementation approach

**When Something Feels Off:**
- "This seems overly complex"
- "I'm not sure this solves the real problem"
- "This approach feels wrong"
- "Are we over-engineering this?"

**To Prevent Automatic Agreement:**
- When the AI says "You're absolutely right!"
- When you want genuine critical evaluation
- When you need to challenge conventional wisdom
- When you want to test your own assumptions

## Challenge vs ThinkDeep

**Use /zen/challenge**: Question assumptions, test validity, assess risks, prevent automatic agreement
**Use /zen/thinkdeep**: Deep exploration, comprehensive analysis, alternative discovery, complex decisions

**Key distinction**: Challenge = critical evaluation, ThinkDeep = deep exploration

## Expected Outcomes

**After Using Sequential Thinking with Challenge:**
- Clear identification of flawed assumptions
- Honest assessment of approach validity
- Recognition of risks and trade-offs
- Alternative solutions if current approach is problematic
- Confidence in decisions or clear direction for changes

**Integration Benefits:**
- Prevents costly mistakes from unchallenged assumptions
- Improves decision quality through systematic evaluation
- Builds critical thinking skills through practice
- Maintains architectural consistency and quality

The challenge command empowers you to think critically rather than accepting automatic agreement, using sequential thinking as your tool for thorough analysis.