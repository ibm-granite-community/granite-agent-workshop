---
title: 4.2 Non-Functional Testing
description: Measure and reduce the cost, latency, memory and energy footprint of a Granite agent
logo: images/ibm-blue-background.png
notebook: notebooks/03_NFR_Agents.ipynb
---

# Non-Functional Testing

[4.1 Functional Testing](functional-testing.md) checks that the agent does the *right thing*. Non-functional testing checks that it does it *well enough to ship*: fast enough, cheap enough, and without wasting compute.

## Goal of the lab

The notebook shows you how to **measure** the non-functional requirements (NFRs) of an agent (cost, latency, throughput, memory and energy footprint) and how to **reduce** them. It ends with a reusable `NFRProfiler` that records these metrics on every run and draws them as a small dashboard.

## Why agents need it

An agent is not one model call, it is a **loop**: the model calls a tool, reads the result, and calls the model again until the task is done. Every step re-sends the whole conversation history. As a result:

- **Cost compounds**: a 5-step task sends about 3× more input tokens than a single call with the same content.
- **Latency stacks up**: model calls and tool calls run one after another.
- **Context grows**: the history keeps getting longer, and fills the context window.

None of this is visible from the outside: a task that looks like one request can hide 5 to 20 model calls.

## What you learn

- **Token economics**: how input and output tokens are priced, and how much the model tier changes the bill.
- **How to measure an agent's spend**: count tokens, model calls and latency per run with LangChain's `UsageMetadataCallbackHandler`.
- **How to reduce cost**: trim old history with a sliding window, and route simple queries to a small model. Prompt caching and output length limits are covered briefly.
- **What drives latency**: time to first token versus generation time, and why the model calls, not the tools, dominate an agent's wall-clock time.
- **Memory and context**: process RAM versus the context window, and why the context window is the budget to manage.
- **Environmental footprint**: energy scales with the number of model calls, so the cost levers also save energy.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete the [pre-work](../pre-work/README.md) first. The notebook is self-contained and doesn't depend on the previous labs.

/// note | Replicate token required
This notebook calls Granite (`ibm-granite/granite-4.2-8b`) on [Replicate](https://replicate.com), so it needs `REPLICATE_API_TOKEN` in your `.env` file (or Colab secrets). See [Running the Notebooks Remotely (Colab)](../pre-work/README.md#running-the-notebooks-remotely-colab).
///

/// warning | Your numbers will differ
Token counts, latency and throughput depend on the model, the provider's load and your network, and change from run to run. Costs are estimates based on illustrative price tiers, not what Replicate bills. Compare runs with each other rather than with the figures on this page.
///

## Lab

[![Non-Functional Testing](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Non-Functional Testing](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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

The notebook has eight short sections: setup, token economics, measuring agent spend, cost optimization, latency and throughput, memory and context, environmental footprint, and the NFR profiler. As you run it, look for:

- how the **number of model calls**, and with it the input tokens, grows with the complexity of the task,
- how much the sliding window and model routing save,
- whether latency follows the length of the prompt or the length of the answer.

## Going further

- **Profile the whole agent**, not just a single model call, and record the number of model calls per run.
- **Turn budgets into tests**, for example "this task stays under 2,000 input tokens and 10 seconds", and run them next to the functional tests from [4.1](functional-testing.md).
- **Watch it in production** by sending the same metrics to Langfuse, as in [3. Observing Agents](../part-03-observing-agents/README.md).

## References

- [FinOps for AI overview](https://www.finops.org/wg/finops-for-ai-overview/)
- [Google Cloud: Measuring the environmental impact of AI inference](https://cloud.google.com/blog/products/infrastructure/measuring-the-environmental-impact-of-ai-inference/)
- [MIT Technology Review: We did the math on AI's energy footprint](https://www.technologyreview.com/2025/05/20/1116327/ai-energy-usage-climate-footprint-big-tech/)
- [Testing agents](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/testing_agents.md) in the Granite Agent Cookbook
