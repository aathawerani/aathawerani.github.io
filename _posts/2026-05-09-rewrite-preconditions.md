---
layout: post
title: "Why Architecture Is Shifting From Diagrams to Guardrails"
excerpt: "AI has made rewrites cheaper without making them better. Without requirements written as specifications, tests evolved from production evidence, and architecture encoded as executable constraints, an AI-assisted rewrite is just a faster way to restart the same problems. This post addresses what a well-governed AI-assisted rewrite actually looks like — and whether the feedback loop that sustains it can be made reliable over time."
author: ali
date: '2026-05-09 00:00:00'
category: ['systems','architecture','scale']
thumbnail: /assets/img/blog/architecture-to-guardrails/architecture-to-guardrails-thumbnail.png
---

## The Cheaper Rewrite Is Not the Better Rewrite

The previous post in this series argued that AI has lowered the cost of rewrites without lowering the knowledge required to do them well — and that without architectural discipline governing the process, the result is a perpetual rewrite loop: each iteration faster, each iteration less understood than the last [[1]](#ref-1).

This post does not contradict that argument. It assumes it.

The previous post described a hard limit: cognitive load ceilings are real, and AI accelerates how quickly teams hit them. The question this post addresses is not how to remove that ceiling — it cannot be removed. It is how to move knowledge out of human cognition and into systems that can preserve, enforce, and evolve it. Specifications. Tests. Architecture as Code. Structured ADRs. These are not workarounds for the cognitive load problem. They are the engineering response to it — externalising what human working memory cannot hold so that AI can be constrained by it rather than unaware of it.

Three preconditions distinguish a rewrite that produces a genuinely better system from one that restarts the same problems faster. Each precondition raises a deeper question about the role of AI in its own governance — questions the industry is only beginning to ask seriously.

---

# Why the Same Problems Keep Returning

Joel Spolsky's 2000 "never rewrite" doctrine was an economic argument, not a technical one [[2]](#ref-2). Rewrites were expensive not primarily because of engineering hours but because of institutional knowledge — the years of edge cases, discovered constraints, and behavioral understanding embedded in existing systems in ways that were difficult to extract and easy to lose.

AI has changed the engineering hour cost substantially. It has not changed the institutional knowledge problem. And it has introduced a new pattern that makes that problem harder to manage.

Call it **architecture by reaction** *(a pattern named here — no established prior term exists for this specific dynamic)*: AI addresses the problem in front of it at each stage of a system's evolution, without reference to the architectural decisions made in previous stages.

Consider a typical trajectory:

- **v1** — monolith, perfectly reasonable for MVP scale
- **v2** — scalability issues hit, AI decomposes into microservices
- **v3** — latency problems emerge, AI introduces caching and async messaging
- **v4** — data inconsistency surfaces, AI reintroduces shared state patterns to resolve it

![architecture by reaction](/assets/img/blog/architecture-to-guardrails/architecture_by_reaction.svg)

Each step is locally correct. Globally, the system has regressed — the shared state in v4 recreates the coupling that the microservices decomposition in v2 was designed to eliminate. No single decision was wrong. The sequence, without accumulated memory of why earlier decisions were made, produced the same problem in a different form.

AI has no memory of v2's rationale when it generates v4. The institutional knowledge that would say "we moved away from shared state at this boundary precisely because of consistency problems — do not go back" is not in the prompt unless someone put it there. **The same structural mistakes recur not because AI is unintelligent, but because intelligence without memory is just pattern matching.**

This dynamic has a name in the practitioner literature. A widely-discussed critique of agentic coding identifies what it calls the **supervision paradox**: you need the engineering skills the agent erodes in order to oversee what the agent produces [[3]](#ref-3). The more you delegate to AI, the less capable you become of catching what AI gets wrong — and the more you need that capability precisely because AI is generating more that could go wrong. This post does not disagree with that diagnosis. It proposes that the answer is not to delegate less, but to move the supervision from inside the developer's head into the governance system — where it can operate at scale, persist across team changes, and accumulate rather than erode over time.

**Architecture Decision Records (ADRs)** are the established mechanism for preserving exactly this institutional memory [[4]](#ref-4). An ADR captures not just what architectural decision was made but why — the context, the options considered, the trade-offs accepted, and the consequences anticipated. Fed to AI as context at the start of a rewrite, a library of ADRs is the closest available substitute for the accumulated experience AI lacks. The critical content is not the decision itself but the rejected options and their rationale — "we considered service decomposition at this boundary and rejected it because X" is the institutional memory that prevents the same mistake in the next iteration. Without it, AI has no way of knowing what was tried and failed, and will reproduce the same rejected approaches dressed in newer patterns.

The three preconditions below are not just preparation for a rewrite. They are the mechanisms by which institutional knowledge is preserved across AI-assisted iterations — making it available to the next rewrite, and the one after that. ADRs are the connective tissue between them.

> **Key Insight:** AI has lowered the cost of producing code without lowering the cost of understanding it. A rewrite that proceeds without adequate knowledge of the existing system's domain behavior and failure modes is not cheaper — it is a faster way to accumulate the same debt.

![Governance Chain](/assets/img/blog/architecture-to-guardrails/governance_chain.svg)

---

# Precondition One: Requirements as Specifications

Most AI-assisted rewrites fail before a line of code is written. Not because the model is wrong — but because the requirements are.

The distinction that matters is between requirements as **prose** and requirements as **specifications**. Requirements written as prose describe intent in natural language — interpretable, ambiguous, dependent on human judgment for translation into implementation. AI tools reading prose requirements produce implementations consistent with one plausible reading, not necessarily the reading that reflects the domain constraints and behavioral expectations that matter in production. **Prose requirements are instructions. Specification-grade requirements are constraints.** AI follows instructions loosely. It cannot violate constraints.

This principle has been formalised in the industry as **Spec-Driven Development (SDD)** — a methodology that elevates specifications to executable system truth, making architecture enforceable through continuous validation while AI generation materializes intent into code [[5]](#ref-5). SDD's core distinction, as articulated by Griffin and Carroll, is that traditional specifications are read by humans while SDD specifications execute as validation gates — if code diverges from the spec, the build fails. The industry is converging on this from multiple directions: practitioners building with AI agents have independently arrived at similar conclusions, finding that writing feature specifications in structured formats rather than natural language prompts substantially reduces AI's tendency to generate plausible but contextually wrong output when given ambiguous instructions [[6]](#ref-6).

Requirements written as specifications are structured, verifiable, and precise enough to function as constraints on AI generation rather than guidance for it. They encode not just what the system should do but what it must not do, what invariants must hold, and what the consequences of violating those invariants are [[7]](#ref-7).

**The objection that immediately arises:** this exercise was already done when the first version was built. Why repeat it?

The answer is that it was done, but not in this form. The domain knowledge exists — in the original requirements documents, in production incident records, in the heads of engineers who built and maintained the system. What does not exist is that knowledge translated into a format that AI can consume as a constraint rather than guidance. And crucially, the domain has evolved since the first version. New edge cases have been discovered in production. New business rules have been added. Constraints have emerged from real usage that were not in the original specification.

**The practical answer is to use AI to do the translation, not humans** *(proposed approach — no established prior source covers this specific workflow)*. The process is more tractable than it sounds:

Feed the existing requirements documents, production incident records, change request history, and any available domain documentation to AI. Ask it to identify gaps, ambiguities, and contradictions. Ask it to generate clarifying questions about what is unclear — specifically around invariants, failure modes, and boundary conditions. Have domain experts answer those questions. Feed the answers back. AI reformats the output as structured, verifiable specifications that can be used to constrain subsequent generation.

This is not a documentation exercise driven by human effort. It is an AI-assisted extraction and clarification process in which human expertise is the validator rather than the primary generator. The domain experts are answering targeted questions, not authoring documents from scratch. The difference in time and effort is substantial.

The result is a specification that encodes what the system must do precisely enough that AI can be held to it — and precisely enough that deviations from it can be detected automatically.

> **Key Insight:** The quality of an AI-assisted rewrite is bounded by the quality of the requirements that govern it. AI can extract, clarify, and reformat existing knowledge into specification-grade constraints — but human validation of those constraints against real domain understanding is not optional.

**What a specification-grade requirement looks like in practice** deserves a concrete example. UML use case specification — with its actor, precondition, main flow, alternate flows, postcondition, and exception flows structure — has been the established standard for capturing behavioral intent precisely since the 1990s [[8]](#ref-8). The format is well-understood by domain experts and captures branching behavior more naturally than flat invariant statements.

However, the standard UML use case format was designed for human-to-human communication, not human-to-AI constraint. It lacks three things that make a specification machine-consumable: a falsifiable invariant that AI can translate directly into a test assertion, a violation consequence that encodes the business cost of non-compliance, and linkage to the ADR and fitness function that enforce the decision architecturally.

The following format is a **proposed concrete implementation of SDD principles** — combining the established UML use case flow structure with YAML front matter for machine-readable metadata. SDD prescribes that specifications should be executable and authoritative; this format is one way of achieving that at the requirement level. No single established prior source prescribes this specific combination:

```yaml
---
id: SPEC-017
title: Payment processing idempotency
type: safety-invariant      # safety | liveness | business-rule | performance
affects: [payment-service]
bounded-contexts: [payment]
verified-by: TEST-041
linked-adr: ADR-033
---

## Actor
Payment consumer (internal service or external client)

## Precondition
- Payment request contains a valid idempotency key
- Payment service is operational

## Main Flow
1. Consumer submits payment request with idempotency key K
2. System checks idempotency log for key K
3. Key not found — system processes payment and records outcome against K
4. System returns payment result

## Alternate Flow: Duplicate Submission
1. Consumer submits payment request with idempotency key K
2. System checks idempotency log for key K
3. Key found — system returns stored outcome, no new charge created
4. Idempotency log records duplicate attempt as no-op

## Exception Flow: Timeout During Processing
1. Consumer submits payment request with idempotency key K
2. System begins processing, timeout occurs before outcome recorded
3. Consumer retries with same key K
4. System detects in-progress state — waits for completion or rolls back cleanly
5. Returns consistent outcome — no duplicate charge under any scenario

## Postcondition
- Exactly one charge exists for any given idempotency key
- Idempotency log contains record of all attempts

## Invariant (machine-verifiable)
forall requests r1, r2: r1.idempotency_key == r2.idempotency_key
  => outcome(r1) == outcome(r2) AND charges_created == 1

## Violation Consequence
Duplicate charges. Regulatory breach. Chargeback liability.

## Out of Scope
Cross-service idempotency — see SPEC-018
```

Three fields in the YAML front matter make this more than a use case document. The **type** field allows AI to apply different validation strategies to safety invariants versus performance constraints. The **verified-by** link connects the specification directly to its test, so AI generating a fix for a failing test can immediately identify the specification it must satisfy. The **linked-adr** closes the loop to the architectural decision that enforces the boundary structurally — giving AI a connected graph of intent rather than an isolated document.

---

# Precondition Two: Tests as Behavioral Contracts

The most expensive tests in an AI-assisted rewrite are not the ones that fail. They are the ones that pass — on code that looks correct but preserves the old system's structural problems in new form.

The second precondition follows from the first. If requirements are specifications, tests are their executable form — the mechanism by which specification compliance is verified continuously rather than asserted once at the point of writing. **A test suite that cannot survive an architectural change is not a safety net. It is an anchor.**

The distinction that matters for a rewrite is between tests written against **implementation** and tests written against **behavior**. Tests written against implementation internals — specific classes, methods, internal data structures — are tightly coupled to the architecture of the system they were written for. Carrying them into a rewrite anchors the new implementation to the old architecture, defeating the purpose of the rewrite. Tests written against behavior — describing what the system does from the perspective of the actors that depend on it — travel across a rewrite cleanly and give AI a verifiable target without prescribing internal structure [[9]](#ref-9).

The practical implication: unit tests that test internal structure should largely be discarded. Integration and acceptance tests written at behavioral boundaries are the primary inheritance from the old system. Consumer-driven contract tests, which encode the behavioral expectations of each service consumer without prescribing how those expectations are satisfied, are particularly valuable in this context [[10]](#ref-10). Property-based tests, which assert that invariants hold across entire categories of inputs rather than specific examples, encode behavioral contracts at a level of generality that constrains AI generation effectively and surfaces edge cases that example-based tests miss [[11]](#ref-11).

**What is missing from this picture — and should not be — is the production system itself as a source of test evidence** *(proposed workflow — the Meta paper [[12]](#ref-12) covers AI-assisted test generation but the specific practice of using production logs to identify redundant tests alongside missing ones is an extension proposed here)*.

Production logs, distributed traces, and incident records contain the most accurate description of how a system actually behaves under real conditions. They reveal execution paths that tests do not cover, edge cases that design-time reasoning did not anticipate, and behavioral patterns that exist in production but have no corresponding assertion in the test suite. AI can read this evidence and act on it systematically:

- Analyse production logs and traces to identify execution paths with no corresponding test coverage — and generate candidate behavioral tests for those paths
- Identify tests in the existing suite that assert behavior that production logs show never actually occurs — candidates for removal as they test assumptions rather than reality
- Surface behavioral patterns that appear consistently in production failures and generate property-based tests that encode the invariant being violated

This is not speculative. AI-assisted test generation tools are already moving in this direction. The key shift is treating the production system as a continuous source of specification evidence — a record of what the system actually does and what it actually fails to do — rather than relying solely on design-time test authorship [[12]](#ref-12).

The test suite that emerges from this process is smaller, more behaviorally accurate, and more useful as AI generation guidance than a test suite authored entirely at design time. It also forms the foundation of the feedback loop — each production failure that reveals a missing behavioral contract can be encoded as a new test, and each test added after a failure closes the loop between what the system actually does and what the specification says it should do.

> **Key Insight:** Production logs and traces are the most accurate specification of system behavior available. AI can read them to identify missing behavioral contracts, remove tests that assert fictional behavior, and generate property-based tests that encode the invariants production failures reveal.

---

# Precondition Three: Architecture as Code

The first two preconditions constrain what the system does. This one constrains what the system is allowed to become.

Architecture documentation that lives in a wiki is not architecture governance. It is archaeology — a record of decisions that may or may not reflect what the system currently does, consulted when something breaks and someone needs to understand why.

The third precondition addresses how architectural intent is preserved and enforced in a world where AI generates code faster than human review can evaluate it. **Documentation describes. Architecture as Code enforces.** The difference is not cosmetic — it is the difference between a rule someone might read and a constraint the build will not pass without satisfying.

**Architecture as Code** is the established term for this approach [[13]](#ref-13). Rather than documenting architecture as diagrams and prose that drift from reality the moment development begins, Architecture as Code treats architectural decisions as version-controlled, executable artifacts — constraints that can be evaluated programmatically, validated automatically, and enforced continuously in the CI pipeline.

The tooling landscape for this is mature. Structurizr allows C4 architecture models to be defined in code and kept alongside the codebase they describe [[14]](#ref-14). ArchUnit encodes dependency rules as executable tests — for example, asserting that no class in the domain package imports from the infrastructure package, and failing the build if that rule is violated [[15]](#ref-15). Dependency Cruiser provides equivalent capability for JavaScript and TypeScript ecosystems [[16]](#ref-16). Custom linting rules can encode naming conventions, interface contracts, and module boundary constraints. Together these form an architectural specification that is not a document — it is a set of constraints that run on every commit.

**AI doesn't violate architecture. It obeys whatever architecture you forgot to encode.** This is why encoding matters more than reviewing. A dependency rule that exists only in a team norm will be violated the first time AI generates code under time pressure without that norm in its context. A dependency rule encoded as an ArchUnit test will fail the build regardless of what AI generated, who reviewed it, or how much pressure the team was under.

**The question of whether humans are required** deserves a direct answer. AI can detect violations of encoded rules automatically and reliably. What it cannot yet do reliably is judge whether the rules themselves are correct — whether the boundary drawn between two bounded contexts reflects the actual domain structure or an outdated assumption, whether a dependency rule encodes a meaningful architectural intent or an arbitrary historical constraint. The enforcement of rules once defined can be fully automated. The definition of the right rules still requires human architectural judgment.

**Whether AI can validate that the architecture is consistent with the requirements** is an open and important question. In principle — if requirements are written as specifications and architecture is encoded as executable constraints — AI can evaluate whether the structural rules are consistent with the behavioral requirements. In practice, the translation between a behavioral specification and an architectural rule is still a judgment that current AI cannot make reliably. This is an active area of development, not a solved problem. For now, that translation requires a human who understands both the domain and the architectural intent.

**The 80-90% similarity risk** deserves explicit acknowledgment. If AI generates code against the same domain, similar requirements, and a structurally similar architectural specification, there is a genuine risk that it produces something architecturally close to what it would have produced without the preconditions — because the preconditions narrow the solution space toward correct behavior without necessarily producing a different structure. The architectural specification must be written to encode the improvements the rewrite is meant to achieve, not merely to describe the old architecture more precisely. If the old architecture had a coupling problem between two bounded contexts, the new specification must encode a rule that prohibits that coupling — making the old structure architecturally invalid rather than merely undesirable. Without this deliberate encoding of improvements, AI will gravitate toward familiar patterns and the rewrite will produce a structurally similar system dressed in newer code.

**Reference architectures address this risk more directly than custom specifications alone.** A reference architecture is a pre-defined architectural template for a specific class of problem — a known good structural solution encoding proven patterns, boundary definitions, technology choices, and integration approaches for a well-understood domain or system type [[17]](#ref-17). Examples include Microsoft's Azure Reference Architectures for cloud workloads, the AWS Well-Architected Framework for distributed systems, and domain-specific reference architectures for healthcare data platforms or financial transaction processing. Where a custom architectural specification mostly encodes what is not allowed — prohibiting the structural problems of the old system — a reference architecture encodes what should be present: which service boundaries are appropriate, which communication patterns are proven, which integration approaches have accumulated the most operational evidence.

Providing a reference architecture as context to AI during generation meaningfully shifts its output distribution away from average training data patterns toward known good structural decisions. This is the practical realisation of your instinct about AI behaving as a junior architect: a junior architect given a reference architecture will apply it faithfully in the common cases it covers, producing better-than-average structural decisions without requiring the depth of experience that would generate those decisions from scratch.

The nuance — and it is important — is that junior architects given a reference architecture struggle precisely in the cases where it does not quite fit: where the specific domain has requirements the reference architecture's authors did not anticipate, and where judgment is required about when to follow the template and when to deviate. AI behaves identically. It applies a reference architecture well in the cases it covers and produces structurally questionable output in the edge cases it does not — without signalling that it is doing so. The human architect's role in this model is specifically to identify where the reference architecture does not fit the domain and to encode those deviations explicitly, rather than relying on AI to exercise correct judgment about them independently.

Reference architectures also carry known anti-patterns — structural decisions that look reasonable but have failed repeatedly in practice for this class of system. Encoding these as constraints alongside the reference architecture's positive guidance gives the Architecture as Code specification a head start on the feedback loop: teams can encode known failure modes preemptively from the reference architecture's accumulated experience, rather than discovering them through production failures and adding fitness functions after the fact.

The instinct to encode known good domain structure predates AI by decades and operates at multiple layers of the architecture. At the data layer, Len Silverston's *Data Model Resource Book* — first published in 1997 — provided libraries of pre-built, reusable ERDs for core business domains: orders, customers, inventory, HR, and financials, with industry-specific variants in subsequent volumes [[18]](#ref-18). At the domain model layer, Arlow and Neustadt's *Enterprise Patterns and MDA* provided a catalog of archetype patterns — reusable UML model components for entities like Customer, Product, and Order, designed to be customized to the idiosyncrasies of specific business environments [[19]](#ref-19). Both works embodied the same principle that reference architectures embody today: here is the known good structure for this class of problem, accumulated from experience across many organisations, available to accelerate design without requiring every team to rediscover the same solutions. What changes with AI is not the value of that accumulated knowledge but the mechanism of its consumption — from a human architect reading and interpreting a reference book to AI consuming structured constraints and applying them directly during generation.

> **Key Insight:** Architecture as Code shifts architectural governance from design-time documentation to continuous automated enforcement. Reference architectures, encoded as executable constraints, shift AI's output distribution from average patterns toward known good structural decisions — making the similarity risk significantly smaller. But the cases where the reference architecture does not fit the domain still require human judgment to identify and encode explicitly. AI enforces rules. Humans must ensure the rules are the right ones.

**What a fitness function looks like in practice** deserves a concrete example. Architectural fitness functions are an established concept from Ford, Parsons, and Kua [[20]](#ref-20). The specific format below — with YAML metadata linking each fitness function back to its ADR and specification — is a **proposed extension** of the established concept, designed to make the governance chain machine-navigable rather than dependent on human knowledge of which rule enforces which decision:

```yaml
---
id: FF-019
title: Payment domain must not import from infrastructure layer
enforces-adr: ADR-042
enforces-spec: SPEC-017
tool: ArchUnit           # ArchUnit | DependencyCruiser | custom-lint
severity: build-fail     # build-fail | warning | metric
threshold: 0             # zero violations permitted
---

## Rule (ArchUnit Java example)
@ArchTest
public static final ArchRule paymentDomainIsolation =
  noClasses()
    .that().resideInAPackage("..payment.domain..")
    .should().dependOnClassesThat()
    .resideInAPackage("..infrastructure..");

## What This Catches
Any class in the payment domain package importing from infrastructure —
database clients, HTTP clients, messaging adapters, framework annotations.

## What This Does Not Catch
Intent drift — whether the boundary itself is still the right one.
That requires periodic human architectural review.

## Conditions for Relaxing
Only with a corresponding ADR update explaining why the boundary
is being changed and what replaces the isolation guarantee.
```

Three fields in the metadata make this format more than an automated test. The **enforces-adr** and **enforces-spec** links mean the fitness function is not a free-floating rule — it is the enforcement arm of a documented decision with documented rationale. When it fails, AI can look up why the rule exists before generating a fix, rather than treating the violation as a style preference to be worked around. The **conditions for relaxing** field is the most important: it prevents AI from simply disabling or weakening the rule when it blocks generation, by requiring a corresponding ADR update that documents why the architectural boundary is being changed.

---

# Keeping Everything Current as AI Writes Code

The three preconditions address the starting conditions for a well-governed rewrite. A harder problem is maintaining those conditions as the rewritten system evolves — keeping requirements, tests, and architectural constraints current as AI continues to generate code after the rewrite is complete.

The drift problem is structural. AI generates decisions faster than humans can document them. Without active intervention, the gap between the specified architecture and the actual architecture widens continuously.

Three practices mitigate this effectively:

**Architecture as a definition of done.** Every pull request that changes architectural structure — introduces a new dependency, crosses a module boundary, adds an external integration — should require a corresponding update to the architectural specification. Making this a gate on merge rather than a post-hoc obligation is the difference between a living specification and a documentation graveyard.

**Structured ADRs as machine-consumable memory.** Traditional ADRs — lightweight Markdown documents storing title, status, context, decision, and consequences — were designed for human readers [[4]](#ref-4). For AI consumption they need to be enriched. The format below is a **proposed extension** of Nygard's original ADR format — adding machine-readable fields, structured rejection rationale, fitness function linkage, and explicit conditions for revisiting that no established prior format includes:

```yaml
---
id: ADR-042
title: Payment service owns its own data store
status: accepted          # proposed | accepted | deprecated | superseded
date: 2024-03-15
supersedes: []
superseded-by: []
affects: [payment-service, order-service]
tags: [service-boundary, data-ownership, consistency]
fitness-functions: [FF-018, FF-019]
---

## Context
[narrative prose describing the problem]

## Options Considered
- Option A: Shared database
  rejected-because: [structured rationale]
- Option B: Event-driven sync
  rejected-because: [structured rationale]
- Option C: Service owns its data — SELECTED

## Decision
[prose]

## Consequences
[prose]

## Conditions for Revisiting
[explicit statement of what would need to change for this decision
to be reconsidered — e.g. "reconsider if team grows beyond 15 engineers"]
```

![specification format map](/assets/img/blog/architecture-to-guardrails/specification_format_map.svg)

Three fields make this format significantly more valuable than prose ADRs for AI consumption. But before explaining them, the framing matters: **the most valuable part of an ADR is not the decision. It is the memory of what failed.**

The rejected options and their rationale are the institutional memory that prevents AI from reproducing the same approaches in the next rewrite. The **fitness-functions link** connects rationale to enforcement, so when a fitness function fails AI can look up why the rule exists before generating a fix. The **conditions for revisiting** field encodes the judgment about when a superseded decision might become relevant again — preventing AI from applying outdated decisions in contexts where circumstances have changed enough to warrant reconsideration.

**AI-assisted architecture extraction.** Tooling can infer the actual architecture of a codebase from its structure — generating dependency graphs, identifying coupling hotspots, surfacing module boundary violations, and comparing the actual architecture against the specified one [[21]](#ref-21). Used in the CI pipeline, this provides continuous validation that the specified and actual architectures remain aligned. Divergences surface as build failures rather than as gradual invisible drift.

**Periodic architectural review as a structured practice.** Fitness functions catch rule violations. They do not catch intent drift — the gradual divergence between what the architecture was designed to achieve and what the business now requires of it. This requires human judgment applied periodically: reviewing whether bounded contexts still reflect the domain, whether dependency rules still encode the right constraints, whether the fitness functions are testing the right properties. In an AI-assisted environment this review should happen more frequently, not less — the pace of change is higher and the signals of drift are subtler.

One failure mode of this governance model deserves naming directly, because it is the most insidious. **Specifications do not prevent drift. They relocate it.** A stale specification is more dangerous than missing documentation — because it is enforced. When a specification drifts from the domain it was written to represent, the governance system does not weaken. It continues enforcing with the same confidence, against constraints that are no longer correct. The system will fail exactly as specified, even when the specification is wrong. AI generation without constraints produces chaos. Constraints without understanding produce systems that fail more quietly. The periodic architectural review is not optional overhead — it is the mechanism that distinguishes a living governance system from an increasingly authoritative record of outdated decisions.

---

# The Feedback Loop: From Production Failures to Architectural Improvement

Production failures are not incidents to be resolved. They are specifications the system wrote for itself — evidence of behavior the design did not anticipate and the test suite did not encode. **Every failure that produces only a fix and no updated specification is institutional knowledge discarded.**

The preceding sections address how to govern a rewrite and maintain that governance over time. They do not fully address the hardest challenge: using the failures that production systems generate as input for architectural improvement rather than local fixes.

When a production system fails, the failure is a symptom. The architectural cause — a boundary violation, a dependency assumption, a domain concept that has drifted from its implementation — is typically not visible in the failure signal. An alert that fires because payment processing is slow does not say "this is caused by a bounded context boundary eroded in the last rewrite." A timeout under load does not say "this is caused by coupling between the order domain and inventory domain introduced when AI generated code without reference to the domain model."

Translating failure symptoms into architectural causes requires a human who understands both the failure and the system's architectural intent. This is exactly the category of understanding that architecture by reaction erodes over time. The less institutional knowledge the team has accumulated, the harder it becomes to diagnose failures as architectural problems rather than implementation bugs.

**What can be structured is the process:**

Every significant production incident should include an architectural review step — a deliberate question of whether the failure reveals a structural issue the fitness functions did not catch. Failures that reveal architectural issues should produce not just a fix but an updated fitness function — a new automated check that would have caught the structural problem earlier. Requirements that production failures expose as incomplete should be updated as specifications. Tests that production failures reveal as missing should be generated from the failure evidence and added to the suite.

This is how the feedback loop accumulates rather than degrades — not by automating away the need for architectural judgment, but by encoding the results of that judgment into the specifications, tests, and fitness functions that govern future generation. Each iteration of the loop, if run deliberately, leaves the system better specified than it was before the failure. Each iteration run carelessly — patching the symptom, leaving the cause unaddressed — leaves the system slightly less understood.

The honest answer to whether this loop can be made reliable is: more reliable, not fully reliable. The boundary of its reliability is the depth of human architectural understanding available to operate it. That understanding is the one resource AI cannot generate on demand — and it is the one resource that architecture by reaction systematically erodes.

> **Key Insight:** The feedback loop between production failures and architectural improvement accumulates knowledge when failures produce updated specifications, new fitness functions, and additional behavioral tests. It degrades when failures produce only local fixes. The difference is not tooling — it is whether the team has retained enough architectural understanding to diagnose the cause rather than just treating the symptom.

---

# What This Means for Engineering Leaders

Three practices distinguish organizations that navigate AI-assisted rewrites well from those that restart the same cycle faster.

**Use AI to extract and reformat institutional knowledge, not to replace it.** The domain knowledge required for specification-grade requirements already exists — in requirements documents, incident records, change histories, and the experience of engineers who built and maintained the system. AI can extract it, surface gaps, ask clarifying questions, and reformat it into machine-consumable specifications. Human domain expertise is the validator of that output, not the primary author of it. The investment is in structuring the extraction process, not in authoring from scratch.

**Treat the CI pipeline as the primary architectural governance mechanism.** Human architectural review cannot operate at the speed AI generates code. Fitness functions, dependency rules, and structural tests in the CI pipeline can. Every architectural rule encoded as an automated check is a unit of governance that operates continuously without consuming human review capacity [[22]](#ref-22). The investment compounds — each rule added after a production failure means that failure cannot recur undetected.

**Build the feedback loop deliberately and protect the judgment required to operate it.** Production failures are the most valuable source of architectural information available. Capturing that information — as updated specifications, new fitness functions, refined behavioral tests — is the mechanism by which the governance model accumulates knowledge rather than degrading. But the loop requires human architectural judgment to translate symptoms into causes. That judgment erodes when generation outpaces comprehension. Protecting it — through deliberate comprehension investment, through architectural reviews that prioritize understanding over velocity — is the leadership decision that determines whether the loop runs well or runs empty.

**Know when a rewrite is justified and when it is not.** The governance model described in this post is not free. Writing specification-grade requirements, designing behavioral test suites, encoding architectural constraints, and maintaining structured ADRs represents real investment. That investment is justified when the expected system lifespan is long, when regulatory or compliance exposure is high, when the domain is complex and domain knowledge hard to replace, or when the rewrite is intended to produce a materially different architecture rather than cleaner code. It is not justified when the system is genuinely short-lived, when the domain is simple and well-understood, or when the cost of the governance model exceeds the cost of the architectural debt it prevents. The threshold question is not "should we govern our AI-assisted rewrite?" but "does the expected value of this system warrant the investment in governing it properly?" A system expected to run for five years with a team of ten engineers warrants the full model. A prototype expected to be replaced in eighteen months does not.

**Know when an organization is not ready to rewrite yet.** The preconditions are not just preparation for a rewrite — they are diagnostic criteria for rewrite readiness. An organization that cannot articulate its domain invariants with enough precision to write verifiable specifications is not ready. An organization whose test suite consists entirely of implementation-coupled unit tests is not ready — the behavioral contracts that a rewrite needs as a target do not exist. An organization with no ADRs has no institutional memory to feed to the AI governing the rewrite. In each of these cases, the right investment is not to begin the rewrite but to build the preconditions first. The governance model built during that investment will serve not just the rewrite but every iteration of the system that follows it.

![Rewrite readiness diagnostic](/assets/img/blog/architecture-to-guardrails/rewrite_readiness.svg)

**Address the test pyramid explicitly before a rewrite begins.** A rewrite inherits a test suite that was written for a system it is about to discard. Not all of that suite travels. Unit tests that test implementation internals — specific classes, methods, internal state — should be discarded. They are anchors to the old architecture and will constrain the new one. Integration tests written at behavioral boundaries should be kept and reviewed for continued relevance. Acceptance tests and contract tests that encode behavioral expectations at system and service boundaries are the primary inheritance — they are the executable specifications that tell AI what the new system must do without prescribing how it must be built. The rule of thumb: if a test would fail because you changed how something works internally but not what it does externally, discard it. If a test would fail only because the system stopped doing something users or dependents rely on, keep it.

**Redefine what architects do.** This is the point most leadership conversations about AI and architecture avoid. The governance system described in this post does not reduce the need for architects — it changes what they are for. Before AI, the architect designed systems and enforced discipline through review and team norms. In an AI-assisted organisation with a mature governance system, the architect's primary responsibility is authoring the governance system itself — what the SDD literature calls **SpecOps**: treating specification authoring as a first-class engineering surface, with the same quality practices, version control, and continuous improvement applied to production code [[23]](#ref-23). In practice this means writing specifications precise enough to constrain generation, defining the ADR structure that preserves institutional memory, encoding fitness functions that enforce architectural intent, diagnosing production failures as architectural causes rather than implementation bugs, and judging when conditions for revisiting a decision have genuinely been met. This is a more abstract job than designing systems directly — and a more powerful one. A well-designed governance system enforces architectural intent at the speed AI generates code. No architect reviewing pull requests manually ever could.

The operationalization of this approach is already visible in production tooling. Addy Osmani's open-source Agent Skills project encodes the workflows and quality gates that senior engineers actually use — spec before code, test before merge, ADRs in the ship checklist — as structured markdown skills that AI agents can consume as persistent context governing every development decision [[24]](#ref-24). This is governance-system authorship made practical: the architect defines the skills, the agent operates within them. The question for engineering leaders is not whether architects are still needed. It is whether the architects on their teams have been given the mandate, the tools, and the time to author governance systems rather than just review code.

---

# Conclusion: The Loop Can Be Made Better. It Cannot Be Made Automatic.

There is one thing this post should say directly that it has so far only implied: **externalising knowledge does not remove the cognitive load ceiling. It moves it.**

Before — the ceiling was in the code. Teams hit it when they could no longer hold the system in their heads. Now — the ceiling is in the governance system. Teams hit it when they can no longer reason confidently about the specifications, fitness functions, ADRs, and feedback loops that govern the code. That is harder — but it fails differently. Cognitive overload in code is invisible until the system breaks in production. Cognitive overload in governance surfaces as failing fitness functions, broken tests, and violated constraints — signals that are detectable, versionable, and correctable before failure propagates. The governance ceiling is not lower. It is more visible. And visibility is what makes it manageable.

The feedback loop can be made more reliable. It cannot be made automatic. At every point — translating failure symptoms into architectural causes, validating that fitness functions test the right properties, judging when an ADR's conditions for revisiting have been met — human architectural understanding is required. That understanding is now directed not at the code but at the governance system. The architect's job is to keep that system honest.

The question is not whether to build guardrails. It is whether the people building them understand that the guardrails themselves are now the system.

---

## Sources & Further Reading

[1] <a name="ref-1"></a>[**Aathawerani, A. (2026)** — *The Cognitive Load Ceiling: What AI Is Doing to Software Architecture — and Why the Rewrite Is No Longer the Last Resort*](https://aathawerani.github.io/systems/scale/2026/03/23/cognitive-load-ceiling/)
The preceding post in this series, establishing the cognitive load ceiling framework and its disruption by AI-assisted development.

[2] <a name="ref-2"></a>[**Spolsky, J. (2000)** — *Things You Should Never Do, Part I*](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/)
Joel on Software.
The landmark essay establishing the economic — not technical — case against rewrites, grounded in the institutional knowledge cost of discarding working systems.

[3] <a name="ref-3"></a>[**Faye, L. (2025)** — *Agentic Coding is a Trap*](https://larsfaye.com/articles/agentic-coding-is-a-trap)
larsfaye.com.
A practitioner critique of agentic coding workflows identifying the supervision paradox — that delegating code generation to AI erodes the engineering skills required to supervise what AI produces, creating a compounding vulnerability as delegation increases.

[4] <a name="ref-4"></a>[**Nygard, M. (2011)** — *Documenting Architecture Decisions*](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
Cognitect Blog.
The original proposal for Architecture Decision Records as lightweight, version-controlled documents capturing architectural decisions and their rationale — the conceptual foundation for the structured, machine-consumable ADR format described in this post.

[5] <a name="ref-5"></a>[**Griffin, L. & Carroll, R. (2026)** — *Spec Driven Development: When Architecture Becomes Executable*](https://www.infoq.com/articles/spec-driven-development/)
InfoQ.
Introduces Spec-Driven Development as a methodology in which specifications become executable system truth, architecture is enforceable through continuous validation, and human authority shifts upward from implementation to intent.

[6] <a name="ref-6"></a>[**acai.sh (2025)** — *Specsmaxxing*](https://acai.sh/blog/specsmaxxing)
acai.sh.
A practitioner account of the benefits of writing feature specifications in structured formats rather than natural language prompts — demonstrating that structured specifications substantially reduce AI's tendency to generate contextually wrong output. Independent practitioner convergence on the spec-as-constraint principle that SDD formalises.

[7] <a name="ref-7"></a>[**Wiegers, K. & Beatty, J. (2013)** — *Software Requirements, 3rd Edition*](https://www.microsoftpressstore.com/store/software-requirements-9780735679665)
Microsoft Press.
Comprehensive treatment of requirements engineering with specific guidance on writing verifiable, unambiguous specifications that function as constraints rather than guidance.

[8] <a name="ref-8"></a>[**Cockburn, A. (2000)** — *Writing Effective Use Cases*](https://www.pearson.com/en-us/subject-catalog/p/writing-effective-use-cases/P200000009217/9780321605801)
Addison-Wesley.
The definitive practical guide to UML use case specification, establishing the actor/precondition/main flow/alternate flow/postcondition/exception flow structure — the established foundation on which the hybrid specification format in this post builds.

[9] <a name="ref-9"></a>[**Freeman, S. & Pryce, N. (2009)** — *Growing Object-Oriented Software, Guided by Tests*](https://www.oreilly.com/library/view/growing-object-oriented-software/9780321574442/)
Addison-Wesley.
Establishes the distinction between tests written against behavior and tests written against implementation, with practical guidance on designing tests that survive architectural change.

[10] <a name="ref-10"></a>[**Richardson, C.** — *Consumer-Driven Contract Testing*](https://microservices.io/patterns/testing/service-integration-contract-test.html)
microservices.io.
Describes the consumer-driven contract testing pattern, which encodes behavioral expectations at service boundaries in a form that constrains implementation without prescribing structure.

[11] <a name="ref-11"></a>[**Hughes, J. (2019)** — *How to Specify It: A Guide to Writing Properties of Pure Functions*](https://link.springer.com/chapter/10.1007/978-3-030-47147-7_4)
Midlands Graduate School lecture notes.
Practical guide to property-based testing, covering how to identify and express behavioral invariants that hold across entire categories of inputs rather than specific examples.

[12] <a name="ref-12"></a>[**Alshahwan, N. et al. (2024)** — *Automated Unit Test Improvement Using Large Language Models at Meta*](https://arxiv.org/abs/2402.09171)
arXiv:2402.09171.
Documents Meta's production experience using LLMs to improve existing test suites at scale, including identification of missing coverage and generation of additional behavioral tests from production evidence.

[13] <a name="ref-13"></a>[**Keeling, M. (2017)** — *Design It! From Programmer to Software Architect*](https://pragprog.com/titles/mkdsa/design-it/)
Pragmatic Bookshelf.
Introduces architecture as a first-class engineering practice with executable, version-controlled artifacts, providing the conceptual foundation for Architecture as Code approaches.

[14] <a name="ref-14"></a>[**Brown, S.** — *Structurizr: Architecture as Code*](https://structurizr.com)
structurizr.com.
The leading tool for defining C4 architecture models as code, enabling architecture diagrams to be generated from version-controlled definitions that live alongside the codebase they describe.

[15] <a name="ref-15"></a>[**Kůdela, P.** — *ArchUnit User Guide*](https://www.archunit.org/userguide/html/000_Index.html)
ArchUnit.
Documentation for ArchUnit, which encodes architectural dependency rules as executable tests that run in the standard test suite and fail the build on violation.

[16] <a name="ref-16"></a>[**Brugts, S.** — *Dependency Cruiser*](https://github.com/sverweij/dependency-cruiser)
GitHub.
Open source tool for validating and visualising dependencies in JavaScript and TypeScript projects, providing Architecture as Code enforcement equivalent to ArchUnit for JVM ecosystems.

[17] <a name="ref-17"></a>[**Bass, L., Clements, P. & Kazman, R. (2012)** — *Software Architecture in Practice, 3rd Edition*](https://www.oreilly.com/library/view/software-architecture-in/9780132942799/)
Addison-Wesley.
The foundational academic treatment of software architecture patterns and reference architectures, defining reference architectures as pre-defined structural solutions to well-understood classes of problems.

[18] <a name="ref-18"></a>[**Silverston, L. (2001)** — *The Data Model Resource Book, Volumes 1 & 2*](https://www.amazon.com/Data-Model-Resource-Book-Vol/dp/0471380237)
Wiley.
A library of pre-built, reusable ERDs for core business domains and industry-specific models — an early embodiment of the reference architecture instinct at the data layer.

[19] <a name="ref-19"></a>[**Arlow, J. & Neustadt, I. (2004)** — *Enterprise Patterns and MDA: Building Better Software with Archetype Patterns and UML*](https://www.oreilly.com/library/view/enterprise-patterns-and/032111230X/)
Addison-Wesley.
A catalog of reusable archetype patterns — UML model components for common business entities like Customer, Product, and Order — operating at the domain model layer.

[20] <a name="ref-20"></a>[**Ford, N., Parsons, R. & Kua, P. (2017)** — *Building Evolutionary Architectures: Support Constant Change*](https://www.oreilly.com/library/view/building-evolutionary-architectures/9781491986356/)
O'Reilly Media.
Introduces architectural fitness functions as automated mechanisms for continuously validating architectural properties, enabling organizations to detect structural drift before it becomes unmanageable — the established concept on which the proposed fitness function format in this post builds.

[21] <a name="ref-21"></a>[**Tornhill, A. & Borg, M. (2022)** — *Code Red: The Business Impact of Code Quality*](https://arxiv.org/abs/2203.04374)
IEEE/ACM International Conference on Technical Debt.
Empirical study demonstrating that automated code health analysis can identify coupling hotspots and structural decay patterns — providing the evidence base for AI-assisted architecture extraction as a continuous governance practice.

[22] <a name="ref-22"></a>[**Humble, J. & Farley, D. (2010)** — *Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation*](https://www.oreilly.com/library/view/continuous-delivery-reliable/9780321670250/)
Addison-Wesley.
The foundational text establishing the CI/CD pipeline as a continuous quality governance mechanism — the conceptual basis for treating the pipeline as the primary enforcement layer for architectural constraints in an AI-assisted development environment.

[23] <a name="ref-23"></a>[**Krishnan, H. (2026)** — *Spec-Driven Development: Adoption at Enterprise Scale*](https://www.infoq.com/articles/enterprise-spec-driven-development/)
InfoQ.
Extends the SDD framework to enterprise adoption, introducing the concept of SpecOps — specification authoring as a first-class engineering surface with the same quality practices and version control applied to production code.

[24] <a name="ref-24"></a>[**Osmani, A. (2026)** — *Agent Skills*](https://addyosmani.com/blog/agent-skills/)
addyosmani.com.
An open-source collection of production-grade engineering skills for AI coding agents — spec before code, test before merge, ADRs in the ship checklist — as structured markdown that agents consume as persistent governance context.
