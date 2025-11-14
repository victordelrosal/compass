# COMPASS Quality Module
**Code Quality, Architecture & Documentation Standards**

Part of COMPASS: Coding Operations Manual & Protocol for AI System Standards

---

## Purpose

This module provides comprehensive code quality standards, architectural principles, naming conventions, and documentation requirements. Quality is not optional - it's a foundation for maintainable software.

---

## I. Architectural Principles

### SOLID Principles (MANDATORY)

**S - Single Responsibility Principle:**
```
✅ GOOD: One reason to change
class UserRepository {
  async save(user) { /* ... */ }
  async find(id) { /* ... */ }
  async delete(id) { /* ... */ }
}
// Only changes if data access logic changes

❌ BAD: Multiple responsibilities
class UserManager {
  async save(user) { /* saves to DB */ }
  sendEmail(user) { /* sends email */ }
  validatePassword(pwd) { /* validates */ }
  logActivity(action) { /* logs */ }
}
// Changes for DB, email, validation, OR logging
```

**O - Open/Closed Principle:**
```
✅ GOOD: Extend without modifying
interface PaymentProcessor {
  process(amount: number): Promise<void>;
}

class CreditCardProcessor implements PaymentProcessor {
  async process(amount: number) { /* ... */ }
}

class PayPalProcessor implements PaymentProcessor {
  async process(amount: number) { /* ... */ }
}
// Add new payment types without modifying existing code

❌ BAD: Modify to extend
class PaymentProcessor {
  process(amount, type) {
    if (type === 'credit') { /* ... */ }
    else if (type === 'paypal') { /* ... */ }
    // Must modify this function for new types
  }
}
```

**L - Liskov Substitution Principle:**
```
✅ GOOD: Subtypes are substitutable
class Bird {
  fly() { /* implementation */ }
}

class Sparrow extends Bird {
  fly() { /* sparrow-specific flying */ }
}

class Eagle extends Bird {
  fly() { /* eagle-specific flying */ }
}
// Any Bird can be substituted without breaking

❌ BAD: Subtype breaks contract
class Bird {
  fly() { /* implementation */ }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can't fly!");
  }
}
// Substituting Penguin for Bird breaks code
```

**I - Interface Segregation Principle:**
```
✅ GOOD: Small, focused interfaces
interface Readable {
  read(): string;
}

interface Writable {
  write(data: string): void;
}

class File implements Readable, Writable { /* ... */ }
class ReadOnlyFile implements Readable { /* ... */ }

❌ BAD: Large, bloated interface
interface DataStore {
  read(): string;
  write(data: string): void;
  delete(): void;
  backup(): void;
  restore(): void;
}

class ReadOnlyFile implements DataStore {
  read() { /* ... */ }
  write() { throw new Error('Read-only'); } // Forced to implement
  delete() { throw new Error('Read-only'); } // Forced to implement
  // etc...
}
```

**D - Dependency Inversion Principle:**
```
✅ GOOD: Depend on abstractions
interface Database {
  query(sql: string): Promise<any>;
}

class UserService {
  constructor(private db: Database) {}
  async getUser(id: string) {
    return this.db.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}
// UserService depends on interface, not concrete DB

❌ BAD: Depend on concrete implementations
class UserService {
  private db = new PostgreSQLDatabase();
  async getUser(id: string) {
    return this.db.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}
// Tightly coupled to PostgreSQL
```

### Composition Over Inheritance

```
✅ GOOD: Compose from small pieces
class User {
  constructor(
    private auth: Authenticator,
    private logger: Logger,
    private validator: Validator
  ) {}

  async login(credentials) {
    this.validator.validate(credentials);
    const result = await this.auth.authenticate(credentials);
    this.logger.log('login_success');
    return result;
  }
}
// Flexible: swap out any component

❌ BAD: Deep inheritance hierarchy
class Entity {}
class User extends Entity {}
class AdminUser extends User {}
class SuperAdminUser extends AdminUser {}
// Rigid: hard to change, complex inheritance chain
```

### Modular Design

**Component Size Limits (ENFORCE):**
```
HARD LIMITS:
- Functions: 20-30 lines maximum
- Classes/Components: 500 lines maximum (warning at 450)
- Modules: 300 lines maximum (warning at 250)
- Files with >50 lines of inline code: Extract to component

When limits exceeded:
1. Extract functions/components
2. Refactor to smaller pieces
3. Document why if truly cannot split (rare)
```

**Single Responsibility Components:**
```
✅ GOOD: Focused component
function UserProfile({ user }) {
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
// Only renders user profile

❌ BAD: Multiple responsibilities
function UserDashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [comments, setComments] = useState([]);

  useEffect(() => { /* fetch user */ }, []);
  useEffect(() => { /* fetch posts */ }, []);
  useEffect(() => { /* fetch comments */ }, []);

  return (
    <div>
      {/* 200 lines of JSX */}
    </div>
  );
}
// Fetches data, manages state, renders UI
```

