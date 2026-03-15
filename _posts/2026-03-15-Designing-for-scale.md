---
layout: post
title: "Designing for Scale Before Scaling: A Practical Reading of Conway's Law"
date: 2026-03-15
categories:
  - systems
  - culture
  - scale
author: ali
toc: true
excerpt: "A structural perspective on scaling fintech systems using Conway's Law. Exploring how organizational structure, branching strategy, and delivery mechanics must align before growth introduces irreversible complexity."
thumbnail: /assets/img/blog/designing-for-scale/designing-for-scale-thumbnail.png
---

## Where Organizational Structure Meets Delivery Economics

> "Organizations design systems that mirror their own communication structure."  
> — Melvin Conway, 1967

Conway wrote that sentence nearly sixty years ago, and engineering teams are still learning it the hard way. Not in architecture reviews. Not in retrospectives. In production, at 2am, when a revert cascades into three new incidents, or when a customer upgrade fails because two teams integrated the same shared schema in incompatible ways and nobody noticed until it was far too late.

Conway's Law is not a philosophical observation you cite in a tech talk and then ignore. It is a diagnostic instrument. It will tell you, with brutal accuracy, exactly where your organization is lying to itself.

In a [previous post on Practical Git Branching for FinTech](https://aathawerani.github.io/systems/culture/scale/2026/01/02/practical-git-branching-fintech/#/), I laid out a concrete branching model for multi-customer financial systems — how release branches protect delivery stability, how customer isolation preserves contractual commitments, and how the structure of your Git workflow must reflect the realities of on-prem deployments, schema evolution, and regulatory versioning.

That post answered the question: *what does a sound delivery structure look like?*

This post asks the question that should come before it: *why does your delivery structure need to look like that — and does your current structure honestly reflect your organizational reality?*

The answer runs through Conway's Law. Not as philosophy. As a diagnostic.

---

## The Question Nobody Asks Before It's Too Late

When an engineering team starts struggling — with integration conflicts, unstable releases, endless hotfixes, or customer-specific regressions — the instinct is to reach for process. Add a staging branch. Create a feature freeze window. Gate things more tightly.

Sometimes those interventions help. Often, they obscure the real problem long enough that it metastasizes.

The question that actually matters is not "which Git branching model should we use?" It is: **where does complexity actually live in our organization — and is our delivery structure honest about that?**

In multi-customer FinTech systems specifically, there are two completely different kinds of complexity that constantly get conflated, and conflating them is the root cause of most branching dysfunction in the wild.

**Development coordination complexity** asks: how many teams integrate simultaneously? How large are the parallel workstreams? How tightly coupled are the modules? How mature are the internal API contracts? How often do teams step on each other?

**Delivery and release complexity** asks: how many customers are on different versions? What are the contractual version commitments? How divergent are customer-specific configurations? How constrained are database upgrade paths? What does the regulatory audit trail require?

These two forces are not just different in degree. They are orthogonal in nature. You cannot solve delivery complexity by adding more development branches, and you cannot solve coordination complexity by tightening your release governance. Teams that don't make this distinction clearly will end up building a branching strategy that attacks the wrong problem — and they will feel busy and rigorous while doing it.

If you misidentify where your real complexity resides, your branching model will fight your architecture instead of supporting it.

![Development-Delivery matrix](/assets/img/blog/designing-for-scale/two_complexities_matrix.svg).

---

## What Conway's Law Actually Tells You About Your Branches

Here is how to use Conway's Law as a diagnostic: look at your Git graph and ask yourself whether its complexity maps to a real communication complexity in your organization.

If you have one cohesive product team with clear ownership, regular communication, shared context, and strong CI/CD pipelines — and your branch diagram looks like a subway map — that is a red flag. Not because complex branching is always wrong, but because structural complexity you cannot justify with organizational reality is debt in disguise. It is ceremony that provides the feeling of rigor without delivering it.

In that scenario, your multiple staging branches and integration gates are not protecting you. They are hiding the fact that your team hasn't truly resolved what "done" means, or what "the product" is at any given moment. The branches are a coping mechanism for a clarity problem.

Conversely, if you have a genuine multi-team coordination problem — multiple squads working in parallel on shared infrastructure, tight release windows, significant domain overlap — and your trunk is a single unguarded main branch with no integration discipline, that is also a structural lie. You are pretending coordination complexity doesn't exist, and eventually a merge conflict at 4pm on a Friday before a major customer release will make the lie undeniable.

The point is not "use trunk-based development" or "don't use trunk-based development." The point is that your branching strategy should be an honest representation of your organizational structure. No more complex than it needs to be. No simpler than reality demands.

Take your current branch model and draw it out. Then draw your organization's communication structure — who talks to whom, how frequently, about what. If the two diagrams don't roughly correspond in complexity and topology, your branching model is lying about something. The question is what.

---

## When Development Is Not the Hard Part

Consider a product maintained by a single cohesive team with shared ownership, continuous integration discipline, and clear domain boundaries. This environment does not suffer from high coordination entropy. There are no parallel squads colliding daily, no large-scale merge storms, no need for multi-team synchronization layers.

In this setting, adding integration buffers does not reduce risk. It diffuses responsibility. It creates a second "almost-main" baseline that introduces ambiguity about what the product actually is at any given moment.

Trunk-based development is structurally aligned with this reality:

- Short-lived feature branches
- Immediate integration into a single authoritative baseline
- Fast conflict discovery
- Clear source of truth at all times
- Reduced cognitive overhead for everyone on the team

Simplicity, when it matches organizational reality, is not a shortcut. It is a force multiplier.

The key word is *matches*. Trunk discipline is not appropriate because it is fashionable. It is appropriate when development coordination complexity is genuinely low — and in a single-team product, it almost always is.

---

## Why Trunk-Based Development Makes Sense for Single-Team FinTech Products

Let's be concrete about why this matters specifically in financial systems.

In financial-grade systems, the most dangerous surface is not your code — it is your persisted state. Database schemas. Transaction records. Authorization policies. Downstream integration contracts. These things do not roll back cleanly. A schema migration that runs in one environment and fails in another is not just a bug. It is a potential data integrity incident. In regulated systems, it can be a compliance event.

When you integrate continuously into a single trunk, migration conflicts surface immediately. Contract violations between services are detected within minutes of the offending commit. Security scanning evaluates the actual integrated system, not a feature branch that only exists in developer environments. The CI/CD pipeline validates the thing that will actually ship — not a local approximation of it.

Compare this to a model where feature branches live for days and conflicts are discovered late in the cycle. In that model, the cost of a database migration conflict is not a five-minute fix. It is a multi-day archaeological exercise involving schema diff tools, version-specific migration scripts, and at least one very uncomfortable call with a customer's DBA.

Integration latency is not just an inconvenience. In FinTech, it is direct exposure to state-level risk that compounds with every hour conflicts go undetected.

There is also an underappreciated cultural benefit. When `main` is always the authoritative representation of the product — not one of several competing baselines — there is no ambiguity about what "the product" is at any moment. Engineers cannot hide behind "it works on my branch." Either the integrated system is healthy or it isn't. This forces the kind of engineering honesty that financial systems require.

---

## Where Real Complexity Emerges: Multi-Customer Delivery

Now shift the lens to what the previous post addressed directly.

In a multi-customer financial system, customers upgrade at different times. Each may receive a different feature set based on contractual commitments. Regulatory posture must be preserved per version. Post-delivery customizations may exist in customer environments. Schema evolution must remain upgrade-safe across every version in the field.

This is not development complexity. This is delivery governance complexity. And it cannot be solved by adding more development branches.

This is precisely why the branching model from the previous post is structured the way it is. Release branches are not bureaucratic overhead — they are contractual artifacts. Customer isolation branches are not fragmentation — they are version commitments made tangible. The structure exists because delivery risk in financial systems is asymmetric: a failed customer upgrade, a schema migration inconsistency, or version drift between environments carries consequences that no amount of elegant code quickly undoes.

The branching model must reflect where risk truly lives. In multi-customer FinTech, risk does not primarily live in development integration. It lives in delivery.

---

## Where the Real Risk Actually Lives: Release Governance

Here is the part that teams often get backwards. They introduce complex development branching to manage delivery risk — and in doing so, they delay the feedback loops that would actually help them manage it.

In multi-customer FinTech, delivery risk is real and unavoidable. Your customers upgrade at different times. Some have customizations layered on top of your core product. Some operate under regulatory frameworks that require version-specific audit trails. Some have DBAs who need to sign off on every schema change before it touches their environment. This complexity exists, and no amount of trunk discipline eliminates it.

But the right tool for that problem is not more development branches. It is explicit release governance. In practice, this means a clear separation of three concerns:

**Trunk is where you build.** It represents the current integrated state of the product — a state you would be comfortable deploying if a customer asked for it. Every commit is validated. Every migration is tested against upgrade paths. Every feature flag is accounted for. `Main` is sacred not because of ceremony, but because every customer release is eventually cut from it.

**Release branches are where you commit.** When a customer release is due, you cut a release branch from a known-good integration point on trunk. That branch represents a contractual commitment. It receives only critical fixes, applied forward from trunk. It does not receive new features. It is not a development environment — it is a delivery artifact.

**Customer branches are where you isolate.** In cases where a customer requires post-delivery customization or where their upgrade path diverges significantly, a customer isolation branch preserves that state explicitly. It is not where you build new things — it is where you honor existing commitments while the main product moves forward.

Development complexity is managed through trunk discipline and CI/CD maturity. Delivery complexity is managed through release governance and customer isolation. These are separate concerns, and the moment you try to solve one with the tools designed for the other, you create structural debt that will surface at the worst possible moment.

---

## Separating Development Flow from Delivery Flow

A clean architecture makes this separation explicit, and it is what makes the overall model coherent rather than ad hoc.

**Development** optimizes for clarity and speed: continuous integration into a single trunk, immediate validation of database migrations, immediate detection of integration conflicts, security baseline established at integration, `main` always representing the current product state.

**Delivery** optimizes for stability and traceability: releases cut from specific validated integration points, customers receiving controlled versioned snapshots, upgrade paths tested explicitly before promotion, post-delivery isolation preserved where required by contract or regulation.

When these two concerns are conflated — when teams attempt to manage delivery risk through development branching — you end up with a workflow that is simultaneously too rigid for development and too loose for delivery. The branches multiply, ownership blurs, and the actual delivery risk remains unaddressed beneath layers of process.

![Development-Delivery flow](/assets/img/blog/designing-for-scale/development_vs_delivery_flow.svg).

---

## Feature Variance Across Customers: A Delivery Problem, Not a Development One

One of the hardest practical questions in multi-customer systems is what to do when a feature is not intended for every customer. The instinct is often to reach for branching — a customer-specific branch, a conditional merge, a separate integration path. This instinct is wrong, and expensive at scale.

Feature variance across customers is not a development branching problem. It is a release governance problem. The correct architectural solutions are configuration-driven behavior, feature flags, or controlled release cuts from earlier integration points on trunk.

The trunk represents product evolution. Release branches represent contractual commitments to specific customers at specific points in that evolution. When these concerns are kept separate, the system remains stable without fragmenting development. When they are conflated, you end up maintaining multiple divergent codebases that converge on merge complexity rather than product clarity.

---

## Multiple Products, Independent Trunks

When an organization operates multiple independent products, each with its own team, the model scales naturally. Each product maintains its own trunk, its own integration discipline, and its own release cadence. Cross-product coordination happens at architectural boundaries — through versioned APIs and explicit contracts — not inside Git.

The temptation as organizations grow is to manage cross-product complexity through shared branching structures. That approach compounds badly. Branching complexity does not compensate for unclear system boundaries. It amplifies them.

If two products cannot be maintained on independent trunks without constant cross-product branch dependencies, that is a signal that the product boundaries themselves are not real — that what has been administratively divided is architecturally unified. No branching strategy fixes an architectural boundary problem. The fix is clarifying that boundary at the code and data level.

Independent trunks scale horizontally. Shared branching hierarchies scale by becoming progressively more painful until the architectural investment that should have happened earlier is finally unavoidable.

---

## Scaling Trunk Discipline as Teams Grow

The natural objection to this model is scale: what happens when the team is no longer small?

Trunk-based development does not collapse under scale. It evolves through reinforcement. What changes as teams grow is not the branch model — it is the reinforcement around it.

**Phase 1 — Single team:** Integration cost is low. Communication is immediate. Conflicts are resolved socially as much as technically. Trunk absorbs changes continuously. Simplicity is an advantage; adding integration layers adds only ceremony.

**Phase 2 — Multiple teams, single product:** Trunk discipline does not disappear — it hardens. Stronger CI pipelines. More granular ownership boundaries. Domain-driven modularization. Clear API contracts between internal components. Mandatory code review depth. Stricter risk classification. The branch structure remains simple. The system architecture becomes more explicit.

If trunk begins to feel unstable as teams multiply, the problem is almost always architectural coupling — not the branch model itself. Trunk-based development at scale demands clear service boundaries, modular code ownership, and automated validation maturity. When those exist, trunk remains stable even as the organization grows. When they don't, no branching complexity compensates.

![Scaling phases](/assets/img/blog/designing-for-scale/scaling_phases_arrows_fixed.svg).

---

## Designing for Scale Before Scaling — And Why Most Teams Get This Backwards

Here is the uncomfortable truth about engineering organizations that struggle at scale: most of them scaled their Git workflow long before they scaled their architecture. Their branching complexity is not a reflection of organizational sophistication — it is a monument to accumulated avoidance.

When trunk integration becomes painful, the natural response is to add a buffer. When that buffer creates its own friction, add another layer. Before long, you have a branching hierarchy that requires a diagram to explain, an onboarding guide dedicated entirely to "how we do Git," and senior engineers spending meaningful time just figuring out where to merge things.

Conway's Law is merciless here. Branching complexity does not fix unclear domain boundaries — it amplifies them. Every extra branch is a place where ownership becomes ambiguous. Every integration layer is an opportunity for "who's responsible for that?" to become an unanswerable question at the worst moment.

The organizations that scale well do it through architecture, not through Git ceremony. As coordination complexity genuinely increases — as teams multiply, as domains diverge, as parallel workstreams become real — the correct response is:

**Make domain boundaries explicit in the code.** Module ownership should be unambiguous. Internal APIs should be formal, not informal. Shared state should be minimal and clearly governed. When boundaries are real, trunk integration stays stable even as the team grows, because changes to one domain don't silently break another.

**Deepen CI/CD, don't widen branching.** A mature CI/CD pipeline — one that runs comprehensive migration testing, contract validation, security scanning, and regression testing on every commit — eliminates most rationale for staging buffers. The staging function moves into automation, where it belongs.

The evolution path is clear:

| Stage | Response |
|---|---|
| Small team | Simplicity |
| Growing team | Reinforced simplicity |
| Large organization | Architectural segmentation |

At no point is the answer "add more permanent branches." Branching complexity should follow demonstrated organizational necessity — not anticipate it prematurely. Premature complexity becomes structural debt, and structural debt in delivery pipelines compounds faster than technical debt in code.

---

## The Economic Frame: Why This Is a Capital Allocation Question, Not a Tooling One

Engineering leaders in FinTech sometimes resist thinking about software development structure in economic terms. That resistance is expensive.

Every structural decision in your delivery pipeline has a cost and a yield. Integration latency has a cost: the longer conflicts go undetected, the more expensive they are to resolve. Fragmented baselines have a cost: cognitive overhead multiplied across every engineer who needs to understand where things actually stand. Unclear release state has a cost: compliance exposure, customer confusion, and the engineering time spent answering "wait, what version is this customer actually on?"

Database migration failures in production have a cost that is easy to underestimate until you have lived through one: direct customer downtime, potential data inconsistency, compliance review, customer success escalations, and a rebuild of trust that takes months. Preventing one of those with better integration discipline and more explicit release governance is an extremely high-ROI investment.

Conversely, premature branching complexity also has a cost. Every permanent integration layer is an ongoing tax on developer velocity and cognitive bandwidth. Every merge ceremony that exists without clear organizational justification is waste — not disciplined waste, just waste.

The economically rational structure for a single-team FinTech product with multi-customer delivery obligations is not complicated to state:

1. **Trunk discipline for building** — one authoritative baseline, continuous integration, forward-fix culture
2. **Release branching for distribution** — controlled snapshots, contractual commitments, upgrade-path validation
3. **Customer isolation where necessary** — post-delivery preservation of contractual state without fragmenting development

Nothing more. Nothing less. The goal is minimal structural complexity commensurate with actual organizational reality.

---

## A Subtle but Important Shift in Perspective

Most teams design branching models around what might go wrong. They add gates and buffers reactively, in response to past incidents or imagined future complexity.

A more resilient approach is designing around where things actually are:

- Where does state persist?
- Where are version commitments made?
- Where does coordination truly exist?
- Where does operational risk concentrate?

In the systems this series has been describing, the answer is consistent: development risk is low, delivery risk is high. So the correct response is to optimize development for clarity and speed, and optimize delivery for governance and traceability.

That is what the branching model from the previous post implements in practice. This post has argued why it is the right model — not because of aesthetic preference for simplicity, but because it honestly reflects where complexity lives in the organization.

---

## Closing Thought

The goal is not to defend trunk-based development as an ideology. The goal is to align the delivery model with organizational reality — today and as the organization grows.

Today, if development complexity is low and delivery complexity is high, the structure should reflect that honestly. Tomorrow, as development complexity genuinely increases, the model evolves — through stronger architecture, deeper automation, explicit domain separation, and temporary orchestration when necessary. Not through permanent branching layers that accumulate as artifacts of decisions made under pressure.

Simplicity is not fragile.  
It is scalable — when reinforced intentionally and when it honestly mirrors the organization that maintains it.

Structure is not aesthetics.  
In FinTech, it is economics.  
And economic misalignment compounds faster than code complexity ever will.
