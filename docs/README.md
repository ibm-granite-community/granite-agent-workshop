---
title: Building Efficient AI Agents with IBM Granite
description: Building Efficient AI Agents with IBM Granite -- Workshop
logo: images/ibm-blue-background.png
---

## Introduction

In this session you will build, test and observe AI agents using the open-source [IBM Granite](https://ibm.com/agent) models.

An agent is a system in which a large language model directs its own work. The model chooses which tools to call, your code executes them and returns the results, and the model uses what it learns to choose its next step, repeating until it decides the task is complete.

The code that runs this loop is the harness. It sends messages to the model, executes the tools it requests, manages context, and decides when to stop. Skills are packaged instructions the agent loads only when a task needs them, teaching it how to do specific work well.

In short: the model provides judgment, tools provide actions, skills provide know-how, and the harness ties them together.

You will work through recipes from the [Granite Agent Cookbook](https://github.com/ibm-granite-community/granite-agent-cookbook) as Jupyter notebooks, on the lab workstation, on your own machine, or in Google Colab. The workshop follows the [agent lifecycle](https://www.ibm.com/think/topics/agent-lifecycle-management): you **build** agents, choosing the architecture (Function Calling, Plan-and-Solve, Route-and-Solve, ToolRAG or ReAct) that is worth its latency and token cost; **observe** them with Langfuse so every model call and tool call is traced; **test and evaluate** both their trajectories and their final answers; and **operationalize** them by packaging an agent so other applications can call it.

/// tip | Getting started at the workshop
Your workstation is ready: nothing needs to be installed. Follow the five steps in [At the workshop](pre-work/README.md#at-the-workshop), then start with [1. Access the Model](part-01-access-the-model/README.md).
///

New to Granite? See [About Granite](about-granite.md) for a primer on the model family and the Granite 4.2 models this workshop runs on.

By the end of this workshop, you will learn about:

### 1. Access the Model

Connect to a Granite model, either on a local Ollama server or hosted on Replicate, and send it a first prompt. This is where you meet `get_llm()`, the small helper every notebook uses to pick a backend.

### 2. Building Agents

Five agent architectures, each in its own self-contained notebook. You start with a **Function Calling** agent, then work through **Plan-and-Solve**, **Route-and-Solve**, **ToolRAG** and **ReAct**, learning when each pattern is worth its extra latency and token cost.

### 3. Observing Agents

Once an agent leaves your notebook, print statements stop being an option. This part covers instrumenting an agent with Langfuse so that every model call and tool call is recorded as a trace you can search, cost and analyze.

### 4. Testing Agents

How to evaluate your agents with a structured test framework. Agents fail differently from ordinary software: the output can be fluent, confident and wrong, and the same input can take a different path on a different day. This part covers testing the trajectory as well as the final answer, single-turn and multi-turn test cases, and using an LLM-as-a-judge to score answers where an exact-match check is not enough. Correctness is only half the picture: because an agent is a loop, cost and latency compound with every step, so you also measure and reduce its non-functional requirements (token cost, latency, throughput, memory and energy footprint) and track them alongside quality on every change.

### 5. Packaging Agents

How to package an agent so other applications can call it, for example as an MCP server.

## About this workshop

The introductory page of the workshop is broken down into the following sections:

* [Agenda](#agenda)
* [Technology Used](#technology-used)
* [Getting help](#getting-help)
* [Credits](#credits)

### Agenda

The workshop is organized into five parts, each covered by one or more Jupyter notebooks. Labs that are still being written are marked as coming soon.

There is deliberately more material here than most people finish in the 90-minute session, and that is fine: work at your own pace, or follow along as the instructors walk through each notebook. Every notebook stays published on this site, so you can finish the rest afterwards.

| Part | Lab | Description | Status |
| :--- | :--- | :--- | :--- |
| Pre-work | [Pre-work](pre-work/README.md) | Set up your environment | ✅ |
| Access the Model | [1. Access the Model](part-01-access-the-model/README.md) | Connect to Granite and run a first prompt | ✅ |
| Building Agents | [2.1 Function Calling Agent](part-02-building-agents/function-calling.md) | The model selects tools, your program runs them | ✅ |
| | [2.2 Plan-and-Solve Agent](part-02-building-agents/plan-and-solve.md) | Plan the steps up front, execute, replan | ✅ |
| | [2.3 Route-and-Solve Agent](part-02-building-agents/route-and-solve.md) | Route each query to a specialized subagent | ✅ |
| | [2.4 ToolRAG Agent](part-02-building-agents/toolrag.md) | Retrieve the relevant tools before calling the model | ✅ |
| | [2.5 ReAct Agent](part-02-building-agents/react.md) | Interleave reasoning with tool calls | ✅ |
| | [2.6 Context Engineering](part-02-building-agents/context-engineering.md) | Keep the context window focused as conversations and tool lists grow | 🚧 Notebook coming soon |
| | [2.7 Agent Harnesses](part-02-building-agents/agent-harnesses.md) | Agent loops, preset harnesses (Pi, OpenCode, Hermes) and Agent Skills | 🚧 Notebook coming soon |
| Observing Agents | [3. Observing Agents](part-03-observing-agents/README.md) | Instrument an agent and read a trace with Langfuse | ✅ |
| Testing Agents | [4.1 Functional Testing](part-04-testing-agents/functional-testing.md) | Trajectory and response tests, multi-turn tests, summary metrics, LLM-as-a-Judge | ✅ |
| | [4.2 Non-Functional Testing](part-04-testing-agents/non-functional-testing.md) | Latency, cost, robustness and consistency | 🚧 Coming soon |
| Packaging Agents | [5. Packaging Agents](part-05-packaging-agents/README.md) | Package an agent so other applications can call it | 🚧 Coming soon |

### Technology Used

The following technology is used in the workshop:

* [Google Colab](https://colab.research.google.com)
* [IBM Granite AI foundation models](https://www.ibm.com/granite) -- see [About Granite](about-granite.md)
* [Jupyter notebooks](https://jupyter.org/)
* [LangGraph](https://www.langchain.com/langgraph)
* [Langfuse](https://langfuse.com)
* [Ollama](https://ollama.com)
* [Replicate](https://replicate.com/)

### Getting help

During the session, raise your hand and an instructor or lab assistant will come to you.

After the session:

* Check this website first: it has the setup instructions and notebook links.
* Open an issue on the [workshop GitHub repository]({{ config.repo_url }}/issues), including the notebook name, the cell, and the full error text.

### Credits

* [BJ Hargrave](https://github.com/bjhargrave)
* [Prattyush Mangal](https://github.com/prattyushmangal)
* [Jacques-Sylvain Lecointre](https://github.com/jslecointre)
* [Aditya Gidh](https://github.com/adigidh)
* [Shonda Witherspoon](https://github.com/swith004)
