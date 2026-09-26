---
name: test-engineering
description: "Invoke whenever writing, changing, reviewing, validating, or auditing tests, and whenever behavior-changing production work relies on tests as a quality gate. Enforces TDD, owner-boundary testing, regression proof, test sensitivity, reliable gates, and evidence-based cleanup of low-value tests and test-only production seams."
---

# Test Engineering

Tests are executable evidence.

Their purpose is not to maximize test count, coverage, or assertions. Their purpose is to provide fast, independent, trustworthy evidence that important behavior and contracts still hold.

Optimize for **confidence per maintenance cost**, not deletion count, test count, or coverage percentage.

Use repository-specific instructions for commands, runners, CI, environments, and required gates. This skill defines the quality bar, not the toolchain.

## Core principles

**Test the contract where it lives.**  
Each meaningful contract should have one primary test owner at the strongest appropriate boundary: the cheapest stable boundary that independently proves the real contract.

“Strongest” does not mean highest-level. A pure function, public API, adapter, database boundary, protocol, generated artifact, or full system may each be the correct owner depending on the contract.

**Do not duplicate proof without a distinct risk.**  
A second layer needs something the primary owner cannot prove: transport, serialization, lifecycle, wiring, platform behavior, persistence, permissions, integration, or another independently meaningful failure mode.

**Behavior beats implementation.**  
A test that breaks under behavior-preserving refactoring is suspect. Test observable outcomes, invariants, and independently meaningful contracts rather than internal call shapes.

**Production does not exist to satisfy tests.**  
Do not add exports, flags, globals, wrappers, getters, dependency hooks, or alternate code paths solely to expose internals. A seam is justified when it is also a reasonable production abstraction.

**A green test is evidence only if it could credibly go red.**

## Development protocol

For behavior-changing production work, default to:

**RED → GREEN → REFACTOR → PROVE**

### RED

Write the test at the owning boundary before implementing the behavior.

Run it.

Confirm that it fails because the required behavior is absent or incorrect — not because of broken setup, an unrelated guard, invalid fixtures, compilation failure, or a mocked path the production system never reaches.

If the test is already green, determine why before proceeding. Existing behavior or existing coverage may make the new test unnecessary.

### GREEN

Implement the smallest coherent production change that satisfies the behavior.

Run the owning test and relevant nearby tests.

Do not weaken the assertion or modify the fixture merely to make the test pass.

### REFACTOR

Improve production and test code while preserving behavior.

Prefer fewer clearer tests, shared setup where it improves comprehension, and production APIs designed for production rather than testing convenience.

Keep the suite green throughout behavior-preserving restructuring.

### PROVE

Validate that the resulting tests are trustworthy sensors.

Use the strongest economical evidence available: regression reproduction, mutation testing, property checks, boundary integration, negative controls, targeted coverage inspection, repeatability checks, or another independent validation appropriate to the risk.

TDD proves how the change was developed. PROVE checks how much confidence the resulting tests deserve.

## Authoring gate

Before adding or materially changing a test, be able to answer:

1. **What contract does this protect?**  
   Name the observable behavior, invariant, or independently meaningful contract.

2. **What credible regression would make it fail?**  
   Describe a realistic defect, not merely a source-code edit.

3. **Why is that regression not already caught?**  
   Find the current owner and overlapping tests. Extend an existing table, property, fixture, or contract test when that gives the same proof more clearly.

4. **Would this survive behavior-preserving refactoring?**  
   If not, move toward the owning boundary or rewrite the assertion around behavior.

5. **Does this require production code that exists only for the test?**  
   If yes, reconsider the boundary before creating the seam.

Perform this gate as part of the work. Do not produce ceremonial answers unless they are useful to the task or requested.

## Regression tests

A regression test should prove the regression, not merely accompany the fix.

Whenever practical, demonstrate that the test fails on the pre-fix behavior for the intended reason and passes after the repair.

Acceptable proof can include running against the baseline, temporarily reverting the fix, reproducing the defect through the owner boundary, or applying an equivalent controlled mutation.

If direct pre-fix execution is impractical, explain the limitation and establish the strongest alternative evidence available.

Keep one canonical regression at the owning boundary. Add another layer only for a distinct failure mode the owner cannot observe.

Never claim regression proof merely because a newly added test passes after the fix.

## Test sensitivity

Passing tests show that the current implementation satisfies their assertions. They do not show that the assertions are strong enough.

When confidence matters, ask:

> If this behavior were subtly wrong, would the suite notice?

Use mutation testing as the preferred automated sensitivity probe when suitable tooling exists.

