# COMPASS Integration Guide
**How to Integrate COMPASS into Your AI Coding Workflow**

Part of COMPASS: Coding Operations Manual & Protocol for AI System Standards

---

## Overview

This guide explains how to integrate COMPASS into various AI coding platforms (Claude Code, Cursor, GitHub Copilot, ChatGPT, etc.) to ensure AI agents consistently follow professional coding standards.

---

## I. The COMPASS Architecture

### File Structure

```
your-project/
├── COMPASS.md              # Full manual (~4000 tokens)
├── COMPASS-MINI.md         # Core principles (~400 tokens) [ALWAYS LOAD]
├── COMPASS-SECURITY.md     # Security deep dive
├── COMPASS-TESTING.md      # TDD & debugging
├── COMPASS-AI.md           # AI behaviors
├── COMPASS-QUALITY.md      # Code quality & architecture
└── .claude/                # Claude Code specific
    └── (or .cursorrules, .github/copilot-instructions.md, etc.)
```

### Loading Strategy

**Three-Tier System:**

1. **COMPASS-MINI** (400 tokens) - ALWAYS in memory
   - Loaded as system prompt / permanent context
   - Contains P0 critical rules only
   - Never unloaded

2. **COMPASS** (4000 tokens) - Session initialization
   - Loaded at start of each session
   - Provides complete reference
   - Agent refers back by name when needed

3. **COMPASS-[MODULE]** (500-800 tokens each) - On-demand
   - Loaded when working on specific areas
   - Security → Load COMPASS-SECURITY
   - Testing → Load COMPASS-TESTING
   - etc.

---

## II. Platform-Specific Integration

### Claude Code

**Location:** `CLAUDE.md` in project root or `~/.claude/`

**Option A: Merge with Existing CLAUDE.md**
```markdown
# Your Existing CLAUDE.md Rules

[... your existing rules ...]

---

# COMPASS Core Operating Principles

[Paste contents of COMPASS-MINI.md here]

---

Full COMPASS manual: Load COMPASS.md at session start
Modules: COMPASS-[security|testing|ai|quality].md as needed
```

**Option B: Reference COMPASS Files**
```markdown
# Your Existing CLAUDE.md Rules

[... your existing rules ...]

---

# Operating Standards

This project follows COMPASS (Coding Operations Manual & Protocol for AI System Standards).

**MANDATORY:** At session start, read COMPASS.md completely.
**ALWAYS:** Follow COMPASS-MINI.md principles (included below).
**AS NEEDED:** Load COMPASS-[module].md for specialized guidance.

[Paste contents of COMPASS-MINI.md here]
```

**Session Initialization:**
```
User starts session →
AI auto-loads CLAUDE.md (with COMPASS-MINI embedded) →
User or AI: "Load COMPASS.md for this session" →
AI reads full COMPASS →
Ready to code with full COMPASS context
```

### Cursor

**Location:** `.cursorrules` file in project root

**Integration:**
```markdown
# Cursor Rules for [Your Project]

## COMPASS Operating Standards

This project follows COMPASS (Coding Operations Manual & Protocol for AI System Standards).

[Paste contents of COMPASS-MINI.md here]

---

**Full reference:** See COMPASS.md in project root
**Specialized modules:** COMPASS-[security|testing|ai|quality].md

When starting complex work:
1. Reference COMPASS.md Section [relevant section]
2. Load COMPASS-[MODULE].md if needed
3. Follow systematic processes outlined in COMPASS-TESTING.md

## Project-Specific Rules

[Your project-specific rules here]
```

**Usage:**
- Cursor auto-loads .cursorrules at session start
- COMPASS-MINI principles always active
- Reference full COMPASS by name: "Check COMPASS.md Section II.4"
- Load modules: "Load COMPASS-SECURITY.md for this auth implementation"

### GitHub Copilot

**Location:** `.github/copilot-instructions.md`

**Integration:**
```markdown
# Copilot Instructions for [Your Project]

## COMPASS Operating Standards

[Paste contents of COMPASS-MINI.md here]

Complete manual: COMPASS.md
Modules: COMPASS-[security|testing|ai|quality].md

Reference these files when:
- Implementing security features → COMPASS-SECURITY.md
- Writing tests → COMPASS-TESTING.md
- Uncertain about approach → COMPASS-AI.md Section "When to Stop and Ask"
- Refactoring code → COMPASS-QUALITY.md

## Project Context

[Your project-specific context]
```

### ChatGPT / Custom GPTs

**Location:** System instructions / Configuration

**Integration:**
```markdown
You are an AI coding assistant operating under COMPASS standards.

[Paste contents of COMPASS-MINI.md here]

At the start of each session, the user will provide access to:
- COMPASS.md (full manual)
- COMPASS-[module].md files as needed

When user shares code or asks for implementation:
1. Apply COMPASS-MINI principles immediately
2. Reference COMPASS.md for detailed guidance
3. Load relevant modules for specialized work
4. Use TodoWrite for multi-step tasks

[Your additional instructions]
```

