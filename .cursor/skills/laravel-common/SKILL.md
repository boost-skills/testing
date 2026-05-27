---
name: laravel-common
description: >-
  Writes and refactors Laravel tests with PHPUnit or Pest following strict
  self-contained, DB-independent rules; shared setup only via data providers.
  Use when the user asks for Laravel feature/unit tests, Pest, PHPUnit,
  phpunit.xml, factories, fakes, RefreshDatabase, TestDox, or artisan test.
---

# Laravel: common testing rules

## When to use this skill

Requests about Laravel tests (new files, edits, Pest/PHPUnit style), `php artisan test`, factories, fakes, and DB isolation.

## Before generating code

1. Read `composer.json`, `phpunit.xml` / `phpunit.xml.dist`, `tests/TestCase.php`, and if present `tests/Pest.php` plus neighboring tests of the same kind (`tests/Feature`, `tests/Unit`).
2. Match the project’s existing style (Pest **or** PHPUnit classes — do not mix syntax in one file unless necessary).

## Mandatory rules

Tests must be self-contained, must not depend on external data or the current database state. No shared methods for multiple tests — only data providers.

Every PHPUnit test method must have `#[TestDox('...')]` with a short human-readable scenario title in **the user’s locale** (see [laravel-apis.md](laravel-apis.md#testdox-and-readable-names)).

## Agent interpretation

- Each test defines its own scenario **in that test’s body** (Arrange) or receives inputs via a **data provider** (`@dataProvider` / `#[DataProvider]` / Pest `with()` / datasets). There must be **no** shared `private`/`protected` setup methods “for multiple tests”.
- Do not rely on seeds “as dumped”, manually created rows that only exist locally, or test execution order. Create the needed state inside the test (factories, etc.) on a clean database per [testing-constraints.md](testing-constraints.md).
- External HTTP/mail/queues — only via Laravel fakes; see [laravel-apis.md](laravel-apis.md).
- Add or update `#[TestDox('...')]` on every new/changed PHPUnit test; locale rules in [laravel-apis.md](laravel-apis.md#testdox-and-readable-names).
- After changes, give a verification command, e.g. `php artisan test` or with `--filter`.

## Anti-patterns

- Extracting shared setup into class/trait methods called from different `@Test` / `it()` methods (except what is explicitly allowed in [testing-constraints.md](testing-constraints.md)).
- Depending on whatever is “currently” in the DB without the project’s migrations/refresh approach.
- Real network calls and unmanaged side effects outside fakes.

## Additional material

| File | Contents |
|------|----------|
| [testing-constraints.md](testing-constraints.md) | Self-contained tests, DB, data providers, what counts as allowed infrastructure |
| [laravel-apis.md](laravel-apis.md) | RefreshDatabase, factories, HTTP/auth, main fakes, how to run |
| [examples.md](examples.md) | Short examples without shared setup helpers |
