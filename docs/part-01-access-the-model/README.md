---
title: 1. Access the Model
description: Warm-up notebook -- connect to Granite and run a first prompt
logo: images/ibm-blue-background.png
notebook: notebooks/00_access_the_model.ipynb
---

# Access the Model

This is the warm-up to confirm your environment can reach a Granite model.

It introduces `get_llm()`, the helper every notebook in this workshop uses to connect to Granite with:

* Replicate when a `REPLICATE_API_TOKEN` environment variable is set
* local Ollama server, with Replicate as the fallback.

By the end of this notebook, you will have:

1. Confirmed which backend you're using (a local Ollama server, or Replicate).
2. Sent a single prompt to Granite and read back a response, with nothing else in the way -- no tools, no agent loop.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install, and a Replicate API token is already configured in the repository's `.env` file. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) and [Setting up Replicate](#setting-up-replicate) first.

## Lab

[![Access the Model](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Access the Model](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

Open the notebook in the way that matches where you are working:

/// tab | Lab workstation

In JupyterLab, open `granite-agent-workshop/notebooks/00_access_the_model.ipynb` in the file browser and run it. If JupyterLab isn't running yet, see [Getting Started](../getting-started/README.md#open-jupyterlab).

/// note | Notebook fails?
If the notebook can't reach a Granite model, for example with an authentication error from Replicate, check the token in your `.env` file: see [Setting up Replicate](#setting-up-replicate).
///

///

/// tab | Colab

Click **Open in Colab** above. The first code cell installs everything the notebook needs. Colab needs a Replicate API token: see [Setting up Replicate](#setting-up-replicate).

///

/// tab | Your own machine

From the `granite-agent-workshop` folder you cloned in [Getting Started](../getting-started/README.md), with its virtual environment active, run:

```shell
jupyter notebook {{ notebook }}
```

///

## Setting up Replicate

`get_llm()` needs a Granite model served by an AI model runtime. [Replicate](https://replicate.com/) is a cloud platform that hosts and serves AI models for you. Several labs call Granite on Replicate, and it is the only option in Colab.

On the lab workstation, the token is already in place: you only need this section if the notebook fails, or if you're working in Colab or on your own computer. In it you create a Replicate API token and make it available to the notebooks.

### Create a Replicate API token

1. Create a [Replicate](https://replicate.com/) account. You will need a [GitHub](https://github.com/) account to do this.

1. Add credit to your Replicate account (optional). To remove a barrier to entry to try the Granite models on the Replicate platform, use [this link](https://replicate.fyi/ibm) to add a small amount of credit to your Replicate account.

1. Create a Replicate [API token](https://replicate.com/account/api-tokens).

### Create your `.env` file

The notebooks read API keys from a `.env` file at the root of the repository.

/// tab | Lab workstation

The `.env` file already exists. Don't recreate it from the template, or you will lose the values already set up for you: open it to check or replace the token.

```shell
cd /root/granite-agent-workshop-tx2026/granite-agent-workshop
```

///

/// tab | Your own computer

Create it from the template, in the `granite-agent-workshop` folder you cloned in [Getting Started](../getting-started/README.md):

```shell
cd granite-agent-workshop
cp .env.example .env
```

///

Keep this terminal open: you will edit `.env` again when you [set up Langfuse](../part-03-observing-agents/README.md#setting-up-langfuse) in the Observing Agents lab.

### Add the token to `.env`

Add the token to your `.env` file:

```dotenv
REPLICATE_API_TOKEN=<your_replicate_api_token>
```

Save the file. If a notebook is already running, restart its kernel so it picks up the new value.

### Alternative: local models with Ollama

If you want to run the models locally on your own computer instead, you can use [Ollama](https://ollama.com/), a lightweight tool for running LLMs from the command line. You will need a computer with a GPU and at least 32GB RAM, preferably more.

/// note | Tested system
This was tested on a MacBook with an M1 processor and 32GB RAM. It may be possible to serve models with a CPU and less memory. On a Mac with Apple Silicon, Ollama uses the Metal GPU for accelerated inference.
///

1. Validate Ollama is installed by opening [http://localhost:11434](http://localhost:11434){:target="_blank"}: it should show `Ollama is running`.

1. Pull the Granite model:

    ```shell
    ollama pull granite4.2:3b
    ```

    `granite4.2:3b` is enough for most of the workshop. The [Plan-and-Solve](../part-02-building-agents/plan-and-solve.md) lab asks for the larger `granite4.2:8b` model; if you haven't pulled it, that lab automatically falls back to Replicate instead. Pull it too if you'd rather run everything fully locally:

    ```shell
    ollama pull granite4.2:8b
    ```

1. Ollama runs automatically and exposes an OpenAI-compatible API at <http://localhost:11434>.
