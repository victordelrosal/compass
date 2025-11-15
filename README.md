# 🧭 COMPASS: Coding Operations Manual & Protocol for AI System Standards

I've been coding with AI agents (mostly Claude Code, ChatGPT Codex, GitHub Copilot) and while I think they're fantastic (almost magical) I've seen a number of issues: AI tools can over-engineer, reinvent established patterns, hallucinate dependencies or APIs, skip input validation, create monolithic codebases, ignore error handling, bypass testing, hardcode secrets, skip null checks, and crucially, lead to a range of security risks.

So I was wondering: **what might be the equivalent of having a very senior engineer always guiding the AI coding agent?**

To answer this I curated well-established software engineering frameworks into a single resource: **COMPASS** (Coding Operations Manual & Protocol for AI System Standards). It's my small (hopefully useful) open source contribution to the growing community of AI-augmented coders.

Think of it as **"true north" for AI coding agents** based on best practice to help keep code secure, predictable, and maintainable, drawing from well-established software engineering guidelines and protocols.

---

## What is COMPASS?

**COMPASS anchors AI agents in the engineering discipline human developers already trust.**

It draws from battle-tested standards:
- **OWASP** for secure-by-default design
- **SOLID** for architecture
- **Test-Driven Development** for reliability
- **YAGNI** for simplicity
- **Defensive programming** for robustness
- **Systematic debugging protocols** for problem-solving

COMPASS is the accumulated wisdom of modern software engineering in one framework—giving AI assistants the same foundation that professional developers rely on.

**One source of truth:** COMPASS-MINI.md
**Deployment:** Via each tool's native configuration format

---

## Why COMPASS?

AI coding agents are powerful but can be inconsistent. Without clear standards, they may:

- ❌ Generate code with security vulnerabilities
- ❌ Skip tests or create inadequate test coverage
- ❌ Hallucinate dependencies that don't exist
- ❌ Over-engineer simple solutions
- ❌ Modify data destructively without backups
- ❌ Ignore existing codebase patterns
- ❌ Hardcode secrets into version control
- ❌ Create monolithic components

**COMPASS solves these problems** by providing:

✅ **Security-first principles** — OWASP Top 10 compliance, input validation, secrets management
✅ **Test-driven workflows** — Mandatory TDD, 70%+ coverage, systematic debugging
✅ **Quality standards** — SOLID principles, clean architecture, maintainable code
✅ **AI-specific guidance** — Counter-instructions for common AI failure modes
✅ **Professional workflows** — Git protocols, code review checklists, task management
✅ **Lightweight design** — COMPASS-MINI.md is just ~1,200 tokens

---

## Quick Start

### Installation (The Easy Way)

**For Claude Code, Cursor, or Windsurf** — Just talk to your AI:

```
I want to add COMPASS coding standards that load automatically.

The COMPASS-MINI.md file is at: /path/to/COMPASS-MINI.md

Please set it up so it loads in every session.
```

Your AI assistant will handle all the configuration!

**For other tools** — See the [Universal Installation Guide](INSTALL-UNIVERSAL.md) for:
- GitHub Copilot
- Aider
- Continue.dev
- Any other AI coding tool

### What Gets Installed

**COMPASS-MINI.md** (~1,200 tokens) loads automatically and provides:
- Security requirements (OWASP-based)
- Data operation safety protocols
- Testing requirements (TDD workflow)
- Git protocols
- Core development principles (YAGNI, minimal changes, etc.)

This tiny file becomes your AI's persistent "senior engineer" guidance throughout every coding session.

---

## How It Works

### For AI Agents

COMPASS provides clear, actionable instructions that transform AI behavior:

**Before COMPASS:**
- AI might skip input validation
- Tests written after code (if at all)
- Secrets might get committed
- Over-engineered solutions
- Hallucinated dependencies

**With COMPASS:**
- Input validation is mandatory and non-negotiable
- Tests written first (TDD workflow)
- Secrets never committed, .env files properly ignored
- Minimal changes, YAGNI principle enforced
- Dependencies verified before use

### For Development Teams

COMPASS works alongside project-specific guidelines:

```
your-project/
├── COMPASS-MINI.md          # Universal engineering standards
├── .cursor/rules/           # Project-specific Cursor rules
├── .github/
│   └── copilot-instructions.md  # Project setup for Copilot
└── README.md               # Project documentation
```

**COMPASS = Base layer of universal standards**
**Project files = Your specific conventions**

---

## Supported AI Tools

COMPASS works with all major AI coding assistants:

