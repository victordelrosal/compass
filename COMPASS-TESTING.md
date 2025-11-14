# COMPASS Testing Module
**Test-Driven Development & Systematic Debugging**

Part of COMPASS: Coding Operations Manual & Protocol for AI System Standards

---

## Purpose

This module provides comprehensive testing requirements, TDD workflows, and systematic debugging processes. Testing is not optional - it's a mandatory quality gate.

---

## I. Test-Driven Development (TDD)

### The TDD Cycle (MANDATORY)

```
FOR EVERY NEW FEATURE OR BUGFIX:

1. Write a failing test
   - Test should validate desired functionality
   - Test should fail for the right reason
   - Test should be specific and focused

2. Run the test
   - Confirm it fails as expected
   - Verify failure message is clear
   - Ensure no false passes

3. Write minimal code
   - Write ONLY enough code to make test pass
   - Don't add extra features
   - Don't optimize prematurely

4. Run the test again
   - Confirm it passes
   - Verify all existing tests still pass
   - Check for any side effects

5. Refactor if needed
   - Improve code quality
   - Remove duplication
   - Keep all tests green
```

### TDD Benefits

**Why TDD is mandatory:**
- ✅ Ensures testable design (forces modularity)
- ✅ Provides immediate feedback
- ✅ Creates living documentation
- ✅ Prevents regressions
- ✅ Reduces debugging time
- ✅ Increases confidence in changes

### TDD Anti-Patterns

```
❌ NEVER:
- Write tests after implementation
- Skip tests because "it's simple"
- Test implementation details instead of behavior
- Write tests that test the mocks
- Delete tests to make build pass
- Skip tests with "TODO: fix later"

✅ ALWAYS:
- Write test first
- Test behavior, not implementation
- Keep tests independent
- Make tests readable
- Update tests when requirements change
```

---

## II. Testing Pyramid

### Layer Distribution (REQUIRED)

```
      /\
     /E2E\         5% - End-to-End Tests
    /------\
   /Integr-\      25% - Integration Tests
  /----------\
 /Unit Tests \   70% - Unit Tests
/--------------\
```

**70% Unit Tests:**
- Fast execution (<1ms per test)
- Test single functions/methods
- No external dependencies
- High coverage of edge cases

**25% Integration Tests:**
- Test component interactions
- Real database/API calls
- Test critical paths
- Moderate execution speed

**5% End-to-End Tests:**
- Test complete user flows
- Real browser/environment
- Critical business scenarios only
- Slow but comprehensive

### Coverage Requirements

```
MINIMUM COVERAGE: 70% overall

Critical paths: 100% coverage
Business logic: 90%+ coverage
UI components: 70%+ coverage
Utility functions: 90%+ coverage

✅ Run coverage checks in CI/CD:
npm test -- --coverage --coverageThreshold='{
  "global": {
    "branches": 70,
    "functions": 70,
    "lines": 70,
    "statements": 70
  }
}'
```

---

## III. Unit Testing Patterns

### Good Test Structure (AAA Pattern)

```javascript
describe('calculateTotal', () => {
  it('should sum all item prices', () => {
    // Arrange
    const items = [
      { price: 10.00, quantity: 2 },
      { price: 5.50, quantity: 1 }
    ];

    // Act
    const total = calculateTotal(items);

    // Assert
    expect(total).toBe(25.50);
  });
});
```

### Test Naming Convention

```
✅ GOOD: Descriptive test names
- "should return empty array when no items"
- "should throw error for negative quantity"
- "should calculate discount for premium users"

❌ BAD: Vague test names
- "test1"
- "it works"
- "edge case"
```

### Edge Cases (MANDATORY)

```
ALWAYS test:
- Empty inputs ([], "", null, undefined)
- Boundary values (0, -1, MAX_INT)
- Invalid inputs (wrong type, malformed data)
- Error conditions (network failure, timeout)
- Race conditions (concurrent operations)
- Large datasets (performance, memory)
```

### Test Independence

```
✅ GOOD: Independent tests
beforeEach(() => {
  database.clear();
  user = createTestUser();
});

test('user can update profile', () => {
  // Test works regardless of other test order
});

❌ BAD: Tests depend on execution order
test('create user', () => {
  globalUser = createUser(); // Sets global state
});

test('update user', () => {
  updateUser(globalUser); // Depends on previous test
});
```

---

## IV. Integration Testing

### Database Integration Tests

```javascript
✅ GOOD: Real database, test data
describe('UserRepository', () => {
  beforeAll(async () => {
    await database.connect();
  });

  afterAll(async () => {
    await database.disconnect();
  });

  beforeEach(async () => {
    await database.clear();
  });

  it('should save user to database', async () => {
    const user = { name: 'Alice', email: 'alice@example.com' };

    const saved = await userRepo.save(user);
    const fetched = await userRepo.findById(saved.id);

    expect(fetched).toEqual(expect.objectContaining(user));
  });
});
```

### API Integration Tests

