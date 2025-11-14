# COMPASS Security Module
**Deep Security Patterns & Requirements**

Part of COMPASS: Coding Operations Manual & Protocol for AI System Standards

---

## Purpose

This module provides comprehensive security requirements and patterns for AI-generated code. **ALL security requirements are MANDATORY** - there are no optional security features.

---

## I. OWASP Top 10 Compliance (MANDATORY)

### 1. Broken Access Control

**Requirements:**
- ✅ ALWAYS verify authorization for EVERY protected operation
- ✅ ALWAYS implement least-privilege access control
- ✅ ALWAYS validate user permissions server-side
- ❌ NEVER rely on client-side authorization checks
- ❌ NEVER expose direct object references without authorization

**Patterns:**
```
✅ GOOD: Role-Based Access Control (RBAC)
function deleteDocument(userId, documentId) {
  const doc = await db.getDocument(documentId);
  if (doc.ownerId !== userId && !user.hasRole('admin')) {
    throw new UnauthorizedError('Access denied');
  }
  await db.deleteDocument(documentId);
}

❌ BAD: No authorization check
function deleteDocument(documentId) {
  await db.deleteDocument(documentId); // Anyone can delete anything!
}

❌ BAD: Client-side only check
// Client sends: { userId, documentId, isAdmin: true }
// Server trusts client-provided isAdmin flag
```

### 2. Cryptographic Failures

**Requirements:**
- ✅ ALWAYS use TLS/HTTPS for data in transit
- ✅ ALWAYS encrypt sensitive data at rest
- ✅ ALWAYS use proven cryptographic libraries
- ✅ ALWAYS use strong, modern algorithms (AES-256, RSA-2048+)
- ❌ NEVER implement custom cryptography
- ❌ NEVER use weak algorithms (DES, MD5, SHA1 for passwords)
- ❌ NEVER store passwords in plaintext

**Password Storage (MANDATORY):**
```
✅ GOOD: bcrypt with salt
const bcrypt = require('bcrypt');
const saltRounds = 10;
const hashedPassword = await bcrypt.hash(password, saltRounds);

✅ GOOD: argon2 (recommended)
const argon2 = require('argon2');
const hashedPassword = await argon2.hash(password);

❌ BAD: Plain MD5 or SHA1
const hashedPassword = md5(password); // NEVER DO THIS

❌ BAD: Plaintext storage
await db.users.insert({ password: password }); // NEVER DO THIS
```

**Encryption at Rest:**
```
✅ GOOD: AES-256-GCM
const crypto = require('crypto');
const algorithm = 'aes-256-gcm';
const iv = crypto.randomBytes(12);
const cipher = crypto.createCipheriv(algorithm, key, iv);

❌ BAD: Weak encryption
const cipher = crypto.createCipher('des', password); // Obsolete algorithm
```

### 3. Injection Vulnerabilities

**SQL Injection (CRITICAL):**
```
❌ NEVER GENERATE THIS:
const query = "SELECT * FROM users WHERE id = " + userId;
const query = `DELETE FROM posts WHERE id = ${postId}`;
const query = "SELECT * FROM " + tableName; // Table name injection

✅ ALWAYS GENERATE THIS:
// Parameterized queries
const query = "SELECT * FROM users WHERE id = ?";
const result = await db.query(query, [userId]);

// ORM with parameter binding
const user = await User.findOne({ where: { id: userId } });

// Prepared statements
const stmt = db.prepare("SELECT * FROM users WHERE id = ?");
const result = stmt.get(userId);
```

**Command Injection:**
```
❌ NEVER:
exec(`rm -rf ${userPath}`);
exec("ping " + userInput);
child_process.spawn('sh', ['-c', userCommand]);

✅ ALWAYS:
// Use array arguments (no shell interpretation)
child_process.spawn('rm', ['-rf', sanitizedPath]);

// Or validate against strict allowlist
const allowedCommands = ['start', 'stop', 'restart'];
if (!allowedCommands.includes(userCommand)) {
  throw new Error('Invalid command');
}
```

