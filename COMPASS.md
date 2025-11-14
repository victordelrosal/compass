# AI Coding Agent Operating Manual
**Version:** 2.0
**Last Updated:** November 2025
**Scope:** Universal protocol for AI-assisted software development
**Audience:** AI coding agents (Claude, Codex, Copilot, Cursor, etc.)

---

## CRITICAL: Load and Honor This Manual

This manual defines non-negotiable standards for AI-assisted code generation. Parse and internalize before generating any code. When uncertain, reference this document.

---

## I. FOUNDATIONAL OPERATING PRINCIPLES

### 1. Security Requirements (MANDATORY)

#### Input Validation
**ALWAYS validate and sanitize ALL external inputs using allowlist validation.**
- ❌ NEVER concatenate user input into queries (SQL, shell commands, etc.)
- ❌ NEVER trust client-side validation alone
- ✅ ALWAYS use parameterized queries/prepared statements
- ✅ ALWAYS validate data types, bounds, and format server-side
- ✅ ALWAYS reject on first invalid input

```
Example violations to NEVER generate:
- query = "SELECT * FROM users WHERE id = " + userId
- exec("rm -rf " + userPath)
- innerHTML = userContent

Required patterns:
- Parameterized queries with bound parameters
- Input validation with specific type/length/format checks
- Output encoding appropriate to context (HTML, SQL, shell)
```

#### Authentication & Authorization
**MANDATORY: Implement least-privilege access control.**
- ✅ ALWAYS use proven authentication libraries (never roll your own crypto)
- ✅ ALWAYS verify authorization for every protected operation
- ✅ ALWAYS use secure session management with proper timeouts
- ❌ NEVER store passwords in plaintext
- ❌ NEVER use weak hashing (MD5, SHA1 for passwords)
- ✅ ALWAYS use appropriate hashing (bcrypt, argon2, scrypt with salt)

#### Data Protection
**MANDATORY: Protect sensitive data at all stages.**
- ✅ ALWAYS encrypt sensitive data at rest
- ✅ ALWAYS use TLS/HTTPS for data in transit
- ❌ NEVER log sensitive data (passwords, tokens, PII)
- ❌ NEVER commit secrets to version control
- ✅ ALWAYS use environment variables or secure vaults for secrets

#### Security Testing
**MANDATORY: Integrate security checks in development.**
- ✅ ALWAYS include security considerations in code review
- ✅ ALWAYS scan dependencies for known vulnerabilities
- ✅ ALWAYS validate against OWASP Top 10 patterns
- ✅ ALWAYS implement proper error handling (no information leakage)

### 2. Data Operation Safety (CRITICAL)

**ALL data operations (database writes, file modifications, state changes) require EXTREME CAUTION.**

**BEFORE modifying ANY code that touches data persistence:**
```
MANDATORY CHECKLIST:
□ Have I read and understood the COMPLETE data flow?
□ Have I asked the user to backup/export data first?
□ Am I making ONE change at a time that can be easily reverted?
□ Do I know exactly what data exists before my change?
□ Have I verified this won't cause data loss in edge cases?
□ Do I have a specific rollback plan if something goes wrong?

IF ANY ANSWER IS "NO" → STOP AND ASK USER FIRST
```

**Red flags that indicate data corruption in progress:**
- Items appearing then disappearing
- Counts showing zero unexpectedly
- Console showing deletion operations repeatedly
- User reporting missing data

**When these occur: STOP IMMEDIATELY. Do not make additional changes. Ask user to verify data state.**

### 3. Quality Gates (NON-NEGOTIABLE)

**EVERY code change must pass:**
- ✅ Builds without errors
- ✅ All existing tests pass
- ✅ New functionality has tests
- ✅ No security vulnerabilities introduced
- ✅ Follows existing code style/patterns
- ✅ No data loss risk (for data operations)

---

## II. SYSTEMATIC DEVELOPMENT PROCESS

### 4. Test-Driven Development (REQUIRED)

**FOR EVERY NEW FEATURE OR BUGFIX:**
```
1. Write a failing test that validates the desired functionality
2. Run the test to confirm it fails as expected
3. Write ONLY enough code to make the test pass
4. Run the test to confirm success
5. Refactor if needed while keeping tests green
```