```javascript
✅ GOOD: Test actual HTTP requests
describe('POST /api/users', () => {
  it('should create user and return 201', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Bob', email: 'bob@example.com' })
      .expect(201);

    expect(response.body).toHaveProperty('id');
    expect(response.body.name).toBe('Bob');

    // Verify in database
    const user = await db.users.findOne({ email: 'bob@example.com' });
    expect(user).toBeTruthy();
  });

  it('should return 400 for invalid email', async () => {
    await request(app)
      .post('/api/users')
      .send({ name: 'Invalid', email: 'not-an-email' })
      .expect(400);
  });
});
```

---

## V. End-to-End Testing

### E2E Test Requirements

```
✅ MUST test:
- Critical user journeys (signup, purchase, etc.)
- Authentication flows
- Payment processing
- Data loss scenarios
- Cross-browser compatibility (if web)

❌ DON'T test:
- Every edge case (that's for unit tests)
- Implementation details
- Third-party services directly (use mocks in E2E)
```

### E2E Example

```javascript
describe('User Purchase Flow', () => {
  it('should allow user to complete purchase', async () => {
    // 1. Navigate to site
    await page.goto('https://app.example.com');

    // 2. Login
    await page.fill('[data-test=email]', 'user@example.com');
    await page.fill('[data-test=password]', 'password123');
    await page.click('[data-test=login-button]');
    await page.waitForSelector('[data-test=dashboard]');

    // 3. Add item to cart
    await page.click('[data-test=product-1]');
    await page.click('[data-test=add-to-cart]');

    // 4. Checkout
    await page.click('[data-test=cart-icon]');
    await page.click('[data-test=checkout-button]');

    // 5. Complete payment
    await page.fill('[data-test=card-number]', '4242424242424242');
    await page.click('[data-test=pay-button]');

    // 6. Verify success
    await page.waitForSelector('[data-test=success-message]');
    expect(await page.textContent('[data-test=order-id]')).toMatch(/^ORD-/);
  });
});
```

---

## VI. Test Quality Standards

### Pristine Test Output (MANDATORY)

```
✅ GOOD: Clean output
✓ User can login (23ms)
✓ User can update profile (45ms)
✓ User can delete account (31ms)

Tests: 3 passed, 3 total
Time: 1.234s

❌ BAD: Noisy output
✓ User can login (23ms)
  Warning: Deprecated method used
  Error: Network timeout (retrying...)
  Retry successful
✓ User can update profile (45ms)
  Database connection pool warning
```

**If logs are expected, capture and validate them:**
```javascript
test('should log error for invalid input', () => {
  const spy = jest.spyOn(console, 'error').mockImplementation();

  processInput('invalid');

  expect(spy).toHaveBeenCalledWith('Invalid input: invalid');
  spy.mockRestore();
});
```

### Test Readability

```
✅ GOOD: Clear and readable
test('should reject orders with negative quantity', () => {
  const order = { productId: 1, quantity: -5 };

  expect(() => validateOrder(order))
    .toThrow('Quantity must be positive');
});

❌ BAD: Cryptic and unclear
test('test_order_validation', () => {
  const o = { p: 1, q: -5 };
  expect(() => v(o)).toThrow();
});
```

---

## VII. Systematic Debugging Process

### Phase 1: Root Cause Investigation (BEFORE fixes)

```
MANDATORY STEPS:

1. Read Error Messages Completely
   - Error messages often contain the exact solution
   - Don't skim or skip past errors
   - Look for line numbers, stack traces, context

2. Reproduce Consistently
   - Ensure you can reliably reproduce the issue
   - Identify minimum reproduction steps
   - Note environment details (OS, versions, etc.)

3. Check Recent Changes
   - What changed that could have caused this?
   - Review git diff, recent commits
   - Review recent dependency updates
```

### Phase 2: Pattern Analysis

```
MANDATORY STEPS:

1. Find Working Examples
   - Locate similar working code in the same codebase
   - What's different between working and broken?
   - Why does the working version work?

2. Compare Against References
   - If implementing a pattern, read reference completely
   - Understand dependencies and requirements
   - Check version compatibility

3. Identify Differences
   - List specific differences between working/broken
   - Which differences are significant?
   - What's missing or incorrect?
```

### Phase 3: Hypothesis and Testing

```
MANDATORY STEPS:

1. Form Single Hypothesis
   - State clearly: "I think X is the root cause because Y"
   - Only ONE hypothesis at a time
   - Be specific, not vague

2. Test Minimally
   - Make the SMALLEST possible change
   - Change only what's needed to test hypothesis
   - Don't pile on multiple fixes

3. Verify Before Continuing
   - Did the change fix the issue?
   - If YES: Understand why it worked
   - If NO: Form new hypothesis (don't add more fixes)

4. When You Don't Know
   - Say "I don't understand X"
   - Don't pretend to know
   - Ask for clarification or research
```

### Phase 4: Implementation Rules

```
✅ ALWAYS:
- Have simplest possible failing test case
- Make one change at a time
- Test after each change
- Re-analyze if first fix doesn't work
- Document what you learned

❌ NEVER:
- Add multiple fixes at once
- Claim to implement pattern without reading it completely
- Continue trying random fixes
- Skip root cause analysis
```

### Debugging Example (Right Way)

