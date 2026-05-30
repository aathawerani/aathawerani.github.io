---
layout: post
title: "Tech Stack Selection in the Age of AI Agents"
excerpt: "AI-assisted development changes stack selection in a fundamental way: the stack is no longer just a decision about runtime characteristics or team familiarity. It is also a decision about how much leverage AI tooling can provide. The architectural disciplines established at prototype stage determine whether future migration becomes incremental or catastrophic."

author: ali
date: '2026-05-27 00:00:00'
category: ['systems', 'scale']
thumbnail: /assets/img/blog/tech-stack-ai-agents/tech-stack-ai-agents-thumbnail.png
---


## A Third Variable

Historically, stack selection was driven by two things: the problem domain and the team's existing expertise. Both remain relevant. But there is now a third variable that most teams are not accounting for explicitly.

The AI agent writing the code has strong opinions about what it can build well — and those opinions do not always align with what would be optimal for the domain.

AI coding capability is not uniform across stacks. It is ecosystem-relative.

> An AI agent is not simply a language model applied to a codebase. It is a language model whose suggestions are calibrated to the density and quality of training examples it has seen — and that density varies enormously across stacks.

![AI stack confidence spectrum](/assets/img/blog/tech-stack-ai-agents/ai_stack_confidence_spectrum.svg)

Python dominates that distribution. An AI agent generating FastAPI route handlers, SQLAlchemy models, Pydantic schemas, and Alembic migrations is not just producing correct Python. It is producing *idiomatic* Python — the specific patterns and library combinations that the ecosystem has converged on over years of collective practice. That is qualitatively different from an AI that knows the syntax but not which library to reach for, which patterns are now antipatterns, or which dependency combinations cause subtle production issues.

Vite and React sit in the same high-confidence tier on the frontend. The AI is operating close to the centre of its training distribution: component structure, state management patterns, API integration conventions — all rendered with high reliability.

This has a practical implication that is worth stating directly: your stack choice is also, implicitly, a choice about what kind of AI partner you will have for the next six months. The students at Habib University's Kaavish exhibition who built Python + Vite prototypes were not making a conservative choice. They were making a strategically correct one — working where the tools are strongest rather than where the theory is most elegant.

There is a further dimension to this that the AI fluency argument alone does not capture: Python and TypeScript are also globally the most hireable stacks, with the deepest tutorial ecosystems, the largest communities, and the most abundant open-source examples. When a new engineer joins the team, onboarding them onto idiomatic FastAPI or React is weeks faster than onboarding them onto a niche framework with thin documentation. The AI and the hiring market are pointing in the same direction. That alignment is not a coincidence — both are responding to the same underlying signal about where collective expertise has concentrated.

Go, Rust, and Kotlin sit at an intermediate layer — well-represented for common patterns but with more variance at the edges. The further you move from the high-confidence core, the more the AI shifts from an asset into a liability — not useless, but less consistent in ways that compound.

The stack choice is the first architectural decision. It is also the decision that determines how much leverage everything else provides.

---

## Build Only the USP. Buy Everything Else.

Speed at prototype stage comes from a simple principle: build only what makes your product worth using, and buy everything else.

Every software product has to solve a class of problems that have nothing to do with why users would choose it over an alternative. Authentication, file storage, email delivery, SMS, database hosting, queuing, monitoring — none of these are why a logistics product wins customers. No fintech product earns its market position because it built a better password reset flow. These are cross-cutting concerns: genuinely hard problems that every product has, solved reliably by specialised providers, with no competitive advantage to be gained from building them from scratch.

A second category sits just beyond: domain-specific commodity services. Payments via Stripe. Maps via Google Maps or Mapbox. Identity verification via a KYC provider. These feel like core functionality because they are domain-specific — but they are common enough across the domain that mature solutions already exist. A fintech prototype does not need to build its own payment processing. A logistics prototype does not need to build its own geocoding.

What remains after removing both categories is the core business value — the unique insight, the differentiated workflow, the algorithm or data model that is the actual reason the product should exist. This is the only thing worth building from scratch. This is what the prototype validates.

![Buy vs build zones](/assets/img/blog/tech-stack-ai-agents/buy_vs_build_zones.svg)

