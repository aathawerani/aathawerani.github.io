---
layout: post
title: "The Other Side of the Ledger"
excerpt: "The first four posts in this series priced a decision by what it costs to be wrong: how far its reversal cascades, who could confirm it, and whether that confirmation still holds. None of them asked what a decision is worth if it is right. Agile planning has the same imbalance, with careful estimates of effort and very little measurement of value, and AI makes the imbalance matter more as producing software gets cheaper. This post looks at the other side: what value is, when it can arrive, and how it combines with weight. What is built on a decision is often both its value and its reversal cost, but not always, and where both are high the case for building early and the case for deciding carefully collide. The useful question is not only what a decision would cost to undo, but whether it deserves to become load-bearing at all."
summary: "A decision has two prices: what it costs to undo, and what it is worth if it is right."
author: ali
date: '2026-11-22 00:00:00'
category: ['systems','culture','scale']
thumbnail: /assets/img/blog/other-side-of-the-ledger/other-side-of-the-ledger-thumbnail.svg
---
In [The Context Moves](/systems/culture/scale/2026/11/14/the-context-moves/) I ended with six questions to ask about a load-bearing decision: spread, reach, outside dependents, reversal path, validator distance and accumulation. Reading them again, I noticed what they have in common. Every one of them measures what the decision would cost if it turned out to be wrong. None of them asks what it is worth if it turns out to be right.

The same is true of the series as a whole. [The Hollow Sprint](/systems/culture/scale/2026/10/09/the-hollow-sprint/) asked when a decision should be made, and answered with its reversal cost. [A Requirement Rather Than a Reality](/systems/culture/scale/2026/10/29/a-requirement-rather-than-a-reality/) asked what must survive implementation. [Who Can Say Yes](/systems/culture/scale/2026/11/05/who-can-say-yes/) asked who can confirm a decision, and The Context Moves asked whether that confirmation still holds. Each is a question about the risk of being wrong. A poorly chosen reference format that has spread across every integration and a well-designed transaction state model that every product relies on are both load-bearing. One is a liability. The other may be the most important thing the company ever decided. The six questions cannot tell them apart.

It is also, I realised, the imbalance agile planning has had for twenty years. We built careful machinery for estimating what work will cost, and very little for estimating what it is worth.

This post is an attempt to look at the other side of the ledger. Put briefly: a decision has two prices, what it costs to undo and what it is worth if it is right. The two are linked, but they do not always move together, and what to do with a decision depends on both. As before, it is an opinion, not advice.

Some years ago I worked at a software house that sold to banks in the Gulf. It spent a long time building a cash-deposit automation product on a hardware partner's machines before the product earned much, and then sold it across the region for well over a decade. The same company built an ATM monitoring product with the same partner, which had a noticeably shorter life, and a cash forecasting product that no bank ever bought. If I had applied the six questions to the first product while it was being built, they would have marked it as heavy and risky, which it was. They would have said nothing about why it was worth the risk, why its sister product lasted so much less time, or why the third was never sold. Those are questions about the other side.

---

## We measure the cost side

Prioritization is usually a ratio: what a piece of work is worth, over what it costs. Agile practice put most of its effort into the bottom of that ratio.

![Prioritisation is a ratio, and most of the care went into the bottom. On top, what it is worth if it is right, the least well measured: business value points are hard because the value of small pieces is intertwined; cost of delay asks what each week of delay costs in money; WSJF as SAFe uses it sums three relative scores and divides by job size. Underneath, what it costs to build, the most carefully measured: story points, a relative estimate of effort, complexity, and risk and uncertainty, saying nothing about value. Two warnings: much of software engineering is practised as if every requirement, test and defect were equally important; and in Lean software development, extra features nobody values are waste, however well estimated.](/assets/img/blog/other-side-of-the-ledger/prioritisation-ratio.svg)

