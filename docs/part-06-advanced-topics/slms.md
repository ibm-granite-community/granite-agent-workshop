---
title: 6.3 Small Language Models
description: Why and how to build agents on small language models
logo: images/ibm-blue-background.png
---

# Small Language Models

/// note | Notebook coming soon
The hands-on notebook for this lab is still in progress. This page covers the concepts.
///

Every lab in this workshop runs on Granite models with a few billion parameters, small enough to run on a laptop. That's a deliberate choice. Most of what an agent does is narrow and repetitive: pick a tool, fill in its arguments, extract a field, route a query. A **small language model (SLM)** can do that work well, at a fraction of the cost and latency of a frontier model.

The NVIDIA Research position paper [Small Language Models are the Future of Agentic AI](https://arxiv.org/abs/2506.02142) makes the case directly: SLMs are powerful enough for most agentic subtasks, more economical, and better suited to the repetitive, specialized calls that agents make.

## Why small models suit agents

| Benefit | Why it matters for an agent |
| :--- | :--- |
| **Cost** | An agent is a loop: one user request can be ten or more model calls. Savings per call multiply with every step. |
| **Latency** | Faster calls add up to a noticeably faster agent, and make multi-step patterns such as [2.2 Plan-and-Solve](../part-02-building-agents/plan-and-solve.md) practical. |
| **Local and private deployment** | A small model runs on a laptop, a single GPU or on-premises, so sensitive data never leaves your infrastructure. |
| **Fine-tuning** | Specializing a small model on your tools and formats is fast and affordable. |
| **Energy** | Fewer parameters mean less compute per token. |

## Where small models struggle

Small models aren't simply smaller large models. Plan around their weaknesses:

| Weakness | Mitigation |
| :--- | :--- |
| **Many tools at once** | Selection accuracy drops as the toolset grows. Show fewer tools per call with [2.4 ToolRAG](../part-02-building-agents/toolrag.md) or [2.3 Route-and-Solve](../part-02-building-agents/route-and-solve.md). |
| **Long, noisy context** | Small models are more easily distracted. Keep the context focused with the strategies in [6.1 Context Engineering](context-engineering.md). |
| **Long-horizon planning** | Break the task into explicit steps, or use a larger model only for the planner. |
| **Output format drift** | Use native function calling or constrained decoding (JSON schema, structured output), and validate every output in code. |
| **Ambiguous instructions** | Be explicit. Short, concrete system prompts and one or two examples help a small model more than a large one. |
| **Open-ended knowledge** | Don't rely on what the model remembers; retrieve the facts with tools or RAG. |

## Design patterns for SLM agents

### Narrow the task

The single most effective technique: give each model call one small, well-defined job. A router chooses one of four domains; a subagent with three tools fills in arguments; a separate call writes the final answer. Each of those is easy for a small model, even when the whole task isn't.

### Mix model sizes

An agent doesn't have to use one model for everything. A common setup is a larger model for the hard, infrequent steps (planning, final synthesis) and a small model for the frequent, simple ones (tool calls, extraction, classification). The `get_llm()` helper used in every notebook makes it easy to give each node of a LangGraph graph its own model.

| Step | Typical model |
| :--- | :--- |
| Routing, classification, guardrails | Small |
| Tool selection and argument filling | Small |
| Summarizing or pruning tool output | Small |
| Up-front planning, replanning | Small or larger, depending on task difficulty |
| Final answer synthesis | Small or larger, depending on quality needs |

### Constrain the output

Small models are good at filling in a structure and less reliable at inventing one. Prefer native tool calling over free-form text parsing, bind a JSON schema or Pydantic model where your serving stack supports it, and keep schemas flat and small.

### Specialize with fine-tuning

When prompting isn't enough, fine-tune. Every call that your agent makes is a potential training example: log the calls (see [3. Observing Agents](../part-03-observing-agents/README.md)), keep the ones that succeeded, and use them to train a small model specialized for that step. Parameter-efficient methods such as LoRA keep this affordable. Re-run your [4. Testing Agents](../part-04-testing-agents/README.md) suite after every change to confirm the specialized model is actually better.

## Choosing a model size

Start small and move up only when tests say you must:

1. Build the agent with the smallest Granite model that runs your workflow end to end.
2. Measure it with your functional and non-functional tests.
3. Where a step fails, first try to narrow the task, reduce the tools or tighten the context.
4. Only if that isn't enough, move *that step* to a larger model, or fine-tune a small one for it.

This keeps the cost of each step proportional to its difficulty, instead of paying frontier-model prices for every tool call.

## Key takeaways

- **Most agent calls are narrow.** Small models handle them well and much more cheaply.
- **Small models need a small, focused context** and a small toolset per call.
- **Mix model sizes.** Reserve larger models for the steps that genuinely need them.
- **Constrain and validate outputs** rather than trusting free-form text.
- **Let tests decide** when a step needs a bigger or a specialized model.

## References

- [Small Language Models are the Future of Agentic AI](https://arxiv.org/abs/2506.02142)
- [About Granite](../about-granite.md)
- [IBM Granite models](https://www.ibm.com/granite)
- [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685)