**The cost curve will shift.** As the product scales, what was negligible becomes significant. The economics of self-hosting gradually improve relative to the managed service cost — a pattern documented across software organisations at growth stage. When that crossover arrives, the managed service should be replaceable without touching the rest of the system.

That is only true if every managed service sits behind one interface file from day one. `auth/provider.py`. `storage/provider.py`. `notifications/provider.py`. When Supabase Auth's pricing model changes at scale, the migration scope is one file. When email volume justifies a self-hosted mail server, the migration scope is one file. The rest of the codebase does not know and does not care.

> Buy the cloud. Own the seam.

---

## The Architecture of the Prototype That Survives

The prototype built with speed but no structural discipline tends to become the thing that blocks future progress — not because it failed, but because it succeeded. It is in production. Real users depend on it. And the choices that were fine for a learning exercise are now structural problems with no clean way out.

The disciplines below are not overhead. Each takes an hour or a day at prototype stage and saves weeks or months at migration stage.

**Organise by domain, not by technical layer.** An `orders/` module, a `drivers/` module, a `payments/` module — rather than `models/`, `routes/`, `services/` layers that scatter every feature across the whole codebase. The domain module is the unit of future extraction. Without it, there is no clean unit to extract.

**Manage the schema from the first table.** Use Alembic, Flyway, or Prisma Migrate from day one. The schema is the most expensive thing to change under real load. A migration history that starts at the first table is a foundation. A schema altered ad hoc is an archaeology problem.

**Wrap every external dependency behind one interface file.** Every third-party service — auth provider, payment processor, storage, email — sits behind a single file all callers use. This is the one structural decision that keeps every future replacement bounded to one file and one scope of work.

**Delegate authentication entirely.** Auth couples to the user model, the session model, and the permission model — all of which accumulate real data quickly. The prototype's auth tends to become the production auth. Buying it from a provider is not a shortcut. It is the correct decision for the stage.

**Add structured logging with a correlation ID before you have users.** A JSON log event with a consistent shape, carrying a correlation ID that propagates through every downstream call, is almost free to add early and expensive to retrofit. It is also the specific precondition for safely validating a replacement service against real production traffic before switching over.

**Run in containers from day one.** Docker forces enumeration of every dependency the system has — runtime version, system packages, environment variables. The result is a system that can be deployed anywhere and can host transitional infrastructure without environment drift.

**Separate configuration from code.** Environment variables for all configuration. A `.env.example` that documents what is required without values. The gap between staging and production configs, under go-live pressure, is where secrets get committed to version control.

![Module boundary seam diagram](/assets/img/blog/tech-stack-ai-agents/module_boundary_seam_diagram.svg)

---

## Test the Boundaries, Not the Implementation

TDD has a reputation problem at prototype stage. Writing tests before code feels like overhead when learning speed is everything. The resolution is not to abandon testing — it is to be precise about what to test.

Tests at prototype stage should cover boundaries, not implementation details. The internal workings of a module can be refactored freely as the domain is better understood. What cannot change without consequences is the contract a module exposes — the service interface between modules, the API contract between backend and frontend, the interface file wrapping an external vendor. These are the seams. The seams need tests. Everything else is optional until it proves itself worth testing.

This connects directly to AI-assisted development in a specific way. Given a well-defined Pydantic schema and a service function signature, an AI generates meaningful boundary tests — happy paths, edge cases, error conditions — faster than a human. Given an unstructured codebase, the same AI generates tests tightly coupled to implementation details: tests that break on every refactor, produce false confidence, and eventually get deleted. The template precedes the tests. Boundary discipline is what makes AI-generated tests worth having.

The tests that directly enable future migration are worth naming: a service interface test suite means that when you rewrite the implementation behind a module boundary — a new Go service, a self-hosted auth provider, a rewritten notification engine — you run the same tests against the new implementation and know immediately whether it behaves correctly. That is the confidence mechanism for safe replacement. Without it, every migration requires manual verification of every behaviour the old implementation had.

One anti-pattern to name explicitly: testing the framework instead of the domain. Tests that verify FastAPI routing mechanics or SQLAlchemy query behaviour are testing things the libraries already guarantee. The valuable tests are on domain logic — does the order state machine transition correctly, does the pricing calculation handle the edge cases the business cares about. The framework can be trusted. The domain logic cannot.

