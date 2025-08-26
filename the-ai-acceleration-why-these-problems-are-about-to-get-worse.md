---
description: >-
  With AI, you'll be facing a new set of challenges that will make it harder to
  deploy changes smoothly.
---

# The AI Acceleration: Why These Problems Are About to Get Worse

AI is about to make traditional Salesforce development problems significantly worse, especially for smaller organizations that have been getting by with simpler approaches.

#### The Development Velocity Paradox

AI-powered development tools are incredible at generating code quickly. GitHub Copilot, Claude, ChatGPT, and Salesforce's own AI solution for Developers can help you build features faster than ever before. **AI amplifies your existing development practices, both good and bad.**

If your current process is "write code in a shared sandbox and hope the deployment works," AI will help you write that code faster—but it won't make your deployments any more reliable. It might make them worse because you're introducing changes at a higher velocity into an already fragile system.

#### AI, like humans, needs to iterate

AI is not the magic bullet that gets it right the first time. It needs to iterate, the same way we do. If you have 10 AI agents iterating on your shared sandbox, imagine the amount of clean up you'll need to revert to a stable state.

To be efficient, AI needs environments whose state can be easily reverted or re-created. AI agents need ephemeral environments to work in.

#### The Complexity Multiplication Effect

AI tools excel at creating sophisticated automations, complex integrations, and intricate business logic. A developer who previously might have built a simple workflow can now build a comprehensive, multi-object automation with AI assistance. This sounds great until you realize:

* **More code means more dependencies** between components
* **Faster development means less time spent on architecture** and planning
* **AI-generated solutions often prioritize functionality over maintainability**

Small organizations that could previously manage their Salesforce complexity manually are suddenly dealing with enterprise-level complexity without enterprise-level processes. The "happy soup" that was manageable when it was small becomes unmanageable when AI accelerates its growth.

#### The Testing Gap Widens

AI can generate test classes, but it can't generate testing strategy. It can write assertions, but it can't ensure your tests actually validate business requirements. As AI helps teams build more sophisticated functionality faster, the gap between what they're building and what they're properly testing grows wider.

Traditional testing approaches that were already too slow become completely inadequate when development velocity increases 3-5x through AI assistance.

#### The Knowledge Fragmentation Problem

AI democratizes development, which means more people in your organization can build on Salesforce. Business analysts can create flows, admins can build automations, and part-time developers can generate complex Apex classes. This is powerful, but it also means:

* **Knowledge becomes distributed** across more people who may not understand system-wide implications
* **Standards become harder to enforce** when multiple people are contributing code
* **Integration points multiply** as different people solve similar problems in different ways

#### Why Small Teams Can't Ignore This Anymore

Previously, small Salesforce teams could justify simpler approaches: "We only have 2 developers, we don't need enterprise-level processes." But AI changes the equation:

1. **Your 2-person team now has the development capacity of a 6-person team**, but still has only 2 people managing complexity
2. **Business stakeholders expect faster delivery** because they know AI makes development faster
3. **The cost of deployment failures increases** as you're deploying more frequently
4. **Technical debt accumulates faster** because you're building more, faster

#### The Competitive Pressure Reality

Your competitors are using AI to build features faster. If your deployment process can't keep up with your AI-accelerated development process, you'll find yourself in a frustrating position: you can build features quickly, but you can't deliver them reliably.

The organizations that combine AI-powered development velocity using ephemeral environments powered by an artefact based approach and modular deployment practices will have an enormous competitive advantage over those that only solve half the equation.

#### The Choice: Scale Your Practices or Accept the Chaos

AI isn't going to slow down. The tools will get better, the code generation will get faster, and the pressure to deliver quickly will increase. You have two choices:

1. **Accept the chaos**: Keep using traditional approaches and deal with increasingly frequent deployment failures, longer debugging sessions, and growing technical debt
2. **Scale your practices**: Adopt modular, package-based approaches that can handle the increased velocity and complexity that AI enables
