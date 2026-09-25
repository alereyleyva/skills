---
name: implementation-brief
description: "Invoke at the end of a technical implementation task, after the SDLC work and verification are complete, to produce a concise human-readable brief of what actually changed, why the important choices matter, and what deserves attention."
---

# Implementation Brief

## Purpose

**Keep the human close to the implementation.**

AI-oriented specs, plans, diffs, and implementation artifacts can contain the exhaustive detail needed to build software. Humans should not need to read all of that to understand what materially changed.

Produce a compact technical brief that lets a technical teammate understand:

- the implementation approach,
- the important system changes,
- the meaningful decisions or deviations,
- what was actually verified,
- and anything material that still deserves attention.

This is not documentation, a changelog, an implementation log, or a summary of the plan.

It is the human-facing closing artifact of the implementation task.

## When to run

Run this at the **end of the full SDLC for a technical task**, after implementation and the expected verification work have finished.

Typical scope includes changes to:

- application code,
- configuration,
- infrastructure,
- schemas or migrations,
- dependencies,
- tests that are part of an implementation,
- other technical system behavior.

Generate the brief whether the execution ended as:

- `Implemented`
- `Partial`
- `Blocked`
- `Failed`

A task that required no code change may still produce an `Implemented` brief if the requested behavior was already present and was verified.

Do not use this for research, planning, design-only, or other non-implementation work.

## Core rule

Report the implementation you can **observe**, not the implementation you remember performing.

Before writing the brief, inspect the final state of the work:

1. Read the original task and relevant spec or plan only to understand intent and detect meaningful deviations.
2. Inspect the final diff.
3. Inspect the relevant resulting code, configuration, schemas, tests, or infrastructure.
4. Review the verification that was actually executed.
5. Compare intended implementation with the final result.

The task, spec, and plan are context. **Do not summarize or retell them.**

## What to preserve

Optimize for preserving the implementation details a technical human would most regret missing.

Prioritize, in this order:

1. Changes that alter how the system works.
2. Changes across component or architectural boundaries.
3. Non-obvious technical decisions and trade-offs.
4. Configuration, persistence, dependency, API, schema, or infrastructure changes with operational consequences.
5. Relevant verification and material caveats.

Do not optimize for completeness.

Report changes at the **highest useful level of abstraction without hiding implementation mechanics that materially affect how the system works**.

For example, prefer:

> Added an in-memory cache in front of `CatalogClient`, with a configurable TTL propagated through application configuration.

over enumerating every helper, file, and internal refactor used to build it.

## Decisions

Mention a technical decision only when it carries meaningful signal.

A decision is usually worth reporting when one or more of these are true:

- several reasonable implementation approaches existed,
- responsibilities or architectural boundaries changed,
- a dependency was introduced, removed, or deliberately avoided,
- an API, schema, persistence model, configuration contract, or external behavior changed,
- the implementation involved a meaningful trade-off,
- the final implementation materially deviated from the original plan.

Do not turn routine implementation steps into “decisions”.

Explain the **why** only for choices that are not obvious from the change itself.

## Deviations

Always detect meaningful deviations between the intended plan and the final implementation.

Do not create a separate deviations section.

Integrate them naturally into `Why it matters` or `Checks / caveats`, depending on their significance.

Do not report abandoned experiments or the agent's implementation journey unless they explain an important property of the final result.

## Tests and verification

Describe **what behavior was verified**, not the internal structure of the tests.

Prefer:

> Retry behavior, timeout handling, and failed responses are covered by the related tests.

over:

> Added 12 unit tests, 3 mocks, and 2 fixtures.

Numbers may appear as secondary evidence when useful, but test counts alone are not meaningful verification.

Never infer success.

Only claim checks that were actually executed.

If a materially relevant check was not performed or could not be performed, say so plainly.

Prefer:

> Related tests, typecheck, and build pass. The migration was not exercised against a production-like database.

Do not include raw commands or CI logs unless a command itself is technically relevant.

## Output format

Write the brief in **English**, regardless of the language of the task or conversation.

Use one canonical Markdown artifact for every host: CLI, agent output, Kanban, or other UI.

Start with a single technical headline:

**`<Status> — <main technical outcome in one sentence>`**

The headline must communicate the technical result, not merely task completion.

Good:

> **Implemented — Request retries are now centralized in `RetryPolicy` and configurable across payment flows.**

Bad:

> **Implemented — Task completed successfully.**

After the headline, use at most these three sections:

### What changed

Combine the implementation approach and the important technical movements.

Keep this compact and system-oriented.

Use concrete component, service, endpoint, parameter, table, schema, job, or configuration names when they help the reader stay oriented.

### Why it matters

Include this only when there are meaningful:

- architectural choices,
- non-obvious decisions,
- trade-offs,
- important scope boundaries,
- or deviations from the intended plan.

Omit the section when it would contain only obvious or generic commentary.

