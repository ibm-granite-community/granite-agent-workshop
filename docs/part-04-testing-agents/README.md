---
title: 4. Testing Agents
description: Test a Granite agent's final answers, tool calls, multi-turn behavior and latency
logo: images/ibm-blue-background.png
notebook: notebooks/03_Agent_Evaluation.ipynb
---

# Testing Agents

Agents fail differently from ordinary software: an answer can be fluent, confident and wrong, and the same input can take a different path on a different day. Without tests, you cannot confidently change a prompt, swap a model or add a tool.

This lab applies the idea behind Test-Driven Development (TDD) to an agent: write down the expected behavior as test cases, run the agent against them, and rerun them after every change to see what broke.

## What you will learn

The notebook tests a minimal function-calling agent with two tools, `get_current_weather` and `get_stock_price`. Both return hard-coded values, so the expected results never change and the tests need no external API keys.

| Step | What you test | How | Takeaway |
| :--- | :--- | :--- | :--- |
| 4 | Set up the agent under test | `create_agent` with two deterministic tools | Fixed tool outputs make the tests repeatable. |
| 5 | The **final answer** | **LLM-as-a-Judge**: a second model call compares the answer with the expected contents and returns PASS or FAIL | String matching is fragile for natural language; a judge accepts any valid phrasing. |
| 6 | The **trajectory**: which tools the agent called, with which arguments | Programmatic checks on tool names and parameters | An agent can give a plausible answer after calling the wrong tool. Check how it got there, not only what it said. |
| 7 | **Multi-turn** conversations | Carry the history between turns and check each turn's tool calls | A follow-up such as "How about in Tokyo?" only works if the agent keeps the context. |
| 8 | **Non-functional requirements** | Latency per test, and a summary of pass rates | A correct agent can still be too slow or too expensive to ship. |

## How the tests work

Each test case is a small dictionary, and its shape grows with each step:

- **Final answer (step 5):** a `name`, the `input` prompt and the `expected_agent_response_contents`. The judge receives the query, the expected contents, the agent's answer and a rubric (factual consistency, completeness, no hallucinations).
- **Tool calling (step 6):** the same `name` and `input`, plus the `expected_tool_calls`:

    ```python
    {
        "name": "stock_price_with_optional_param",
        "input": "What were the IBM stock prices on September 5, 2025, in euros?",
        "expected_tool_calls": [
            {"name": "get_stock_price", "args": {"ticker": "IBM", "date": "2025-09-05", "currency": "EUR"}},
        ],
    }
    ```

    The agent must pick the right tool and also normalize the arguments, for example "September 5, 2025" into `2025-09-05`.

- **Multi-turn (step 7):** a list of `turns`, each shaped like a tool-calling test case. The conversation history is passed from one turn to the next.

### Optional parameters

`get_stock_price` has an optional `currency` parameter that defaults to `USD`. When the user doesn't ask for a currency, the agent may omit it or pass `USD` explicitly, and both are correct. To avoid false failures, the parameter check has two rules:

1. Every expected parameter must be present with the expected value.
2. Any extra parameter must be an optional parameter of the tool.

The notebook runs one test with and one without a requested currency, to exercise both sides.

### Simulated users

The multi-turn test in the notebook is scripted. Step 7 also explains the next level: a **simulated user**, typically another model, that holds hidden details (a ticker, a date, a currency) and reveals them only when the agent asks. This tests whether the agent asks for missing information instead of guessing it.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete [Getting Started](../getting-started/README.md) first.

The notebook is self-contained, but it helps to complete [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) first, because this lab tests the same kind of agent. It runs Granite on Ollama (`granite4.2:3b`) when the model is available locally, and on [Replicate](https://replicate.com) otherwise: see [Setting up Replicate](../part-01-access-the-model/README.md#setting-up-replicate).

/// warning | Results vary between runs
The model can phrase an answer differently, or pass an optional parameter on one run and not on the next. Run a failing test again before you conclude that the agent is broken, and compare pass rates across several runs.
///

## Lab

[![Testing Agents](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Testing Agents](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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

The notebook ends with a summary of the tool-calling, multi-turn and overall pass rates, and the average latency.

For more background, see the [Agent TDD recipe](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/recipes/Agent_TDD/Function_Calling_Agent_TDD.ipynb), the [Non-Functional Requirements recipe](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/recipes/NFR_Agents/NFR_Agents.ipynb) and [Testing agents](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/testing_agents.md) in the Granite Agent Cookbook.
