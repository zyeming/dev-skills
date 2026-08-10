# Python Review Rules

Companion reference for the code-review skill. Load when reviewing `.py` files.

---

## Security

- SQL injection: `cursor.execute(f"SELECT ... {user_id}")` should use parameterized queries.
- Command injection: `os.system(f"ls {input}")` should use `subprocess.run([...], check=True)` with arguments.
- SSRF: user-controlled URLs passed to `requests`, `httpx`, `aiohttp`, or similar clients need allowlist validation or an equivalent trust boundary.
- Path traversal: user-controlled file names need canonicalization and containment checks before file access.
- Unsafe deserialization: `pickle.loads(user_data)` and `yaml.load(data)` on untrusted input are dangerous; prefer safe formats and `yaml.safe_load`.
- Dangerous execution: `eval` or `exec` on untrusted input should be flagged.
- Secrets must not be logged or returned in API errors.

---

## Code Quality

- Mutable default arguments:
  ```python
  def process(items=[]):      # BAD: shared state across calls
  def process(items=None):    # GOOD
      items = items or []
  ```
- Bare `except:` catches `SystemExit` and `KeyboardInterrupt`; catch specific exceptions and preserve context.
- Prefer `pathlib.Path` over `os.path` when it improves path safety, readability, or cross-platform behavior.
- Dead compatibility shims, unused variables, or removed-code comments left after refactors.

**Pydantic v2 / FastAPI specific:**

- `@validator` should become `@field_validator` in Pydantic v2 code.
- `class Config: orm_mode = True` should become `model_config = ConfigDict(from_attributes=True)`.
- FastAPI routes returning raw `dict` may bypass response validation when a response model is expected.
- External-input routes should validate request models.
- Protected routes need dependency-based auth or permission checks.

---

## Backend / API

- Public endpoints should validate body, params, and query input at the boundary.
- Public or abuse-prone endpoints may need throttling or rate limiting.
- User-facing list endpoints should paginate or otherwise bound result size.
- External HTTP calls in request paths or jobs should configure timeouts.
- API errors should not expose stack traces, raw database errors, or internal implementation details.

---

## Performance

- CPU-bound work in async event loops should move to an executor or worker.
- Missing `select_related` / `prefetch_related` in Django ORM code that renders related objects.
- String concatenation in loops should use `"".join(...)` when it affects real hot paths.
- Large lists where generators or streaming would avoid avoidable memory pressure.
- `asyncio.gather` may need structured cancellation; consider `asyncio.TaskGroup` in Python 3.11+ code when cancellation behavior matters.

---

## Type Safety

- Public APIs and complex data-shape functions should have type hints.
- `# type: ignore` needs a reason when it hides a real contract issue.
- Nullable returns should use `Optional` or `X | None`.
- `Any` at boundaries should be replaced with explicit models or protocols when it weakens validation.