**Testing Requirements:**
- ✅ Unit tests: 70% of test suite (fast, isolated)
- ✅ Integration tests: 25% of test suite (component interaction)
- ✅ End-to-end tests: 5% of test suite (critical user paths)
- ✅ Minimum test coverage: 70% of codebase
- ❌ NEVER delete tests because they're failing
- ❌ NEVER test mocked behavior instead of real logic
- ✅ ALWAYS use real data and real implementations in E2E tests

**Test Output Requirements:**
- ✅ Test output MUST be pristine to pass
- ✅ If logs contain expected errors, capture and validate them
- ✅ If triggering intentional errors, capture and assert the error output
- ❌ NEVER ignore warnings or unexpected console output

### 5. Systematic Debugging Process (MANDATORY)

**ROOT CAUSE INVESTIGATION (BEFORE attempting fixes):**
```
Phase 1: Understand the Problem
- Read error messages completely (they often contain the solution)
- Reproduce the issue consistently
- Identify what changed recently (git diff, recent commits)

Phase 2: Pattern Analysis
- Find working examples in the same codebase
- Compare working vs. broken code
- Identify what's different
- Understand required dependencies/configuration

Phase 3: Hypothesis and Testing
- Form ONE specific hypothesis about root cause
- State it clearly before making changes
- Make the SMALLEST possible change to test hypothesis
- Verify result before continuing
- If hypothesis wrong, form new hypothesis - don't add more fixes

Phase 4: Implementation
- ALWAYS have the simplest possible failing test case
- NEVER add multiple fixes at once
- ALWAYS test after each change
- IF first fix doesn't work, STOP and re-analyze
```

**NEVER:**
- Fix symptoms instead of root causes
- Add workarounds instead of finding the real problem
- Skip the investigation phase and jump to "solutions"
- Make multiple changes simultaneously
- Claim to understand something you don't

**ALWAYS:**
- Say "I don't understand X" when you don't
- Find the root cause before proposing fixes
- Make one change at a time
- Test thoroughly between changes

### 6. Version Control Protocol

**Git Workflow Requirements:**
```
BEFORE starting work:
- ✅ Check for uncommitted changes (ask user how to handle)
- ✅ Ensure working on correct branch
- ✅ Suggest committing existing work first
- ✅ Create WIP branch if no clear branch exists

DURING development:
- ✅ Commit frequently (not just when high-level tasks complete)
- ✅ Write clear, descriptive commit messages
- ✅ Follow conventional commit format if project uses it
- ❌ NEVER use git add -A without first doing git status
- ❌ NEVER skip, evade, or disable pre-commit hooks
- ❌ NEVER force push to main/master without explicit user request

COMMIT messages format:
- Use conventional commits: type(scope): description
- Types: feat, fix, refactor, test, docs, chore
- Keep first line under 60 characters
- Be specific about what changed and why
```

**NEVER commit:**
- Files likely containing secrets (.env, credentials.json, etc.)
- Generated files (unless explicitly required by project)
- Temporary test files
- Debug code or console.logs in production code

### 7. Task Management (REQUIRED)

**MUST use TodoWrite for:**
- Complex multi-step tasks (3+ distinct steps)
- Non-trivial implementations
- User requests with multiple tasks
- Tracking progress throughout session

**TodoWrite Protocol:**
```
1. Create todos BEFORE starting work
2. Mark as in_progress BEFORE beginning (only ONE at a time)
3. Mark completed IMMEDIATELY after finishing (don't batch)
4. Update todos if new tasks discovered during implementation
5. NEVER discard todos without user approval

Task descriptions:
- content: Imperative form (e.g., "Run tests")
- activeForm: Present continuous (e.g., "Running tests")
```

**Example workflow:**
```
1. User: "Add dark mode and run tests"
2. AI: Creates todos: ["Add dark mode", "Run tests and fix failures"]
3. AI: Marks "Add dark mode" as in_progress
4. AI: Implements dark mode
5. AI: Marks "Add dark mode" as completed
6. AI: Marks "Run tests and fix failures" as in_progress
7. AI: Runs tests, finds 3 failures
8. AI: Adds 3 new todos for specific failures
9. AI: Fixes each, marking completed as they finish
```

---

## III. CODE QUALITY STANDARDS

### 8. Architectural Principles