---

## III. Session Initialization Ritual

### For Users (Manual Start)

**Recommended Session Start:**
```
1. Start AI session (Claude Code, Cursor, etc.)

2. Initialize COMPASS:
   "This session operates under COMPASS protocol.
   Load COMPASS.md now for full context."

3. AI loads and confirms:
   "COMPASS loaded. Operating under:
   - Security-first requirements
   - TDD workflow
   - Data operation safety protocols
   - Systematic debugging
   Ready to begin."

4. Begin work with COMPASS active
```

### For AI Agents (Auto Start)

**When COMPASS-MINI is in system prompt:**
```
Session starts →
COMPASS-MINI automatically loaded →
AI behavior automatically follows P0 rules →
User can optionally load full COMPASS for complex work →
Modules loaded on-demand
```

**Confirmation Protocol:**
```
AI should confirm COMPASS activation:

"Session initialized with COMPASS protocol:
✓ Security validation active
✓ Data safety checks enabled
✓ TDD workflow required
✓ TodoWrite tracking available

Full manual: COMPASS.md
Modules: COMPASS-[security|testing|ai|quality].md

Ready to begin. What should we work on?"
```

---

## IV. Session Checkpoint System

### Periodic Reinforcement

**Every 10-15 interactions:**

```
AI self-check (internal):
□ Following COMPASS security protocols?
□ TodoWrite tracking active tasks?
□ Tests written for new features?
□ No data operation risks?
□ No over-engineering?
□ Dependencies verified?

If any NO → Correct course immediately
```

**Example Auto-Checkpoint:**
```
[After 15 interactions]

AI (internal check): "Haven't written tests for last 2 features.
COMPASS requires TDD. Need to address."

AI (to user): "I notice we've implemented two features without tests.
COMPASS requires TDD workflow. Should I:
1. Write tests for existing features now
2. Continue and write tests at end
3. Explain why tests weren't needed"
```

### Drift Detection

**Watch for these patterns:**

```
Warning signs of COMPASS drift:
- Multiple features without tests
- Skipped TodoWrite for complex task
- Unverified dependencies
- Over-engineered solutions
- Haven't committed in 30+ minutes
- Data operations without safety checks

When detected → Re-read relevant COMPASS section
```

---

## V. On-Demand Module Loading

### When to Load Modules

**COMPASS-SECURITY.md - Load when:**
- Implementing authentication/authorization
- Handling user input
- Working with sensitive data
- Adding API endpoints
- Processing uploads
- Example: "Load COMPASS-SECURITY.md for this auth implementation"

**COMPASS-TESTING.md - Load when:**
- Starting TDD workflow
- Debugging failing tests
- Writing test suite
- Investigating systematic bug
- Example: "Load COMPASS-TESTING.md for debugging protocol"

**COMPASS-AI.md - Load when:**
- AI behavior seems off
- Need reminder about stopping conditions
- Adding dependencies
- Uncertain about approach
- Example: "Load COMPASS-AI.md Section III (When to Stop)"

**COMPASS-QUALITY.md - Load when:**
- Refactoring code
- Reviewing architecture
- Improving naming
- Extracting components
- Example: "Load COMPASS-QUALITY.md for refactoring guidance"

### Loading Syntax

**Explicit Loading:**
```
User: "Load COMPASS-SECURITY.md Section II (Input Validation)"
AI: [Reads section, applies guidance]

User: "Reference COMPASS-TESTING.md Phase 3"
AI: [Reads Phase 3, follows hypothesis testing protocol]
```

**Contextual Loading:**
```
User: "Implement user authentication"
AI: "I'll load COMPASS-SECURITY.md for auth implementation requirements.

Per COMPASS-SECURITY Section 7:
- Use proven libraries (bcrypt/argon2)
- Implement rate limiting
- HttpOnly cookies for sessions
- MFA for sensitive operations

Proceeding with implementation..."
```

---

## VI. Token Budget Management

### Understanding Token Limits

**Typical Limits:**
- Claude Code: ~200k context window
- ChatGPT: ~128k context window
- Cursor: Varies by model
- Copilot: ~8k-32k (more limited)

**COMPASS Token Usage:**
- COMPASS-MINI: ~400 tokens (0.2% of 200k)
- COMPASS full: ~4,000 tokens (2% of 200k)
- Module: ~800 tokens each (0.4% of 200k)

**Total if all loaded:** ~6,600 tokens (3.3% of 200k context)
**Leaves:** 193k+ tokens for code, conversation, files

### Optimization Strategies

**Strategy 1: Minimal Loading**
```
Always loaded: COMPASS-MINI (400 tokens)
Load on demand: Full COMPASS + specific module
Total active: 400-5200 tokens depending on task
```

**Strategy 2: Session-Based Loading**
```
Session start: Load COMPASS-MINI + COMPASS (4400 tokens)
During work: Load modules as needed (up to +800 tokens each)
Long session: May need to refresh by summarizing and reloading
```

