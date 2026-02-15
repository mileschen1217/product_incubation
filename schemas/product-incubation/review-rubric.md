# Review Rubric

General principles for evaluating spec quality in the product incubation framework. The `/product-incubation:review` command applies these systematically.

## Principles

### 1. Specificity

Scenarios must have concrete WHEN conditions with specific values, states, or triggers — not vague statements like "the system should handle errors" or "when the user interacts with the feature."

**Pass:** `WHEN a user submits a login form with an invalid email format`
**Fail:** `WHEN the user provides bad input`

For document areas, sections must contain specific details (names, numbers, dates, concrete descriptions) rather than generic placeholders.

### 2. Testability

THEN clauses must describe observable, verifiable outcomes — not implementation details or internal state changes. A tester should be able to confirm the outcome without reading source code.

**Pass:** `THEN the API returns a 422 response with field-level error messages`
**Fail:** `THEN the system processes the request correctly`

For document areas, each section should define something that can be independently verified or validated.

### 3. Edge Coverage

Error states, empty inputs, boundary conditions, and concurrent access must be explicitly addressed. A spec that only covers the happy path is incomplete.

Check for:
- Error/failure scenarios (network errors, invalid data, timeouts)
- Empty/null inputs and boundary values (0, max, empty string)
- Concurrent access and race conditions (where applicable)
- Authorization failures and permission boundaries

### 4. Completeness

Every section in a spec must have substantive content — more than two sentences of meaningful detail. Placeholder text, TODO comments, and stub content indicate incomplete work.

Check for:
- `<!-- TODO` comments or placeholder markers
- Sections with only a title and no content
- Generic descriptions that could apply to any project
- Missing sections expected for the area type

### 5. Cross-references

When a spec references another area (e.g., "see API spec for endpoint details" or "authentication is defined in the Security spec"), that reference must:
- Point to a spec that actually exists
- Be bidirectional where appropriate (if FUNC references API, API should reference FUNC)
- Use consistent capability names

### 6. Consistency

Terminology must be consistent across all specs in the project:
- Entity names (same name for the same concept everywhere)
- Endpoint names and paths
- Field/column names
- Status values and enumerations
- Error code formats

A term defined in one spec must not be renamed or redefined in another.

## Rating Scale

For each principle applied to a spec:
- **Pass** `✓` — Principle is satisfied
- **Suggestion** `ℹ` — Minor improvement opportunity, not blocking
- **Warning** `⚠` — Significant gap that should be addressed

## Future: Per-Area Rubrics

The general principles above apply to all areas. Future versions may add area-specific rubrics for deeper review:

- **SEC**: OWASP top 10 coverage, auth/authz scenario completeness, data classification
- **API**: Error response documentation, rate limiting, versioning strategy, pagination
- **PERF**: Quantitative targets (latency percentiles, throughput), load test scenarios
- **DATA**: Referential integrity, migration strategy, indexing, soft-delete handling
- **TEST**: Coverage targets per area, test pyramid balance, CI integration

These per-area rubrics would extend (not replace) the general principles.
