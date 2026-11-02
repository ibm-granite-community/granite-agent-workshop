---
title: 2. Building Agents
description: Five agent architectures with Granite and LangGraph
logo: images/ibm-blue-background.png
---

# Building Agents

This part covers agent development. Each lab is a self-contained notebook that builds one agent architecture with IBM Granite and [LangGraph](https://www.langchain.com/langgraph). Start with the Function Calling Agent: every other pattern builds on it.

| Lab | Pattern | Core idea | Good fit when |
| :--- | :--- | :--- | :--- |
| [2.1](function-calling.md){ .lab-run } | **Function Calling** | The model picks tools, your program runs them, loop until a final answer | Always start here: check whether the simplest agent already meets your use case |
| [2.2](plan-and-solve.md) | **Plan-and-Solve** | Plan all the steps up front, execute them, replan from what actually happened | The task has several dependent steps and you want to inspect the plan |
| [2.3](route-and-solve.md) | **Route-and-Solve** | A router sends each query to one specialized subagent with a smaller toolset | Your tools group naturally into domains |
| [2.4](toolrag.md){ .lab-run } | **ToolRAG** | Retrieve a small relevant subset of tools before binding them to the model | You have too many tools to fit in the model's context at once |
| [2.5](react.md) | **ReAct** | Interleave visible reasoning ("Thought") with tool calls in one loop | You want to inspect the model's reasoning step by step |
| [2.6](agent-harness.md){ .lab-demo } | **Agent Harnesses** | Start from a preset agent (Pi, OpenCode, Hermes) and customize it with instructions, tools and Agent Skills | Your use case fits an existing harness, or you want portable skills instead of custom code |

<span class="lab-legend lab-legend-run">Run during the workshop</span> <span class="lab-legend lab-legend-demo">Demoed by the instructor</span>

<style>
  .md-typeset tr:has(a.lab-run) > td { background-color: rgba(36, 161, 72, 0.15); }
  .md-typeset tr:has(a.lab-demo) > td { background-color: rgba(15, 98, 254, 0.12); }
  .md-typeset .lab-legend { display: inline-block; padding: 0.1rem 0.6rem; margin-right: 0.5rem; border-radius: 0.2rem; font-size: 0.75rem; }
  .md-typeset .lab-legend-run { background-color: rgba(36, 161, 72, 0.15); }
  .md-typeset .lab-legend-demo { background-color: rgba(15, 98, 254, 0.12); }
</style>

Each notebook installs its own dependencies and defines its own `get_llm()` helper, so you can run the labs in any order.

/// tip | Short on time?
This part is planned for about 30 minutes, which isn't enough to finish all six labs. During the workshop, run [2.1 Function Calling](function-calling.md) and [2.4 ToolRAG](toolrag.md); the instructor demos [2.6 Agent Harnesses](agent-harness.md). Every notebook stays published here, so you can finish the rest after the session.
///

For context engineering, agent security and small language models, see [6. Advanced Agent Topics](../part-06-advanced-topics/README.md).