Prefer mutation testing on changed or high-risk code rather than optimizing a repository-wide mutation score. Investigate meaningful surviving mutants; do not chase equivalent, unreachable, generated, or otherwise irrelevant mutants simply to improve a number.

A meaningful mutant that survives in changed behavior is evidence of a test gap or weak assertion until shown otherwise.

If mutation tooling is unavailable, manual mutation probes are acceptable for important logic: invert a condition, remove an assignment, alter a boundary, change a returned value, skip a state transition, or otherwise introduce a plausible fault and verify that the expected test fails.

Never optimize for mutation score itself.

## Quality sensors

No single metric measures test quality. Treat metrics as sensors with different meanings.

### Correctness evidence

Prefer direct evidence such as:

- the intended RED failure was observed;
- the repaired behavior turns the test GREEN;
- a bug regression was reproduced;
- meaningful mutations are killed;
- negative controls fail for the intended reason;
- relevant integration or contract boundaries execute successfully.

### Coverage

Coverage is a searchlight, not a quality score.

Use line, branch, or condition coverage to identify behavior that tests never exercise, especially in changed code. Investigate uncovered important paths.

Do not infer strong tests from high coverage and do not create weak tests solely to reach a percentage.

Respect repository-mandated coverage gates without treating their threshold as proof of test quality.

### Reliability

A gate is trustworthy only when the same code reliably produces the same result.

Treat flaky tests as defects in the evidence system.

Do not normalize rerun-until-green behavior. A passing retry does not invalidate the previous failure.

Investigate nondeterminism, shared state, time, randomness, concurrency, networking, external dependencies, environment assumptions, resource contention, and ordering dependencies.

Prefer hermetic and deterministic tests when they can faithfully exercise the contract.

Quarantine only according to repository policy and never confuse quarantine with repair.

### Cost

Track feedback speed, resource use, setup complexity, fixture complexity, and maintenance burden when they materially affect the usefulness of the gate.

A slower or broader test may still be correct if it independently protects an important contract. A fast test is not valuable merely because it is fast.

## Stronger testing techniques

Escalate beyond example-based tests when the contract or risk justifies it.

Use **property-based testing** when behavior is better described by invariants across a large input space than by a handful of examples.

Use **fuzz testing** for parsers, protocol boundaries, untrusted or malformed input, robustness, and unexpected combinations.

Use **state-machine or model-based testing** for workflows, protocols, lifecycle transitions, and systems where the validity of one operation depends on previous operations.

Use **contract testing** when independently evolving components must agree on an interface or protocol.

Use **differential testing** when two independent implementations or execution paths can serve as useful oracles for each other.

Use **metamorphic testing** when the exact answer is difficult to compute but known relationships between inputs and outputs must hold.

Use **mutation testing** to measure whether assertions and scenarios are sensitive to plausible defects.

Do not introduce a technique because it is sophisticated. Use it when it gives stronger or cheaper evidence for the actual risk.

## Junk patterns

New tests matching these patterns fail the authoring gate unless they independently protect a meaningful contract.

Existing tests matching them become audit candidates, not automatic deletions.

### No signal

Examples include assertion-free execution probes, assertions that cannot fail meaningfully, self-comparisons, identity copies, and tests that only prove that a mock returns what it was configured to return.

### Implementation mirrors

Tests that primarily assert private predicates, internal call shapes, imports, method counts, internal identifiers, exact implementation structure, or source text are suspect when observable behavior already owns the contract.

Exact bytes, source fragments, names, paths, generated output, or strings may still be valid when their exact representation is itself the contract.

### Duplicate proof

Do not replay the same behavioral scenario across unit, service, transport, integration, and E2E layers without identifying a distinct risk at each layer.

Do not create provider-local copies of a shared contract when one generic contract suite can independently prove it.

### Mock theater

A mock must not implement the behavior the test claims to verify.

Beware of mocks whose configured response effectively becomes both the implementation and expected result, or one generic mock pretending to prove materially different external APIs.

Mock interactions only when the interaction itself is part of the contract.

### Fixture theater

Fixtures should establish prerequisites, not manufacture the outcome.

Be suspicious when fixtures provide the receipt, acknowledgement, ordering, persisted state, callback, generated value, lifecycle transition, or other result that production is supposed to create.

### Declaration instead of behavior

Do not prove a capability merely by checking a flag, manifest, enum, config entry, registration, or advertised feature when the real contract is delivery, acknowledgement, persistence, execution, or another observable outcome.

### False proof

A test is invalid when it passes or fails for a reason unrelated to the contract it claims to protect.

Negative controls must reach the intended guard.

Positive controls must exercise the intended production path.

