# Node.js / TypeScript Review Rules

Companion reference for the code-review skill. Load when reviewing `.ts`, `.tsx`, `.js`, `.mjs`, or `.cjs` files.

---

## Security

- SQL injection via template literal: ``db.query(`... ${id}`)`` should use parameterized queries.
- Command injection: ``exec(`ls ${input}`)`` should use `execFile('ls', [input])` or equivalent argument passing.
- SSRF: user-controlled URLs passed to `fetch`, `axios`, `got`, or similar clients need allowlist validation or an equivalent trust boundary.
- XSS: `dangerouslySetInnerHTML={{ __html: userComment }}` needs proven sanitization or React escaping.
- Prototype pollution: avoid merging untrusted objects directly into plain objects; prefer key validation and safe object construction.
- Path traversal: user-controlled file names need canonicalization and containment checks, not string concatenation.
- Timing attacks: compare secrets or tokens with `crypto.timingSafeEqual` when timing differences matter.
- Secrets must not be logged or returned in API errors.

---

## Code Quality

- Missing `await` where caller expects resolved values or local error handling.
- Mutation that violates project conventions or makes state transitions hard to reason about.
- N+1 queries or API calls in request paths, jobs, or user-visible flows.
- `any` or unsafe casts on exported/public boundaries, security-sensitive data, or complex payloads without justification.
- Built-in imports without the `node:` prefix only matter when project conventions require it.
- Dead compatibility shims, unused variables, or removed-code comments left after refactors.

**NestJS specific:**

- Controllers should delegate business logic to services.
- Protected routes need guards and role/permission checks.
- DTOs should validate request bodies when the route accepts external input.
- Swagger decorators should follow project convention when Swagger is used.
- `ValidationPipe` should exist globally or per endpoint when DTO validation is expected.
- Circular service imports are architecture risks.

---

## Backend / API

- Public endpoints should validate body, params, and query input at the boundary.
- Public or abuse-prone endpoints may need throttling or rate limiting.
- User-facing list endpoints should paginate or otherwise bound result size.
- External HTTP calls in request paths or jobs should configure timeouts.
- API errors should not expose stack traces, raw database errors, or internal implementation details.
- CORS changes should be reviewed against intended origins.

---

## Performance

- Blocking I/O in async handlers, such as `fs.readFileSync` in request paths.
- Missing database connection pooling in services that create clients.
- Synchronous crypto in hot paths, such as `crypto.pbkdf2Sync`.
- Repeated parsing, serialization, or large allocations inside loops.

---

## Type Safety

- `any` usage without justification on exported functions, DTOs, or service interfaces.
- `!` non-null assertions without a runtime guard before them.
- Missing return type annotations on exported functions when inference hides an API contract.
- Unsafe casts such as `as unknown as X`, especially around external input.
