---
title: Building Agents with Granite Workshop
description: Learn about building agents with Granite AI Models
logo: images/ibm-blue-background.png
---

## Introduction

Welcome to our workshop! In this workshop we'll be using the open-sourced [IBM Granite
AI foundation models](https://www.ibm.com/granite) to develop [AI agents](https://www.ibm.com/think/topics/ai-agents).

An AI agent is an autonomous software system powered by large language models (LLMs) that perceives its environment, reasons, plans, and takes actions using external tools to achieve specific goals with little to no human supervision.

By the end of this workshop, you will learn about:

### Function Calling Agents

A function calling agent is an AI system that can intelligently select and invoke predefined functions or tools to accomplish tasks. Instead of just generating text responses, it can:

1. Analyze user requests to determine which functions are needed
1. Extract parameters from natural language and format them correctly
1. Execute functions by making structured API calls with proper arguments
1. Process results and integrate them into coherent responses

### Plan-and-Solve Agents

Plan-and-Solve Agents consist of a planner node and function calling node. The planner node is responsible for considering the query, coming up with reasoning and generating a complete plan for execution, selecting the tools to use and their ordering.
The output of the planner node is passed to the FC Node which loads up the tool calls, assisted by the LLM to set the tool call parameters.
Once the FC node has executed the plan, the agent may or may not invoke the planner node again to determine if some additional steps are required based on the tool results.

### Route-and-Solve Agents

Route-and-Solve Agents consist of a router and multiple function calling nodes. Each of the function calling nodes has, in its toolkit, a subset of the complete list of tools. This enables a sort of semantic grouping of the tools into different categories, that the router can select from based on the end user query.
This approach can be thought of as a Router which routes to multiple function calling sub-agents.

### ToolRAG Agents

ToolRAG Agents operate by first doing RAG on the set of available tools based on the query and only show a subset of the toolset to the model to select from for a given query. This pre-filtering approach reduces the likelihood of the model choosing the wrong tools as it is already being shown a smaller set of relevant tools to pick from.

### ReAct Agents

ReAct and Reasoning Agents are similar to function calling agents but with additional reasoning. They justify which tool to use with some prerequisite reasoning to help correctly identify the tool to use and run.
Each tool selection is followed by another iteration to see if the agent reasons to select another tool or is ready to answer.

## About this workshop

The introductory page of the workshop is broken down into the following sections:

* [Agenda](#agenda)
* [Technology Used](#technology-used)
* [Credits](#credits)

### Agenda

| Lab | Description |
| :--- | :--- |
| [Lab 0: Pre-work](pre-work/README.md) | Pre-work for the workshop |
| [Lab 1: Function Calling Agent](lab-1/README.md) | Build a simple function calling agent with Granite |
| [Lab 2: Plan-and-Solve Agent](lab-2/README.md) | Build a plan-and-solve agent with Granite |
| [Lab 3: Route-and-Solve Agent](lab-3/README.md) | Build a route-and-solve agent with Granite |
| [Lab 4: ToolRAG Agent](lab-4/README.md) | Build a tool RAG agent with Granite. |
| [Lab 5: ReAct Agent](lab-5/README.md) | Build a ReAct agent with Granite |

### Technology Used

The following technology is used in the workshop:

* [Google Colab](https://colab.research.google.com)
* [IBM Granite AI foundation models](https://www.ibm.com/granite)
* [Jupyter notebooks](https://jupyter.org/)
* [LangGraph](https://www.langchain.com/langgraph)
* [Ollama](https://ollama.com)
* [Replicate](https://replicate.com/)

### Credits

* [BJ Hargrave](https://github.com/bjhargrave)
* [Prattyush Mangal](https://github.com/prattyushmangal)
* [Jacques-Sylvain Lecointre](https://github.com/jslecointre)
* [Aditya Gidh](https://github.com/adigidh)
* [Shonda Witherspoon](https://github.com/swith004)
* The notebooks used in this workshop are versions of notebooks from the [IBM Granite Community](https://github.com/ibm-granite-community) modified for the workshop needs
