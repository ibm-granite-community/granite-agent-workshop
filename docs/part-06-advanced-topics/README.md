---
title: 6. Advanced Agent Topics
description: Context engineering, agent security and small language models
logo: images/ibm-blue-background.png
---

# Advanced Agent Topics

The labs in [Building Agents](../part-02-building-agents/README.md) get an agent working. This part covers what decides whether it keeps working once real users, real data and real costs are involved.

| Topic | Core question | Read it when |
| :--- | :--- | :--- |
| [6.1](context-engineering.md) **Context Engineering** | What enters the context window, what stays out and what is stored elsewhere? | Conversations get long, tools get numerous, or answers degrade as the history grows |
| [6.2](agent-security.md) **Agent Security** | How do you stop untrusted content from steering the agent's actions? | The agent reads content you don't control, or its tools can change something |
| [6.3](slms.md) **Small Language Models** | When is a small model the right engine for an agent, and how do you make it reliable? | You care about cost, latency, privacy or running on your own hardware |

The three topics reinforce each other. A small model has a small context window, so context engineering matters more. A focused context and a narrow toolset are also a smaller attack surface. And a small, locally hosted model keeps sensitive data on infrastructure you control.

/// note | Notebooks coming soon
These pages cover the concepts. Hands-on notebooks for this part are still in progress.
///
