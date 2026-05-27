# Constraints: self-contained tests and reuse

## Self-contained

- Everything assertions need is created **in this test** or passed **via a data provider** as scalars/arrays/DTOs/models produced according to the provider rules below.
- No hidden reliance on production environment variables, arbitrary user files on disk, or “already existing” rows without explicit creation in the test.

## Current database state

- Do not assume required rows already exist in tables (except rows **this very test** created after reset/refresh).
- Use the project’s mechanism for a clean DB: commonly `RefreshDatabase` / `LazilyRefreshDatabase` on the test class or in Pest `uses()` — this is **infrastructure**, not a shared “business scenario”.
- After refresh, data appears only from **this** test’s code (factories, `Model::create` in the body) or from generation inside **a data-provider iteration** for that dataset row.

## No shared setup methods

**Forbidden:** helpers like `setupOrder()`, `createDefaultUser()` on the test class that several tests call.

**Allowed:**

- **Data providers** (PHPUnit): a static method returning input sets; the test body uses the arguments. Heavier model setup may live **inside the provider per row** if it does not become a grab-bag of shared steps for unrelated cases; prefer minimal inputs in the provider and entity creation **in the first lines of the test** from those inputs.
- **Pest:** `with([...])`, `dataset()`, and `describe` closures only if they do not recreate the anti-pattern “one hidden helper for many tests”; avoid moving Arrange into a helper file.
- Duplicating several Arrange lines across two tests is **acceptable** if it keeps the rule: no shared methods.

## Infrastructure vs scenario

| Allowed | Not a reusable scenario |
|--------|-------------------------|
| `RefreshDatabase` in `beforeEach` / `uses()` | Creating a canonical “global app user” in `beforeEach` for many cases |
| A single base `TestCase`, Laravel traits | A custom trait with `prepareForFeatureX()` shared across classes |

If the user explicitly relaxes or extends these rules — follow the user.

## External data

- Do not read arbitrary OS files as the source of truth for expectations unless the repo has an agreed fixture (per project convention).
- Time: fix with `Carbon::setTestNow()` / `freezeTime()` **inside the test** when behavior depends on it; do not rely on the real clock.