### Extract, Don't Rewrite

**The Sacred Rule:**
```
When refactoring:

1. EXTRACT exact code to new location (unchanged)
2. VERIFY it works identically
3. THEN refactor in separate step (optional)

❌ WRONG: Extract and improve simultaneously
// Old location
function calculateTotal(items) {
  let total = 0;
  for (let i = 0; i < items.length; i++) {
    total += items[i].price;
  }
  return total;
}

// Extraction + changes (DANGEROUS)
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}
// Changed logic + extracted - can't isolate issues

✅ RIGHT: Extract unchanged, then refactor
// Step 1: Extract EXACT code
function calculateTotal(items) {
  let total = 0;
  for (let i = 0; i < items.length; i++) {
    total += items[i].price;
  }
  return total;
}
// Test: Works identically ✓

// Step 2: Refactor (separate commit)
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}
// Test: Still works ✓
```

---

## II. Naming Conventions

### Core Naming Principles

**Names tell WHAT, not HOW or WHEN:**
```
✅ GOOD: Describes purpose
- User (not UserEntity)
- authenticate() (not authenticateWithJWT())
- EmailService (not SMTPEmailSender)
- formatCurrency() (not formatCurrencyString())

❌ BAD: Implementation details
- ZodValidator (implementation: Zod library)
- MCPWrapper (implementation detail)
- JSONParser (implementation detail)
- NewAPI (temporal context)
- LegacyHandler (temporal context)
- ImprovedService (historical context)
```

### Variable Naming

**Boolean Variables:**
```
✅ GOOD: Auxiliary verbs
- isLoading
- hasError
- canSubmit
- shouldRetry
- wasSuccessful

❌ BAD: Ambiguous
- loading (is it a boolean or state?)
- error (is it the error object or a boolean?)
- submit (is it the function or a flag?)
```

**Collections:**
```
✅ GOOD: Plural nouns
- users
- products
- orders
- comments

❌ BAD: Singular or suffixed
- userList
- productArray
- orderCollection
```

**Temporary Variables:**
```
✅ ACCEPTABLE in narrow scope:
for (let i = 0; i < items.length; i++) {
  const item = items[i];
  const x = item.x;
  const y = item.y;
}

❌ BAD: Cryptic in broad scope
function processData(d) {
  const x = d.value;
  const y = transform(x);
  const z = calculate(y);
  return z;
}
// What are x, y, z?
```

### Function Naming

**Use Verb Phrases:**
```
✅ GOOD: Clear actions
- getUser()
- createOrder()
- validateInput()
- calculateTotal()
- fetchData()
- handleClick()

❌ BAD: Nouns or unclear
- user() (is it a getter? constructor?)
- order() (is it creating? fetching?)
- validation() (what does it do?)
```

**Be Specific:**
```
✅ GOOD: Precise
- saveUserToDatabase()
- sendWelcomeEmail()
- validateEmailFormat()

❌ BAD: Vague
- process() (process what? how?)
- handle() (handle what?)
- do() (do what?)
```

### Class/Type Naming

**Use Nouns or Noun Phrases:**
```
✅ GOOD: Clear entities
- User
- Order
- EmailService
- PaymentProcessor
- ValidationError

❌ BAD: Verbs or unclear
- Authenticate (verb)
- Manager (what does it manage?)
- Helper (what does it help with?)
- Util (utility for what?)
```

**Avoid Pattern Noise:**
```
✅ GOOD: Domain language
- EmailService (not EmailServiceImpl)
- UserRepository (not UserRepositoryBean)
- Payment (not PaymentModel)

❌ BAD: Pattern/framework noise
- AbstractFactoryManager
- BaseHelperUtil
- ModelBean
- ControllerImpl
```

---

## III. Code Clarity

### Self-Documenting Code

**Code Should Explain Itself:**
```
✅ GOOD: Clear without comments
function calculateMonthlyPayment(principal, annualRate, years) {
  const monthlyRate = annualRate / 12 / 100;
  const numberOfPayments = years * 12;
  const payment = principal * (monthlyRate * Math.pow(1 + monthlyRate, numberOfPayments)) /
                  (Math.pow(1 + monthlyRate, numberOfPayments) - 1);
  return payment;
}

❌ BAD: Needs comments to understand
function calc(p, r, y) {
  const m = r / 12 / 100;
  const n = y * 12;
  const pmt = p * (m * Math.pow(1 + m, n)) / (Math.pow(1 + m, n) - 1);
  return pmt;
}
```