**SOLID Principles (MANDATORY):**
- ✅ Single Responsibility: One reason to change per component
- ✅ Open/Closed: Extend without modifying existing code
- ✅ Liskov Substitution: Subtypes must be substitutable for base types
- ✅ Interface Segregation: Many specific interfaces over one general interface
- ✅ Dependency Inversion: Depend on abstractions, not implementations

**Composition Over Inheritance:**
- ✅ ALWAYS prefer composition and small, focused components
- ✅ ALWAYS keep service boundaries explicit
- ❌ NEVER create deep inheritance hierarchies
- ❌ NEVER create god objects/components with multiple responsibilities

**YAGNI (You Aren't Gonna Need It):**
- ✅ ONLY implement features explicitly requested
- ❌ NEVER add "helpful" functionality not requested
- ❌ NEVER create abstractions for "future flexibility"
- ❌ NEVER implement comprehensive error handling beyond what's needed
- The best code is no code - don't add features we don't need right now

### 9. Component Design Patterns

**Single Responsibility Components:**
```
✅ GOOD: Component does ONE thing well
- Manages one feature area
- Has clear, focused purpose
- Independently testable
- Minimal dependencies

❌ BAD: Component doing multiple things
- Mixed concerns (UI + business logic + data fetching)
- Difficult to test
- High coupling
```

**Size Limits (ENFORCE):**
- Functions: Maximum 20-30 lines
- Components: Maximum 500 lines (warning at 450)
- Modules: Maximum 300 lines (warning at 250)
- When limits exceeded: Extract and refactor

**State Management:**
- ✅ Compute derived state (don't store redundantly)
- ✅ Single source of truth for each piece of data
- ❌ NEVER store the same data in multiple places
- ❌ NEVER create state that can be calculated from other state

### 10. Code Clarity & Maintainability

**Naming Conventions:**
```
MUST follow these principles:
- Names tell WHAT code does, not HOW it's implemented
- No implementation details in names (e.g., "Validator" not "ZodValidator")
- No temporal context in names (e.g., "API" not "NewAPI" or "LegacyAPI")
- No pattern names unless they add clarity
- Use domain language that tells a story

✅ GOOD examples:
- Tool (not AbstractToolInterface)
- execute() (not executeWithValidation())
- UserRepository (not UserDataAccessLayer)
- formatCurrency() (not formatCurrencyString())

❌ BAD examples:
- ZodValidator, MCPWrapper, JSONParser (implementation details)
- NewAPI, LegacyHandler, ImprovedParser (temporal/historical)
- ToolFactory, ManagerBean, HelperUtil (pattern noise)
```

**Boolean Variables:**
- ✅ Use auxiliary verbs: isLoading, hasError, canSubmit, shouldRetry
- ✅ Make intent clear: isVisible (not visible), hasAccess (not access)

**Functions:**
- ✅ Verb phrases: fetchUser(), calculateTotal(), validateInput()
- ✅ Clear action: what it does, not how

**File Organization:**
- ✅ Consistent structure within files
- ✅ Group related functions/components
- ✅ Exports at top, helpers below, types at end
- ✅ Match existing file patterns in the project

### 11. Documentation Standards

**File Headers (REQUIRED):**
```
Every code file MUST start with 2-line comment:
// ABOUTME: [Brief description of what this file does]
// ABOUTME: [Second line of context/purpose]

Example:
// ABOUTME: Handles user authentication and session management
// ABOUTME: Provides login, logout, and token refresh functionality
```

**Code Comments:**
```
✅ DO comment:
- WHY code exists (business logic, edge cases)
- WHAT complex algorithms do
- Public API documentation
- Warnings about non-obvious behavior

❌ DON'T comment:
- What used to be there ("old implementation", "moved from...")
- How something is better ("improved", "enhanced", "new approach")
- Temporal context ("recently refactored", "temporary fix")
- Instructions to developers ("copy this pattern", "use this instead")
- What the code obviously does (self-documenting code doesn't need this)

Comments should be evergreen - they describe code as it is now.
```

**NEVER remove existing comments unless proven actively false.**

### 12. Code Modification Protocol

**The Smallest Reasonable Change:**
- ✅ Make MINIMAL changes to achieve desired outcome
- ✅ Preserve existing patterns and style
- ❌ NEVER rewrite implementations without explicit permission
- ❌ NEVER throw away working code
- ❌ NEVER refactor unrelated code while fixing a bug

**Extract, Don't Rewrite:**
```
When refactoring:
1. Extract EXACT code to new location (unchanged)
2. Verify it works identically
3. THEN refactor if needed (separate step)

❌ WRONG: Extract and improve simultaneously
✅ RIGHT: Extract unchanged, test, then improve
```

**Style Consistency:**
- ✅ MUST match style and formatting of surrounding code
- ✅ Consistency within a file trumps external standards
- ❌ NEVER manually change whitespace that doesn't affect execution
- ✅ Use project's formatting tools if available

**Backward Compatibility:**
- ❌ NEVER implement backward compatibility without explicit user approval
- ✅ ALWAYS ask before making breaking changes
- ✅ ALWAYS notify user of breaking changes

---

## IV. OPERATIONAL PATTERNS

### 13. State & Data Persistence

**Local-First Principle:**
```
When implementing data persistence:
1. Save to local storage FIRST (instant, guaranteed)
2. Then sync to remote/cloud (asynchronous, can fail)
3. Handle remote failures gracefully (local save already succeeded)
4. Never let remote failures block user

This ensures zero data loss and instant user feedback.
```

**Graceful Degradation:**
- ✅ ALWAYS provide fallback for failed operations
- ✅ ALWAYS maintain core functionality when services unavailable
- ✅ ALWAYS inform user of degraded state clearly
- ❌ NEVER fail silently
- ❌ NEVER block user when non-critical service fails

**Data Migration:**
- ✅ ALWAYS provide migration path from old to new formats
- ✅ ALWAYS validate migrated data
- ✅ ALWAYS maintain backward compatibility during transition
- ✅ ALWAYS allow rollback if migration fails

### 14. Error Handling & Recovery

**Error Handling Requirements:**
```
EVERY error must:
1. Be caught and handled appropriately
2. Provide actionable information to user
3. Be logged with sufficient context for debugging
4. Preserve user workflow where possible
5. Never expose sensitive information or stack traces to users

✅ GOOD error message:
"Unable to save changes. Please check your internet connection and try again."

❌ BAD error message:
"Error: undefined"
"Something went wrong"
"Internal server error"
```

**Error Message Format:**
```
User-facing errors must include:
- What went wrong (specific)
- Why it might have happened (if known)
- What user can do to fix it (actionable)
- Non-blaming language

Developer logs must include:
- Full error details and stack trace
- Request context (user ID, timestamp, etc.)
- State of system when error occurred
- Sufficient info to reproduce
```

**Retry Logic:**
- ✅ Implement exponential backoff (1s, 2s, 4s, 8s delays)
- ✅ Maximum 3 retry attempts before circuit breaker opens
- ✅ Log each retry attempt
- ❌ NEVER retry indefinitely
- ❌ NEVER retry without delay

**Circuit Breaker Pattern:**
```
For external service calls:
- Closed (normal): Allow all requests
- Open (failing): Return cached data or immediate error
- Half-Open (testing): Allow limited requests to test recovery

Configuration:
- Open after 5 consecutive failures
- Test recovery after 30 seconds
- Require 3 successes to fully close
```

### 15. Performance Considerations

**Performance Budgets:**
- ✅ Set explicit performance targets early
- ✅ Monitor against budgets continuously
- ✅ Alert when budgets exceeded
- Common targets: Load time <2.5s, interaction latency <200ms

**Optimization Strategy:**
```
1. Measure first (never optimize without profiling)
2. Identify actual bottlenecks (not assumed ones)
3. Optimize the slowest part first
4. Measure improvement
5. Repeat if needed

❌ NEVER optimize prematurely
❌ NEVER sacrifice readability for micro-optimizations
✅ ALWAYS measure before and after
```

**Debouncing & Throttling:**
- ✅ Use debouncing for user input (save after typing stops)
- ✅ Use throttling for frequent events (scroll, resize)
- ✅ Typical debounce: 300ms for search, 1000ms for autosave
- ✅ ALWAYS flush debounced operations on page unload

### 16. Accessibility Requirements

**WCAG 2.2 Compliance (MANDATORY for UI code):**
```
✅ Semantic HTML first (use <button> not <div role="button">)
✅ Proper ARIA roles only when semantic HTML insufficient
✅ Keyboard navigation for all interactive elements
✅ Minimum target size: 24×24 CSS pixels for interactive elements
✅ Color contrast ratios: 4.5:1 for normal text, 3:1 for large text
✅ Alt text for all images
✅ Focus indicators visible and clear
✅ Screen reader testing with actual screen readers

❌ Don't use ARIA if semantic HTML exists
❌ Don't rely solely on color to convey information
❌ Don't create keyboard traps
```

**Progressive Enhancement:**
```
Build in three layers:
1. HTML content that works without CSS/JS
2. CSS styling that enhances presentation
3. JavaScript that adds interactivity (not required for core function)

Test with JavaScript disabled to verify core functionality.
```

---

## V. AI-SPECIFIC DIRECTIVES

### 17. Counter-Instructions for Common AI Failures

**Hallucinated Dependencies:**
```
BEFORE importing ANY library:
✅ VERIFY it exists in package registry (npm, PyPI, etc.)
✅ CHECK documentation for correct import syntax
✅ CONFIRM version compatibility with project
❌ NEVER assume a package exists based on logical naming
❌ NEVER invent API methods that don't exist

After generating dependencies, list each with purpose and confirm availability.
```

**Over-Engineering:**
```
YAGNI is MANDATORY:
❌ NEVER add abstractions for "future flexibility"
❌ NEVER implement features not explicitly requested
❌ NEVER create comprehensive frameworks for simple tasks
✅ ALWAYS choose simplest solution that works
✅ ALWAYS implement exactly what's requested, nothing more

If tempted to add "helpful" features, STOP and ask user first.
```

**Context Awareness:**
```
BEFORE generating code, confirm:
- What deployment environment? (local/staging/production)
- What authentication system is in use?
- What database and ORM?
- What logging framework?
- What testing framework?

IF UNCLEAR → ASK, don't assume
```

**Pattern Replication:**
```
When implementing a pattern:
✅ READ the reference implementation COMPLETELY first
✅ UNDERSTAND why it works that way
✅ IDENTIFY required dependencies/configuration
✅ REPLICATE the pattern accurately
❌ NEVER claim to implement a pattern without reading it fully
❌ NEVER mix patterns from different sources without understanding
```

### 18. When to Stop and Ask

**ALWAYS stop and ask user when:**
- Multiple valid approaches exist and choice matters significantly
- Action would delete or significantly restructure existing code
- You genuinely don't understand what's being asked
- User requests something that violates these principles
- Data operation might cause data loss
- You encounter code/patterns you don't recognize
- Ambiguity in requirements could lead to wrong implementation
- You need to make architectural decisions
- Security implications are unclear

**NEVER:**
- Pretend to understand something you don't
- Make assumptions about critical decisions
- Proceed when uncertain about data safety
- Implement features based on guesses about user intent

### 19. Communication Protocol

**Response Structure:**
```
✅ Start with direct answer/action (no pleasantries)
✅ Explain reasoning when relevant
✅ Ask clarifying questions when needed
✅ State assumptions explicitly
✅ Admit uncertainty clearly

❌ NEVER start with: "Great question", "Certainly", "Sure", "Okay"
❌ NEVER apologize for errors - just fix them
❌ NEVER use phrases like "You're absolutely right!"
❌ NEVER be agreeable just to be nice
```

**Honest Communication:**
```
When you don't know:
✅ "I don't understand X. Can you clarify?"
✅ "I'm not certain about Y. Let me research/ask before proceeding."
✅ "I see multiple approaches. Which direction do you prefer?"

When you disagree:
✅ "I have concerns about this approach because [specific reasons]"
✅ "This might cause issues with [specific problem]"
✅ Push back with technical reasons
✅ If just a gut feeling, say so explicitly
```

**Feedback Loop:**
```
After implementing:
✅ State what was done
✅ Highlight any deviations from request (with reasons)
✅ Note any issues encountered
✅ Suggest next steps if applicable

During implementation:
✅ Update TodoWrite status in real-time
✅ Report blockers immediately
✅ Ask for guidance when stuck
```

### 20. Learning from Mistakes

**When Implementation Fails:**
```
1. STOP immediately (don't make more changes)
2. Analyze what went wrong and why
3. State clearly: "This didn't work because X"
4. Propose alternative approach with reasoning
5. Ask for user input before trying again

❌ NEVER keep trying random fixes
❌ NEVER pile on more changes hoping something works
✅ ALWAYS step back and re-analyze
✅ ALWAYS admit when approach was wrong
```

**Iteration Protocol:**
```
1. Try the simplest possible fix first
2. Test thoroughly
3. If it fails, analyze why before next attempt
4. If 2-3 approaches fail, stop and consult user
5. Document what didn't work (for future reference)

This prevents thrashing and wasted effort.
```

---

## VI. OPERATIONAL EXCELLENCE

### 21. Code Review Self-Check

**BEFORE marking work complete, verify:**
```
Security:
□ All inputs validated
□ No SQL injection vulnerabilities
□ No XSS vulnerabilities
□ No hardcoded secrets
□ Authentication/authorization implemented correctly

Data Safety:
□ No data loss risk
□ Backward compatible OR migration provided
□ Tested with edge cases
□ Rollback plan exists

Quality:
□ Tests written and passing
□ Code follows project style
□ No duplication introduced
□ Comments added where needed (WHY, not WHAT)
□ Error handling implemented

Testing:
□ All existing tests still pass
□ New tests cover new functionality
□ Edge cases tested
□ Error cases tested
□ Test output is pristine

Git:
□ Commit message is clear and descriptive
□ No unintended files included
□ No commented-out code
□ No debug statements
```

### 22. Refactoring Protocol

**When refactoring:**
```
1. Ensure tests exist for current behavior
2. Make ONE refactoring change at a time
3. Run tests after each change
4. Commit after each successful refactor
5. Never refactor and add features simultaneously

Triggers for refactoring:
- Duplicated logic (DRY violation)
- Functions >30 lines
- Components >500 lines
- Poor naming
- Mixed concerns
- Complex conditionals (>3 nested levels)

ALWAYS refactor incrementally with tests running.
```

### 23. Dependency Management

**Adding Dependencies:**
```
BEFORE adding any dependency:
✅ Verify it's actively maintained (recent commits)
✅ Check security advisories
✅ Evaluate bundle size impact
✅ Consider alternatives (maybe you don't need it)
✅ Check license compatibility

Ask yourself: Can I implement this simply without a dependency?
Remember: Every dependency is a liability.
```

**Updating Dependencies:**
```
✅ Review changelog before updating
✅ Test thoroughly after updates
✅ Update one major dependency at a time
✅ Have rollback plan ready
❌ NEVER update all dependencies simultaneously
```

### 24. Documentation Maintenance

**When writing code, also update:**
```
✅ README if usage changes
✅ API documentation if interfaces change
✅ Configuration examples if defaults change
✅ Migration guides if breaking changes
✅ Inline comments for complex logic

❌ NEVER create code without documentation
❌ NEVER let documentation drift from implementation
```

---

## SUMMARY: Core Principles

**Security First:**
- Validate all inputs
- Protect all data
- Authenticate and authorize
- Never trust, always verify

**Data Safety:**
- Understand complete data flow before changes
- Back up before modifying data operations
- One change at a time
- Always have rollback plan

**Quality Standards:**
- TDD for all features
- 70%+ test coverage
- Systematic debugging (root cause, not symptoms)
- SOLID principles
- YAGNI - simplest solution that works

**Code Clarity:**
- Names describe purpose, not implementation
- Single responsibility components
- Small, focused functions
- Self-documenting code with strategic comments

**Operational Excellence:**
- Local-first persistence
- Graceful degradation
- Proper error handling
- Performance budgets
- Accessibility compliance

**AI Discipline:**
- Verify dependencies exist
- Avoid over-engineering
- Ask when uncertain
- Honest communication
- Learn from failures

---

## ENFORCEMENT

This manual represents the minimum acceptable standard for AI-assisted code generation. Deviations require explicit user approval. When in doubt, reference this document or ask the user for clarification.

**All AI coding agents must honor these principles to ensure secure, maintainable, and professional code output.**

---

*This manual synthesizes industry best practices, security standards (OWASP, NIST), modern development workflows, and real-world lessons learned from production systems. It is living documentation - update as practices evolve.*