The practical starting point: one test file per module service interface, one per API endpoint group, one mock-based test per external dependency wrapper. That is the minimum that keeps migration options open.

---

## The UI Gap Nobody Talks About

There is a dimension missing from almost every architectural template written for early-stage products: the frontend.

Backend architecture gets most of the attention. The frontend is treated as the presentation layer — a consequence of the real decisions, not a domain with its own architectural concerns. This is a mistake that becomes expensive exactly when the product starts to scale.

**Frontend debt is cognitive rather than structural.**

> Frontend debt is cognitive rather than structural. Not coupling between modules or schema problems, but components that grew without boundaries, state in the wrong place, API calls inlined wherever first needed and then copied rather than centralised. The result is a frontend that resists change because there are no clean units to work with.

Three rules prevent this:

**Organise by feature, not by type.** A `components/` folder containing both `Button` and `OrderSummaryCard` is not a components folder — it is a pile. `OrderSummaryCard` contains business logic, depends on a specific data shape, and belongs in `features/orders/components/`. Genuinely shared UI belongs in `components/`. Domain-specific code lives in its feature folder. This boundary is the frontend equivalent of the domain module.

```
src/
  features/
    orders/
      components/  # OrderList, OrderCard — orders-specific
      hooks/       # useOrders, useOrderStatus
      types.ts
    payments/
      components/
      hooks/
      types.ts
  components/      # Button, Modal, Input — shared, no business logic
  services/        # ALL API calls — never inline fetch in components
  hooks/           # shared hooks, no domain specificity
```

**Centralise all API calls in a services layer.** `src/services/` owns every external call — typed functions that speak the domain language (`getOrderById(id)`, not `get('/api/v1/orders/{id}')`), with consistent error handling. The AI's instinct is to put the fetch call where the data is first needed. A template that encodes the rule changes what the AI generates.

**Layer state management deliberately.** Server state — data from the API — belongs in TanStack Query. It has its own caching and invalidation semantics that a global store does not handle well. UI state belongs at the component or feature level. Only genuinely global state — current user, authentication — belongs in a global store. Without this in the template, the AI reaches for the global store for everything.

---

## Encoding It All in a Template

The disciplines above — domain modules, schema migrations, interface files, boundary tests, feature-based frontend structure — are the decisions that need to be made before the first line of code is written. Left unencoded, each one will be reinvented inconsistently across sessions as the AI fills the gaps with whatever seemed convenient at the moment.

The previous post in this series argued that architecture is shifting from diagrams to guardrails — from documentation that describes intent to constraints that enforce it. The practical form of that at prototype stage is a `CLAUDE.md` at the root of the repository: not a prompt, but an architecture. Every rule in the file is implicitly an ADR — a decision with a rationale that future engineers and AI agents can read rather than re-derive.

A minimal template for a Python + Vite stack, encoding everything covered above:

```markdown
# Architecture Template

## Stack
- Backend: Python 3.12, FastAPI, SQLAlchemy 2.0, Alembic, Pydantic v2
- Frontend: Vite, React 18, TypeScript, TanStack Query
- Auth: Supabase Auth — never implement custom auth
- Database: PostgreSQL — all schema changes via Alembic migrations only

## Backend Module Structure
Organise by domain, not by technical layer.

src/
  auth/          # authentication, session management
  users/         # user profiles, preferences
  orders/        # order lifecycle
  payments/      # payment processing
  notifications/ # outbound communication

Each module: models.py, schemas.py, service.py, router.py
No cross-module imports except through explicit service interfaces.

## External Dependency Rule
Every third-party service behind one interface file.
src/payments/provider.py      — all Stripe calls go here, nowhere else.
src/notifications/provider.py — all SendGrid calls go here, nowhere else.

## Schema Management
Every database change through an Alembic migration. Never ALTER TABLE manually.

## Logging
Structured JSON via structlog. Every request carries a correlation_id.

## Configuration
All config via environment variables. No hardcoded credentials.
.env.example documents required variables without values.

## Frontend Structure
src/
  features/    # one folder per domain (orders/, payments/, users/)
  components/  # shared UI only — no business logic, no API calls
  services/    # ALL API calls here — never inline fetch in components
  hooks/       # shared hooks only

## Testing
One test file per module service interface.
One test file per API endpoint group.
One mock-based test per external dependency wrapper.
Test domain logic, not framework mechanics.
```

