---
title: 2.4 ToolRAG Agent
description: ToolRAG Agent
logo: images/ibm-blue-background.png
notebook: notebooks/01_ToolRAG_Agent.ipynb
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

This dynamic filtering is different from [Route-and-Solve](route-and-solve.md) agents, which use a static grouping defined by the agent developer at build time. This approach instead dynamically filters at runtime based on the incoming query.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first.

## Lab

[![ToolRAG Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![ToolRAG Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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
