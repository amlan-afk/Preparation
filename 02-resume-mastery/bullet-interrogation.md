# Resume bullet interrogation

Your resume currently has nine experience bullets and four achievement bullets. Use the prompts below to make each claim defensible. Fill in facts from your work; the prompts deliberately do not assume implementation details, metrics, or ownership beyond the resume wording.

## Optum — Software Development Engineer II (Feb 2026–Present)

### 1. Develop full-stack healthcare applications using React, TypeScript, Java, Spring Boot, GraphQL, and REST APIs for enterprise consumer experiences.

- Name the application/features and the user problem:
- Which technologies did you use on which features?
- Pick one complete request path and draw UI → service(s) → downstream data:
- What did you personally implement versus maintain or integrate?
- One design decision and its trade-off:
- How was it tested and released?
- Outcome / evidence (avoid unsupported metrics):
- Follow-ups: Why these technologies? How do contracts and errors cross the stack? Where are auth and validation enforced? What would you change at higher scale?

### 2. Built Spring Boot APIs for UMR to fetch provider data from a GraphQL service owned by a separate platform team, enabling React-based UIs to consume provider information.

- Actual user need and provider data involved:
- Draw the real request/data flow, including gateways, auth, and any other layer:
- Why was the Spring Boot API layer used? What was the actual reason for not having the UI call GraphQL directly?
- How did the Java service issue GraphQL queries and represent the response?
- How were GraphQL results mapped to the REST response contract?
- What happened for GraphQL errors, timeouts, empty results, and malformed data?
- How did you handle authentication, authorization, logging, and sensitive data?
- Tests: unit, integration, contract, or end-to-end; what did each verify?
- Your exact contribution versus the platform team and UI team:
- Result / evidence:
- Follow-ups: retries, caching, API versioning, pagination, observability, scaling, and failure isolation.

### 3. Lead development of scalable and accessible UI components across the MyUHC mobile application and multiple React-based consumer web applications.

- Which components/features and which platforms did you lead?
- What did “lead” mean in practice: design, implementation, coordination, review, mentoring?
- How were components shared or adapted across native and web applications?
- Accessibility requirements and how you verified them:
- What scale constraint did you address (users, data, bundle, teams, reuse, or performance)? Provide evidence:
- State, API, navigation, and error/loading patterns:
- A challenging component decision and trade-off:
- Follow-ups: rendering performance, platform-specific behavior, design system, testing strategy, accessibility semantics.

### 4. Follow AI-DLC practices and leverage AI-assisted development tools including Claude Code and GitHub Copilot to accelerate development, code exploration, testing, and documentation.

- What AI-DLC means in your organization and which stages you actually use it for:
- One concrete task where Claude Code helped:
- One concrete task where Copilot helped:
- What did you ask the tool to do, and what did you review/change?
- How did you validate generated code, tests, and documentation?
- How do you protect proprietary data and avoid unsafe or insecure suggestions?
- Evidence of acceleration or quality improvement, if measured:
- Follow-ups: When do you avoid AI? How do you test generated code? How do you handle hallucinated APIs or incorrect repository assumptions?

### 5. Own end-to-end delivery of Cost Estimate Experience modules, mentor engineers, review PRs, and lead production incident resolution including CI/CD and canary deployments.

- Which modules and user journeys are included?
- Define “end-to-end” with a specific example from requirement through production:
- Your decision authority and partner teams:
- Mentoring example and observable outcome:
- What do you look for in a PR review?
- Describe one production incident using [the incident worksheet](../05-interview-stories/production-incident.md): impact, detection, investigation, root cause, fix, rollout, prevention.
- Actual CI/CD path, tools, gates, and your contribution:
- How did the canary work and what signals controlled promotion or rollback?
- Follow-ups: release risk, feature flags/LaunchDarkly, health checks, rollback, observability, incident communication.

## Optum — Software Development Engineer (Sep 2023–Jan 2026)

### 6. Developed modular and accessible UI components for the MyUHC mobile application and three React-based consumer web applications.

- Name representative components and their users:
- How did component boundaries and reuse work across apps?
- Which accessibility standards or checks applied, and how did you validate them?
- How did you test React Native vs web behavior?
- A performance or maintainability trade-off:
- Your individual contribution and collaboration:
- Result / evidence:
- Follow-ups: state ownership, props/API design, responsive behavior, accessibility, regression testing.

### 7. Integrated frontend applications with multiple backend APIs and developed API aggregation layers to streamline data consumption.

- Which applications, APIs, and aggregation layer(s)?
- What problem did aggregation solve (round trips, data shaping, contract, orchestration)?
- Draw the before and after request flow:
- How were parallel calls, partial failures, timeouts, and loading states handled?
- Where did data mapping and validation happen?
- How did you prevent duplicated or inconsistent client logic?
- Tests and deployment:
- Your ownership boundary and outcome:
- Follow-ups: BFF vs direct calls, caching, retries, rate limits, observability, contract evolution.

### 8. Implemented Adobe Analytics instrumentation and automated testing using Jest, Selenium, and Appium to improve application reliability and observability.

- Which user events and journeys were instrumented, and why?
- How did you ensure event names/properties were consistent and avoid sensitive data?
- What did Jest, Selenium, and Appium each cover?
- How were test data, flaky tests, and CI execution handled?
- How did instrumentation help detect or understand behavior? Give evidence:
- What reliability issue did automation prevent or expose?
- Follow-ups: unit vs integration vs end-to-end scope, selectors, test pyramid, analytics validation.

### 9. Collaborated with cross-functional teams on high-priority healthcare initiatives, production support, vulnerability remediation, and deployment activities.

- Choose one initiative and state the goal, your role, and partner functions:
- What made it high priority?
- Choose a production-support example and your debugging steps:
- Choose a vulnerability remediation example: severity, fix, verification, and release (without exposing sensitive details):
- Deployment activities you performed:
- How did you communicate risk, progress, or blockers?
- Outcome / evidence:
- Follow-ups: prioritization, security trade-offs, coordination across ownership boundaries.

## Achievements and profile claims

### 850+ LeetCode questions solved; 1800+ contest rating

- Current date of these figures and public profile link:
- Which patterns are strongest and weakest?
- Explain one recent problem from brute force through proof and complexity:
- What does the rating represent, and how do you avoid over-relying on volume?

### Education and competition awards

- Prepare a concise explanation of the Electrical Engineering background and transition to software:
- Verify dates, exact award names, and what the competition entries involved:

## Readiness check

- [ ] Every experience bullet has a factual example and ownership boundary
- [ ] Each major project has a 30-second and 2-minute explanation
- [ ] Architecture can be drawn without notes
- [ ] Outcomes have evidence or are stated qualitatively
- [ ] Unknown implementation details are verified before interviews
- [ ] No metrics, scale, or leadership claims are invented
