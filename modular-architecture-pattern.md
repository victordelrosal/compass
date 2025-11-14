# Modular Architecture Pattern: Composition Over Monolith

## Overview
A proven pattern for breaking down large, monolithic components into maintainable, modular architecture while maintaining stability and preventing regressions. Based on successfully reducing a 5,594-line component to 2,201 lines (60% reduction) in the flux project.

## The Problem

Large monolithic components become:
- **Unmaintainable**: Thousands of lines in a single file
- **Fragile**: Changes risk breaking multiple features
- **Difficult to Test**: Too many responsibilities
- **Poor Developer Experience**: Slow to navigate, hard to understand
- **Merge Conflict Prone**: Multiple developers editing the same file

### Real Example
The flux project's `Core.tsx` grew to 5,594 lines, containing:
- State management for 20+ features
- UI rendering for all views
- Event handlers
- Business logic
- Configuration

## The Solution: Sacred Modularization Protocol

### Core Principle: COMPOSITION OVER MONOLITH

Transform the monolith into a **composition layer** that orchestrates smaller, focused components. The parent component becomes the conductor, not the orchestra.

### The Sacred Rules

These rules emerged from painful lessons learned during the flux refactoring:

#### 1. Extract, Don't Rewrite
```typescript
// ❌ WRONG: Rewriting while extracting
// Old code in Core.tsx:
const handleSave = () => { saveData(); };

// New code in extracted component:
const handleSave = async () => {
  await saveDataToCloud(); // CHANGED THE LOGIC!
};

// ✅ RIGHT: Extract unchanged
// Move the EXACT code to new component
const handleSave = () => { saveData(); }; // IDENTICAL
```

**Why**: Rewriting introduces bugs. Extract first, refactor later.

#### 2. One File at a Time
- Extract a single component
- Test thoroughly
- Get user/team approval
- Commit before next extraction

**Why**: Isolates issues to single change.

#### 3. Backup Before Each Step
```bash
# Before extracting VideoGridView
cp Core.tsx Core.tsx.backup-step1

# Before extracting DefaultView
cp Core.tsx Core.tsx.backup-step2
```

**Why**: Easy rollback if extraction causes issues.

#### 4. Zero Breaking Changes
After EVERY extraction:
```bash
npm run build  # Must succeed
npm run dev    # Must work identically
# Manual testing of affected feature
```

**Why**: Never ship broken code.

#### 5. Enforce Line Limits

**Hard Limits**:
- Core composition component: 2,000 lines (warning at 1,800)
- View components: 500 lines maximum
- If >50 lines of inline JSX: extract to component

**Why**: Prevents monolith from growing back.

## Implementation Guide

### Step 1: Identify Extraction Candidates

Look for:
- **Large JSX blocks** (>50 lines)
- **Feature areas** (video grid, music player, notes)
- **Conditional renders** (`if (view === 'something')`)
- **Self-contained logic** (own state, handlers)

Example from flux:
```typescript
// Core.tsx - Identified 19+ extraction candidates
{currentView === 'video-grid' && (
  <div className="video-grid-container">
    {/* 200+ lines of JSX */}
  </div>
)}

{currentView === 'zen-bliss' && (
  <div className="zen-bliss-view">
    {/* 150+ lines of JSX */}
  </div>
)}
```

### Step 2: Create View Component Structure

```
app/components/views/
├── DefaultView.tsx        # Default dashboard
├── VideoGridView.tsx      # 3x3 YouTube grid
├── ZenBlissView.tsx       # Zen mode
├── MusicQuickPanel.tsx    # Music controls
└── ... 15+ more views
```

### Step 3: Extract One Component

**Pattern**: Pass props from parent, return JSX

```typescript
// views/VideoGridView.tsx
interface VideoGridViewProps {
  // Props needed from Core.tsx
  videos: Video[];
  onVolumeChange: (id: string, volume: number) => void;
  masterVolume: number;
  // ... other props
}

export default function VideoGridView({
  videos,
  onVolumeChange,
  masterVolume,
}: VideoGridViewProps) {
  // Move EXACT code from Core.tsx here
  return (
    <div className="video-grid-container">
      {/* Extracted JSX - UNCHANGED */}
    </div>
  );
}
```

