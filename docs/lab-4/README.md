---
title: Building Agents with Granite Workshop Lab 4
description: ToolRAG Agent
logo: images/ibm-blue-background.png
---

# ToolRAG Agent

ToolRAG Agents operate by first doing RAG on the set of available tools based on the query and only show a subset of the toolset to the model to select from for a given query. This pre-filtering approach reduces the likelihood of the model choosing the wrong tools as it is already being shown a smaller set of relevant tools to pick from.

**Pros:**

- Reduced likelihood of wrong tool selection.

- Reduce number of input tokens used for model invocation.

- Easy way to extend with new tools without incurring concerns of context windows or agent accuracy regression.

**Cons:**

- Tool pre-filtering may not raise up the relevant tool for model to select from.

**When to use:**

This approach is effective when you have a large set of tools and not all are relevant for any single query.

This dynamic filtering is different to the Route and Solve Agents which is a static grouping defined by the agent dev at build time. This approach instead dynamically filters at runtime based on the incoming query.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). Please follow the instructions in [pre-work](../pre-work/README.md) to run the lab.

## Lab

[![# ToolRAG Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ git.commit }}/notebooks/ToolRAG_Agent.ipynb){:target="_blank"}
[![# ToolRAG Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ git.commit }}/notebooks/ToolRAG_Agent.ipynb){:target="_blank"}

To run the notebook from your command line in Jupyter using the active virtual environment from the [pre-work](../pre-work/README.md#install-jupyter), run:

```shell
jupyter notebook notebooks/ToolRAG_Agent.ipynb
```

The path of the notebook file above is relative to the `granite-agent-workshop` folder from the git clone in the [pre-work](../pre-work/README.md#clone-the-workshop-repository).