### DRY (Don't Repeat Yourself)

**Extract Common Logic:**
```
❌ BAD: Repeated logic
function validateUser(user) {
  if (!user.name || user.name.length < 3) {
    throw new Error('Invalid name');
  }
  if (!user.email || !user.email.includes('@')) {
    throw new Error('Invalid email');
  }
}

function validateAdmin(admin) {
  if (!admin.name || admin.name.length < 3) {
    throw new Error('Invalid name');
  }
  if (!admin.email || !admin.email.includes('@')) {
    throw new Error('Invalid email');
  }
  if (!admin.role || admin.role !== 'admin') {
    throw new Error('Invalid role');
  }
}

✅ GOOD: Extracted common logic
function validateBasicInfo(person) {
  if (!person.name || person.name.length < 3) {
    throw new Error('Invalid name');
  }
  if (!person.email || !person.email.includes('@')) {
    throw new Error('Invalid email');
  }
}

function validateUser(user) {
  validateBasicInfo(user);
}

function validateAdmin(admin) {
  validateBasicInfo(admin);
  if (!admin.role || admin.role !== 'admin') {
    throw new Error('Invalid role');
  }
}
```

### Computed State vs. Stored State

**Prefer Computed:**
```
✅ GOOD: Single source of truth
function ShoppingCart({ items }) {
  const total = items.reduce((sum, item) => sum + item.price, 0);
  const itemCount = items.length;
  const isEmpty = items.length === 0;

  return <div>Total: {total}, Items: {itemCount}</div>;
}
// total, itemCount, isEmpty are computed from items

❌ BAD: Duplicate state
function ShoppingCart() {
  const [items, setItems] = useState([]);
  const [total, setTotal] = useState(0);
  const [itemCount, setItemCount] = useState(0);
  const [isEmpty, setIsEmpty] = useState(true);

  // Must manually keep all state in sync
  // Easy to create bugs when state gets out of sync
}
```

---

## IV. Documentation Standards

### File Headers (REQUIRED)

**Every File Must Start With:**
```javascript
// ABOUTME: Handles user authentication and session management
// ABOUTME: Provides login, logout, token refresh, and session validation

export class AuthService {
  // ...
}
```

### Function Documentation

**Document WHY, not WHAT:**
```javascript
✅ GOOD: Explains why
// Check permissions before deletion to prevent users from
// accidentally removing data they need. This prevents support
// tickets where users ask to restore deleted items.
function checkDeletePermissions(userId, itemId) {
  // ...
}

❌ BAD: Restates the code
// Checks delete permissions
function checkDeletePermissions(userId, itemId) {
  // ...
}
```

### Complex Logic Comments

**Explain Non-Obvious Behavior:**
```javascript
✅ GOOD: Explains complexity
// Use binary search instead of linear search because user lists
// can exceed 10,000 items in enterprise accounts. This reduces
// lookup from O(n) to O(log n), keeping UI responsive.
function findUser(users, targetId) {
  // binary search implementation
}

❌ BAD: States the obvious
// Finds a user
function findUser(users, targetId) {
  // binary search implementation
}
```

### What NOT to Comment

**Never Comment:**
```
❌ Historical context
// This used to use promises but we switched to async/await
// Moved from UserManager.js
// Refactored on 2024-01-15
// Old implementation was too slow

❌ Temporal context
// New improved version
// Temporary fix until we refactor
// Recent changes to support...

❌ Instructions to developers
// TODO: Copy this pattern for other services
// Use this instead of the old method
// Remember to update tests when changing this

❌ Commented-out code
// function oldImplementation() {
//   // ... 50 lines of old code
// }
```

### Comment Maintenance

**Comments Must Stay Current:**
```
✅ DO:
- Update comments when code changes
- Remove comments that become obsolete
- Ensure comments accurately reflect code

❌ DON'T:
- Leave outdated comments
- Remove comments just because they're old (if still accurate)
- Add comments explaining changes (use git history)
```

---

## V. Code Organization

### File Structure

**Consistent Organization:**
```javascript
// ABOUTME: User management service
// ABOUTME: Handles CRUD operations and validation

// 1. Imports (external, then internal)
import express from 'express';
import { User } from './models/User';
import { logger } from './utils/logger';

// 2. Type definitions
interface UserCreateParams {
  name: string;
  email: string;
}

// 3. Constants
const MAX_NAME_LENGTH = 100;
const MIN_PASSWORD_LENGTH = 12;

// 4. Main exports (classes, functions)
export class UserService {
  async createUser(params: UserCreateParams) {
    // ...
  }
}

// 5. Helper functions (not exported)
function validateEmail(email: string) {
  // ...
}

// 6. Types/interfaces at end if not used above
```

