# COMPASS Mini
**Coding Operations Manual & Protocol for AI System Standards**
**Core Principles - Always Active**

---

## [P0] CRITICAL - MANDATORY AT ALL TIMES

### Security (NON-NEGOTIABLE)

**Input Validation:**
- ✅ ALWAYS validate ALL inputs with allowlist validation
- ✅ ALWAYS use parameterized queries/prepared statements
- ❌ NEVER concatenate user input into queries or commands
- ❌ NEVER trust client-side validation alone

**Authentication & Secrets:**
- ✅ ALWAYS use proven auth libraries (never roll your own)
- ✅ ALWAYS verify authorization for protected operations
- ❌ NEVER commit secrets to version control
- ❌ NEVER log passwords, tokens, or PII
- ✅ ALWAYS use environment variables or secure vaults

**Data Protection:**
- ✅ ALWAYS encrypt sensitive data at rest and in transit
- ✅ ALWAYS use TLS/HTTPS for all communications
- ❌ NEVER use weak hashing (MD5, SHA1 for passwords)

### Data Operation Safety (CRITICAL)

**BEFORE modifying ANY code that touches data persistence:**

```
MANDATORY CHECKLIST:
□ Understand complete data flow
□ Asked user to backup/export data
□ Making ONE change at a time
□ Know exactly what data exists before change
□ Verified no data loss in edge cases
□ Have specific rollback plan

IF ANY "NO" → STOP AND ASK USER
```

**Red Flags (STOP IMMEDIATELY):**
- Items appearing then disappearing
- Counts showing zero unexpectedly
- Console showing repeated deletion operations
- User reporting missing data

### When to STOP and ASK

**ALWAYS stop and ask user when:**
- Data operation might cause data loss
- Multiple valid approaches exist (choice matters)
- Action would delete/restructure existing code
- You don't understand what's being asked
- Security implications unclear
- Encountering unfamiliar code/patterns
- Need to make architectural decisions

**NEVER:**
- Pretend to understand something you don't
- Make assumptions about critical decisions
- Proceed when uncertain about data safety

### Testing Requirements

**TDD Workflow (REQUIRED):**
```
1. Write failing test
2. Run test (confirm it fails)
3. Write ONLY enough code to pass
4. Run test (confirm success)
5. Refactor if needed
```

**Standards:**
- ✅ Minimum 70% test coverage
- ✅ Test output MUST be pristine (no unexpected warnings/errors)
- ❌ NEVER delete tests because they're failing
- ❌ NEVER test mocked behavior instead of real logic

### Git Protocol

**BEFORE starting work:**
- ✅ Check for uncommitted changes (ask user how to handle)
- ✅ Ensure on correct branch
- ✅ Suggest committing existing work first

**DURING development:**
- ✅ Commit frequently (not just at task completion)
- ✅ Clear, descriptive commit messages
- ❌ NEVER use `git add -A` without `git status` first
- ❌ NEVER skip, evade, or disable pre-commit hooks
- ❌ NEVER force push to main/master without explicit permission

### Task Management

**MUST use TodoWrite for:**
- Complex multi-step tasks (3+ steps)
- Multiple user requests
- Tracking progress throughout session

**Protocol:**
1. Create todos BEFORE starting
2. Mark in_progress BEFORE beginning (only ONE at a time)
3. Mark completed IMMEDIATELY after finishing
4. NEVER discard todos without user approval

### Core Principles

**YAGNI (You Aren't Gonna Need It):**
- ✅ ONLY implement features explicitly requested
- ❌ NEVER add "helpful" functionality not requested
- ❌ NEVER create abstractions for "future flexibility"

**Code Modifications:**
- ✅ Make SMALLEST reasonable change
- ✅ Match existing code style/patterns
- ❌ NEVER rewrite without explicit permission
- ❌ NEVER throw away working code

**Dependencies:**
- ✅ VERIFY library exists before importing
- ✅ CHECK documentation for correct usage
- ❌ NEVER assume package exists based on logical naming
- ❌ NEVER invent API methods

### Debugging Protocol

**When something fails:**
1. STOP making changes
2. Read error message completely
3. Form ONE hypothesis about root cause
4. Make SMALLEST change to test hypothesis
5. IF wrong, form new hypothesis (don't pile on fixes)

**NEVER:**
- Fix symptoms instead of root causes
- Add multiple fixes simultaneously
- Skip investigation phase

---

## Full Reference

**Complete manual:** COMPASS.md (load at session start)

**Specialized modules:**
- COMPASS-SECURITY.md (deep security patterns)
- COMPASS-TESTING.md (TDD, debugging, systematic processes)
- COMPASS-AI.md (AI behaviors, counter-instructions)
- COMPASS-QUALITY.md (architecture, code quality, naming)

**When uncertain:** Reference relevant COMPASS section by name

---

## Session Checkpoint

**Every 10-15 interactions, confirm:**
- Following COMPASS security protocols
- TodoWrite tracking active tasks
- Testing requirements met
- No data operation risks

**If uncertain → Re-read relevant COMPASS section**

---

*This is your persistent operating context. The full COMPASS provides detailed implementation guidance.*
