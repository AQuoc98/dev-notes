# Hook review template

```yaml
name: hook-name
host: codex | claude | ci | generic
event: ""
matcher: ""
mode: observe | warn | block | rewrite | post-verify
scope: user | project | plugin | managed
```

## Why must this be a hook?

Giải thích vì sao instruction/skill/CI thông thường không đủ.

## Trigger input

## Deterministic policy

## Allowed behavior

## Block/rewrite behavior

## Failure behavior

Fail closed hay fail open? Vì sao?

## Data handling

Secrets, transcript, tool input/output, retention.

## Tests

- Safe fixture.
- Unsafe fixture.
- Bypass attempt.
- False positive.

## Rollback / disable