This file is written once. From that point, the AI follows it — not because it has judgment, but because it has instructions. The judgment was applied once, by a human, at the start.

---

## Designing for Replaceability

Here is what connects every discipline above to the long-term arc of the product: the patterns used to migrate a system to enterprise grade each have a precondition, and those preconditions are exactly what good prototype architecture creates.

Three structural choices — made once, at the start — keep every migration option available later. Miss them and the only path forward is a full rewrite.

**The seam is everything.** Michael Feathers called the natural split points in a codebase *seams* — places where the system can be divided without modifying surrounding code. In a prototype, the domain module boundary is the seam. The Strangler Fig pattern — where new services are built alongside the old system and traffic is redirected one capability at a time — only works if a clean intercept point exists. The domain module, with its own router and service interface, is that intercept point. A codebase with no module boundaries has no seams. A codebase with no seams cannot be incrementally migrated. It can only be rewritten.

**The interface file is the abstraction that already exists.** Branch by Abstraction — for replacing a component deep inside the system — requires first introducing an abstraction and migrating all callers to use it. That is the expensive step. The external dependency interface file does this work at day zero. When the time comes to swap Stripe for a self-built payment service, or replace Supabase Auth with a self-hosted provider, the abstraction already exists and every caller already uses it. The same logic applies to cloud services: buying managed solutions behind interface files means the cost-curve replacement is always one file deep.

**The feature boundary is what makes UI migration possible.** Replacing a frontend section by section — while the rest continues running — requires that sections are independently identifiable and modifiable. In its coarser form, an entire page or user-type experience is the replacement unit: a new driver dashboard built as a separate application, calling the existing backend through its clean API surface, with traffic routing directing drivers to the new version while the customer experience is untouched. In its finer form, individual widgets within a page are replaced one at a time. Both require the feature-based frontend structure. A frontend where every domain's code is scattered across shared folders requires untangling before it can be extended.

Three further patterns follow from the remaining disciplines without requiring their own names. The migration-based schema enables making schema changes on a live system in phases — add the new structure alongside the old, migrate data, remove the old — each phase independently deployable and reversible, no maintenance window required. The correlation ID in structured logs enables running old and new implementations simultaneously on real traffic and comparing outputs before committing to the switch — the precondition being that you can match a request across both systems. And for multi-sided products, keeping each user type in its own vertical slice means one side can be migrated completely while the other continues on the original system.

One anti-pattern deserves explicit naming: Feature Parity — the requirement that a replacement must reproduce every behaviour of the old system before any switch can happen. In the prototype-to-enterprise context this most often appears when a team takes over a solo-built prototype and treats every undocumented quirk as a feature to preserve. The correct target is outcome parity — the new implementation delivers the same results for users — not behavioural parity. Architecture Decision Records are the countermeasure: an ADR explaining why a workaround exists gives the replacement team permission to do better rather than obligation to replicate.

What this collection of disciplines adds up to is something Neal Ford and Rebecca Parsons called *evolutionary architecture* — systems designed to adapt through guided incremental change rather than periodic rewrites. The three mechanisms they identify map directly onto what the prototype disciplines create: appropriate coupling (domain module boundaries that let parts change independently), incremental change (deployable units small enough to be reversible, enabled by migration-based schemas and interface files), and fitness functions (the boundary test suite that verifies architectural properties — the interface contracts — hold after every change). The prototype built with these disciplines is not just easier to migrate. It is a system that can evolve continuously, absorbing change without accumulating the structural debt that eventually forces a rewrite. The migration patterns described above are not the destination. They are the evidence that the architecture is working as designed.

---

## What the Prototype Disciplines Protect

![Disciplines to patterns map](/assets/img/blog/tech-stack-ai-agents/disciplines_to_patterns_map.svg)

> The seam is the module boundary. Everything else follows from having drawn it deliberately.

---

## Domain Gravity and the Migration

The disciplines above address what survives from the prototype. They do not address whether the prototype's stack is the right substrate for the product's second and third years.

