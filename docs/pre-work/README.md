---
title: Lab 0. Pre-work
description: Preparation for Building Efficient AI Agents with IBM Granite
logo: images/ibm-blue-background.png
---

# Pre-work

The labs in the workshop are [Jupyter notebooks](https://jupyter.org/). You can run them on the workshop workstation, on your own computer, or remotely on the [Google Colab](https://colab.research.google.com) service.

## At the workshop

If you are attending the workshop, your workstation is already set up: the Python environment, the notebooks and access to the Granite models are in place, so **you can skip the rest of this page** apart from the Langfuse step below.

1. Keep this website open in a browser tab for the whole session.
2. Open a terminal and start JupyterLab from the workshop directory:

    ```shell
    cd ~/granite-agent-workshop-tx2026
    uv run jupyter-lab
    ```

3. In JupyterLab, open the `notebooks` folder. The notebooks are numbered in the order you will run them.
4. Run the first cell of any notebook to confirm the kernel starts. If it fails, raise your hand now rather than during the first part.
5. Before the [Observing Agents](../part-03-observing-agents/README.md) lab, add your Langfuse keys to `.env` as described in [Setting up Langfuse](#setting-up-langfuse-for-the-observing-agents-lab). Langfuse is already running on the workstation at <http://localhost:3000>.

## Running the Notebooks

If you are working on your own, follow the instructions corresponding to how you want to run the notebooks:

- [Locally on your computer](#running-the-notebooks-locally) OR
- [Remotely on the Google Colab service](#running-the-notebooks-remotely-colab)

## Running the Notebooks Locally

It is recommended if you want to run the lab notebooks locally on your computer that you have:

- A computer or laptop
- Knowledge of [Git](https://git-scm.com/) and [Python](https://www.python.org/)

If not, then it recommended to go to the [Running the Notebooks Remotely (Colab)](#running-the-notebooks-remotely-colab) section instead.

Running the lab notebooks locally on your computer requires the following steps:

- [Local Prerequisites](#local-prerequisites)
- [Serving the Granite AI Models for locally run Notebooks](#serving-the-granite-ai-models-for-locally-run-notebooks)
- [Clone the Workshop Repository](#clone-the-workshop-repository)
- [Install Jupyter](#install-jupyter)

### Local Prerequisites

- Git
- Python 3.11, 3.12, or 3.13

/// tip | Installing Python

If you don't have Python installed, or the installed version is not one of the versions supported by this workshop, you should consider installing and using the [`uv` tool](https://docs.astral.sh/uv/getting-started/installation/) to assist in installing the proper Python version.
`uv` works on macOS, Linux, and Windows.
Once `uv` is installed, you can install Python 3.13 with

```shell
uv python install --default 3.13
```

You can then update the shell configurations files to add the Python commands to the PATH.

```shell
uv python update-shell
```

///

### Clone the Workshop Repository

Clone the workshop repo on the `tx-26-lab` branch and cd into the repo directory.

```shell
git clone --branch tx-26-lab https://github.com/jslecointre/granite-agent-workshop.git
cd granite-agent-workshop
```

### Serving the Granite AI Models for locally run Notebooks

The labs require Granite models to be served by an AI model runtime so that the models can be invoked or called. The following sections provide instructions to either run the models in the cloud using [Replicate](https://replicate.com/) or locally using [Ollama](https://ollama.com/).

#### Replicate AI Cloud Platform

[Replicate](https://replicate.com/) is a cloud platform that will host and serve AI models for you.

1. Create a [Replicate](https://replicate.com/) account. You will need a [GitHub](https://github.com/) account to do this.

1. Add credit to your Replicate Account (optional). To remove a barrier to entry to try the models on the Replicate platform, use [this link](https://replicate.fyi/ibm) to add a small amount of credit to your Replicate account.

1. Create a Replicate [API Token](https://replicate.com/account/api-tokens).

1. Set your Replicate API Token as an environment variable in your terminal where you will run the notebook:

    ```shell
    export REPLICATE_API_TOKEN=<your_replicate_api_token>
    ```

#### Local Model Inference with Ollama

If you want to run the AI models locally on your computer, you can use [Ollama](https://ollama.com/).

Ollama is a lightweight tool for running LLMs locally from the command line.

You will need to have a computer with:

- GPU processor
- At least 32GB RAM, preferably more

/// note | Tested system
This was tested on a MacBook with an M1 processor and 32GB RAM. It maybe possible to serve models with a CPU and less memory.
///

/// tip | Apple Silicon
If you have a Mac with Apple Silicon (M1/M2/M3), Ollama can leverage the Metal GPU for accelerated inference.
///

If you computer is unable to serve the models, then it is recommended to go to the [Replicate AI Cloud Platform](#replicate-ai-cloud-platform) section instead.

Running Ollama locally on your computer requires the following steps:

1. Download and install [Ollama](https://ollama.com/download) for your platform.

2. Pull the Granite model:

    ```shell
    ollama pull granite4.2:3b
    ```

    `granite4.2:3b` is enough for most of the workshop. The [Plan-and-Solve](../part-02-building-agents/plan-and-solve.md) lab asks for the larger `granite4.2:8b` model; if you haven't pulled it, that lab automatically falls back to Replicate instead (see [Serving the Granite AI Models](#serving-the-granite-ai-models-for-locally-run-notebooks) above). Pull it too if you'd rather run everything fully locally:

    ```shell
    ollama pull granite4.2:8b
    ```

3. Ollama runs automatically and exposes an OpenAI-compatible API at <http://localhost:11434>

### Install Jupyter

/// note | Use a virtual environment
Before installing dependencies and to avoid conflicts in your environment, it is advisable to use a [virtual environment (venv)](https://docs.python.org/3/library/venv.html).
///

1. Create virtual environment:

    /// tab | uv

    ```shell
    uv venv --clear --seed --python 3.13 venv
    ```

    ///

    /// tab | venv

    ```shell
    python3 -m venv --upgrade-deps --clear venv
    ```

    ///

1. Activate the virtual environment by running:

    ```shell
    source venv/bin/activate
    ```

1. Install Jupyter notebook in the virtual environment:

    /// tab | uv

    ```shell
    uv pip install notebook ipywidgets
    ```

    ///

    /// tab | venv

    ```shell
    python3 -m pip install --require-virtualenv notebook ipywidgets
    ```

    ///

    For more information, see the [Jupyter installation instructions](https://jupyter.org/install)

1. To open a notebook in Jupyter (in the active virtual environment), run:

    ```shell
    jupyter notebook <notebook-file-path>
    ```

## Running the Notebooks Remotely (Colab)

Running the lab notebooks remotely using [Google Colab](https://colab.research.google.com) requires the following steps:

- [Colab Prerequisites](#colab-prerequisites)
- [Serving the Granite AI Models for Colab](#serving-the-granite-ai-models-for-colab)

### Colab Prerequisites

- [Google Colab](https://colab.research.google.com) requires a Google account that you're logged into.

### Serving the Granite AI Models for Colab

The labs require Granite models to be served by an AI model runtime so that the models can be invoked or called.

#### Replicate AI Cloud Platform for Colab

[Replicate](https://replicate.com/) is a cloud platform that will host and serve AI models for you.

1. Create a [Replicate](https://replicate.com/) account. You will need a [GitHub](https://github.com/) account to do this.

1. Add credit to your Replicate Account (optional). To remove a barrier to entry to try the Granite models on the Replicate platform, use [this link](https://replicate.fyi/ibm) to add a small amount of credit to your Replicate account.

1. Create a Replicate [API Token](https://replicate.com/account/api-tokens).

1. Add your Replicate API Token to the Colab Secrets manager to securely store it. Open [Google Colab](https://colab.research.google.com) and click on the 🔑 Secrets tab in the left panel. Click "Add new secret" and enter `REPLICATE_API_TOKEN` into the name field and paste your token into the value field. Toggle the button on the left to allow notebook access to the secret.

![Colab Secrets](../images/colab-secrets.png)

## Setting up Langfuse (for the Observing Agents lab)

The [Observing Agents](../part-03-observing-agents/README.md) lab needs a [Langfuse](https://langfuse.com) project. You don't need this for the earlier labs -- come back to this section when you get there.

/// tab | Lab workstation

Langfuse is already running on the workstation at <http://localhost:3000>. There is nothing to install.

///

/// tab | Self-hosted (local)

Run Langfuse locally with Docker Compose, following the [deployment guide](https://langfuse.com/self-hosting/deployment/docker-compose):

```shell
git clone --depth 1 https://github.com/langfuse/langfuse.git
cd langfuse
docker compose up -d
```

Langfuse will be available at <http://localhost:3000>.

///

/// tab | Cloud

If you'd rather not run Docker, [create a free account](https://us.cloud.langfuse.com/) on Langfuse Cloud instead. This is also the option to use from Colab, which can't reach `localhost`.

///

Once you have access, sign up, create an organization and a project, then go to **Settings → API Keys** and generate a new key pair. Copy the **Public Key**, **Secret Key**, and **Host URL** into the `.env` file in the workshop directory. If there is no `.env` file yet, copy `.env.example` to `.env` first. The secret key is shown only once, so copy it before closing the dialog.

```dotenv
LANGFUSE_SECRET_KEY=sk-lf-xxxxx
LANGFUSE_PUBLIC_KEY=pk-lf-xxxxx
LANGFUSE_HOST=http://localhost:3000
```
