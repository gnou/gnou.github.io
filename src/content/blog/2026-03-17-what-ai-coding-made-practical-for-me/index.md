---
title: "What AI Coding Made Practical for Me"
description: "I'll build it someday? No, I can build it today"
date: "03/17/2026"
draft: false
---

We maintain white-label mobile apps for over a hundred clients. Since iOS and Android are separate codebases, that means nearly 300 apps to release.

At that scale, the problem is no longer “how do we automate a build?” We already had that. Each app had its own Fastlane-based release scripts. The real problem was orchestration: tracking release status across hundreds of apps, figuring out what failed, understanding why it failed, and resuming without rebuilding the entire mental model from scratch.

Over time, release work became a mess of scattered scripts, partial state, and operational overhead. We had talked for a long time about building a release orchestrator, but like many internal tools, it always stayed in the category of “important, but not urgent enough.”

That changed when I started using Claude Code.

I didn’t begin with a polished spec. I started with the problem, used planning mode to think through the workflow, and gradually turned that into a build plan. One early surprise was that Claude immediately started writing Ruby. That made sense from its perspective: our release stack is heavily tied to Fastlane, but it was the wrong choice for me. I wanted something I could maintain confidently, so I redirected it to Python.

That turned out to be an important lesson: coding agents don’t know your real constraints unless you state them clearly.

From there, I kept the process deliberately human-in-the-loop. We built the system feature by feature. I used planning mode first, made sure I understood the intended behavior, then reviewed and tested the generated code at each step. I didn’t want a black box. I wanted a tool I could reason about.

The most important design choice was to build the orchestrator around our existing release scripts instead of replacing them. That let us preserve what already worked at the repo level while adding the layer we were missing: centralized, easier-to-understand release workflow.

The result is a release system that makes failures easier to diagnose, progress easier to track, and releases much less dependent on a few people carrying everything in their heads. It also grew beyond the original white-label scope, because once the orchestration layer existed, the value of it was obvious.

What this project changed for me is not just my view of AI coding tools, but my sense of what is practical to build. This was exactly the kind of internal tool we had wanted for years and never quite prioritized. AI didn’t replace engineering judgment here. It made the project possible to start, iterate, and finish.

That, to me, is the real power of AI coding: not that it writes code for you, but that it makes long-postponed leverage projects finally worth building.