Different domains have accumulated expertise and ecosystem depth in specific stacks, and that accumulation represents real leverage. Financial systems have decades of compliance tooling and institutional knowledge embedded in JVM ecosystems — Java and Kotlin won that domain not by preference but because the ecosystem was shaped by the actual demands of high-volume transaction processing and regulatory compliance. A fintech prototype that starts in Python should plan for the possibility that its transaction processing core will eventually need stronger type guarantees and richer compliance tooling.

Real-time systems have their own gravitational pull towards strong concurrency models. Elixir and Erlang exist because the telecom industry needed fault tolerance that most languages could not deliver architecturally. A product that starts as a Python messaging layer and grows into a real-time communication platform will at some point encounter constraints inherent to Python's concurrency model, not to any specific implementation choice.

Machine learning has converged almost entirely on Python — not because it is fastest for numerical computation, but because the ecosystem of libraries, tooling, and practitioners is so concentrated there that building outside it means building without the compounding effect of that accumulated expertise. For ML-heavy products, Python is both the AI-fluent choice and the domain-correct choice. The prototype stack and the production stack are the same stack.

The migration in each case is not a correction of the original choice. It is a natural stage in the product's maturation — pulled by specific, observed constraints rather than pushed by theoretical elegance. The disciplines of the prototype stage are what make each migration a bounded step rather than a crisis.

---

## What This Looks Like End to End

A team building a two-sided logistics platform starts with Python + FastAPI + Vite + React. Before writing the first line of code, they create a `CLAUDE.md` defining the module structure, dependency rules, vendor wrapping policy, logging format, and frontend organisation. The AI agent reads this as context on every task.

The backend has domain modules: `routes/`, `drivers/`, `customers/`, `notifications/`. Supabase Auth handles authentication. Stripe handles payments. Google Maps handles geocoding. Each external service sits behind one interface file. Alembic migrations from the first table. Docker from day one. Correlation IDs in structured JSON logs. An ADR written for every non-obvious decision. One test file per module service interface, one mock-based test per external dependency wrapper.

The frontend has `features/driver/`, `features/customer/`, `features/ops/`. All API calls through `src/services/`. TanStack Query for server state. TypeScript types for every API response shape.

Six months later, twelve customers. The route optimisation engine takes eight seconds under load. The module boundary is already clean, the service interface already tested. A Go rewrite of that one module, validated against the existing test suite and confirmed with a parallel run on real traffic, takes three weeks. The rest of the system is untouched.

A year later, the driver experience needs a complete redesign. UI Composition: a new driver application built separately, calling the existing FastAPI backend through its clean API surface. The routing layer sends driver traffic to the new application. The customer experience is untouched.

Eighteen months in, expansion to a new market with compliance requirements that affect the customer product only. Extract Product Lines: the customer-facing backend migrates to a new stack with the required tooling. The driver product continues on the original system. The vertical slice discipline makes the boundary clean enough to migrate independently.

Two years in, real-time tracking volume exposes Python's concurrency limits. The `tracking/` module migrates to Elixir. Expand/Contract handles the schema migration without a maintenance window. The rest of the system does not change.

At no point was the original choice treated as permanent. At no point was anything rewritten for its own sake. The `CLAUDE.md` written before the first commit is the reason each migration remained a bounded step rather than a crisis.

![Prototype to enterprise timeline](/assets/img/blog/tech-stack-ai-agents/prototype_to_enterprise_timeline.svg)

---

## The Right Stack at the Wrong Stage

The stack does not exist in isolation. It is part of a system that includes the team building on it, the AI tools assisting with that building, the domain it is solving problems in, and the stage of the product's life.

The architecture template connects all these dimensions. It encodes the stack, the structural rules, and the conventions that give the AI a consistent context to work within. It also — as a byproduct of genuine discipline rather than premature optimisation — encodes the preconditions for the migration options that will be needed later.

The right stack at the wrong stage is a wrong choice. The wrong stack, built with the right disciplines, can still get you to the point where you can make a better decision — with real data, real constraints, and a codebase structured to support the change.

> The right stack at the wrong stage is a wrong choice.

That is what separates products that compound from products that get stuck.

---

*Ali Ahmed Thawerani writes about the systems, culture, and scale decisions that shape how technology organisations develop. TECTONICS publishes at [aathawerani.github.io](https://aathawerani.github.io).*

