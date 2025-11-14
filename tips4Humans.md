# Tips for Humans: How to Talk to AI Coding Assistants

## Overview
Best practices for communicating with AI coding assistants (Claude Code, GitHub Copilot, Cursor, etc.) to get the best results. Based on real-world experience building the flux project.

## The Golden Rule

**AI assistants are like really smart interns: They know a lot, but they need clear direction and context.**

## Core Communication Principles

### 1. Be Specific, Not Vague

```
❌ BAD: "Fix the favicon"
✅ GOOD: "The favicon.png is not showing in Chrome on MacBook. It works on iPhone. The file is in /public directory and is 256x256 PNG. Help me fix it for desktop browsers."
```

**Why**: Specificity gives the AI actual problems to solve, not guesses to make.

### 2. Provide Context

```
❌ BAD: "Add authentication"
✅ GOOD: "Add authentication to this Next.js 15 app. We're using Supabase for the backend. Users should be redirected to /login if not authenticated. Protect all routes except /login and /auth/callback."
```

**What to include**:
- Framework/library versions
- What you've already tried
- What's not working
- Related files or code
- Expected vs actual behavior

### 3. One Task at a Time

```
❌ BAD: "Add dark mode, fix the navbar, implement search, and optimize images"
✅ GOOD: "Add dark mode toggle to the settings page. Use Tailwind's dark mode with localStorage persistence."
```

**Why**: Multiple tasks lead to incomplete implementations. Finish one thing well, then move to the next.

### 4. Correct Politely But Firmly

```
❌ BAD: "No that's wrong"
✅ GOOD: "That didn't work. The favicon is still not showing. Can you research the Next.js 15 app router documentation for the correct favicon placement?"
```

**Remember**: AI can look things up! If it's not working, ask it to research or check documentation.

### 5. Say When You're Stuck

```
✅ "I've tried placing the favicon in /public and /app, cleared cache, hard refreshed, and it still shows the Vercel logo. Can you help troubleshoot?"
```

**Why**: Admitting you're stuck helps the AI understand you've exhausted obvious solutions.

## Advanced Communication Patterns

### Pattern 1: The "Show, Don't Just Tell" Approach

When describing a bug:

```
✅ GOOD:
"The video grid is not respecting master volume. Here's what happens:
1. Set master volume to 50%
2. Set video 1 to 80%
3. Actual volume: 80% (should be 40% = 80% × 50%)

Expected: All videos should scale proportionally with master volume
Actual: Videos play at their individual volume, ignoring master

Relevant code is in Core.tsx lines 200-250."
```

**What this includes**:
- Steps to reproduce
- Expected behavior
- Actual behavior
- File/line references

### Pattern 2: The "I've Read the Error" Approach

```
✅ GOOD:
"Getting this TypeScript error:
```
Type 'string | undefined' is not assignable to type 'string'
  at app/components/VideoGrid.tsx:45
```

This is happening when I try to pass `video.id` to the YouTube player. The `id` field is optional in my type definition, but it should always exist at runtime. Should I:
1. Add a runtime check, or
2. Update the type definition to make it required?"
```

**Why**: Showing you've read the error demonstrates you're engaged, and giving options helps the AI understand your thinking.

### Pattern 3: The "Teach Me Why" Approach

```
✅ GOOD:
"You suggested placing the favicon in /app instead of /public for Next.js 15. That fixed it! Can you explain why Next.js 15's app router requires favicons in /app? I want to understand the reasoning."
```

**Why**: Understanding the "why" helps you apply the pattern elsewhere.

### Pattern 4: The "Hold Up" Approach

```
✅ GOOD:
"Wait, before you make that change - won't that break the mobile layout? Let me verify the responsive behavior first."

or

"Actually, let's not do that yet. I need to commit the current working version first in case we need to rollback."
```

**Why**: You're the project owner. It's okay to pause and think.

### Pattern 5: The "Let's Plan First" Approach

```
✅ GOOD:
"I want to refactor this 5,000-line component into smaller pieces. Don't make any changes yet. First, help me:
1. Identify extraction candidates
2. Create a plan
3. Prioritize by risk
4. Then we'll execute one at a time with my approval"
```

**Why**: Planning prevents breaking changes. AI should help you think, not just execute.

## What AI Assistants Are Great At

### ✅ Excellent

1. **Boilerplate code**: "Create a TypeScript interface for a user profile with name, email, and avatar"
2. **Following patterns**: "Create another adapter for todo items following the same pattern as stickyNotesAdapter"
3. **Searching documentation**: "Look up the Next.js 15 app router favicon conventions"
4. **Debugging errors**: "This useEffect is causing infinite renders. Here's the code..."
5. **Code explanations**: "Explain what this PBKDF2 function does line by line"
6. **Refactoring**: "Extract this 200-line JSX block into a separate component"
7. **Testing**: "Write tests for this encryption utility function"
8. **Finding files**: "Find all components that use the `useStickyNotes` hook"

