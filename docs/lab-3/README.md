---
title: Lab 3. Route-and-Solve Agent
description: Route-and-Solve Agent
logo: images/ibm-blue-background.png
notebook: notebooks/Route_and_Solve_Agent.ipynb
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

This lab is a [Jupyter notebook](https://jupyter.org/). Please follow the instructions in [pre-work](../pre-work/README.md) to run the lab.

## Lab

[![# Route-and-Solve Agent](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ git.commit }}/{{ notebook }}){:target="_blank"}
[![# Route-and-Solve Agent](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ git.commit }}/{{ notebook }}){:target="_blank"}

To run the notebook from your command line in Jupyter using the active virtual environment from the [pre-work](../pre-work/README.md#install-jupyter), run:

```shell
jupyter notebook {{ notebook }}
```

The path of the notebook file above is relative to the `granite-agent-workshop` folder from the git clone in the [pre-work](../pre-work/README.md#clone-the-workshop-repository).