Names, fixtures, and assertions must describe the behavior actually exercised.

### Test-induced architecture

Suspect production exports, wrappers, globals, mutable hooks, flags, alternate paths, getters, setters, and dependency seams whose only consumers are tests.

If removing a low-value test makes production code unnecessary, investigate whether both should disappear together.

## Retention bar

Keep a test when it is the cheapest reliable independent observer of a meaningful contract.

Common examples include public APIs, protocols, wire formats, persistence, migrations, authorization, security boundaries, configuration semantics, backwards compatibility, platform behavior, defaults, package contents, release artifacts, generated cross-language contracts, architecture constraints, user-visible exact values, and observable ordering.

Also keep credible regressions even when their implementation resembles internal behavior if no stronger independent proof exists.

Static, broad, integration-heavy, or slow is not by itself a deletion reason.

If a valuable retained test fails on the baseline, treat that as evidence of a possible product defect. Reproduce and investigate it rather than deleting the evidence.

## Audit mode

Audits optimize confidence, not deletion count.

Start with read-only discovery. Understand repository instructions, test infrastructure, CI routing, test ownership, relevant production entry points, and available coverage or mutation tooling.

Prefer a few high-confidence candidates over a large speculative inventory.

Before modifying or deleting an existing test, establish:

- the contract it claims to protect;
- the failure it can actually detect;
- the production owner of that contract;
- overlapping or stronger proof that will remain;
- non-test callers of any production seam involved;
- why the test exists when history is relevant;
- production or test-support code the change may unlock;
- the risk of the change;
- the focused validation needed afterward.

A candidate that lacks enough evidence remains unresolved rather than being forced into cleanup.

Valid audit outcomes are:

**keep** — independent useful proof;  
**rewrite** — useful contract, weak implementation;  
**consolidate** — useful proof duplicated across tests;  
**move** — useful proof lives at the wrong boundary;  
**delete** — no independent useful proof remains.

Remove obsolete test-only production seams in the same coherent change when safe.

Prefer net-negative complexity, not necessarily net-negative test count.

## Audit scope

A focused audit examines one coherent contract, feature, pattern, or owner boundary.

A campaign audit examines the complete test surface of a subsystem or package.

Do not silently turn a focused cleanup into a repository-wide campaign. Complete one coherent batch, validate it, and rediscover from the new baseline before continuing.

## Validation strategy

Use progressive proof.

Start with the smallest test that owns the changed contract, then expand only as needed to establish confidence in affected boundaries.

Respect repository-required formatting, type, lint, build, test, packaging, or CI gates.

Use changed-code or incremental mutation testing when available and warranted by risk.

Use test-impact analysis to accelerate feedback when supported, but do not treat predicted non-impact as proof when the dependency model is uncertain.

After structural test cleanup, verify that removed seams have no production callers and that retained proof still reaches the real behavior.

For high-risk or flaky areas, repeatability itself may be part of validation.

Never edit code merely to silence a gate without establishing why the gate failed.

## Gate integrity

The purpose of a gate is to support an automated decision.

Therefore optimize gates for:

**relevance** — failures correspond to meaningful risks;  
**sensitivity** — plausible regressions are detected;  
**specificity** — failures occur for the intended reason;  
**reliability** — identical code does not randomly alternate between pass and fail;  
**speed** — feedback arrives early enough to influence development;  
**maintainability** — behavior-preserving refactors do not trigger unnecessary rewrites.

There is no universal weighted score for these properties.

Do not collapse them into a single Test Quality Score unless the repository explicitly defines one for a specific operational purpose.

A dashboard may expose separate signals. The agent should reason about what each signal measures rather than optimizing the dashboard.

## Agent trust model

When automated agents perform increasingly large changes with decreasing line-by-line human inspection, tests and validation gates become part of the control system.

A green suite is not enough.

The desired evidence chain is:

**behavior specified → RED observed → implementation GREEN → behavior-preserving refactor → sensitivity and relevant boundaries PROVED → repository gates GREEN**

For trivial or non-behavioral changes, use the smallest evidence chain that honestly establishes safety.

Never fabricate evidence for a step that was not performed.

## Handoff

Report evidence, not ceremony.

Summarize:

- the behavior or contract changed;
- where its primary proof lives;
- RED/regression evidence actually observed;
- relevant validation actually run;
- additional sensitivity proof such as mutation testing when used;
- important tests kept, rewritten, moved, consolidated, or removed;
- production simplifications unlocked by test cleanup;
- unresolved risks, flaky evidence, or meaningful surviving mutants.

Do not report a command, test, mutation, coverage result, or TDD step as completed unless it was actually executed.