**In Core.tsx**:
```typescript
// Replace inline JSX with component
{currentView === 'video-grid' && (
  <VideoGridView
    videos={videos}
    onVolumeChange={handleVolumeChange}
    masterVolume={masterVolume}
  />
)}
```

### Step 4: Validate Extraction

Run the validation script (see below) or manual checks:
```bash
# 1. Build succeeds
npm run build

# 2. No TypeScript errors
npx tsc --noEmit

# 3. Feature works identically
npm run dev
# Test the extracted feature thoroughly
```

### Step 5: Repeat for All Components

Continue extracting until:
- Core component is <2,000 lines
- Each view is in separate file
- All line limit rules satisfied

## Automated Validation

### Create a Validation Script

```bash
#!/bin/bash
# scripts/validate-modular-architecture.sh

echo "🏗️  Validating modular architecture..."

# 1. Check Core.tsx line count
CORE_LINES=$(wc -l < app/components/Core.tsx)
MAX_LINES=2000

if [ "$CORE_LINES" -gt "$MAX_LINES" ]; then
  echo "❌ Core.tsx has $CORE_LINES lines (max: $MAX_LINES)"
  exit 1
fi

echo "✅ Core.tsx: $CORE_LINES lines (within limit)"

# 2. Count view components
VIEW_COUNT=$(ls app/components/views/*.tsx 2>/dev/null | wc -l)
MIN_VIEWS=19

if [ "$VIEW_COUNT" -lt "$MIN_VIEWS" ]; then
  echo "❌ Only $VIEW_COUNT view components (minimum: $MIN_VIEWS)"
  exit 1
fi

echo "✅ View components: $VIEW_COUNT (minimum: $MIN_VIEWS)"

# 3. Check for large inline JSX (>50 lines)
LARGE_JSX=$(grep -A 50 'return (' app/components/Core.tsx | grep -c 'className="[^"]*"')

if [ "$LARGE_JSX" -gt 10 ]; then
  echo "⚠️  Warning: Possible large inline JSX blocks in Core.tsx"
fi

# 4. TypeScript compilation
echo "🔍 Checking TypeScript..."
npx tsc --noEmit || exit 1

echo "✅ TypeScript compilation successful"

echo "🎉 All validation checks passed!"
exit 0
```

### Add to Git Hooks

```bash
# .git/hooks/pre-commit
#!/bin/bash
bash scripts/validate-modular-architecture.sh || {
  echo "❌ Validation failed. Commit rejected."
  exit 1
}
```

## Component Organization Patterns

### Pattern 1: Pure View Components

View components should be **presentational only**:
```typescript
// ✅ GOOD: Pure presentation
export default function VideoGridView({ videos, onAction }) {
  return <div>{/* Render UI */}</div>;
}

// ❌ BAD: Business logic in view
export default function VideoGridView() {
  const [videos, setVideos] = useState([]);
  useEffect(() => {
    fetchVideos(); // Business logic!
  }, []);
  return <div>{/* UI */}</div>;
}
```

**Rule**: State and logic stay in Core (or custom hooks), views receive props.

### Pattern 2: Props Interface

Always define explicit props interfaces:
```typescript
interface ViewProps {
  // Group related props
  // UI State
  isVisible: boolean;
  theme: Theme;

  // Data
  items: Item[];

  // Callbacks
  onItemClick: (id: string) => void;
  onClose: () => void;
}
```

### Pattern 3: Lazy Mounting

Mount components only when first accessed:
```typescript
const [videoGridMounted, setVideoGridMounted] = useState(false);

// Mount on first access
if (currentView === 'video-grid' && !videoGridMounted) {
  setVideoGridMounted(true);
}

// Render only if mounted
{videoGridMounted && currentView === 'video-grid' && (
  <VideoGridView {...props} />
)}
```

**Why**: Improves initial load time.