**NoSQL Injection:**
```
❌ NEVER:
db.users.find({ username: req.body.username });
// Attacker sends: { username: { $gt: "" } }

✅ ALWAYS:
// Validate input type
if (typeof req.body.username !== 'string') {
  throw new ValidationError('Username must be string');
}
db.users.find({ username: req.body.username });
```

**LDAP Injection:**
```
❌ NEVER:
const filter = `(uid=${username})`;

✅ ALWAYS:
// Escape special characters
const ldap = require('ldapjs');
const filter = `(uid=${ldap.escapeFilter(username)})`;
```

### 4. Insecure Design

**Requirements:**
- ✅ ALWAYS apply security by design (not retrofit)
- ✅ ALWAYS use threat modeling for sensitive features
- ✅ ALWAYS implement rate limiting for API endpoints
- ✅ ALWAYS validate business logic constraints
- ❌ NEVER trust client-side business logic enforcement

**Rate Limiting (MANDATORY for APIs):**
```
✅ GOOD: Implement rate limiting
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per window
  message: 'Too many requests, please try again later'
});

app.use('/api/', limiter);
```

**Business Logic Validation:**
```
❌ BAD: Trust client-provided totals
app.post('/checkout', (req, res) => {
  const total = req.body.total; // Client says: $1
  processPayment(total);
});

✅ GOOD: Calculate server-side
app.post('/checkout', (req, res) => {
  const cart = await getCart(req.user.id);
  const total = calculateTotal(cart.items); // Server calculates
  processPayment(total);
});
```

### 5. Security Misconfiguration

**Requirements:**
- ✅ ALWAYS disable debug mode in production
- ✅ ALWAYS remove default credentials
- ✅ ALWAYS implement proper CORS policies
- ✅ ALWAYS set security headers
- ❌ NEVER expose stack traces to users
- ❌ NEVER leave verbose error messages in production

**Security Headers (MANDATORY):**
```
✅ ALWAYS set these headers:
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  next();
});
```

**Error Handling:**
```
❌ BAD: Expose stack traces
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.stack }); // Leaks implementation details
});

✅ GOOD: Generic error messages
app.use((err, req, res, next) => {
  logger.error(err); // Log full details server-side
  res.status(500).json({
    error: 'Internal server error',
    requestId: req.id // For support reference
  });
});
```

### 6. Vulnerable and Outdated Components

**Requirements:**
- ✅ ALWAYS check for known vulnerabilities before adding dependencies
- ✅ ALWAYS keep dependencies updated
- ✅ ALWAYS use dependency scanning tools
- ✅ ALWAYS verify package authenticity
- ❌ NEVER use deprecated packages with known vulnerabilities

**Dependency Verification:**
```
BEFORE adding ANY dependency:
1. Check npm audit / Snyk / GitHub Security Advisories
2. Verify last update date (avoid abandoned packages)
3. Check number of dependents (community trust)
4. Review package homepage and repository
5. Scan for known CVEs

# Run security audit
npm audit
npm audit fix

# Or use Snyk
snyk test
snyk monitor
```

### 7. Identification and Authentication Failures

**Requirements:**
- ✅ ALWAYS implement multi-factor authentication for sensitive operations
- ✅ ALWAYS use secure session management
- ✅ ALWAYS implement account lockout after failed attempts
- ✅ ALWAYS use secure password reset flows
- ❌ NEVER implement custom session management
- ❌ NEVER store session tokens in localStorage (XSS vulnerable)

**Session Management:**
```
✅ GOOD: HTTP-only cookies
res.cookie('sessionId', sessionId, {
  httpOnly: true,  // Not accessible via JavaScript
  secure: true,    // HTTPS only
  sameSite: 'strict', // CSRF protection
  maxAge: 3600000  // 1 hour
});

❌ BAD: localStorage
localStorage.setItem('sessionToken', token); // XSS vulnerable
```

**Password Reset (Secure Flow):**
```
✅ GOOD:
1. User requests reset
2. Generate cryptographically random token
3. Store hashed token + expiration (15 minutes)
4. Send token via email only
5. Verify token + expiration on submission
6. Invalidate token after use
7. Require re-authentication after reset

❌ BAD:
- Predictable reset tokens
- Tokens that never expire
- No rate limiting on reset requests
- Sending password directly via email
```

