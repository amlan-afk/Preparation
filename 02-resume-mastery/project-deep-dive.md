# Project deep dive: UMR / MyUHC provider experience

Use this as the canonical architecture and ownership note. Mark unknowns as questions to verify; do not fill gaps with assumptions.

## 90-second overview

- User / business problem:
- Product context:
- My role and team:
- What I personally built:
- Outcome and evidence:

## Request and data flow

```text
React UI → Spring Boot API → GraphQL service → provider data source
```

Confirm whether this matches the actual production path and add auth, gateways, caches, and downstream dependencies if applicable.

| Layer | Responsibility | Contract / technology | Owner | Failure behavior |
|---|---|---|---|---|
| UI | | React / TypeScript | | |
| API | | Spring Boot / REST | | |
| GraphQL service | | GraphQL | | |
| Provider source | | | | |

## Architecture decisions to defend

For each answer, distinguish actual historical reasons from a hypothetical redesign.

- Why was the Spring Boot API layer present?
- Why did the UI use REST instead of calling GraphQL directly?
- How was GraphQL queried from Java?
- How were GraphQL results mapped to API response DTOs?
- What did the API add: aggregation, security boundary, contract stability, transformation, or something else?
- What alternatives were considered?
- What was the cost or downside of this design?

## Reliability and security

- Authentication and authorization:
- Timeout configuration:
- Retry policy and retryable errors:
- Circuit breaking / bulkheads, if any:
- Partial failure behavior:
- Logging and correlation IDs:
- Metrics, dashboards, alerts:
- Sensitive data handling:
- Rate limits / abuse controls:
- API compatibility and versioning:

## Testing and delivery

- Unit tests:
- API / integration tests:
- GraphQL client tests or mocks:
- Test data strategy:
- CI pipeline:
- Deployment and rollout:
- Rollback path:
- Production verification:

## Ownership boundary

- I implemented:
- My team implemented:
- Partner team owned:
- Decisions I influenced:
- Claims I should avoid overstating:

## Improvements if rebuilding today

1. 
2. 
3. 
