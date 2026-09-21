# Modernization decision guide

Use this guide to make code easier to reason about. It is not a mandate to
replace every familiar construct with a newer one.

## Decision rule

Before changing a construct, answer all three questions:

1. Does the replacement reduce states, branches, duplicated work, or exposed
   extension points that make the current code harder to reason about?
2. Does the module's language level and dependency set support it?
3. Is the resulting code clearer to a maintainer who knows this repository?

Use the replacement when the answers are yes. Keep the current construct when
it is clearer, protects behavior, handles checked exceptions better, or serves a
measured performance need. Record the reason in the proposal when the choice
could be controversial.

## Compatibility checks

Inspect the module before using a language or library feature:

- Java source and target level, or the equivalent language setting.
- Framework and persistence requirements.
- Existing dependency versions and local usage patterns.
- Serialization, reflection, proxy, and no-argument-constructor requirements.
- Collection ordering, laziness, exception, and null-handling contracts.

A feature that compiles in one module may not be valid in another. Apply the
module's constraints to every touched file.

## Useful transformations

### Local state and loops

Prefer a direct expression, a private method that returns a value, or a stream
when it removes mutable state and keeps the operation readable. Retain a loop
when it expresses the algorithm more clearly, handles control flow that a
pipeline would obscure, or is on a measured hot path.

For collection work, inspect these risks before changing it:

- encounter order
- duplicate keys
- empty input behavior
- short-circuiting
- checked exceptions
- allocation and performance

### Null and result contracts

Use `Optional` or an existing repository result type when it makes the API's
absence contract explicit. Prefer the repository's established type over
introducing a second abstraction. For new domain code, use Vavr's `Option`,
`Try`, or `Either` only when Vavr is already an approved dependency and the
result semantics justify it.

Do not convert null handling mechanically. Preserve the distinction between a
missing value, an empty collection, and an invalid value when the caller relies
on it.

### Records and value objects

Use records for immutable data carriers when the module supports them and the
class does not require framework features such as a no-argument constructor,
mutable properties, inheritance, proxying, or custom identity semantics. Keep a
class when those requirements apply or when the existing value object carries
meaningful behavior that a record would obscure.

### Pattern matching

Use pattern matching for `instanceof` or `switch` when it removes repeated
casts or a type ladder and the source level supports it. Preserve explicit
branches when they make validation, error reporting, or business rules easier to
read.

### Branches

Prefer guard clauses when they make exit conditions clearer. Remove an `else`
when it only wraps the normal path after a return. Keep an `else` when both
branches are part of one readable decision or when removing it would make the
control flow harder to follow. The goal is a clear decision, not a punctuation
rule.

### Names

Apply these naming improvements when touching the relevant declaration:

- Do not repeat the class name in record, DTO, or entity fields unless the
  domain requires it (`User.id`, not `User.userId`).
- Name collections after their contents (`userAccounts`, not
  `userAccountList`).
- Preserve public names and serialized names unless changing them is part of the
  confirmed request.

## Behavior-risk review

Before using a modernization, check whether it can change:

- public method signatures or binary compatibility
- exception types, messages, or timing
- transaction, authorization, or validation boundaries
- persistence and serialization behavior
- ordering, laziness, concurrency, or thread safety
- allocation or performance characteristics

Include any relevant risk and the focused test that covers it in the proposal.
