---
title: 3. Observing Agents
description: Instrument an agent and read a trace with Langfuse
logo: images/ibm-blue-background.png
notebook: notebooks/02_Agent_Tracing_Observability.ipynb
---

# Agent Observability with Langfuse

Once an agent leaves your notebook, print statements stop being an option. Unlike traditional deterministic software, agentic AI systems produce non-deterministic, multi-step behaviors that shift the operational question from "is it up?" to "is it right?" Observability -- capturing rich telemetry and traceability -- is what lets you answer that question in production.

This lab takes the agent from [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) (`get_stock_price`, `get_current_weather`) and makes every run visible in [Langfuse](https://langfuse.com).

The notebook:

1. Connects to Langfuse with the keys from your `.env` file.
2. Builds the agent and checks that it answers a simple query.
3. Adds Langfuse's callback handler to the agent call. The agent code doesn't change, and each run becomes a trace.
4. Opens the trace in the Langfuse UI: each Granite call shows its prompt, tools, response, token counts and latency, and each tool call shows as its own node.
5. Fetches the trace through the Langfuse API and reads its JSON: the input and output messages, the nested observations and the latency, so you can write your own analysis over it. If Langfuse isn't reachable, it reads a bundled sample trace instead.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first. The notebook is self-contained, but it helps to complete [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) first, because this lab instruments the same agent.

The notebook needs a running Langfuse and its API keys in `.env`. On the lab workstation, Langfuse is already running at <http://localhost:3000>; if you don't have an account and keys there yet, or you're working elsewhere, follow [Setting up Langfuse](#setting-up-langfuse) first.

## Lab

[![Agent Observability](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Agent Observability](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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

## Setting up Langfuse

This lab sends traces to a [Langfuse](https://langfuse.com) project. In this section you sign in, create an organization and a project, then copy the project's API keys into `.env`.

### Get access to Langfuse

/// tab | Lab workstation

Langfuse is already running on the workstation. There is nothing to install: just check that it's up.

1. In the browser, open <http://localhost:3000>. The Langfuse sign-in page should appear.

    /// note | Langfuse doesn't load?
    If the page doesn't load, Langfuse isn't running. Open a terminal and start it from its directory:

    ```shell
    cd /root/granite-agent-workshop-tx2026/langfuse
    sudo docker compose up -d
    ```

    Wait a minute for the containers to start, then reload <http://localhost:3000>. If it still doesn't load, raise your hand.

    ///

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

The steps below show the workstation's Langfuse at <http://localhost:3000>. On Langfuse Cloud, the screens are the same but the URL differs.

### Sign in

1. On the lab workstation, a Langfuse user was created for you before the lab. On the Langfuse sign-in page at <http://localhost:3000>, sign in with the email and password from your lab instructions.

    /// note | Still need to create an account?
    If you don't have a Langfuse user yet, or you're using your own Langfuse or Langfuse Cloud, create one: on the sign-in page, click **No account yet? Sign up**, then enter your name, email and a password. This account exists only in the Langfuse instance you are using.

    ![Langfuse sign-in page with the Sign up link](../images/observing-agents/langfuse_sign_up.png)

    ///

### Create an organization and a project

1. Create a new organization, for example `workshop`, and click **Create**.

    ![Creating the workshop organization](../images/observing-agents/langfuse_create_organization.png)

1. Create a new project in the organization, for example `lab1538`, and click **Create**.

    ![Creating the lab1538 project](../images/observing-agents/langfuse_create_project.png)

### Create an API key

1. Click **Create new API key**. Langfuse shows the **Secret Key**, the **Public Key**, and a ready-made `.env` snippet.

    ![The new Langfuse API keys and environment snippet](../images/observing-agents/langfuse_api_keys.png)

    /// warning | Copy the secret key now
    The secret key is shown only once. Copy the `.env` snippet before you close this page. If you lose it, create a new key in the project settings.
    ///

### Add the keys to `.env`

1. Open the `.env` file you created in [1. Access the Model](../part-01-access-the-model/README.md#create-your-env-file), for example with `vi .env` in the `granite-agent-workshop` directory.

1. Paste the snippet at the top of the file, replacing any existing Langfuse lines, and add a `LANGFUSE_HOST` line with the same URL:

    ```dotenv
    LANGFUSE_SECRET_KEY="sk-lf-xxxxxxx"
    LANGFUSE_PUBLIC_KEY="pk-lf-xxxxxxx"
    LANGFUSE_BASE_URL="http://localhost:3000"
    LANGFUSE_HOST="http://localhost:3000"
    ```

    ![The environment file with the Langfuse keys](../images/observing-agents/langfuse_env_file.png)

    /// note | `LANGFUSE_HOST`
    The notebooks read the Langfuse URL from `LANGFUSE_HOST`. The snippet Langfuse generates names it `LANGFUSE_BASE_URL`, so keep both lines. On Langfuse Cloud, use your cloud URL, for example `https://us.cloud.langfuse.com`.
    ///

1. Save the file. If a notebook is already running, restart its kernel so it picks up the new values.