### ⚠️ Use With Caution

1. **Architecture decisions**: AI can suggest, but YOU decide
2. **Security implementations**: Always verify security code independently
3. **Performance optimization**: Profile first, optimize second (don't trust AI's guesses)
4. **Design decisions**: AI doesn't know your users

### ❌ Don't Rely On AI For

1. **Understanding your users**: Only you know what they need
2. **Business logic**: AI doesn't understand your domain
3. **Final security audit**: Use human security experts
4. **Accessibility testing**: Use real assistive technologies and users

## Communication Anti-Patterns

### Anti-Pattern 1: The Vague Question

```
❌ "Why doesn't this work?"
```

**Fix**: Describe what "this" is, what "work" means, and what's happening instead.

### Anti-Pattern 2: The Assumption Dump

```
❌ "You know that thing we talked about yesterday? Change it."
```

**Fix**: AI has short-term memory within a session. Restate context.

### Anti-Pattern 3: The Copy-Paste Disaster

```
❌ *pastes 500 lines of code* "Fix it"
```

**Fix**: Point to specific files, describe the problem, and what you've tried.

### Anti-Pattern 4: The Silent Treatment

```
❌ *AI makes suggestion*
❌ *User goes silent for 10 minutes*
❌ *Comes back*: "That didn't work"
```

**Fix**: Provide feedback quickly. "Working on testing this..." or "Let me try it and get back to you."

### Anti-Pattern 5: The Moving Target

```
❌ "Add a login page"
❌ *AI creates login page*
❌ "Actually make it dark mode"
❌ *AI updates*
❌ "Wait I want Google OAuth too"
❌ *AI updates*
❌ "Also add password reset"
```

**Fix**: Plan your requirements FIRST. "I need a login page with: Google OAuth, dark mode, and password reset."

## Power User Tips

### Tip 1: Leverage File References

```
✅ "In app/components/Core.tsx, the handleVolumeChange function around line 250 needs to apply master volume. Can you update it?"
```

**Why**: Specific file+line references help AI find exact code.

### Tip 2: Use Examples

```
✅ "Create a custom hook for todos following the same pattern as hooks/useStickyNotes.ts - with debounced save, flush-on-unload, and real-time subscription."
```

**Why**: "Follow this pattern" is clearer than describing the pattern.

### Tip 3: Ask for Explanations

```
✅ "Before making that change, explain what it will do and what could break."
```

**Why**: Catch issues before implementation.

### Tip 4: Iterate in Steps

```
✅ "Let's do this in 3 steps:
1. First, create the adapter interface
2. Then implement localStorage fallback
3. Finally add Supabase sync
Test between each step."
```

**Why**: Isolates issues to specific changes.

### Tip 5: Use AI as a Rubber Duck

```
✅ "I'm trying to decide between two approaches:
Approach A: Store encrypted blobs in database
Approach B: Encrypt on Supabase side with pgcrypto

Help me think through the tradeoffs of each."
```

**Why**: Explaining helps YOU think through the problem.

### Tip 6: Ask About Alternatives

```
✅ "You suggested using localStorage. What are the alternatives and their tradeoffs?"
```

**Why**: AI might default to one solution, but others might be better for your case.

### Tip 7: Admit When You Don't Understand

```
✅ "I don't understand what 'row-level security' means. Can you explain it in simple terms before we implement it?"
```

**Why**: Better to understand before implementing than debug mysterious issues later.

### Tip 8: Request Documentation

```
✅ "After we finish this encryption implementation, create a markdown doc explaining:
- How it works
- How to use it
- Common issues
- How to migrate existing data"
```

**Why**: Future you (and your team) will thank present you.

## Session Management

### Start of Session

```
✅ GOOD start:
"I'm working on the flux project - a Next.js 15 app with Supabase backend. Today I need to implement real-time sync for sticky notes. The existing adapter is in app/lib/stickyNotesAdapter.ts."
```

### During Session

- Give feedback on suggestions: "That worked!" or "Got this error: ..."
- Ask for clarification: "Why did you use useCallback there?"
- Course correct: "Actually, let's go with approach B instead"
- Check understanding: "So if I understand correctly, this will..."

### End of Session

```
✅ "Thanks! Let's document what we did today:
1. Implemented real-time sync with device ID filtering
2. Added flush-on-unload to prevent data loss
3. Created tests for sync conflicts

Next session: Add encryption to the notes content"
```

**Why**: Helps next session (or next AI) pick up where you left off.

## When AI Gets It Wrong

### The AI Confidently Says Something Incorrect

```
✅ "I don't think that's right. Can you verify that against the Next.js 15 documentation?"
```

or

```
✅ "That doesn't match what I see in the official docs. Here's the link: [URL]. Can you update your answer?"
```

