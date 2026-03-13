---
title: Lab 2. Plan-and-Solve Agent
description: Plan-and-Solve Agent
logo: images/ibm-blue-background.png
notebook: notebooks/Plan_Solve_Agent.ipynb
---

# Plan-and-Solve Agent

Plan-and-Solve Agents consist of a planner node and function calling node. The planner node is responsible for considering the query, coming up with reasoning and generating a complete plan for execution, selecting the tools to use and their ordering.

The output of the planner node is passed to the FC Node which loads up the tool calls, assisted by the LLM to set the tool call parameters.

Once the FC node has executed the plan, the agent may or may not invoke the planner node again to determine if some additional steps are required based on the tool results.

**Pros:**

- Plan can be extracted and observed.

- Planning evokes thinking/reasoning behaviors, often leading to higher accuracy.

- The FC node does not have to do the complex task of decision-making, it only has to load up the prescribed tool-call objects.

- Supporting hints/examples of tools used for queries can aide in agent accuracy.

**Cons:**

- Additional model invocations required for planning.

**When to use:**

This approach is useful when you have tools or an agent setup that requires custom reasoning instructions, such as hints. This approach is also useful when you want to define interdependencies between tools.

Also this setting can empower use cases where there are longer reasoning traces or more steps required then simpler agents with target use cases only needing a few tools to be executed per query.

In production settings, this approach can offer a more simplified human-in-the-loop pattern by allowing the user to validate generated plans before execution.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). Please follow the instructions in [pre-work](../pre-work/README.md) to run the lab.

## Lab

[![# Plan-and-Solve Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ git.commit }}/{{ notebook }}){:target="_blank"}
[![# Plan-and-Solve Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ git.commit }}/{{ notebook }}){:target="_blank"}

To run the notebook from your command line in Jupyter using the active virtual environment from the [pre-work](../pre-work/README.md#install-jupyter), run:

```shell
jupyter notebook {{ notebook }}
```

The path of the notebook file above is relative to the `granite-agent-workshop` folder from the git clone in the [pre-work](../pre-work/README.md#clone-the-workshop-repository).
