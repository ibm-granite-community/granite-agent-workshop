---
title: 4.1 Functional Testing
description: Trajectory and response tests, multi-turn tests, summary metrics and LLM-as-a-Judge
logo: images/ibm-blue-background.png
notebook: notebooks/03_Agent_Evaluation_TDD.ipynb
judge_notebook: notebooks/03_LLM_as_Judge_Basics.ipynb
---

# Functional Testing

Functional testing answers one question: **given this input, does the agent do the right thing?** For a tool-calling agent, "the right thing" has two parts:

- **The trajectory:** the tools the agent called, in which order, with which arguments.
- **The response:** the final answer the user sees.

This lab applies Test-Driven Development (TDD) to agents. You write the expected behavior down first, as test cases, then run the agent against them. The test set becomes a regression suite: every time you change a prompt, a tool schema or the model, you rerun it and see immediately what broke.

The lab has two notebooks:

| Notebook | What it tests | How |
| :--- | :--- | :--- |
| [Agent Evaluation (TDD)](#how-the-tests-work) | Trajectory and response | Exact tool-call matching and substring checks, single-turn and multi-turn, summary metrics |
| [LLM as a Judge](#scoring-responses-with-an-llm-judge) | Response quality | A second model scores each answer from 0 to 5 and explains why |

**Why test your agent?**

- **Catch regressions early** when you update prompts, tool schemas or models.
- **Compare alternatives objectively**, for example Granite 8B against a smaller model.
- **Ship with confidence** because you know the core use cases still pass.
- **Debug faster** by reproducing a failure with one specific test case.
- **Document behavior**: test cases are living examples of what the agent is supposed to do.

## How the tests work

The notebook builds a small test framework in plain Python around the agent from [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md). No test library is needed.

### Trajectory and response evaluation

Two helpers decide whether a run passes:

| Helper | Checks | How | When to use it |
| :--- | :--- | :--- | :--- |
| `trajectory_match` | The tool calls | **Exact match** on the list of tool names and parameters | Always for tools with side effects (writing, deleting, paying): a wrong argument is a real bug |
| `response_match` | The final answer | **Case-insensitive substring** check | Deterministic facts (a city, a ticker). For open-ended answers, use an [LLM judge](#scoring-responses-with-an-llm-judge) or semantic similarity instead |

Testing the trajectory separately from the response matters. An agent can give a plausible answer while calling the wrong tool, or no tool at all, and a response-only check would never notice.

/// note | Exact match is strict on purpose
`{"location": "New York"}` and `{"location": "New York, NY"}` are different trajectories. If the model's normalization of arguments varies but is harmless, relax the comparison for that parameter, but only on purpose, and keep exact matching for anything risky.
///

### The test runner

`run_agent_for_test` streams the agent's execution and records, in an `AgentTestResult`:

- every tool call (`ToolCall(tool_name, tool_parameters)`) taken from the AI messages,
- the final text response (the last AI message with no tool calls),
- the wall-clock latency, and a rough token estimate (characters ÷ 4).

The runner is the only piece that knows about LangGraph. The test cases and the checks are plain data and functions, so you can reuse them for a different agent architecture.

### Test cases

Each test case is a dictionary holding the input and the expected outcome:

```python
{
    "name": "stock_price_query",
    "input": "What were the IBM stock prices on September 5, 2025?",
    "expected_tool_calls": [
        {"tool_name": "get_stock_price", "tool_parameters": {"ticker": "IBM", "date": "2025-09-05"}}
    ],
    "expected_response_contains": "IBM"
}
```

This case tests more than tool selection. The agent must also **extract and normalize** the arguments: turn "September 5, 2025" into the `YYYY-MM-DD` format that the tool's docstring asks for.

The notebook covers each tool with two variations (a different city, a different ticker) so a pass isn't just the model memorizing one prompt.

### Single-turn and multi-turn tests

- **Single-turn tests** send one question and check one trajectory and one response. They cover the basic functionality of each tool.
- **Multi-turn tests** send a sequence of questions and check every turn. They cover how real users talk: switching from weather to stocks halfway through, or asking a follow-up such as "How about in Tokyo?" that only makes sense given the previous turn.

### Summary metrics

After the runs, the notebook reduces the results to a few numbers you can track over time:

| Metric | What it tells you |
| :--- | :--- |
| **Pass rate** | Share of tests (or turns) where *both* the trajectory and the response pass: the main correctness signal |
| **Average latency** | How long users wait. Watch for regressions after a model or prompt change |
| **Average tokens** | A proxy for cost. Check that any quality gain is worth the extra tokens |

Latency and tokens are measured here as side information. [4.2 Non-Functional Testing](non-functional-testing.md) treats them as first-class test targets.

## Scoring responses with an LLM judge

A substring check can't tell that "NYC" and "New York City" are the same place, or that "72°F" and "72 degrees" are the same temperature. It also can't tell whether an answer is helpful, or whether it used the tool result correctly. **LLM as a Judge** closes that gap: a second model reads the interaction and grades the response.

The judge receives:

- the user's query,
- the agent's response,
- optionally, the tool results the agent had available, and a reference answer.

It returns a score from 0 (wrong or harmful) to 5 (accurate, complete, relevant), plus a short justification, as JSON. The notebook's judge prompt shows the pattern to follow: clear criteria for each score, all the context the judge needs, a request to reason step by step, and a fixed output format so the score can be parsed.

A judge can look at several dimensions of quality: **accuracy** (did the answer use the tool results correctly?), **completeness**, **helpfulness**, **relevance** and **safety**.

### When to use a judge

| Use a judge for | Keep programmatic checks for |
| :--- | :--- |
| Natural-language answers where wording varies | Tool calls and their arguments: `trajectory_match` is exact, fast and free |
| Qualities like completeness and helpfulness | Facts you can check with a substring or a regular expression |
| Development and test runs, where a justification helps you debug | High-volume, low-latency checks in production |

The two approaches combine well: assert the trajectory exactly, then require a minimum judge score (for example, 4 out of 5) on the response.

### Judges have limits

- **Cost and latency**: every evaluation is a full model call.
- **Variability**: the same response can get a different score on a different run. Set the judge's `temperature` to 0, and run important cases more than once.
- **Judge bias**: a model can favor answers that look like its own. The notebook uses Granite as both agent and judge to keep the setup simple; in practice, a judge from a different model family reduces this bias.
- **Prompt sensitivity**: the scores are only as good as the judge prompt. Check the judge against a few examples you have scored by hand before you trust it.
- **Parsing failures**: if the judge doesn't return valid JSON, the notebook records a score of `-1`. Treat that as a failed evaluation, not a failed agent.

## Prerequisites

This lab is a [Jupyter notebook](https://jupyter.org/). At the workshop, your workstation is already set up: there is nothing to install. To run it on your own machine or in Colab, complete the [pre-work](../pre-work/README.md) first. The notebook is self-contained, but it helps to complete [2.1 Function Calling Agent](../part-02-building-agents/function-calling.md) first, because this lab tests the same agent.

/// note | Replicate token required
This notebook calls Granite on [Replicate](https://replicate.com), so it needs `REPLICATE_API_TOKEN` in your `.env` file (or Colab secrets). See [Running the Notebooks Remotely (Colab)](../pre-work/README.md#running-the-notebooks-remotely-colab).
///

## Lab

### Notebook 1: Agent Evaluation (TDD)

[![Functional Testing](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}
[![Functional Testing](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ notebook }}){:target="_blank"}

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

This notebook walks through seven steps:

1. Define the data structures and the evaluation helpers.
2. Wrap the agent in a test runner.
3. Define the single-turn test set.
4. Run the single-turn tests.
5. Define the multi-turn tests.
6. Run the multi-turn tests.
7. Compute the summary metrics.

### Notebook 2: LLM as a Judge

[![LLM as a Judge](https://badgen.net/badge/icon/github?icon=github&label=View%20on "View on GitHub")]({{ config.repo_url }}/blob/{{ extra.repo_branch }}/{{ judge_notebook }}){:target="_blank"}
[![LLM as a Judge](https://colab.research.google.com/assets/colab-badge.svg "Open In Colab")]({{ extra.colab_url }}/blob/{{ extra.repo_branch }}/{{ judge_notebook }}){:target="_blank"}

Open `{{ judge_notebook }}` the same way as the first notebook.

Like the first notebook, it calls Granite (`ibm-granite/granite-4.2-8b`) on Replicate for both the agent and the judge, so it needs the same `REPLICATE_API_TOKEN`.

The notebook:

1. Explains what LLM as a Judge is, when to use it and its limits.
2. Builds the same weather and stock price agent to evaluate.
3. Sets up the judge model and designs the judge prompt.
4. Parses the judge's JSON answer into a score and a justification.
5. Evaluates three examples: a weather query, a stock price query, and a query the tools can't answer well.
6. Runs a batch evaluation over several test cases and summarizes the scores.

## Going further

- **Add edge cases and failure scenarios**: a city that doesn't exist, a date in the future, a question no tool can answer (the expected trajectory is then an empty list).
- **Combine both notebooks**: keep the exact trajectory checks, and replace `response_match` with a minimum judge score.
- **Use several judges** and combine their scores to reduce the bias of any single judge model.
- **Run the suite in CI** on every change to prompts, tools or model version.
- **Compare models** by running the same suite against different Granite sizes.
- **Store the results** over time so you can spot a regression in pass rate, latency or tokens.
