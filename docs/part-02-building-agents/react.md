---
title: 2.5 ReAct Agent
description: ReAct/Reasoning Agent
logo: images/ibm-blue-background.png
notebook: notebooks/01_ReAct_Agent.ipynb
---

# ReAct/Reasoning Agent

ReAct and Reasoning Agents are similar to function calling agents but with additional reasoning. They justify which tool to use with some prerequisite reasoning to help correctly identify the tool to use and run.

Each tool selection is followed by another iteration to see if the agent reasons to select another tool or is ready to answer.

**Pros:**

- Reasoning augmented tool calling can improve accuracy on non-reasoning approaches.

- Iterative tool loading can improve on agent accuracy for longer trajectory based problems.

**Cons:**

- Reasoning requires additional token outputs which can contribute to inference time and dollar token costs.

- ReAct and reasoning patterns may require custom response parsing which can require model specific code.

**When to use:**

This approach is effective and extends the simple [function calling](function-calling.md) approach, but requires the model to have been fine-tuned on reasoning and tool calling.

This approach is suitable for scenarios that have a small to medium list of tools and the use cases for that agent are targeted to a domain.

Accompanying this approach with ToolRAG can enable the extension of this agent to work with more tools and more generic utterances.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first.

## Lab

[![ReACT/Reasoning Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![ReACT/Reasoning Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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