```
Problem: "Authentication fails after password reset"

❌ WRONG approach:
- Try adding more logging
- Change session timeout
- Update password hashing
- Modify cookie settings
- Change database query
(Random changes, no hypothesis)

✅ RIGHT approach:

1. Read error: "Session token invalid"
2. Reproduce: Reset password → Login → Error
3. Check recent changes: Password reset flow modified yesterday
4. Find working example: Regular login works fine
5. Hypothesis: "Password reset doesn't invalidate old sessions"
6. Test: Check if old session token still valid after reset
7. Confirm: Old sessions remain valid (that's the bug!)
8. Fix: Invalidate all sessions on password reset
9. Test: Password reset → Login → Success
10. Verify: All tests pass, no regressions
```

---

## VIII. Test-Specific Anti-Patterns

### Don't Test Mocks

```
❌ BAD: Testing the mock, not the logic
test('user service returns user', () => {
  const mockDb = {
    getUser: jest.fn().mockResolvedValue({ id: 1, name: 'Alice' })
  };

  const service = new UserService(mockDb);
  const user = await service.getUser(1);

  expect(user.name).toBe('Alice'); // Just testing the mock!
});

✅ GOOD: Test real logic
test('user service formats user data', () => {
  const mockDb = {
    getUser: jest.fn().mockResolvedValue({
      id: 1,
      first_name: 'Alice',
      last_name: 'Smith'
    })
  };

  const service = new UserService(mockDb);
  const user = await service.getUser(1);

  // Testing actual formatting logic
  expect(user.fullName).toBe('Alice Smith');
});
```

### Don't Test Implementation Details

```
❌ BAD: Testing private methods
test('internal validation works', () => {
  const validator = new Validator();
  expect(validator._internalCheck()).toBe(true); // Testing private method
});

✅ GOOD: Test public API
test('validator accepts valid input', () => {
  const validator = new Validator();
  expect(validator.validate('valid-input')).toBe(true);
});
```

---

## IX. Test Maintenance

### When to Update Tests

```
✅ Update tests when:
- Requirements change
- Bug is discovered (add regression test)
- API contract changes
- Refactoring changes behavior

❌ Don't delete tests when:
- Tests fail (fix the code or understand why test is wrong)
- Refactoring (update tests, don't delete)
- "Test is annoying" (that's a code smell, not test problem)
```

### Test Refactoring

```
✅ GOOD: Extract test helpers
// test-helpers.js
export function createTestUser(overrides = {}) {
  return {
    id: 1,
    name: 'Test User',
    email: 'test@example.com',
    ...overrides
  };
}

// user.test.js
test('can update user email', () => {
  const user = createTestUser({ email: 'old@example.com' });
  const updated = updateEmail(user, 'new@example.com');
  expect(updated.email).toBe('new@example.com');
});
```

---

## X. Continuous Integration Requirements

### CI Pipeline Tests

```
MANDATORY in CI/CD:

□ All unit tests pass
□ All integration tests pass
□ All E2E tests pass (on staging)
□ Code coverage meets threshold (70%+)
□ No test timeouts
□ No flaky tests
□ Linting passes
□ Security scans pass
□ Build succeeds

IF ANY FAIL → Build fails, no deployment
```

### Handling Flaky Tests

```
❌ NEVER:
- Ignore flaky tests
- Add retries to hide flakiness
- Delete flaky tests

✅ ALWAYS:
- Investigate root cause of flakiness
- Fix race conditions
- Add proper waits/synchronization
- Ensure test independence
- If truly random, quarantine and fix
```

---

## XI. Performance Testing

### Performance Test Requirements

```
✅ Test performance for:
- Database queries (< 100ms for simple queries)
- API endpoints (< 200ms for standard requests)
- Page loads (< 2.5s for LCP)
- Memory usage (no memory leaks)
- Concurrent users (load testing)
```

### Performance Test Example

```javascript
test('bulk operation completes in reasonable time', async () => {
  const start = Date.now();

  await processBulkData(generateData(1000));

  const duration = Date.now() - start;
  expect(duration).toBeLessThan(5000); // 5 seconds max
});
```

---

## XII. Test Documentation

### Document Test Intent

```
✅ GOOD: Clear test purpose
test('should reject duplicate email addresses', () => {
  // Business rule: Email addresses must be unique across all users
  // This prevents account hijacking and confusion

  await createUser({ email: 'alice@example.com' });

  await expect(createUser({ email: 'alice@example.com' }))
    .rejects.toThrow('Email already exists');
});

❌ BAD: No context
test('duplicate email', () => {
  await createUser({ email: 'alice@example.com' });
  await expect(createUser({ email: 'alice@example.com' })).rejects.toThrow();
});
```

---

## Summary: Testing First

Testing is not optional. Every feature must have tests. TDD is the required workflow. Systematic debugging prevents thrashing. When in doubt:

1. Write the test first
2. Make it fail
3. Make it pass
4. Refactor
5. Repeat

**Zero tolerance for untested code in production.**

---

*Reference: COMPASS.md for complete operating manual*
*Related: COMPASS-MINI.md for core testing requirements*