| Tool | Installation Method | Docs |
|------|-------------------|------|
| **Claude Code** | Global SessionStart hook | [INSTALL.md](INSTALL.md) |
| **Cursor** | .cursor/rules/*.mdc | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |
| **GitHub Copilot** | .github/copilot-instructions.md | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |
| **Windsurf** | .windsurfrules | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |
| **Aider** | .aider.conf.yml | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |
| **Continue.dev** | ~/.continue/config.yaml | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |
| **ChatGPT / Codex** | Custom instructions | [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) |

**See [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md)** for complete installation instructions for all tools.

---

## Core Principles

COMPASS is built on **mandatory, non-negotiable standards**:

### 🔒 Security (NON-NEGOTIABLE)

**Input Validation:**
- ✅ ALWAYS validate ALL inputs with allowlist validation
- ✅ ALWAYS use parameterized queries/prepared statements
- ❌ NEVER concatenate user input into queries or commands

**Authentication & Secrets:**
- ✅ ALWAYS use proven auth libraries (never roll your own)
- ✅ ALWAYS verify authorization for protected operations
- ❌ NEVER commit secrets to version control
- ❌ NEVER log passwords, tokens, or PII

**Data Protection:**
- ✅ ALWAYS encrypt sensitive data at rest and in transit
- ✅ ALWAYS use TLS/HTTPS for all communications

### 🧪 Testing (REQUIRED)

**TDD Workflow:**
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

### 🏗️ Code Quality

**Core Principles:**
- ✅ Make SMALLEST reasonable change
- ✅ Match existing code style/patterns
- ❌ NEVER rewrite without explicit permission
- ❌ NEVER throw away working code
- ❌ NEVER add "helpful" functionality not requested (YAGNI)

### ⚠️ When to STOP and ASK

**ALWAYS stop and ask user when:**
- Data operation might cause data loss
- Multiple valid approaches exist (choice matters)
- Action would delete/restructure existing code
- You don't understand what's being asked
- Security implications unclear
- Encountering unfamiliar code/patterns

### 📋 Task Management

**MUST use TodoWrite for:**
- Complex multi-step tasks (3+ steps)
- Multiple user requests
- Tracking progress throughout session

**Protocol:**
1. Create todos BEFORE starting
2. Mark in_progress BEFORE beginning (only ONE at a time)
3. Mark completed IMMEDIATELY after finishing

### 🔧 Git Protocol

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

---

## Real-World Impact

### What COMPASS Prevents

**Security Issues:**
- SQL injection via concatenated queries
- Hardcoded API keys in source code
- Missing input validation
- Weak password hashing (MD5, SHA1)
- Missing authentication on sensitive endpoints

**Data Loss:**
- Destructive schema changes without backups
- Missing rollback plans
- Simultaneous multiple changes (hard to debug)
- Missing edge case validation

**Quality Issues:**
- Over-engineered abstractions for "future flexibility"
- Hallucinated dependencies that don't exist
- Tests written after code (or not at all)
- Monolithic components (500+ lines)
- Unclear naming with implementation details

**Workflow Issues:**
- Vague commit messages
- Force pushing to main branch
- Skipping pre-commit hooks
- No task tracking for complex work

### What COMPASS Enables

**Confidence:**
- AI follows proven engineering standards
- Security is baked in from the start
- Tests exist before code is written

**Maintainability:**
- Minimal changes, clear patterns
- Code matches existing conventions
- Proper documentation and naming

**Speed:**
- Less rework from security/quality issues
- Systematic debugging saves time
- Clear protocols reduce decision paralysis

---

## Example Workflow

### Adding a New Feature with COMPASS

**User:** "Add user registration with email verification"

**AI with COMPASS:**

1. **Creates todos** (task management protocol)
   - Design user registration endpoint
   - Write failing tests for registration
   - Implement registration logic
   - Add email verification flow
   - Test email verification

2. **Writes failing test first** (TDD protocol)
   ```python
   def test_user_registration_with_email():
       response = client.post('/register', json={
           'email': 'test@example.com',
           'password': 'SecurePass123!'
       })
       assert response.status_code == 201
       # Test fails - endpoint doesn't exist yet
   ```

3. **Implements with security first** (OWASP protocol)
   - Uses parameterized queries (no SQL injection)
   - Validates email with allowlist pattern
   - Hashes password with bcrypt
   - Stores secrets in environment variables
   - Adds rate limiting

4. **Confirms test passes** (TDD protocol)
   - Runs test suite
   - Confirms 70%+ coverage
   - Ensures pristine output

5. **Commits professionally** (Git protocol)
   ```bash
   git add tests/test_registration.py src/auth/registration.py
   git commit -m "feat: Add user registration with email verification

   - Implement POST /register endpoint
   - Add bcrypt password hashing
   - Include email validation
   - Add rate limiting (100 req/hour)

   Tests: 75% coverage
   Security: Input validation, parameterized queries
   ```

6. **Asks before proceeding** (safety protocol)
   - "Should I also add password reset functionality, or handle that separately?"

---

## Benefits

### For Organizations
- **Standardized AI output** — Consistent quality across all AI-generated code
- **Reduced security risk** — OWASP Top 10 compliance by default
- **Lower technical debt** — Code meets quality standards from the start
- **Faster onboarding** — New developers and AI agents follow same standards
- **Audit compliance** — Documented standards for regulatory requirements

### For Developers
- **Higher code quality** — SOLID principles, clean architecture, maintainability
- **Better test coverage** — TDD workflow ensures 70%+ coverage minimum
- **Fewer bugs** — Systematic debugging and root cause analysis
- **Professional workflows** — Git protocols, task tracking, verification gates
- **Confidence** — AI won't introduce security vulnerabilities or data loss

### For AI Agents
- **Clear operating guidelines** — No ambiguity about what "good code" means
- **Failure mode prevention** — Counter-instructions for common AI mistakes
- **Quality feedback loop** — Verification requirements before completion claims
- **Lightweight** — COMPASS-MINI.md is just ~1,200 tokens

---

## Technology Agnostic

COMPASS principles apply to **any programming language, framework, or tech stack**:

- **Languages:** Python, JavaScript/TypeScript, Java, Go, Rust, Ruby, PHP, C#, etc.
- **Frameworks:** React, Vue, Angular, Django, Flask, FastAPI, Spring Boot, .NET, etc.
- **Databases:** PostgreSQL, MySQL, MongoDB, Redis, etc.
- **Platforms:** Web, mobile, desktop, embedded, cloud, on-premise

The principles are **universal professional standards** that transcend specific technologies.

---

## Installation Guides

- **[INSTALL.md](INSTALL.md)** — Claude Code installation (easy setup)
- **[INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md)** — All AI tools (GitHub Copilot, Cursor, Windsurf, Aider, Continue.dev)

---

## Files & Documentation

### Core Files
- **[COMPASS-MINI.md](COMPASS-MINI.md)** — Lightweight version (~1,200 tokens) for persistent context
- **COMPASS.md** — Full reference manual (coming soon)

### Installation Guides
- **[INSTALL.md](INSTALL.md)** — Claude Code setup
- **[INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md)** — Universal installation for all AI tools

### Specialized Modules (Coming Soon)
- **COMPASS-SECURITY.md** — Deep dive into OWASP, authentication, encryption
- **COMPASS-TESTING.md** — TDD workflows, debugging, root cause analysis
- **COMPASS-QUALITY.md** — SOLID principles, clean architecture, refactoring
- **COMPASS-AI.md** — AI-specific behaviors and counter-instructions

---

## Contributing

We welcome contributions to improve COMPASS! Here's how you can help:

### Ways to Contribute
- **Report issues** — Found something unclear or incorrect? [Open an issue](https://github.com/victordelrosal/COMPASS/issues)
- **Suggest improvements** — Ideas for new principles or better workflows
- **Share examples** — Real-world use cases and success stories
- **Add integrations** — Guides for additional AI coding platforms
- **Improve documentation** — Clarify explanations, fix typos, add examples

### Contribution Guidelines
1. Read [COMPASS-MINI.md](COMPASS-MINI.md) to understand the philosophy
2. Open an issue to discuss significant changes before submitting
3. Follow the existing structure and tone
4. Include examples and rationale for new principles
5. Test integrations with actual AI coding agents before submitting

---

## Acknowledgments

COMPASS is built on decades of software engineering best practices from:

- **OWASP** — Security standards and vulnerability prevention
- **SOLID Principles** — Object-oriented design by Robert C. Martin
- **Test-Driven Development** — Kent Beck, Martin Fowler
- **Clean Code** — Robert C. Martin
- **The Pragmatic Programmer** — Andy Hunt, Dave Thomas
- **Defensive Programming** — Steve McConnell, Code Complete
- **AI Safety Research** — Anthropic, OpenAI, and the AI safety community

---

## Support & Community

- **Repository:** [https://github.com/victordelrosal/COMPASS](https://github.com/victordelrosal/COMPASS)
- **Issues:** [GitHub Issues](https://github.com/victordelrosal/COMPASS/issues)
- **Discussions:** [GitHub Discussions](https://github.com/victordelrosal/COMPASS/discussions)

---

## License

MIT License - See [LICENSE](LICENSE) file for details

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│ 🧭 COMPASS: True North for AI Coding Agents                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ 🔒 Security First         → OWASP Top 10, input validation │
│ 🧪 Test-Driven           → 70%+ coverage, TDD workflow     │
│ 🏗️  Quality Code          → SOLID, minimal changes, YAGNI │
│ ⚠️  Stop & Ask            → Data loss, uncertainty, choice │
│ ⚙️  Professional Workflow → Git, tasks, verification       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ One Source: COMPASS-MINI.md (~1,200 tokens)                │
│ Deployment: Each tool's native format                      │
│ Philosophy: Battle-tested engineering standards            │
└─────────────────────────────────────────────────────────────┘
```

---

**Transform your AI coding assistant from helpful to professional with COMPASS.**

**Get started:** See [INSTALL-UNIVERSAL.md](INSTALL-UNIVERSAL.md) for your AI tool

**Questions?** Open an [issue](https://github.com/victordelrosal/COMPASS/issues) or start a [discussion](https://github.com/victordelrosal/COMPASS/discussions)

Let me know if it improves your workflow or if you have any suggestions!