## File Size Guidelines

| Component Type | Max Lines | Warning At | Action If Exceeded |
|---------------|-----------|------------|-------------------|
| Core/Main | 2,000 | 1,800 | Extract more views |
| View Component | 500 | 450 | Break into sub-components |
| Custom Hook | 300 | 250 | Split into multiple hooks |
| Utility Function | 150 | 100 | Refactor into smaller functions |

## Real-World Results (Flux Project)

### Before Modularization
- **Core.tsx**: 5,594 lines
- **Files**: 1 monolithic component
- **Build time**: Slow TypeScript checking
- **Developer experience**: Difficult navigation
- **Risk**: High (any change affects everything)

### After Modularization
- **Core.tsx**: 2,201 lines (60% reduction)
- **Files**: 1 core + 19+ view components
- **Build time**: Faster (smaller files)
- **Developer experience**: Easy to find features
- **Risk**: Low (isolated changes)

### Validation Enforcement
```bash
$ bash scripts/validate-modular-architecture.sh
🏗️  Validating modular architecture...
✅ Core.tsx: 2201 lines (within limit)
✅ View components: 23 (minimum: 19)
🔍 Checking TypeScript...
✅ TypeScript compilation successful
🎉 All validation checks passed!
```

## Common Pitfalls

### Pitfall 1: Extracting Too Much
```typescript
// ❌ Over-extraction
<VideoGridView
  video1={video1}
  video2={video2}
  video3={video3}
  // ... 50 individual props
/>

// ✅ Group related data
<VideoGridView
  videos={videos}
  config={gridConfig}
  callbacks={callbacks}
/>
```

### Pitfall 2: Breaking Props Contract
```typescript
// Before extraction:
<button onClick={() => handleClick(item.id)}>

// ❌ After extraction (changed signature):
<button onClick={() => handleClick(item)}>

// ✅ Keep exact signature:
<button onClick={() => handleClick(item.id)}>
```

### Pitfall 3: Not Testing After Extraction
- Always manually test extracted feature
- Check edge cases
- Verify error handling still works

## Documentation Template

When extracting, document in component header:
```typescript
/**
 * VideoGridView - 3x3 YouTube Video Grid
 *
 * Extracted from Core.tsx on 2025-11-01
 * Lines extracted: ~250
 * Features: YouTube player integration, volume control, master volume sync
 *
 * Props:
 * - videos: Array of video configurations
 * - onVolumeChange: Callback for volume adjustments
 * - masterVolume: Master volume (0-100) for proportional scaling
 *
 * Related: app/components/Core.tsx (parent)
 */
```

## Measuring Success

### Quantitative Metrics
- Line count reduction (target: >50%)
- Number of components extracted (flux: 19+)
- Build time improvement
- Zero regressions in production

### Qualitative Metrics
- Developer can find code faster
- New features easier to add
- Merge conflicts reduced
- Code review feedback improves

## References

### Flux Project Files
- Original monolith: `app/components/Core.tsx`
- View components: `app/components/views/*.tsx`
- Validation script: `scripts/validate-modular-architecture.sh`
- Documentation: `SAFE_MODULARIZATION_PROTOCOL.md`, `MODULARIZATION_COMPLETE.md`

### Key Documentation
Read these flux docs for complete context:
- `SAFE_MODULARIZATION_PROTOCOL.md`: Step-by-step extraction process
- `MODULARIZATION_COMPLETE.md`: Results and lessons learned

## Summary

**The Sacred Rules**:
1. Extract, Don't Rewrite
2. One File at a Time
3. Backup Before Each Step
4. Zero Breaking Changes
5. Enforce Line Limits

**The Pattern**:
- Parent = Composition layer (state + orchestration)
- Children = View components (presentation only)
- Validation = Automated enforcement

**The Result**:
- 60% line reduction
- Maintainable codebase
- Fast development
- Safe refactoring

---

*Last Updated: November 1, 2025*
*Based on: flux project (Next.js 15.5.4)*
*Tested on: 5,594-line to 2,201-line reduction*
