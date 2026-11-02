---
title: 2.2 Plan-and-Solve Agent
description: Plan-and-Solve Agent
logo: images/ibm-blue-background.png
notebook: notebooks/01_Plan_Solve_Agent.ipynb
---

# Plan-and-Solve Agent

Plan-and-Solve Agents consist of a planner node and Function Calling (FC) node. The planner node is responsible for considering the query, coming up with reasoning and generating a complete plan for execution, selecting the tools to use and their ordering.

The output of the planner node is passed to the FC node which loads up the tool calls, assisted by the LLM to set the tool call parameters.

Once the FC node has executed the plan, the agent may or may not invoke the planner node again to determine if some additional steps are required based on the tool results.

**Pros:**

- Plan can be extracted and observed.

- Planning evokes thinking/reasoning behaviors, often leading to higher accuracy.

- The FC node does not have to do the complex task of decision-making, it only has to load up the prescribed tool-call objects.

- Supporting hints/examples of tools used for queries can aid agent accuracy.

**Cons:**

- Additional model invocations required for planning.

**When to use:**

This approach is useful when you have tools or an agent setup that requires custom reasoning instructions, such as hints. This approach is also useful when you want to define interdependencies between tools.

Also this setting can empower use cases where there are longer reasoning traces or more steps required than simpler agents with target use cases only needing a few tools to be executed per query.

In production settings, this approach can offer a simplified human-in-the-loop pattern by allowing the user to validate generated plans before execution.

/// note | Model size
Plan-and-Solve needs the larger `granite4.2:8b` model. The notebook uses it from Ollama if it has been pulled, and otherwise runs it on Replicate automatically, so you don't need to change anything.
///

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first.

## Lab

[![Plan-and-Solve Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Plan-and-Solve Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

Open the notebook in the way that matches where you are working:

/// tab | Lab workstation

In the JupyterLab file browser, open `granite-agent-workshop/{{ notebook }}`. If JupyterLab isn't running yet, see [Getting Started](../getting-started/README.md#open-jupyterlab).

///

/// tab | Colab

Click **Open in Colab** above. The first code cell installs everything the notebook needs. Colab needs a Replicate API token: see [Setting up Replicate](../part-01-access-the-model/README.md#setting-up-replicate).

///

/// tab | Your own machine

From the `granite-agent-workshop` folder you cloned in [Getting Started](../getting-started/README.md), with its virtual environment active, run:

```shell
jupyter notebook {{ notebook }}
```

///