### 8. Software and Data Integrity Failures

**Requirements:**
- ✅ ALWAYS verify data integrity with checksums/signatures
- ✅ ALWAYS use CI/CD pipeline with security scanning
- ✅ ALWAYS implement code signing where applicable
- ✅ ALWAYS validate uploaded file types and content
- ❌ NEVER trust file extensions alone
- ❌ NEVER deserialize untrusted data without validation

**File Upload Validation:**
```
❌ BAD: Trust file extension
if (file.name.endsWith('.jpg')) {
  // Attacker renames malicious.php to malicious.jpg
}

✅ GOOD: Validate file content
const fileType = require('file-type');
const type = await fileType.fromBuffer(fileBuffer);
if (!['image/jpeg', 'image/png'].includes(type.mime)) {
  throw new Error('Invalid file type');
}

// Also check file size
if (fileBuffer.length > 5 * 1024 * 1024) { // 5MB
  throw new Error('File too large');
}
```

**Deserialization:**
```
❌ NEVER:
const obj = eval(userInput); // EXTREMELY DANGEROUS
const obj = new Function(userInput)(); // ALSO DANGEROUS

❌ DANGEROUS: pickle (Python), unserialize (PHP)
import pickle
data = pickle.loads(user_input) # Code execution risk

✅ SAFER: Use JSON with validation
const obj = JSON.parse(userInput);
validateSchema(obj); // Validate against expected structure
```

### 9. Security Logging and Monitoring Failures

**Requirements:**
- ✅ ALWAYS log authentication events (success and failure)
- ✅ ALWAYS log authorization failures
- ✅ ALWAYS log input validation failures
- ✅ ALWAYS implement alerting for suspicious patterns
- ❌ NEVER log sensitive data (passwords, tokens, PII)
- ❌ NEVER log without proper log levels

**Security Event Logging:**
```
✅ GOOD: Comprehensive security logging
logger.security({
  event: 'authentication_failure',
  userId: userId || 'unknown',
  ip: req.ip,
  userAgent: req.headers['user-agent'],
  timestamp: new Date(),
  reason: 'invalid_password'
});

logger.security({
  event: 'authorization_failure',
  userId: req.user.id,
  resource: '/admin/users',
  action: 'DELETE',
  reason: 'insufficient_permissions'
});

❌ BAD: Log sensitive data
logger.info(`Login attempt: ${username}:${password}`); // NEVER LOG PASSWORDS
logger.info(`API request with token: ${authToken}`); // NEVER LOG TOKENS
```

### 10. Server-Side Request Forgery (SSRF)

**Requirements:**
- ✅ ALWAYS validate and sanitize URLs from user input
- ✅ ALWAYS use allowlist of permitted domains/IPs
- ✅ ALWAYS disable redirects or limit redirect count
- ❌ NEVER make requests to user-provided URLs without validation
- ❌ NEVER allow access to internal network ranges

**SSRF Prevention:**
```
❌ NEVER:
const response = await fetch(req.body.url); // User provides: http://localhost:8080/admin

✅ ALWAYS:
const allowedDomains = ['api.example.com', 'cdn.example.com'];
const url = new URL(req.body.url);

// Check domain allowlist
if (!allowedDomains.includes(url.hostname)) {
  throw new Error('Domain not allowed');
}

// Block internal IPs
const dns = require('dns');
const ip = await dns.resolve4(url.hostname);
if (isInternalIP(ip[0])) {
  throw new Error('Internal IPs not allowed');
}

// Make request with restrictions
const response = await fetch(url, {
  redirect: 'manual', // Don't follow redirects
  timeout: 5000
});
```

---

## II. Input Validation Patterns

### Allowlist Validation (REQUIRED)

**Always prefer allowlist over blocklist:**
```
❌ BAD: Blocklist (easily bypassed)
if (input.includes('<script>') || input.includes('javascript:')) {
  throw new Error('Invalid input');
}
// Bypasses: <ScRiPt>, javascri&#112;t:, etc.

✅ GOOD: Allowlist (explicit permission)
const allowedPattern = /^[a-zA-Z0-9_-]+$/;
if (!allowedPattern.test(input)) {
  throw new Error('Input contains invalid characters');
}
```