### The AI Solution Doesn't Work

```
✅ "That didn't work. Here's the error I got: [error]. Can you research this specific error and suggest alternatives?"
```

### The AI Misunderstood Your Request

```
✅ "I think there was a misunderstanding. What I meant was [restate request more clearly]. Can we try again?"
```

**Remember**: AI is a tool, not a coworker. It won't be offended if you correct it.

## Framework-Specific Tips

### For Claude Code / Cursor

- Use explicit file paths: `/app/components/VideoGrid.tsx`
- Reference line numbers when discussing specific code
- Ask it to read files: "Read the current implementation first"
- Use its tool features: "Search the codebase for usages of this function"

### For GitHub Copilot

- Write clear comment instructions above where you want code
- Use descriptive variable names (helps Copilot understand context)
- Accept suggestions partially - you can edit inline
- Use Copilot Chat for explanations, inline for generation

### For General LLM Coding Assistants

- Provide framework versions explicitly
- Share relevant error messages in full
- Describe file structure when relevant
- Ask for step-by-step plans for complex tasks

## Real-World Examples from Flux Project

### Example 1: The Favicon Saga

**Initial (vague)**:
```
❌ "The favicon isn't working"
```

**After iterations (specific)**:
```
✅ "The favicon.png is not showing in Chrome desktop browser on MacBook. It works perfectly on iPhone as an apple-touch-icon. The file is 256x256 PNG located in /public/favicon.png. I've tried:
- Clearing cache
- Hard refresh (Cmd+Shift+R)
- Restarting the dev server

Can you research the Next.js 15 app router favicon best practices?"
```

**Result**: AI researched, found that Next.js 15 requires favicons in `/app` directory, not `/public`. Problem solved!

**Lesson**: Keep adding context until the problem is solved. Don't give up after first attempt.

### Example 2: The Modularization Project

**Good approach used**:
```
✅ "I want to break down Core.tsx (5,594 lines) into smaller components. Let's NOT start coding yet. First:
1. Analyze the file and identify extraction candidates
2. Propose an extraction order (safest first)
3. Suggest line limits for each type of component
4. Create a validation script to enforce limits
Then we'll extract ONE component at a time with my approval."
```

**Result**: Successfully reduced to 2,201 lines (60% reduction) with zero breaking changes.

**Lesson**: Planning prevents disasters. Make AI help you think, not just code.

### Example 3: The Encryption Implementation

**Good approach used**:
```
✅ "I need client-side encryption for notes. Requirements:
- AES-256-GCM encryption
- Encrypt before sending to Supabase
- Support multiple devices (key sync)
- Backward compatible (key history)

Let's implement in this order:
1. Basic encrypt/decrypt utilities
2. Key management (generate, store, sync)
3. Integration with notes adapter
4. Migration for existing unencrypted notes
5. Error handling with helpful messages

Show me step 1 first."
```

**Result**: Robust encryption system with cross-device support, zero data loss.

**Lesson**: Break complex features into steps. Implement and test each step.

## Measuring Success

### You're Communicating Well If:
- ✅ AI understands your request on first try
- ✅ Suggested solutions work (or close to working)
- ✅ You can explain what the AI's code does
- ✅ You catch AI mistakes before implementing
- ✅ Sessions are productive, not frustrating

### You Need to Improve If:
- ❌ AI keeps misunderstanding you
- ❌ Solutions never work on first try
- ❌ You're copying code you don't understand
- ❌ You're fighting with AI instead of collaborating
- ❌ You feel frustrated more than productive

## The Ultimate Tip

**Treat AI like a junior developer on your team**:
- Give clear requirements
- Provide context
- Review their work
- Ask questions
- Teach them when they're wrong
- Learn from them when they're right
- Be patient but firm
- Document what you learn together

The AI is there to help YOU be more productive, not to replace YOUR thinking.

## Quick Reference Card

```
✅ DO:
- Be specific
- Provide context
- One task at a time
- Correct politely but firmly
- Ask "why"
- Plan before executing
- Test changes
- Document learnings

❌ DON'T:
- Be vague
- Dump code without explanation
- Change requirements mid-task
- Trust blindly
- Skip testing
- Assume AI knows your context
- Get frustrated - iterate instead
```

## Conclusion

The best AI interactions feel like pair programming with a knowledgeable colleague:
- You guide the direction
- AI handles the details
- You review and verify
- Together you build something great

The flux project was built with exactly this approach, and these communication patterns were learned through real experience. Use them, adapt them, and build amazing things!

---

*Last Updated: November 1, 2025*
*Based on: Real experience building flux with Claude Code*
*Result: 15,000+ lines of production code, zero major rewrites*

**Remember**: The quality of your results depends on the quality of your communication. Good communication with AI isn't about tricking it - it's about being clear, specific, and collaborative.
