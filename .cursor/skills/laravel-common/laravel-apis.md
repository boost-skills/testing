# Laravel: testing APIs and practices

## TestDox and readable names

### PHPUnit: `#[TestDox]`

- Put `use PHPUnit\Framework\Attributes\TestDox;` when needed.
- Every **public test method** gets `#[TestDox('...')]` **above** the method (with `#[Test]` / `#[DataProvider]` as the project already orders attributes).
- The string is a **short scenario title** (what is checked), not a duplicate of the method name — e.g. `Guest cannot delete another user’s post`, not `test_guest_cannot_delete_post`.

### Locale (mandatory)

Write TestDox text in **the locale the user uses**:

1. **User’s language in the request/chat** — if the user writes in Russian, TestDox in Russian; in English → English; same for any other language they use.
2. If unclear, match **neighboring tests** in the same directory/file (existing `#[TestDox]` or Pest titles).
3. Do **not** mix locales in one file unless the project already does.

Data-provider rows: optional `#[TestDox('...')]` on the test method still describes the overall case; per-row labels can use the provider’s **named keys** (`yield 'label' => [...]`) in the same locale.

### Pest

PHPUnit `TestDox` does not apply. Use a clear `it('...')` / `test('...')` description in the **same locale** as above (readable sentence, not `snake_case`).

## Running tests

- `php artisan test` — default.
- Targeted: `--filter`; if configured — `--parallel`, `--coverage`.

## Database and models

- Prefer `Model::factory()` in the **test body** with needed attributes (`->for()`, `->state()`).
- Do not rely on global seeders “always having run”; if the project mandates a minimal seed, call it explicitly **in this test** and mention it briefly.

## HTTP and authentication

- JSON: `getJson`, `postJson`, …
- User session: `actingAs($user)` or the API guard (`actingAs(..., 'sanctum')`, etc.) as the project uses.
- Assertions: status, `assertJson`, `assertJsonStructure`, strict body equality — match strictness of neighboring tests.

## Fakes (when needed)

- `Http::fake`, `Queue::fake`, `Bus::fake`, `Mail::fake`, `Notification::fake`, `Event::fake`, `Storage::fake`.
- Fake boundaries only; do not duplicate domain logic inside the test.

## Cache and config

- Use `Config::set` inside a test when required; avoid global mutable state leaking across tests without reset.

## Packages

- Use only packages in `composer.json` (Sanctum, Livewire, Dusk, etc.) that already appear in the project’s tests.

## Minimal diff

- Add tests and factories when needed; change production code only when asked or to fix an untestable seam.
