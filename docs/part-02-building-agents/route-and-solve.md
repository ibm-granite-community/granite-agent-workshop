---
title: 2.3 Route-and-Solve Agent
description: Route-and-Solve Agent
logo: images/ibm-blue-background.png
notebook: notebooks/01_Route_and_Solve_Agent.ipynb
---

# Route-and-Solve Agent

Route-and-Solve Agents consist of a router and multiple function calling nodes. Each of the function calling nodes has, in its toolkit, a subset of the complete list of tools. This enables a sort of semantic grouping of the tools into different categories, that the router can select from based on the end user query.

This approach can be thought of as a Router which routes to multiple function calling sub-agents.

**Pros:**

- Subagent architectures enable conceptual segmentation and reduce likelihood of incorrect tool selection.

- Easily extendable in a modular way with new subagents as required.

**Cons:**

- Subagent routing and rerouting to supervisor can impose complicated logic with unique edge cases.

**When to use:**

This approach is effective when there is a natural clustering of tools which can be applied to the complete toolkit.

/// example
If your agent has tools that interact with the Internet, a Database and Email, you may define a sub-agent for each of these.
///

This approach also improves performance when there may be a large set of tools available. Since each sub-agent only chooses from a subset of the tools, it is less likely to select an incorrect tool if the Router has effectively done its job.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete the [pre-work](../pre-work/README.md) first.

## Lab

[![Route-and-Solve Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Route-and-Solve Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

Open the notebook in the way that matches where you are working:

/// tab | Lab workstation

In JupyterLab, open `{{ notebook }}` from the file browser. If JupyterLab isn't running yet, see [At the workshop](../pre-work/README.md#at-the-workshop).

///

/// tab | Colab

Click **Open in Colab** above. The first code cell installs everything the notebook needs. Colab needs a Replicate API token: see [Running the Notebooks Remotely (Colab)](../pre-work/README.md#running-the-notebooks-remotely-colab).

///

/// tab | Your own machine

From the `granite-agent-workshop` folder you cloned in the [pre-work](../pre-work/README.md#clone-the-workshop-repository), with its virtual environment active, run:

```shell
jupyter notebook {{ notebook }}
```

///
