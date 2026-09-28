---
title: 3. Observing Agents
description: Instrument an agent and read a trace with Langfuse
logo: images/ibm-blue-background.png
notebook: notebooks/02_Agent_Tracing_Observability.ipynb
---

# Agent Observability with Langfuse

Once an agent leaves your notebook, print statements stop being an option. Unlike traditional deterministic software, agentic AI systems produce non-deterministic, multi-step behaviors that shift the operational question from "is it up?" to "is it right?" Observability -- capturing rich telemetry and traceability -- is what lets you answer that question in production.

This lab takes the agent from [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) (`get_llm()`, `get_stock_price`, `get_current_weather`, `build_agent()`) and instruments it with [Langfuse](https://langfuse.com) tracing. You will:

1. Instrument the agent with Langfuse's callback handler and capture a trace, with no code changes to the agent itself.
2. Review the trace in the Langfuse UI: LLM generations, tool calls, token counts, latency and cost.
3. Collect and read a trace as JSON, to understand its structure well enough to write your own analysis over it.
4. **⭐ Stretch:** run a Langfuse experiment to score agent output automatically, and see two ways to build a trace manually when automatic instrumentation isn't enough.

/// tip | Works even without Langfuse running yet
This lab ships with a real, pre-captured sample trace (`trace.json`) alongside the notebook. Step 3 (reading a trace as JSON) works from that sample even before you've set up Langfuse, or if a live extraction fails for any reason -- so you can explore the trace structure immediately and come back to live tracing once your Langfuse instance is ready.
///

## Setting up Langfuse

This lab uses a **self-hosted Langfuse at `http://localhost:3000`** during the live workshop (see the lab guide's familiarization walkthrough). If you're working through this on your own machine, or in Colab where nothing runs on `localhost`, use [Langfuse Cloud](https://us.cloud.langfuse.com/) instead -- the notebook works the same way either way, only `LANGFUSE_HOST` changes.

Once you have access to a Langfuse project:

1. Go to **Settings → API Keys** and generate a new key pair.
2. Copy your **Public Key**, **Secret Key**, and **Host URL** into your `.env` file:

    ```dotenv
    LANGFUSE_SECRET_KEY=sk-lf-xxxxx
    LANGFUSE_PUBLIC_KEY=pk-lf-xxxxx
    LANGFUSE_HOST=http://localhost:3000
    ```

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete the [pre-work](../pre-work/README.md) first. The notebook is self-contained, but it helps to complete [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) first, because this lab instruments the same agent.

## Lab

[![Agent Observability](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Agent Observability](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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
