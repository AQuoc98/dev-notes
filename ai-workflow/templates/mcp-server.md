# MCP server template

```yaml
name: server-name
scope: local | project | shared
transport: stdio | streamable-http | other
data_classification: public | internal | confidential | restricted
auth: none | local | oauth | api-key | other
version: 0.1.0
```

## User problem

## Resources

What context/data is exposed, who controls it, freshness and provenance.

## Tools

For each tool: name, purpose, input schema, output schema, side effect, idempotency, error behavior.

## Permissions

Read/write separation, approval requirement, rate limits and allowed identities.

## Safety

Prompt injection handling, secret filtering, audit log, timeout, retry and rollback.

## Skill pairing

What workflow knowledge must accompany this MCP server?

## Test cases

