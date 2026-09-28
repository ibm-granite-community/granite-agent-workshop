---
title: 1. Access the Model
description: Warm-up notebook -- connect to Granite and run a first prompt
logo: images/ibm-blue-background.png
notebook: notebooks/00_access_the_model.ipynb
---

# Access the Model

This is the warm-up notebook for the workshop. It doesn't build an agent -- it confirms your environment can reach a Granite model, and introduces the `get_llm()` helper that every notebook in this workshop uses to connect to Granite.

`get_llm()` uses [Replicate](https://replicate.com) when the `REPLICATE_API_TOKEN` environment variable is set. Otherwise it uses a local Ollama server when the requested Granite model is already pulled there, and falls back to Replicate if not. Every notebook in this workshop is self-contained, so each one defines its own copy of `get_llm()` near the top rather than importing it from a shared package. If a model connection misbehaves, that cell is the place to look.

By the end of this notebook, you will have:

1. Confirmed which backend you're using (a local Ollama server, or Replicate).
2. Sent a single prompt to Granite and read back a response, with nothing else in the way -- no tools, no agent loop.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete the [pre-work](../pre-work/README.md) first.

## Lab

[![Access the Model](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Access the Model](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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
