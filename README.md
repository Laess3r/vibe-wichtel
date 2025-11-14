# vibe-wichtel
Vibe coded wichtel app

## What it does

The app shows a simple user interface. The input is a list of people (name and e-mail).
After the list of people was entered, we start the shuffling - each person gets the name of another person.
The tool should send an e-mail to each person with the name of the target gift person.

## Promt for prompt

* Create one or more Wichtel groups.
* Input list of participants (name + email).
* Optional exclusions (e.g., spouses not gifting to each other).
* Shuffle to produce a Secret Santa assignment (a derangement: no one gets themselves).
* Send individualized emails: “You will gift to: {receiverName}”.
Prevent:
* Duplicate assignments

## Technology

* No persistence needed
* Use a public mail sender tool that does not require to log in (if that exists).
* Create it in java 25
* Use graalvm
* Use quarkus
* use gradle as build system

## ai prompt

Project: Wichtel (Secret Santa) Assignment Service

Goal:
Provide a minimal REST-based service to create Wichtel (Secret Santa) groups, accept participant lists (name + email), optional exclusion rules (no gifting between specified pairs), produce a valid assignment where:
- No participant gifts to themselves (derangement).
- Exclusion pairs are honored.
- Each participant is assigned exactly one distinct receiver.
Then send individualized emails informing each giver of their receiver.

Non-Functional:
- Implement in Java (latest LTS; prefer Java 21 now, but code should be Java 25-compatible upon release).
- Use Quarkus (latest stable) with Gradle build.
- Provide optional native build via GraalVM (instructions + configuration).
- No persistent database; keep data in-memory for the lifecycle of the process.
- Allow an environment flag to disable email sending (dry-run mode) and log assignments instead.
- Provide seedable shuffle for reproducibility (query parameter or header).
- Expose OpenAPI spec.

API (JSON):
1. POST /api/groups
   Body: { "groupName": "...", "participants": [ { "name": "...", "email": "..." } ], "exclusions": [ { "a": "Name1", "b": "Name2" } ], "sendEmails": true, "emailLanguage": "en", "seed": "optionalString" }
   Response: { "groupId": "...", "assignments": [ { "giver": "NameA", "receiver": "NameB" } ], "emailDispatch": "SENT"|"SKIPPED" }

2. GET /api/groups/{groupId}
   Returns the group definition (but NOT receivers per participant to avoid leakage) or optionally only the size.

Email:
- Use pluggable service: interface EmailService.
- Default implementation: Mock logs to INFO.
- Optional real provider stub (e.g., SendGrid) behind feature flag; placeholder for API key env var: SENDGRID_KEY.
- Email body: "Hello {giverName}, you will gift to: {receiverName}". Plain text only.

Derangement Algorithm:
- Model as bipartite perfect matching with constraints (self edges and exclusion edges removed).
- Use backtracking with pruning (participants <= 200) or implement Hopcroft-Karp on filtered adjacency. Retry with different randomized order if unsatisfied; fail with 409 if impossible.

Validation:
- Reject duplicate emails.
- Reject invalid email format (simple RFC 5322 regex).
- Reject impossible configuration early (e.g., participant with exclusions covering all other participants).

Deliverables:
- build.gradle.kts configured for Quarkus and native image.
- ApplicationConfig showing dev vs prod config.
- Unit tests for derangement algorithm (including impossible case).
- Email service abstraction + mock.
- OpenAPI generated via quarkus-smallrye-openapi.

Run:
./gradlew quarkusDev
Native build:
./gradlew build -Dquarkus.package.type=native
