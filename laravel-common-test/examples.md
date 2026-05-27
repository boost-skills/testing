# Examples (no shared setup helpers)

TestDox / `it()` titles below are in English as samples; in real work use the **user’s locale** (e.g. Russian if the user writes in Russian).

## PHPUnit + data provider

Idea: input variants live in the provider; data creation stays in the test body for each provider row.

```php
#[TestDox('Returns the sum of two amounts for the user')]
#[DataProvider('amountCasesProvider')]
public function test_calculates_total(int $a, int $b, int $expected): void
{
    // RefreshDatabase on this class or parent
    $user = User::factory()->create();

    $total = app(InvoiceCalculator::class)->sum($user, $a, $b);

    self::assertSame($expected, $total);
}

public static function amountCasesProvider(): iterable
{
    yield 'small' => [1, 2, 3];
    yield 'zeros' => [0, 0, 0];
}
```

Do **not** move `User::factory()->create()` and the calculator call into a `private function runCase(...)` shared by several `@Test` methods.

## Pest: `with()`

```php
it('validates tier', function (string $tier, bool $ok) {
    $user = User::factory()->create(['tier' => $tier]);

    $result = TierChecker::acceptable($user);

    expect($result)->toBe($ok);
})->with([
    ['free', true],
    ['pro', true],
]);
```

User records are created **on every iteration** inside the closure, not in an external helper.

## Feature test: everything local

```php
#[TestDox('Guest cannot delete another user’s post')]
public function test_guest_cannot_delete_post(): void
{
    $user = User::factory()->create();
    $post = Post::factory()->for($user)->create();

    $response = $this->deleteJson("/api/posts/{$post->id}");

    $response->assertForbidden();
    $this->assertDatabaseHas('posts', ['id' => $post->id]);
}
```

Each test builds its own `User`/`Post`; do not reuse a shared `seedPost()` across five different test methods.