### Checks / caveats

State what was actually verified.

Also include, when relevant:

- material risks,
- incomplete work,
- unverified behavior,
- important scope exclusions,
- operational caveats,
- blocked dependencies.

There must always be enough verification information for the reader to know what was or was not checked, even if a trivial brief does not need a dedicated heading.

## Brevity

Use **adaptive brevity**.

Typical targets:

- trivial implementation: roughly 3–6 lines,
- normal implementation: roughly 10–18 lines,
- complex implementation: preferably no more than ~25 lines.

For normal tasks, roughly 250–500 words is an upper working range, not a quota.

A complex task does not justify an exhaustive report. Group related changes and select the ones that materially change the reader's model of the system.

Prefer short prose and dense technical sentences.

Use bullets only when several independent changes are clearer when separated.

Avoid more than five bullets in the entire brief unless there is an exceptional reason.

## Style

Be direct, factual, and technically specific.

Prefer:

> `OrderService` now delegates pricing to `PricingPolicy`, allowing the policy to change without modifying order orchestration.

Avoid:

> This provides a cleaner, more robust and maintainable architecture.

Do not use quality adjectives without concrete evidence.

Avoid language such as:

- robust,
- cleaner,
- scalable,
- maintainable,
- production-ready,
- comprehensive,
- seamless,

unless the statement immediately explains the observable technical consequence.

Use code identifiers inline when useful.

## Do not

Do not:

- retell the task,
- summarize the spec,
- copy the implementation plan,
- enumerate every changed file,
- produce a `Files changed` section,
- describe changes line by line,
- include line numbers,
- include commit hashes,
- include diff statistics,
- include ticket or issue metadata,
- create a separate code-navigation section,
- list every test,
- describe trivial implementation decisions,
- report generated artifacts such as lockfiles unless their technical cause or effect matters,
- include code blocks,
- include tables,
- include diagrams,
- include trees,
- include appendices,
- add generic future improvements,
- add a `Next steps` section,
- add a `Review` section,
- include completeness disclaimers,
- inflate the brief merely because the underlying task was large.

Do not say:

> This is not an exhaustive list.

The brief is intentionally selective.

## Generated files and mechanical changes

Ignore mechanically generated changes unless they carry technical meaning.

Do not report:

> Updated `pnpm-lock.yaml`.

Report instead, when relevant:

> Upgraded `foo-sdk` to v4, which required switching authentication to its new client API.

## Paths and code locations

Do not provide an inventory of paths.

Mention a path only when it materially helps orient the reader.

Prefer component and symbol names over file locations.

Never include line numbers.

## Scope boundaries

If something important was deliberately left unchanged and that boundary helps prevent misunderstanding, mention it briefly.

Example:

> Existing token revocation behavior was intentionally left unchanged.

Do not create a dedicated section for scope unless absolutely necessary.

## Incomplete executions

For `Partial`, `Blocked`, or `Failed`, describe the final usable state precisely.

Example:

> **Blocked — The new storage adapter is implemented, but end-to-end verification cannot complete because the staging database is unavailable.**

Separate what exists from what remains unresolved.

Do not make a partially verified implementation sound complete.

## Read-only behavior

This skill is a closing and handoff step.

It must not:

- modify implementation code,
- refactor,
- add tests,
- change documentation,
- fix newly discovered problems,
- reopen implementation work.

If inspection reveals an issue, report it in the brief.

Do not silently fix it.

## Examples

### Normal task

**Implemented — Authentication now runs through a dedicated service with configurable token expiration.**

**What changed**  
`UserController` now delegates authentication to `AuthService`; token lifetime is controlled through `JWT_EXPIRATION`, which is also propagated through the container configuration. Tests now cover invalid credentials and token expiry.

**Why it matters**  
Authentication is no longer coupled to the HTTP controller. The original plan proposed a separate token component, but the existing JWT module was retained because it already provides the required boundary.

**Checks / caveats**  
The related authentication tests, typecheck, and build pass. Existing tokens are not invalidated by this change.

### Trivial task

**Implemented — Default outbound request timeout increased from 5s to 10s.**

Updated the shared HTTP client configuration and the affected timeout test. Related tests pass.

### No-op task

**Implemented — No code change was required; the requested retry behavior already exists in the shared request policy.**

Verified the current implementation against the task and ran the related tests; they pass.

### Blocked task

**Blocked — Database persistence is implemented, but end-to-end verification cannot complete against the staging environment.**

**What changed**  
The new repository writes through the existing transaction boundary and the application now uses it for account persistence.

**Checks / caveats**  
Unit and integration tests using the local database pass. Staging verification could not run because the database is unavailable.

## Final quality test

Before emitting the brief, ask:

**Could a technical teammate who did not inspect the diff understand the implementation approach, the important system changes, and anything worth reviewing in under one minute?**

If not, compress, regroup, or rewrite the brief before returning it.
