---
title: 5.2 Packaging Agents with A2A
description: Serve Granite agents over the Agent2Agent protocol and let them delegate work to each other
logo: images/ibm-blue-background.png
notebook: notebooks/06_packaging_a2a.ipynb
---

# Packaging Agents with A2A

In [5.1](mcp.md) you packaged *tools*: the caller picks the function and its arguments. The [Agent2Agent (A2A)](https://a2a-protocol.org) protocol packages *agents*: the caller states a goal in plain language, and the remote agent decides how to reach it with its own model, prompt and tools. The caller only sees the agent's card.

In this lab you build four Granite agents with LangGraph, serve each one as its own A2A server, and have them book the same Atlanta trip as in 5.1:

```text
            A2A client (notebook or A2A Inspector)
                            │
                    Trip Agent :10022
                     │              │
          Flight Agent :10020    Hotel Research Agent :10021
                     │
          Fare Finder Agent :10023
```

| Agent | Decides | How it coordinates |
| :--- | :--- | :--- |
| **Trip Agent** | Nothing: books the flight, then the hotel, then writes the itinerary | Fixed LangGraph workflow, each step an A2A call |
| **Flight Agent** | Which flight matches the arrival constraint | Has the Fare Finder Agent as a tool; its model chooses when to call it |
| **Fare Finder Agent** | Which booking site to use | Leaf agent |
| **Hotel Research Agent** | The best-rated hotel within budget | Leaf agent, with a mocked web search |

Tools return mock data, so results are repeatable.

## What you will learn

| Step | You do | Takeaway |
| :--- | :--- | :--- |
| 1 | Learn the A2A concepts | An **agent card** at `/.well-known/agent-card.json` advertises skills and the endpoint. A request becomes a **task** (`submitted` → `working` → `completed`) whose output is an **artifact**. |
| 2 | Connect to Granite | The same `get_llm()` as the other labs. |
| 3 | Write the A2A building blocks once | An `AgentExecutor` adapts any agent to A2A, a client calls any agent by URL, and `serve_agent` runs each agent with `uvicorn` on the notebook's own event loop. |
| 4 | Build and serve the four agents | Serving an existing LangGraph agent takes an agent card and one call. Calling another agent is just a tool or a graph node. |
| 5 | Test the agents, alone then together | One request to the Trip Agent makes four A2A calls. The status updates show each agent's decision. |
| 5.4 | Inspect the agents with [A2A Inspector](https://github.com/a2aproject/a2a-inspector) | Validate agent cards and watch the JSON-RPC messages and task updates, with no notebook code involved. |

The expected result:

```text
Flight: UA321, Expedia, departs 08:30, arrives 10:00, $262, confirmation FL-UA321-2026-10-25
Hotel: Atlanta Grand Hotel, 1 night from 2026-10-25, $195, confirmation HT-ATLANTA-2026-10-25
Total: $457
```

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first. Doing [5.1](mcp.md) first helps, but is not required.

The notebook starts four local servers on ports 10020 to 10023. Run the **Stop the servers** cell, or restart the kernel, before running it again.

## Lab

[![Packaging Agents with A2A](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Packaging Agents with A2A](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

Open the notebook in the way that matches where you are working:

/// tab | Lab workstation

In the JupyterLab file browser, open `granite-agent-workshop/{{ notebook }}`. If JupyterLab isn't running yet, see [Getting Started](../getting-started/README.md#open-jupyterlab).

///

/// tab | Colab

Click **Open in Colab** above. The first code cell installs everything the notebook needs. Colab needs a Replicate API token: see [Setting up Replicate](../part-01-access-the-model/README.md#setting-up-replicate).

The agents run inside the Colab runtime, so A2A Inspector (step 5.4) can't reach them: skip that step.

///

/// tab | Your own machine

From the `granite-agent-workshop` folder you cloned in [Getting Started](../getting-started/README.md), with its virtual environment active, run:

```shell
jupyter notebook {{ notebook }}
```

///

## Inspect the agents with A2A Inspector

Run the notebook up to step 5.3 and leave the kernel running: the agents stay up until you stop them. A2A Inspector needs [uv](https://docs.astral.sh/uv/) and Node 22 or later. In a terminal:

```shell
git clone https://github.com/a2aproject/a2a-inspector.git
cd a2a-inspector
uv sync
(cd frontend && npm install)
bash scripts/run.sh
```

Open <http://127.0.0.1:5001> and connect to an agent by URL:

| Agent | URL |
| :--- | :--- |
| Flight Agent | `http://127.0.0.1:10020` |
| Hotel Research Agent | `http://127.0.0.1:10021` |
| Trip Agent | `http://127.0.0.1:10022` |
| Fare Finder Agent | `http://127.0.0.1:10023` |

Then:

1. Connect to the **Hotel Research Agent**. Check the agent card and its validation result, send `Book a hotel in Atlanta for 1 night from 2026-10-25, under $200 a night`, and open the debug console to see the JSON-RPC request, the task status updates and the artifact.
2. Connect to the **Trip Agent** and send `Plan a 2-day trip to Atlanta starting 2026-10-25. I need to arrive before noon, and the hotel must cost less than $200 a night.` Watch the `working` updates arrive: Trip asks Flight, Flight asks Fare Finder, then Trip asks Hotel and writes the itinerary.

/// note | Docker
The agent cards advertise `127.0.0.1` URLs, which a container can't reach. On Linux, run the Inspector image with `docker run --network host` and open <http://127.0.0.1:8080>. On macOS and Windows, run it from source as above.
///
