---
title: Building Agents with Granite Workshop Lab 5
description: ReAct/Reasoning Agent
logo: images/ibm-blue-background.png
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

This approach is effective and extends upon the simple function calling approach we introduced before, but requires the model to have been fine-tuned on reasoning and tool calling.

This approach is suitable for scenarios that have a small to medium list of tools and the use cases for that agent are targeted to a domain.

Accompanying this approach with ToolRAG can enable the extension of this agent to work with more tools and more generic utterances.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). Please follow the instructions in [pre-work](../pre-work/README.md) to run the lab.

## Lab

[![# ReACT/Reasoning Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ git.commit }}/notebooks/ReAct_Agent.ipynb){:target="_blank"}
[![# ReACT/Reasoning Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ git.commit }}/notebooks/ReAct_Agent.ipynb){:target="_blank"}

To run the notebook from your command line in Jupyter using the active virtual environment from the [pre-work](../pre-work/README.md#install-jupyter), run:

```shell
jupyter notebook notebooks/ReAct_Agent.ipynb
```

The path of the notebook file above is relative to the `granite-agent-workshop` folder from the git clone in the [pre-work](../pre-work/README.md#clone-the-workshop-repository).
