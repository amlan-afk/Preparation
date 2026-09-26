# Technical curriculum checklist

Mark a topic complete only when you can explain it, give an example from work or a small exercise, and answer a follow-up.

## Java core

- [ ] OOP and composition; interface vs abstract class
- [ ] `equals()` / `hashCode()` contract
- [ ] String immutability; `StringBuilder`
- [ ] Collections: ArrayList, LinkedList, HashMap, HashSet, TreeMap, TreeSet
- [ ] Comparable vs Comparator
- [ ] Exceptions; checked vs unchecked
- [ ] Generics
- [ ] Lambdas, streams, Optional, functional interfaces

## Advanced Java

- [ ] JVM, heap and stack
- [ ] Garbage collection basics
- [ ] Process vs thread; ExecutorService
- [ ] CompletableFuture
- [ ] synchronized, volatile, locks
- [ ] Race conditions and deadlocks
- [ ] ConcurrentHashMap

## Spring Boot / backend

- [ ] HTTP request lifecycle and layered design
- [ ] DI, IoC, beans, constructor injection
- [ ] Controllers, services, repositories, clients
- [ ] DTOs, entities, mapping, validation
- [ ] Global exception handling and status codes
- [ ] Configuration, profiles, secrets
- [ ] REST contracts and API versioning
- [ ] Authentication and authorization
- [ ] Transactions, JPA/Hibernate, lazy/eager loading, N+1
- [ ] Connection pools and caching
- [ ] Tests: unit, integration, contract
- [ ] Logging, metrics, health checks, Actuator

## GraphQL

- [ ] Schema, types, queries, mutations, resolvers
- [ ] Arguments, variables, fragments, introspection
- [ ] Over-fetching / under-fetching
- [ ] N+1 and batching
- [ ] Error handling and REST comparison
- [ ] Explain the actual UI → API → GraphQL boundary

## React / TypeScript

- [ ] Component architecture, props, state, context, hooks
- [ ] `useEffect`, `useMemo`, `useCallback`, `useRef`
- [ ] Rendering, reconciliation, keys
- [ ] Controlled / uncontrolled inputs; error boundaries
- [ ] Lazy loading, code splitting, Suspense
- [ ] Render and bundle performance
- [ ] Redux Toolkit / Thunk, Context, server vs client state
- [ ] TypeScript modeling and API boundaries

## Production / AI-assisted work

- [ ] CI → tests → build → deployment → canary → monitoring → rollback
- [ ] Jenkins / Azure Pipelines / GitHub Actions as actually used
- [ ] Docker / Kubernetes / Helm as actually used
- [ ] Datadog / Grafana investigation workflow
- [ ] AI use across requirements, coding, testing, docs, review, debugging
- [ ] Validation, security, and accountability for generated suggestions