### Grouping Related Code

```
✅ GOOD: Related code together
class UserService {
  // Authentication methods
  async login(credentials) { /* ... */ }
  async logout(sessionId) { /* ... */ }
  async refreshToken(token) { /* ... */ }

  // Profile methods
  async getProfile(userId) { /* ... */ }
  async updateProfile(userId, data) { /* ... */ }

  // Admin methods
  async deleteUser(userId) { /* ... */ }
  async suspendUser(userId) { /* ... */ }
}

❌ BAD: Random order
class UserService {
  async login(credentials) { /* ... */ }
  async getProfile(userId) { /* ... */ }
  async deleteUser(userId) { /* ... */ }
  async logout(sessionId) { /* ... */ }
  async updateProfile(userId, data) { /* ... */ }
}
```

---

## VI. Error Handling

### Meaningful Error Messages

```
✅ GOOD: Actionable errors
throw new ValidationError(
  'Email address must include @ symbol and domain. ' +
  'Received: ' + email
);

throw new AuthenticationError(
  'Invalid credentials. Please check your email and password. ' +
  'Account will be locked after 5 failed attempts.'
);

❌ BAD: Vague errors
throw new Error('Validation failed');
throw new Error('Authentication error');
throw new Error('Invalid input');
```

### Error Context

```
✅ GOOD: Include context
try {
  await processPayment(userId, amount);
} catch (error) {
  logger.error('Payment processing failed', {
    userId,
    amount,
    error: error.message,
    timestamp: new Date(),
    requestId: req.id
  });
  throw new PaymentError(
    'Unable to process payment. Please try again or contact support.',
    { userId, amount, originalError: error }
  );
}

❌ BAD: Lose context
try {
  await processPayment(userId, amount);
} catch (error) {
  throw error; // Lost context about what was being processed
}
```

---

## VII. Performance Considerations

### Premature Optimization

**Don't Optimize Without Measuring:**
```
❌ BAD: Premature optimization
// "This might be slow, so let's cache it"
const cache = new Map();
function getUser(id) {
  if (cache.has(id)) return cache.get(id);
  const user = db.users.find(id);
  cache.set(id, user);
  return user;
}
// Added complexity before knowing if it's needed

✅ GOOD: Optimize after measurement
// Profiling showed this function is called 1000x per request
// and takes 50ms each time = 50 seconds per request!
// Adding cache reduced request time from 50s to 100ms
const cache = new Map();
function getUser(id) {
  if (cache.has(id)) return cache.get(id);
  const user = db.users.find(id);
  cache.set(id, user);
  return user;
}
// Optimization justified by measurements
```

### Readability vs. Performance

**Prefer Readability:**
```
✅ GOOD: Clear and readable
function calculateTotal(items) {
  return items
    .filter(item => !item.discounted)
    .map(item => item.price * item.quantity)
    .reduce((sum, price) => sum + price, 0);
}
// Clear intent, readable

⚠️ ONLY if profiling shows this is a bottleneck:
function calculateTotal(items) {
  let total = 0;
  for (let i = 0; i < items.length; i++) {
    if (!items[i].discounted) {
      total += items[i].price * items[i].quantity;
    }
  }
  return total;
}
// Faster but less readable - only worth it if measured slow
```

---

## VIII. Code Review Self-Check

**Before Committing Code:**

```
□ Code Quality
  □ Functions under 30 lines
  □ Components under 500 lines
  □ No duplicate logic
  □ Self-documenting names
  □ Complex logic has comments explaining WHY

□ Architecture
  □ Single responsibility per component
  □ Follows SOLID principles
  □ Composition over inheritance
  □ Matches existing patterns

□ Documentation
  □ File has ABOUTME header
  □ Complex logic explained
  □ No historical/temporal comments
  □ No commented-out code

□ Naming
  □ No implementation details in names
  □ No temporal context in names
  □ Boolean variables use auxiliary verbs
  □ Functions use verb phrases
  □ Classes use nouns

□ Error Handling
  □ Meaningful error messages
  □ Context preserved in errors
  □ Errors logged appropriately

□ Testing
  □ Tests written for new code
  □ All tests passing
  □ Test output pristine
```

---

## Summary: Quality First

Code quality is not negotiable. Well-architected, clearly named, properly documented code is easier to maintain, test, and extend. When in doubt:

1. Follow SOLID principles
2. Make code self-documenting
3. Extract common logic (DRY)
4. Name for clarity, not implementation
5. Document WHY, not WHAT
6. Measure before optimizing

**Quality code is professional code.**

---

*Reference: COMPASS.md for complete operating manual*
*Related: COMPASS-MINI.md for core quality requirements*