**Strategy 3: Ultra-Conservative (Low-Token Environments)**
```
Only load: COMPASS-MINI (400 tokens)
Reference: Specific sections of full COMPASS by reading them explicitly
Never: Load multiple modules simultaneously
```

---

## VII. Maintenance & Updates

### Keeping COMPASS Current

**When to Update:**
- Discovered new security vulnerability patterns
- Team adopts new architectural patterns
- AI shows new failure modes
- Project requirements change
- Framework/tool updates

**Update Process:**
```
1. Edit relevant COMPASS file(s)
2. Update version number in header
3. Add entry to COMPASS-CHANGELOG.md
4. Commit with clear message
5. Notify team of changes
6. Have AI reload in next session
```

### Version Control

**Treat COMPASS as Code:**
```bash
git add COMPASS*.md
git commit -m "Update COMPASS-SECURITY: Add rate limiting requirements"
git push
```

**Track Changes:**
```markdown
# COMPASS-CHANGELOG.md

## v2.1 - 2025-11-15
- Added rate limiting requirements to COMPASS-SECURITY.md
- Clarified TDD workflow in COMPASS-TESTING.md
- Fixed typo in COMPASS-MINI.md

## v2.0 - 2025-11-14
- Initial modular release
- Split into MINI + specialized modules
```

---

## VIII. Team Integration

### Onboarding New Team Members

**Share COMPASS:**
```markdown
# Welcome to the Team!

We use COMPASS (Coding Operations Manual & Protocol for AI System Standards)
for all AI-assisted coding.

**Quick Start:**
1. Read COMPASS-MINI.md (5 minutes)
2. Skim COMPASS.md (20 minutes)
3. Integrate into your AI tool (see COMPASS-INTEGRATION.md)
4. Start coding with COMPASS protocol

**Key Principles:**
- Security first (validate all inputs)
- TDD workflow (test → code → refactor)
- Data safety (understand before modifying)
- YAGNI (simplest solution that works)
```

### Consistent Standards

**Across Team:**
- Everyone uses same COMPASS files
- AI agents follow same standards
- Code reviews reference COMPASS sections
- New patterns get added to COMPASS

**Example Code Review:**
```
"This input validation doesn't follow COMPASS-SECURITY.md Section 3.
Please update to use allowlist validation with parameterized queries."
```

---

## IX. Troubleshooting

### AI Not Following COMPASS

**Problem:** AI ignores COMPASS rules

**Solutions:**
1. Confirm COMPASS-MINI in system prompt/config
2. Explicitly load COMPASS.md at session start
3. Reference specific sections: "Per COMPASS-MINI, data operations require checklist"
4. Reset session and reload COMPASS
5. Check token budget (may be deprioritizing COMPASS)

### Token Budget Issues

**Problem:** Context window filling up

**Solutions:**
1. Use COMPASS-MINI only (400 tokens vs 4000)
2. Load specific sections instead of full files
3. Summarize conversation history periodically
4. Start fresh session with COMPASS reloaded

### Conflicting Rules

**Problem:** Project rules conflict with COMPASS

**Solutions:**
1. Update COMPASS to match project needs
2. Add project-specific exceptions in CLAUDE.md/.cursorrules
3. Document why exception is needed
4. Consider if project rule should change

---

## X. Examples & Templates

### Session Start Template

```
# New Session - COMPASS Protocol

User: "Starting new session. Load COMPASS.md protocol."

AI: "COMPASS protocol loaded and active.

Operating under:
✓ Security-first requirements (COMPASS-SECURITY)
✓ TDD workflow (COMPASS-TESTING)
✓ Data operation safety (COMPASS-MINI Section 2)
✓ YAGNI principle (COMPASS-QUALITY)
✓ Systematic debugging (COMPASS-TESTING Section VII)

Ready to begin with full COMPASS context.
What should we work on?"

User: [Describes task]

AI: [Creates todos, begins work following COMPASS principles]
```

### Module Loading Template

```
User: "Implement user registration with email verification"

AI: "I'll load COMPASS-SECURITY.md for auth implementation guidance.

Per COMPASS-SECURITY:
- Use bcrypt for password hashing (Section 2.2)
- Validate email format (Section 3.1)
- Implement rate limiting on registration (Section 4.1)
- Store no plaintext passwords (Section 2.2)
- Use email verification tokens (Section 7.4)

Creating todos:
1. Create user registration endpoint with validation
2. Add password hashing with bcrypt
3. Implement email verification flow
4. Add rate limiting
5. Write tests for all paths

Proceeding..."
```

---

## Summary

COMPASS integration requires:
1. **COMPASS-MINI in system prompt** (always loaded)
2. **Full COMPASS at session start** (reference document)
3. **Modules loaded on-demand** (specialized work)
4. **Periodic checkpoints** (prevent drift)
5. **Version control** (treat as code)

**Result:** AI agents that consistently produce secure, tested, maintainable code following professional standards.

---

*For complete standards, see COMPASS.md*
*For platform-specific details, consult your AI tool's documentation*
