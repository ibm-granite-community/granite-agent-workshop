---
title: 4. Testing Agents
description: Functional and non-functional testing for Granite agents
logo: images/ibm-blue-background.png
---

# Testing Agents

Agents fail differently from ordinary software: an answer can be fluent, confident and wrong, and the same input can take a different path on a different day. Without tests, you cannot confidently change a prompt, swap a model or add a tool.

This part splits agent testing into two complementary concerns:

| Lab | Kind of testing | Question it answers | What you measure |
| :--- | :--- | :--- | :--- |
| [4.1](functional-testing.md) | **Functional testing** | Does the agent do the *right thing*? | Which tools it called, with which arguments, whether the final answer contains what it should, and how an LLM judge scores that answer |
| [4.2](non-functional-testing.md) | **Non-functional testing** | Does the agent do it *well enough* to ship? | Cost, latency, throughput, memory and energy footprint per run |

Both use the same agent from [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) (`get_stock_price`, `get_current_weather`), so the results are directly comparable as you change the model, the prompt or the tools.

/// tip | Short on time?
Start with [4.1 Functional Testing](functional-testing.md): a regression suite for tool calls is the single most useful test you can add to an agent.
///

For more background, see [Testing agents](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/testing_agents.md) in the Granite Agent Cookbook.