Story points are the clearest example of the bottom: a relative estimate of the effort needed to deliver a backlog item, taking in the amount of work, its complexity, and its risk and uncertainty [[1]](#ref-1). They say nothing about value, deliberately. Value usually enters through the order of the backlog, set by the Product Owner's judgment.

Attempts to estimate value with the same care exist, and their difficulties are instructive. Cohn has cautioned against putting business value points on individual user stories, because the value of small pieces of functionality is intertwined: the value of a car's left front wheel is arguably the value of the whole car, since nobody wants the car without it [[2]](#ref-2). That example will matter later, because it describes exactly the decisions this series is about.

The most careful answer on the top of the ratio comes from Don Reinertsen, who suggests that if a team quantifies only one thing, it should be the cost of delay: what it costs, in money, for something to arrive later [[3]](#ref-3). His weighted shortest job first divides the cost of delay by duration. SAFe's version builds the cost of delay from three relative scores and divides by job size [[4]](#ref-4). Barry Boehm had already observed that much of software engineering is practised in a "value-neutral" setting [[5]](#ref-5), and Lean software development, as Mary and Tom Poppendieck adapted it, counts extra features among its seven wastes [[6]](#ref-6).

What is new is that AI is changing the relative economics, unevenly. It shortens generating a screen far more than getting a transaction state model right or certifying a switch, and verification, integration, security and support remain real costs. But where the cost of producing an implementation falls, the effort estimate separates fewer options than it did, and more of what decides the ranking comes from the value estimate. AI has not made effort estimation obsolete. It has made the quality of our decisions about what to build matter more.

And value is the number we are least good at. Ronny Kohavi and his colleagues reported that of the ideas Microsoft evaluated in controlled experiments, ideas that had already passed internal review and been built, only about a third improved the metric they were designed to improve [[7]](#ref-7). In a separate and broader estimate across companies, Kohavi has put the share of ideas that do not improve the metric they were designed to improve at 60 to 90 percent, depending on the domain [[8]](#ref-8). These are different populations, not one figure, but they point the same way. The problems on the value side do not depend on AI. AI only makes them decide more of the ranking.

## What value is

If value is going to carry more of the ranking, it needs a unit, and the obvious one is money. I think that is mostly right, and Reinertsen's cost of delay already expresses it that way. But money arrives in different forms, and they are measured differently.

### Four kinds of value

![Four kinds of value. Direct value: revenue or cost saved; for example a product sold, or a reconciliation that needs fewer people; rough model: customers × adoption × price × months. Losses avoided: money not lost; for example a fraud caught, a reversal settled once not twice, a penalty that never comes; rough model: how often × average loss × share prevented. Enabling value: what it makes possible; for example support for a new payment scheme, or a ledger model other products build on; rough model: value of what it unlocks × chance we pursue it. Option value: a choice kept open; for example a capability ready for something that has not happened yet; rough model: hard to price, judge it as a strategic choice. One investment often carries several kinds. All four can end up as money; they are not measured the same way.](/assets/img/blog/other-side-of-the-ledger/four-kinds-of-value.svg)

Two of these deserve a particular mention. In payments I suspect losses avoided are often the largest part of the value of a load-bearing decision, and the part a revenue-led conversation is most likely to miss. And some capabilities carry enabling value not because of the revenue they create, but because not having them would close a market or leave the company unable to compete. This is Cohn's wheel again: a ledger model or an integration contract produces no revenue by itself, and its value arrives through everything built on it. Option value is the hardest to see, and I return to it below, because it is also what gives waiting its value.

The four are different ways of understanding an investment, not separate accounting categories. A single score that adds them together hides which kind of value a decision is actually carrying.

### Worth to whom

Value also has to be worth something to the right people. In a software house, a feature one bank pays for is real revenue. Built into the product, it can also become a commitment every other customer inherits. So the money question has a second half: does this value come from the market we are trying to serve, or from one customer we happen to have? I find that a more useful test of alignment with a company's direction than checking a feature against its vision statement, because it names what the value commits us to.

### Dependencies: value, lock-in or accident

That leads to what I think is the most important point in this post. **The dependencies that make a decision expensive to reverse are often the same ones that make it valuable.** Stripe's developer-first approach, which I described in [The CTO Decision Stack](/systems/culture/2026/06/07/cto-decision-stack/) and again in The Context Moves, became load-bearing because an API, documentation, a culture and a reliability bar were built on it. Those same things were much of what the company was worth. Spread, in the six questions, is a measure of weight. It is often a measure of value as well.

Something similar may be true of a vendor's outside dependents. In the six questions, outside dependents are a cost. But for the company selling the product, customers building their own operations on it are much of why it keeps earning. I suspect the deposit product I described at the start became hard to replace once a bank's branches and processes ran on it, and that this was part of why it kept earning. Weight inside our own system is a liability. Weight our customers carry on our behalf is often an asset.

But dependence is not the same as value. A dependency can reflect genuine value, or it can reflect switching costs, lock-in, accidental coupling or plain inertia. A bank that stays with a payment platform because it processes reliably is different from a bank that stays because the vendor never offered a reasonable way to leave. Both show up as retention. Only the first says much about the product. A useful test is whether the customer would stay if leaving were easy. Where the honest answer is no, the dependency is weight the customer carries reluctantly, and it tends to end the relationship the first time an alternative appears.

And inside our own systems, some decisions become load-bearing without carrying much value at all: the format of a reference number chosen for the first integration, or a field that started as a convenience and is now read by three downstream systems and a partner bank. A great deal depends on them, and nobody would pay for them.

### Value has a lifetime

Cost of delay describes when value starts. It says nothing about when value ends. Private equity investors use the term J-curve for a related but narrower pattern: a fund's returns are often negative in its early years, then improve as its investments mature and are realised [[9]](#ref-9). The analogy fits the investment before the return. A product's commercial life is a longer story.

![Value has a lifetime: three products from one company, shown as cumulative value over years. All three start with the build, investment before return. Cash-deposit automation earned for well over a decade. ATM monitoring had a shorter tail. Cash forecasting was never sold; the market was not yet ready. The curve flattens where the context moved. Illustrative shapes from my own experience, not actual figures.](/assets/img/blog/other-side-of-the-ledger/value-lifetime.svg)

The value of a decision is the whole curve, not its slope at launch. I think the end of the curve is often where the context moved: a partner changed direction, the technology was replaced, or the customers' needs moved on. A decision's value tends to last about as long as the context it was made for.

The context can move very quickly now. On The Pragmatic Engineer podcast, the host asked Microsoft's chief executive how the company went from an early lead with GitHub Copilot to catching up with Cursor and Claude Code [[10]](#ref-10). As reported, his answer was that Copilot began as code completion inside the editor, that Claude Code caught Microsoft off guard by moving coding to the command line, and that within about six months the IDE "kind of faded away." He also told builders not to be in love with what they built, and allowed that the IDE could come back in another form [[11]](#ref-11). The point is not that development environments stopped mattering. It is how quickly an advantage built on one form factor became less relevant once the form factor shifted. The pattern echoes one part of what Clayton Christensen described in The Innovator's Dilemma: well-managed companies can lose an advantage when the basis of competition changes, even when they have the resources and customer relationships to respond [[12]](#ref-12).

## When value can arrive

A decision can be worth a great deal and still never deliver it, because the value depends on conditions outside the decision.

### Conditions multiply

The clearest case I know is M-Pesa. In Kenya, Safaricom had over 11 million M-Pesa users by 2016 [[13]](#ref-13). In 2010 Vodacom, a sister company in the same Vodafone group, launched it in South Africa with a target of ten million users [[13]](#ref-13). By March 2015 it reported one million registered customers and 76,000 active users [[14]](#ref-14). The concept was similar to Kenya's, but almost everything around it differed: most adults were already banked [[13]](#ref-13), and the narrow agent and merchant network was one of several shortcomings that the 2014 relaunch set out to fix, along with a new bank partner, links to all South African banks and a redeveloped platform [[15]](#ref-15). When Vodacom closed the service in 2016, its stated reason was that there was little prospect of reaching critical mass given high levels of financial inclusion [[16]](#ref-16). Read through Geoffrey Moore's adoption lens, the gap between registered and active users is consistent with a product that never moved from early interest to the pragmatic majority [[17]](#ref-17). The figures alone cannot say which kind of adopter those users were, and the agent network and the existing banks mattered too.

Pix in Brazil is the opposite case, with one important difference: it is public payment infrastructure, built and run by the central bank, not a product launched by a company. The BIS names two key ingredients: mandatory participation of large banks, to start the network effects, and the central bank acting as both infrastructure provider and rule setter [[18]](#ref-18). The regulation made participation compulsory for institutions with more than 500,000 active customer accounts [[19]](#ref-19). A product team has no equivalent mandate to offer.

![Same idea, different conditions. M-Pesa in South Africa, a product: against existing alternatives, about three quarters of adults were already banked and banks and retailers already moved money; route to critical mass, an agent and merchant network too narrow, relaunched in 2014; who controlled it, the market; outcome, a target of ten million users, by March 2015 one million registered and 76,000 active, closed on 30 June 2016. Pix in Brazil, public infrastructure: against existing alternatives, free between individuals and low charges for merchants; route to critical mass, participation compulsory for institutions with more than 500,000 active accounts; who controlled it, the central bank, as infrastructure provider and rule setter; outcome, launched in November 2020, 67% of adults signed up in little over a year. Who controls a condition is part of the condition.](/assets/img/blog/other-side-of-the-ledger/same-idea-different-conditions.svg)

The narrower point I take from both is about arithmetic. A feature's value in a market depends on several conditions at once: a weak existing alternative, a route to critical mass, regulation that permits it, economics that work at the volume it will actually reach. For many products there is also a route to market controlled by someone else. A hardware or channel partner can bring a product to more customers than the company could reach alone, and the same partner can end that route if its own interests change. And where customers tend to settle on one provider, there is a window. The value of being early goes to whoever is ready when the market moves, and a competitor who is ready first can close it. In that narrow sense the race is close to zero-sum, even when the market as a whole is growing.

Not every opportunity behaves the same way. Some products have modest value at small scale and grow with it. But if a condition essential to the business model is missing, the value can collapse, however strong the others are. Michael Kremer made the economic version of this argument in his O-ring theory, named after the single component whose failure destroyed the Challenger shuttle: where production depends on every task succeeding, a mistake in any one can destroy most of the value of the whole [[20]](#ref-20).

Most value scores add, so a strength can hide a missing condition. Intercom's RICE does multiply, reach by impact by confidence, divided by effort [[21]](#ref-21), which is closer to the right shape. But none of its factors is a condition in the market.

![Adding hides a zero. An illustrative feature scored on four conditions, each out of ten: need 9, economics at scale 7, permission 8, critical mass 0. Added, it scores 24 of 40 and looks strong. Multiplied as shares, 0.9 times 0.7 times 0.8 times 0 is 0: nothing arrives. An additive summary can hide a missing prerequisite when the value needs every condition to hold. Neither WSJF nor RICE scores these market conditions; RICE multiplies, but its factors are reach, impact, confidence and effort.](/assets/img/blog/other-side-of-the-ledger/adding-hides-a-zero.svg)

The point is not that WSJF measures these market conditions. It does not. The example shows why an additive summary can conceal a missing prerequisite when the outcome depends on several conditions holding together.

### Conditions, evidence and control

Three things are easy to blur here. A **condition** is what must be true for the value to arrive. **Evidence** is how confident we are that it is true. **Control** is who decides whether it becomes true.

| Condition | Evidence that it holds | Who controls it |
|---|---|---|
| A bank is willing to adopt the product | A pilot commitment is early evidence; a commercial contract or live use is stronger | The bank |
| The network reaches enough participants | Signed agreements show intent; sustained usage across enough participants is stronger evidence of network effects | The market, or a regulator |
| The economics work at scale | Volumes and unit economics observed at a representative scale, or credible evidence from comparable markets | Partly us, partly the market |
| We are permitted to operate | A licence, or explicit regulatory approval for a defined pilot | The regulator |
| Our engineering is ready | Software running in production is a start; demonstrated reliability and operational support are stronger | Us |

Evidence for worth comes in grades too. In a crowded market it can come from financial models, feasibility studies, the history of comparable products, and markets where a similar solution has already succeeded. For a genuinely new idea there are no comparables, and often the only evidence is a founder's conviction that it will change the market. Who Can Say Yes described this as the novel venture, where the champion is the one who says yes. Conviction is not worthless; most new categories began with it. But it should be recorded as conviction, so that the commitment can be staged until something firmer arrives.

The distinction matters most in enterprise fintech, where many of the decisive conditions are controlled by someone else. A value claim that rests on conditions we control and have evidence for is a different thing from one that rests on a regulator's timetable and our own optimism, even if both carry the same estimate in money.

### Fail fast, where a small test can see

The familiar advice for uncertainty is to fail fast and test small. Eric Ries built the Lean Startup around a build-measure-learn loop, and defined the minimum viable product as the version of a new product that allows a team to collect the maximum amount of validated learning about customers [[22]](#ref-22). This is no longer only advice; some teams already work this way. The team behind Claude Code has described engineers pushing around five pull requests a day, the team making dozens of internal releases a day that everyone at Anthropic uses, and one engineer building around twenty prototypes of a single feature over two days [[23]](#ref-23). That is one unusually fast team building a tool for developers, not a standard every team can or should match. But it shows how cheap the build-measure-learn loop has become where the conditions allow it, and how much of the testing now happens in real use, with internal users first.

Building close to the customer has also become a job title. Forward deployed engineers embed with a customer and build inside its environment, so the learning happens where the product is actually used rather than in a demo. Job postings for the role rose by more than 1,000 percent between January and August 2026, compared with the same period in 2025 [[24]](#ref-24). Certification is following: DXC has announced that it will train tens of thousands of Claude-certified forward deployed engineers to work inside the systems it runs for banks, airlines and insurers [[25]](#ref-25). What this shortens is the distance between the people building and a customer's real conditions. It does not supply critical mass, regulatory permission or market readiness, which still have to be met some other way.

For valuable, lightly committed features, this way of working is usually the right method. For heavier decisions, it is often the cheapest way to gather evidence.

But a small test only produces evidence about conditions that show up at small scale, and some of the conditions above do not.

![What a small, fast test can and cannot see. It can show whether early users try it, whether the workflow works in real use, whether it solves the problem for one customer, and how our guesses compare with real behaviour. It cannot show critical mass: a network's value needs many users, and a small pilot shows weak adoption whether or not it works. Permission: in regulated payments you cannot test before you may operate, and the licence itself cannot be tested. Readiness: a fast failure on an idea whose market is not yet ready reads as a permanent one. The test that stays: small versions tend to become the product, and load-bearing whether or not anyone decided. For these, gather evidence another way: comparable markets, early conversations with the regulator, a partner's commitment, or a staged commitment.](/assets/img/blog/other-side-of-the-ledger/small-test-limits.svg)

The last of these was the subject of [A Requirement Rather Than a Reality](/systems/culture/scale/2026/10/29/a-requirement-rather-than-a-reality/): a prototype that was right for learning becomes production because it works. And AI has made the build step cheap without making measuring and learning cheap, which was the argument of [The Evaluation Constraint](/systems/culture/scale/2026/08/02/the-evaluation-constraint/).

### When the value does not arrive

The hardest case is the one where something was judged high value, it was built, and in practice it sank. Kohavi's numbers suggest this is not an exceptional outcome. Failed value assumptions are a routine part of product development. The useful question is not whether it happens, but whether the team learns anything when it does.

It can only learn if it recorded, at the time of the decision, what had to be true for the value to arrive. Gary Klein's premortem helps on the front end: before a project starts, the team assumes it has already failed and explains why [[26]](#ref-26). That can surface risks worth investigating. The record of conditions is different: it captures what must be true for the value to arrive, the evidence for each condition, and who controls it. After launch, the team can compare those assumptions with what actually happened.

The cash forecasting product from the start of this post is the case I keep coming back to. No bank was convinced it would add value, and so none bought it. The missing condition was readiness, and the product would have failed any test of its time. What makes the case interesting is that readiness changes. In August, PYMNTS reported that banks are now using AI to forecast cash demand at individual ATMs, and cited Brink's own estimate, a company figure rather than an independent one, that combining forecasting with cash supply, ordering and monitoring can cut total cash demand across an ATM portfolio by 30% to 40% [[27]](#ref-27). That does not show the earlier product would have succeeded. The underlying need was real. The market was not ready for the product that had been built.

So the record should also say which kind of missing condition it was, never or not yet, and what would show that it has changed. That turns a shelved product into an option rather than a loss.

![The record of conditions. Before the project: a premortem, assume it has already failed and explain why. At the decision: record each condition, the evidence for it, and who controls it. After launch: compare what was assumed with what happened, condition by condition. A missing condition is either never, the alternative is too strong or the regulation will not change, so close it; or not yet, the market or the timing was not ready, so keep it as an option and name the signal that would reopen it. Without the record, the explanation is written afterwards by whoever has the most influence in the room.](/assets/img/blog/other-side-of-the-ledger/record-of-conditions.svg)

## Weight and value together

Putting weight and value side by side says something that neither says alone. They are not independent; much of this post is about how often they are correlated. The grid separates them for analysis, not because they move separately.

![A two-by-two grid of how much rests on a decision against what it is worth if it is right. Little rests on it, worth little: regenerate it freely. Little rests on it, worth a lot: build early and iterate, ordered by urgency and capacity with cost of delay deciding which goes first, for example a new screen for a high-demand journey. Much rests on it, worth little: load-bearing by accident, put it behind an interface before more is built on it, for example a reference format from the first integration. Much rests on it, worth a lot: strategic commitments, where building early and deciding carefully collide, so buy information, stage the commitment, or reduce the weight, for example a ledger model or Stripe's developer-first core. A judgement aid, not a scoring model: the axes are often correlated, and the grid separates them for analysis.](/assets/img/blog/other-side-of-the-ledger/weight-and-value.svg)

The horizontal axis is the same as in The Context Moves: how much rests on the decision. The grid there paired it with drift. This one pairs it with value.

**Little rests on it, and it is worth little.** Regenerate it freely. This is most of the work in any product, and The Hollow Sprint's advice holds: build, show, learn, change.

**Little rests on it, and it is worth a lot.** Build early and iterate. Being wrong is cheap, and being late is expensive. It is not automatically first in the queue: a more urgent dependency may sit elsewhere, and some opportunities have a short window while others can wait, which is what the cost of delay is for.

**Much rests on it, and it is worth little.** This is the corner the six questions alone cannot see. A decision that much depends on but that is worth little is load-bearing by accident. The useful move is to reduce its weight before more is built on it: the cheapest time to put an accidental decision behind an interface is before the next three systems read it directly.

**Much rests on it, and it is worth a lot.** These are the strategic commitments: a ledger model, a pricing model, a developer-first philosophy, the integration contract with the bank most of the revenue flows through. The deposit product from the start of this post sat here. These are where "build it early" and "decide it carefully" pull in opposite directions.

The most useful decisions to recognise are in the upper left, valuable but light. I think they can be recognised by turning the weight questions around. Few parts of the system assume them, nobody outside the company depends on them, and they can change beside the core rather than in place. A simple test is whether the feature could be switched off next month without telling anyone outside the company or changing a contract. Staying in that corner is partly a design choice: a feature built at the edge, as configuration, a plug-in or a separate service on stable interfaces, stays light, and the same feature written into the core becomes heavy. And it does not stay light on its own. Once a customer builds on it, for example a report a bank feeds into its own reconciliation, it has started to move right, so the test is worth repeating.

### When building early and deciding carefully collide

The collision in the strategic corner has a name in economics. Avinash Dixit and Robert Pindyck showed that when an investment is irreversible and its payoff uncertain, the opportunity to invest behaves like an option, and waiting for better, though never complete, information has a value of its own [[28]](#ref-28). Committing early gives that option up. This is the formal version of The Hollow Sprint's advice to settle load-bearing decisions deliberately. Reinertsen's cost of delay says the opposite about valuable work: every week a valuable capability is not in the market costs money [[3]](#ref-3).

A decision that is both load-bearing and valuable has both at once. Waiting is worth something because the decision is hard to reverse, and expensive because the decision is valuable. Neither "decide early" nor "defer" is the answer on its own. I see three ways out of the bind, and they can be combined.

![Three ways out when waiting is valuable and delay is costly. Buy information: reduce the uncertainty instead of waiting, with a throwaway prototype, a pilot with one bank, or a limited rollout, within what a small test can see. Stage the commitment: capture part of the value through a version that commits less, a bounded version at the edge while the core stays as it is. Reduce the weight: hide the decision behind a stable interface so that less depends on it; modularity keeps the option to change. They can be combined, and not every decision should be made lighter.](/assets/img/blog/other-side-of-the-ledger/three-moves.svg)

Buying information is limited by what a small test can see. Staging is what the established ride-hailing companies in The Context Moves did with negotiated fares: bounded versions in particular markets or vehicle types, while the core stayed as it was.

Reducing the weight has the longest history. David Parnas proposed in 1972 that a system be decomposed around the difficult design decisions, or decisions likely to change, with each module hiding one of them from the others [[29]](#ref-29). Carliss Baldwin and Kim Clark later used real options theory to explain why modularity is valuable: it lets designers experiment, as long as they keep to the agreed design rules [[30]](#ref-30). Kevin Sullivan and his colleagues applied the same reasoning to Parnas's own example and found it consistent with his conclusions [[31]](#ref-31), and Hakan Erdogmus and John Favaro made the case for XP in the same terms: "flexibility creates value under uncertainty. The more uncertainty there is, the more value it creates" [[32]](#ref-32). A decision hidden behind a stable interface can carry the same value with less weight.

The clearest lesson I have seen about this bind came from it being met the wrong way round. Later in my career I worked at a wallet company, backed by a large group, that launched a consumer wallet well before wallets were common in its market. The licence it needed from the regulator came years later than it hoped, after rivals had been licensed and the first wave of adoption had passed. In the meantime it spent heavily on deals and discounts to bring users in.

Two commitments were being timed at once, and both depended on the same condition.

![One condition, two commitments: the wallet company. The deciding condition was the licence, controlled by the regulator, not the company. Engineering waited: it kept a modest team rather than building for a scale the regulator had not allowed, which was reasonable at the time, the option value of waiting applied correctly; later, I suspect, the temporary team became load-bearing. Acquisition committed: it spent heavily on deals and discounts to bring users in, spent once, before the deciding condition was met; the licence came after the first wave had passed. Time each commitment by the conditions that decide whether that commitment will produce value.](/assets/img/blog/other-side-of-the-ledger/two-commitments.svg)

Discounts are spent once and do not come back, so the irreversible spending went to the side whose deciding condition had not been met and was not in the company's control. The company is still in business and did get its licence, but it missed the wave, and development was later moved out of the company altogether.

The lesson I take from it is this: **the timing of a commitment should depend on the conditions that decide whether that particular commitment will produce value.** The engineering commitment was rightly held back until the licence was clearer. The acquisition commitment depended on exactly the same condition, and was not.

The same bind is playing out now with AI adoption itself, which The Context Moves described as a load-bearing decision. Many banks and software houses are waiting. Waiting has real option value, because regulation, data protection rules and the tools themselves are all still moving. Waiting also has a cost of delay, because organisations that start now can learn while others wait, but only if they can evaluate and govern what they adopt. Without that, starting early mostly builds weight around tools and practices that are still changing. The outcome is not in yet, and I do not think anyone can say who is right. What the wallet example suggests is that "adopt or wait" is not one decision. It is several commitments, each depending on different conditions.

Put together with The Hollow Sprint, the rule gains a fourth part. Reversal cost sets the deadline for a decision, uncertainty sets how much effort it deserves, the cheapest way to resolve the uncertainty sets the method, and value sets the urgency.

### Not every decision should be made lighter

"Reduce the weight" is not free. Every interface and abstraction has a cost: more to build, more to understand, more places for behaviour to be split. Making a regenerable decision lighter is usually waste, because it was cheap to change already.

More importantly, some decisions are valuable because they are pervasive. A single source of truth for balances is valuable because every component reads it. Stripe's developer-first approach was valuable because it shaped everything, from the API to the documentation to the culture. Fencing it, so that it could be changed more easily, would have removed most of what made it worth having. You can fence a pricing model. I do not think you can fence a philosophy without losing it.

There is also a limit to what architecture can reach. AI has made changing code cheaper, so the part of a decision's weight that lives in code is shrinking. What remains is weight no module boundary can hide: the contract with a bank, the promise made to customers, the money flow a regulator has approved. Reducing that kind of weight is a commercial and contractual design problem as much as a technical one. In my own field it often means versioned integration contracts, behaviour that can be configured per bank rather than fixed for all of them, and agreements that say what may change and how much notice a change needs.

Amazon's distinction between one-way and two-way doors is a useful shorthand [[33]](#ref-33). As I suggested in The Context Moves, a two-way door can become a one-way door as more is built behind it. Reducing weight is the work of keeping a door two-way. For the decisions whose value comes from being built on, the honest answer is sometimes to accept that the door is one-way, and to spend the effort on walking through it carefully.

## So which feature goes first?

Reading this back, the weight questions lead to a judgement fairly directly: answer them and you know how load-bearing a decision is. The value questions do not produce a number in the same way, and I do not think any honest method can, given how often value predictions fail. But when several opportunities compete for the same team, they do give an order in which to make the call. This section is about that portfolio. The next is about a single load-bearing decision.

![Ordering competing opportunities, in five steps. 1. Start from strategy: which kinds of value we trade against each other, and whose value counts. 2. Separate what can be built from what must first be made possible: blocked is not worthless, do the work that resolves the blocking condition. 3. Compare cost of delay, in money, where it can be expressed that way: for direct value and losses avoided, a monthly range divided by how long the work takes. 4. Match the commitment to the evidence: evidence sets the size of the bet, not the order. 5. Let weight shape how and when: light, build early and iterate; heavy, stage it, buy information first, or wait for its conditions. Judgement, not a formula. Enabling and option value are judged at step 1, not forced into step 3.](/assets/img/blog/other-side-of-the-ledger/which-first.svg)

**Start from strategy.** This is where vision and mission become concrete. There is no objective exchange rate between the four kinds of value; which ones a company trades against each other, and whose value counts, is a choice, and two companies can reasonably make it differently. I saw this at two middleware vendors I worked at, both selling to banks. At the first, only the core developers could change the product's behaviour, because the company cared above all about the quality of its code, so problems found at a customer's site went back to the office for a fix. The second built an implementation layer that let engineers on site change behaviour themselves, within limits, because it cared above all about speed of delivery. Most issues could then be solved at the customer's site. The first company built a similar layer some years later, by which time the second had already established itself in the market. Neither company was wrong about its values. But those values priced the same feature very differently: to one it was a risk to quality, to the other it was time to the customer, and looking back, I suspect the market valued that speed more than the first company expected. The implementation layer was also a decision to move behaviour to the edge, the same move described above for keeping valuable features light, and the same instinct behind today's forward deployed engineers.

**Separate what can be built from what must first be made possible.** A blocked opportunity is not a worthless one, but the work it calls for now is not the full product. It is the work that resolves or prepares for the blocking condition: the conversation with the regulator, the pilot agreement, the integration that will be needed either way.

**Compare cost of delay, in money, where it can be expressed that way.** This follows Reinertsen's economic form of weighted shortest job first: cost of delay, expressed in money, divided by duration [[3]](#ref-3). SAFe keeps the same basic ratio but estimates its components and job size relatively [[4]](#ref-4). It works for direct value and losses avoided. Enabling and option value rarely reduce honestly to a monthly figure, and are better judged as strategic choices in the first step.

**Match the commitment to the evidence.** A large opportunity supported by comparable markets can deserve priority over a small one proven by a pilot. Weak evidence calls for a smaller first commitment or a cheaper test, not a lower place in the queue. This is the real-options argument again.

**Let weight shape how and when.** Heavy items are staged, start by buying information, or wait until the conditions they depend on are clearer.

This is still judgement, not a formula. But it turns "which feature first" from a single score into a sequence of questions that each have an answer.

## What I would do as a CTO

None of this needs a new framework. It needs the six questions from The Context Moves to have the half they are missing.

![Both sides of a load-bearing decision. What it costs if it is wrong, the six questions from The Context Moves: weight, through spread, reach, outside dependents and reversal path; drift, through validator distance and accumulation. What it is worth if it is right, the half the six questions leave out: worth, its economic and strategic value including revenue, losses avoided and what it enables, to whom and for how long; conditions, what must be true, who controls it and which is weakest; evidence, how we know it is true and who can confirm it. If an essential condition is missing, the value can collapse, so conditions are recorded with the decision to find the missing one when the value does not arrive. Questions, not a score.](/assets/img/blog/other-side-of-the-ledger/both-sides.svg)

The previous section was about ordering many opportunities. This one is about a single decision with real weight: how to evaluate it, record it and come back to it. For each such decision, I would ask three more questions alongside the six:

1. **Worth.** What is the economic and strategic value if this decision is right, including revenue, losses avoided and the opportunities it enables? To whom, and for how long?
2. **Conditions.** What must be true for that value to arrive, who controls each of those conditions, and which is weakest in the market we are building for?
3. **Evidence.** What evidence supports each condition, and who is in a position to confirm it? That is the question from Who Can Say Yes, applied to value.

The conditions question is close to a what-if analysis on a single decision: if this condition fails, what is left of the value? It is also close to the opportunities and threats of the technology SWOT in The CTO Decision Stack, attached to one decision instead of the whole company. In [The CTO Decision Stack in Startup and Enterprise](/systems/culture/scale/2026/07/12/startup-enterprise-cto-decision-stack/) I noted that founder intuition is often right but rarely written down, and hard to revisit because nobody can reconstruct the assumptions it rested on. Writing the conditions down is a small defence against that.

Then I would use the answers in three ways.

First, place the decision on the weight-and-value grid. If it lands in the strategic corner, choose deliberately between buying information, staging the commitment and reducing the weight, and time each commitment by the conditions it depends on. If it lands in the accidental corner, plan to reduce its weight before more is built on it.

Second, record the answers with the decision, in the specification or the ADR where it already lives, rather than in a separate document, and revisit them when one of the conditions changes.

Third, when the value does not arrive, go back to the conditions before writing the explanation, and send what is found back into the specifications. A specification carries assumptions about the market, the customers and the value as well as instructions for the code. If those assumptions were wrong, generating better code from the same specification does not help: the next round is built on the same missed assumption. A product with many customers and many specifications needs exactly this loop, and it is the part that spec-driven tools leave to people. That is where I want to go next.

These are questions, not a score. I would resist turning them into one, for the reason this post is about: a single number hides the condition that decides the outcome.

## Objections worth taking seriously

**Value cannot be measured precisely.** Agreed. But a rough estimate in money, with its kind of value, its conditions and its evidence written next to it, is easier to argue with than a relative score of eight, and easier to learn from when it is wrong.

**Real options is heavy mathematics.** It is, and I am not suggesting anyone price decisions with option formulas. The logic is what matters: irreversibility and uncertainty make waiting valuable, and flexibility is worth most where uncertainty is highest.

**Two cases, chosen after the outcome.** True. M-Pesa and Pix show that conditions can be decisive, not how often or by how much, and neither case turned on a single condition. My own examples have the same limit, and they are one person's reading of what happened.

**The grid oversimplifies.** It does. Weight and value are judgements, decisions move between corners as more is built on them, and the boundaries are not sharp. Like the grid in The Context Moves, it is a way of organising a conversation, not a way of deciding.

## What this does not show

Everything above is reasoned from published work and from my own experience in payments. None of its parts is new. Real options, modularity and information hiding, cost of delay, Lean Startup experimentation, network effects, the O-ring theory and the premortem are all established ideas. What I have tried to do is bring them together around one decision a CTO actually faces. The link between weight and value is my reading, not a measured finding, and neither it nor the value questions have been tested at scale, by me or, as far as I know, by anyone in this form.

## The other side of the ledger

The first four posts in this series priced decisions by what it costs to be wrong. That is half of the price. A decision also has a value, the value depends on conditions, and the same dependencies that make a decision expensive to undo are often what make it worth making.

So the question I would now ask of a load-bearing decision is not only what it would cost to undo. It is whether it deserves to become load-bearing at all: what it is worth, what has to be true for that worth to arrive, and how we would know if it did not.

---

## Sources

<a id="ref-1"></a>[1] Mike Cohn, "What Are Agile Story Points?" Mountain Goat Software. [mountaingoatsoftware.com](https://www.mountaingoatsoftware.com/blog/what-are-story-points)

<a id="ref-2"></a>[2] Mike Cohn, "The Problems with Estimating Business Value," Mountain Goat Software. [mountaingoatsoftware.com](https://www.mountaingoatsoftware.com/agile/the-problems-with-estimating-business-value)

<a id="ref-3"></a>[3] Donald G. Reinertsen, *The Principles of Product Development Flow: Second Generation Lean Product Development* (Celeritas, 2009).

<a id="ref-4"></a>[4] Scaled Agile Framework, "WSJF" (archived December 2016). [webarchive.library.unt.edu](https://webarchive.library.unt.edu/web/20161225132214mp_/http://www.scaledagileframework.com/wsjf)

<a id="ref-5"></a>[5] Barry Boehm, "Value-Based Software Engineering: Overview and Agenda," in S. Biffl et al. (eds.), *Value-Based Software Engineering* (Springer, 2006). [doi.org](https://doi.org/10.1007/3-540-29263-2_1)

<a id="ref-6"></a>[6] Mary Poppendieck and Tom Poppendieck, *Lean Software Development: An Agile Toolkit* (Addison-Wesley, 2003), chapter 1, "Eliminate Waste".

<a id="ref-7"></a>[7] Ronny Kohavi, Thomas Crook and Roger Longbotham, "Online Experimentation at Microsoft," Microsoft ThinkWeek paper, public version (2009). [ai.stanford.edu](https://ai.stanford.edu/~ronnyk/ExPThinkWeek2009Public.pdf)

<a id="ref-8"></a>[8] Ronny Kohavi, "Online Controlled Experiments: Introduction, Learnings, and Humbling Statistics," industry keynote, ACM Recommender Systems (12 September 2012), slide 18. [ai.stanford.edu](https://ai.stanford.edu/~ronnyk/2012-09ACMRecSysNR.pdf)

<a id="ref-9"></a>[9] Hamilton Lane, "J-Curves," private markets education. [hamiltonlane.com](https://www.hamiltonlane.com/en-us/knowledge-center/j-curves)

<a id="ref-10"></a>[10] Gergely Orosz, interview with Microsoft's chief executive, The Pragmatic Engineer podcast and newsletter (November 2026). [newsletter.pragmaticengineer.com](https://newsletter.pragmaticengineer.com/p/software-engineering-after-code-becomes)

<a id="ref-11"></a>[11] The Next Web, "Satya Nadella says Claude Code caught Microsoft off guard and the IDE faded" (16 November 2026). [thenextweb.com](https://thenextweb.com/news/satya-nadella-claude-code-ide-pragmatic-engineer)

<a id="ref-12"></a>[12] Clayton M. Christensen, *The Innovator's Dilemma: When New Technologies Cause Great Firms to Fail* (Harvard Business School Press, 1997).

<a id="ref-13"></a>[13] Fin24, "Why Vodacom M-Pesa has flopped in SA" (9 May 2016). [fin24.com](https://www.fin24.com/Tech/Companies/why-vodacom-m-pesa-has-flopped-in-sa-20160509)

<a id="ref-14"></a>[14] Vodacom Group, *Integrated report for the year ended 31 March 2015*, p. 26. [vodacom.com](https://www.vodacom.com/pdf/investor/integrated-report/integrated-report-2015/iar-full.pdf)

<a id="ref-15"></a>[15] Mail & Guardian, "In Africa the strongest and richest do not always win the race" (12 December 2014). [mg.co.za](https://mg.co.za/2014-12-12-in-africa-the-strongest-and-richest-do-not-always-win-the-race/)

<a id="ref-16"></a>[16] Vodacom statement on discontinuing M-Pesa in South Africa, as reported by BusinessTech, "Vodacom calls it quits on M-pesa in South Africa" (9 May 2016). [businesstech.co.za](https://businesstech.co.za/news/mobile/122977/vodacom-calls-it-quits-on-m-pesa-in-south-africa/)

<a id="ref-17"></a>[17] Geoffrey A. Moore, *Crossing the Chasm: Marketing and Selling High-Tech Products to Mainstream Customers* (HarperBusiness, 1991; revised 1999 and 2014).

<a id="ref-18"></a>[18] Angelo Duarte, Jon Frost, Leonardo Gambacorta, Priscilla Koo Wilkens and Hyun Song Shin, "Central banks, the monetary system and public payment infrastructures: lessons from Brazil's Pix," *BIS Bulletin* 52 (23 March 2022). [bis.org](https://www.bis.org/publ/bisbull52.htm)

<a id="ref-19"></a>[19] Central Bank of Brazil, Resolution BCB No. 1 of 12 August 2020, establishing the Pix payment arrangement, article 3. [bcb.gov.br](https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Pix_Regulation/Resolution_BCB_1.pdf)

<a id="ref-20"></a>[20] Michael Kremer, "The O-Ring Theory of Economic Development," *The Quarterly Journal of Economics* 108, no. 3 (1993): 551–575. [doi.org](https://doi.org/10.2307/2118400)

<a id="ref-21"></a>[21] Sean McBride, "RICE: Simple prioritization for product managers," Intercom blog. [intercom.com](https://www.intercom.com/blog/rice-simple-prioritization-for-product-managers/)

<a id="ref-22"></a>[22] Eric Ries, "What is an MVP?" originally published on Startup Lessons Learned, republished by Lean Startup Co.; and *The Lean Startup* (Crown Business, 2011). [leanstartup.co](https://leanstartup.co/what-is-an-mvp/)

<a id="ref-23"></a>[23] Gergely Orosz, "How Claude Code is built," The Pragmatic Engineer. [newsletter.pragmaticengineer.com](https://newsletter.pragmaticengineer.com/p/how-claude-code-is-built)

<a id="ref-24"></a>[24] Fortune, "Forward-deployed engineers," citing Lightcast job-posting data (3 September 2026). [fortune.com](https://fortune.com/2026/09/03/forward-deployed-engineers-fast-growing-six-figure-silicon-valley-job-integrate-ai-with-customers-tech-careers-palantir/)

<a id="ref-25"></a>[25] Anthropic, "DXC and Anthropic alliance" (June 2026). [anthropic.com](https://www.anthropic.com/news/dxc-anthropic-alliance)

<a id="ref-26"></a>[26] Gary Klein, "Performing a Project Premortem," *Harvard Business Review* 85, no. 9 (September 2007): 18–19. [hbr.org](https://hbr.org/2007/09/performing-a-project-premortem)

<a id="ref-27"></a>[27] PYMNTS, "Banks Turn to AI to Stop Overstocking ATMs" (28 August 2026). [pymnts.com](https://www.pymnts.com/?p=4131787)

<a id="ref-28"></a>[28] Avinash K. Dixit and Robert S. Pindyck, *Investment under Uncertainty* (Princeton University Press, 1994). [press.princeton.edu](https://press.princeton.edu/node/27002)

<a id="ref-29"></a>[29] D. L. Parnas, "On the Criteria To Be Used in Decomposing Systems into Modules," *Communications of the ACM* 15, no. 12 (1972): 1053–1058. [doi.org](https://doi.org/10.1145/361598.361623)

<a id="ref-30"></a>[30] Carliss Y. Baldwin and Kim B. Clark, *Design Rules, Volume 1: The Power of Modularity* (MIT Press, 2000).

<a id="ref-31"></a>[31] Kevin J. Sullivan, William G. Griswold, Yuanfang Cai and Ben Hallen, "The Structure and Value of Modularity in Software Design," *ESEC/FSE 2001*: 99–108. [doi.org](https://doi.org/10.1145/503209.503224)

<a id="ref-32"></a>[32] Hakan Erdogmus and John Favaro, "Keep Your Options Open: Extreme Programming and the Economics of Flexibility," in G. Succi et al. (eds.), *Extreme Programming Perspectives* (Addison-Wesley, 2002), chapter 43.

<a id="ref-33"></a>[33] Amazon, 2016 Letter to Shareholders (April 2017). [aboutamazon.com](https://www.aboutamazon.com/news/company-news/2016-letter-to-shareholders)