### Type Validation

```
✅ ALWAYS validate types:
if (typeof userId !== 'string') {
  throw new ValidationError('userId must be string');
}

if (!Number.isInteger(age) || age < 0 || age > 150) {
  throw new ValidationError('Invalid age');
}

if (!(email && typeof email === 'string' && /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email))) {
  throw new ValidationError('Invalid email format');
}
```

### Length and Range Validation

```
✅ ALWAYS enforce bounds:
if (username.length < 3 || username.length > 20) {
  throw new ValidationError('Username must be 3-20 characters');
}

if (quantity < 1 || quantity > 100) {
  throw new ValidationError('Quantity must be between 1 and 100');
}
```

---

## III. Authentication Best Practices

### Session Token Generation

```
✅ GOOD: Cryptographically secure random tokens
const crypto = require('crypto');
const sessionToken = crypto.randomBytes(32).toString('hex');

❌ BAD: Predictable tokens
const sessionToken = Date.now() + userId; // Easily guessable
const sessionToken = Math.random().toString(36); // Not cryptographically secure
```

### Password Requirements

```
✅ ENFORCE minimum standards:
- Minimum 12 characters (14+ recommended)
- Mix of uppercase, lowercase, numbers, symbols
- No common passwords (use dictionary check)
- No personal information (username, email, etc.)
- Password history (prevent reuse of last 5 passwords)
```

### Multi-Factor Authentication

```
✅ IMPLEMENT for sensitive operations:
- Authentication (login)
- Password changes
- Email changes
- Payment processing
- Administrative actions
```

---

## IV. API Security

### API Key Management

```
✅ GOOD: API key handling
- Store hashed, not plaintext
- Include expiration dates
- Allow key rotation
- Rate limit per key
- Log usage per key

❌ BAD:
- Hardcoded API keys in code
- Keys in version control
- No expiration
- No rate limiting
```

### CORS Configuration

```
✅ GOOD: Restrictive CORS
app.use(cors({
  origin: ['https://example.com'],
  methods: ['GET', 'POST'],
  credentials: true,
  maxAge: 3600
}));

❌ BAD: Permissive CORS
app.use(cors({
  origin: '*', // Allows any origin
  credentials: true // Dangerous with wildcard origin
}));
```

---

## V. Security Testing Requirements

### Pre-Deployment Checklist

```
BEFORE deploying ANY code:
□ All inputs validated
□ No SQL injection vulnerabilities
□ No XSS vulnerabilities
□ No command injection vulnerabilities
□ Authentication/authorization implemented
□ Secrets not hardcoded
□ Security headers configured
□ Error messages don't leak information
□ Logging configured (without sensitive data)
□ Dependencies scanned for vulnerabilities
□ Rate limiting implemented on APIs
□ HTTPS/TLS enforced
□ CORS properly configured
```

### Security Scanning Tools

```
INTEGRATE in CI/CD:
- npm audit / yarn audit (dependency scanning)
- Snyk (vulnerability scanning)
- SonarQube (code quality + security)
- OWASP ZAP (penetration testing)
- Burp Suite (security testing)
```

---

## VI. Secrets Management

### Never Commit Secrets

```
❌ NEVER in code:
const API_KEY = 'sk_live_abc123def456';
const DB_PASSWORD = 'admin123';

✅ ALWAYS from environment:
const API_KEY = process.env.API_KEY;
const DB_PASSWORD = process.env.DB_PASSWORD;

// Validate at startup
if (!API_KEY || !DB_PASSWORD) {
  throw new Error('Required environment variables not set');
}
```

### .gitignore Requirements

```
✅ ALWAYS ignore:
.env
.env.local
.env.*.local
config/secrets.json
*.key
*.pem
credentials.json
```

---

## Summary: Security First

Security is not optional. Every requirement in this module is MANDATORY. When in doubt:
1. Consult OWASP guidelines
2. Use proven libraries (don't roll your own)
3. Ask user for security review
4. Err on the side of caution

**Zero tolerance for security vulnerabilities.**

---

*Reference: COMPASS.md for complete operating manual*
*Related: COMPASS-MINI.md for core security requirements*
