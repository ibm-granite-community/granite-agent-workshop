---
title: 2.7 Agent Harnesses
description: Agent loops, agent harnesses and agent skills
logo: images/ibm-blue-background.png
---

# Agent Harnesses

/// note | Notebook coming soon
This page introduces the concepts. Hands-on notebooks that run Granite inside a harness are in progress.
///

In labs 2.1 to 2.5 you built agents from scratch with a development framework (LangGraph): you wrote the loop, chose the tools and wired up the state. The industry is increasingly moving the other way. Instead of building the agent, you start from a **preset agent**, a *harness* such as Pi, OpenCode, Hermes or LangChain's Deep Agents, and customize it with instructions, tools and *skills*.

This page covers the three ideas you need to follow that shift:

1. **Agent loops**: the small algorithm at the heart of every agent.
2. **Agent harnesses**: everything that is built around the loop, and what the popular harnesses ship with.
3. **Agent skills**: the open format for packaging know-how that any harness can load.

## Agent loops

Every agent you built in this part runs the same loop:

```python
messages = [system_prompt, user_message]
while True:
    response = model.invoke(messages, tools=tools)   # the model decides
    messages.append(response)
    if not response.tool_calls:                      # no tool call: final answer
        break
    for call in response.tool_calls:                 # your program acts
        result = run_tool(call.name, call.args)
        messages.append(tool_message(result, call.id))
```

The model *decides* what to do next, the program *acts*, and the result goes back into the context for the next decision. That's the [Function Calling Agent](function-calling.md) flow. The other patterns change what happens around the loop, not the loop itself:

| Pattern | What changes |
| :--- | :--- |
| [ReAct](react.md) | The model writes its reasoning before each tool call |
| [Plan-and-Solve](plan-and-solve.md) | A planning step runs before the loop, and a step after each iteration revises the plan |
| [Route-and-Solve](route-and-solve.md) | A router picks which loop, with which tools, handles the query |
| [ToolRAG](toolrag.md) | A retrieval step chooses the tools before each model call |

The loop itself is a few lines of code. It is also the easy part. What makes an agent reliable is everything around it: when to stop, what goes into the context window, which tools are safe to run, what to remember between sessions. That surrounding machinery is the harness.

## Agent harnesses

An **agent harness** is the loop plus everything a production agent needs around it, packaged so you configure it rather than rebuild it. Most harnesses have the same building blocks:

| Building block | What it does | Typical form |
| :--- | :--- | :--- |
| **Built-in system prompt** | Tells the model how to behave as an agent: how to use the tools, when to stop, how to format answers | Shipped with the harness, often tuned per model family, replaceable or extendable |
| **Built-in tools** | The actions available out of the box | Read, write and edit files, run shell commands, search, fetch web pages |
| **Context files** | Project or user instructions loaded into every session | `AGENTS.md` (and `CLAUDE.md` for compatibility) |
| **Skills** | Packaged know-how loaded only when relevant | [Agent Skills](#agent-skills) folders with a `SKILL.md` |
| **Context management** | Keeps the context window focused over long sessions | Compaction, pruning, memory files (see [2.6 Context Engineering](context-engineering.md)) |
| **Permissions and sandboxing** | Controls what the agent may do without asking | Allow / ask / deny rules, containers, remote sandboxes |
| **Extension points** | Adds capabilities without forking the harness | Plugins, MCP servers, custom agents, hooks |
| **Interfaces** | How people and programs reach the agent | Terminal UI, headless or print mode, SDK, messaging apps |

Harnesses differ mainly in how much they build in. Three open-source examples show the range:

### Pi

[Pi](https://pi.dev) is a deliberately **minimal** harness for the terminal, created by Mario Zechner. Its motto is "primitives, not features":

- **Tools**: four by default: `read`, `write`, `edit` and `bash`. Read-only `grep`, `find` and `ls` are available but disabled by default.
- **System prompt**: minimal. According to its author, the system prompt and tool definitions together stay below 1,000 tokens, on the grounds that current models already know how to behave as coding agents. A project can replace or extend it with a `SYSTEM.md` file.
- **Context files**: `AGENTS.md`, loaded from the user's Pi folder, parent directories and the current directory.
- **What it leaves out on purpose**: no MCP, no subagents, no plan mode, no built-in to-do list, no permission prompts. Instead, it relies on skills and command-line tools with a README (the agent reads them only when needed), `tmux` for parallel work, and containers for isolation.
- **Extending it**: TypeScript extensions, skills, prompt templates and themes, bundled as packages you install from npm or git. It runs interactively, in print or JSON mode, over RPC, or embedded through a TypeScript SDK.

### OpenCode

[OpenCode](https://opencode.ai) is an open-source, **provider-agnostic** coding agent with a terminal UI. It sits in the middle of the range: more built in than Pi, and very configurable.

- **Tools**: `bash`, `read`, `write`, `edit`, `apply_patch`, `grep`, `glob`, `webfetch`, `websearch`, `todowrite`, `question` (ask the user), `skill`, and an experimental `lsp` tool for code intelligence. MCP servers add more.
- **System prompt**: several built-in prompts, one chosen according to the model you connect (for example, separate prompts for Claude, GPT and Gemini models), plus an environment block (working directory, platform, date).
- **Agents**: two primary agents, **Build** (full access) and **Plan** (file edits and shell commands require approval), and subagents such as **General** and **Explore**. You define your own agents in Markdown or JSON, each with its own prompt, model, step limit and permissions.
- **Context files**: `AGENTS.md`, falling back to `CLAUDE.md`, plus extra instruction files or URLs listed in `opencode.json`.
- **Permissions**: every tool can be set to `allow`, `ask` or `deny`, globally or per agent.

### Hermes Agent

[Hermes Agent](https://github.com/NousResearch/hermes-agent), from Nous Research, is a **general-purpose, always-on** agent rather than a coding tool. Its distinguishing feature is a learning loop: it improves itself between sessions.

- **Tools**: a large registry organized into *toolsets* you enable per platform, including terminal, file, web, browser, vision, memory, session search, scheduled jobs (cron), delegation to subagents and code execution.
- **System prompt**: built around a `SOUL.md` identity file, which comes first in the system prompt and can be swapped for different personalities. It also loads project context files such as `AGENTS.md`, `CLAUDE.md` and `.hermes.md`.
- **Memory**: bounded, curated memory in `MEMORY.md` (what the agent has learned) and `USER.md` (the user's preferences and environment), plus full-text search over past sessions.
- **Self-written skills**: after a complex task, the agent can write a new skill with its `skill_manage` tool, or patch an existing one, so the procedure is reusable next time. It can also install skills from public registries.
- **Interfaces**: a terminal UI, and gateways to messaging apps such as Telegram, Discord, Slack and WhatsApp. It can run commands locally, in Docker, over SSH or in remote sandboxes.

### Comparison

| | Pi | OpenCode | Hermes Agent |
| :--- | :--- | :--- | :--- |
| **Philosophy** | Minimal core, extend it yourself | Configurable coding agent | Self-improving personal agent |
| **Built-in tools** | 4 (`read`, `write`, `edit`, `bash`) | About a dozen coding tools | Large registry of toolsets |
| **System prompt** | Minimal, replaceable | Selected per model family | Built around `SOUL.md` |
| **Context files** | `AGENTS.md` | `AGENTS.md`, `CLAUDE.md` | `AGENTS.md`, `CLAUDE.md`, `.hermes.md`, `SOUL.md` |
| **Skills** | Agent Skills | Agent Skills, loaded with the `skill` tool | Agent Skills, and it writes its own |
| **Subagents** | No (use `tmux` or extensions) | Yes | Yes |
| **MCP** | No (by design) | Yes | Yes |
| **Permissions** | None built in: use containers | `allow` / `ask` / `deny` per tool | Isolated backends (Docker, SSH, remote sandboxes) |

All three share one thing: they load skills in the same open format. That's what makes skills portable.

## Agent skills

A **skill** packages know-how, such as instructions, scripts and reference material, so an agent can load it *only when it needs it*. Anthropic introduced the format, and it is now an open standard, the [Agent Skills specification](https://agentskills.io/specification), supported by Pi, OpenCode, Hermes and many other agents.

### Anatomy of a skill

A skill is a folder that contains at least a `SKILL.md` file:

```text
stock-report/
├── SKILL.md          # required: metadata + instructions
├── scripts/          # optional: code the agent can run
├── references/       # optional: extra documentation, loaded on demand
└── assets/           # optional: templates, images, data files
```

`SKILL.md` starts with YAML front matter, followed by free-form Markdown instructions:

```markdown
---
name: stock-report
description: Writes a short daily report on a stock's low and high prices. Use when the user asks for a stock summary, a price report or a comparison between two dates.
---

# Stock report

1. Call `get_stock_price` for each date the user mentions (dates in YYYY-MM-DD).
2. Present the low and high prices in a table, in US dollars.
3. If a price is missing, say so; never estimate it.

For the report layout, see [references/TEMPLATE.md](references/TEMPLATE.md).
```

The specification's main rules:

| Field | Required | Rules |
| :--- | :--- | :--- |
| `name` | Yes | 1 to 64 characters: lowercase letters, digits and single hyphens. Must match the folder name. |
| `description` | Yes | Up to 1,024 characters. Says **what** the skill does and **when** to use it: this is all the agent sees before it decides to load the skill. |
| `license`, `compatibility`, `metadata` | No | License, environment requirements, and free key-value pairs |
| `allowed-tools` | No | Experimental: tools the skill may use without asking |

### Progressive disclosure

Skills are designed to cost almost nothing until they're used. Harnesses load them in three stages:

| Stage | What is loaded | When | Cost |
| :--- | :--- | :--- | :--- |
| 1. Metadata | `name` and `description` of every skill | At startup | About 100 tokens per skill |
| 2. Instructions | The full `SKILL.md` body | When the agent decides the skill is relevant | Under 5,000 tokens recommended |
| 3. Resources | Files in `scripts/`, `references/`, `assets/` | Only if the instructions call for them | As needed |

That's [2.6 Context Engineering](context-engineering.md) applied to know-how: an agent can have hundreds of skills installed while its context window holds only a line for each.

### Skills, tools and MCP

| | What it gives the agent | Where it lives | Context cost |
| :--- | :--- | :--- | :--- |
| **Tool** | An action it can perform | Code registered with the harness | Its schema, on every call |
| **MCP server** | A set of tools served by another process | A separate server | All its tool schemas, on every call |
| **Skill** | Know-how: *how* to do a task, often by combining tools or running bundled scripts | A folder of Markdown and scripts | One line until it's used |

Skills don't replace tools: a skill often tells the agent *which* tools to use and in what order. A skill is also plain text, so writing one takes no programming, and the same skill works in any harness that supports the standard.

/// warning | Skills are code you choose to trust
A skill can contain scripts that the agent will run with the harness's permissions. Only install skills from sources you trust, and read their scripts first, as you would any dependency.
///

## Building your own harness

You don't have to adopt a preset harness to use these ideas. LangChain's `create_agent` is a harness you configure in Python: it builds the same loop you wrote by hand in 2.1, and middleware adds the policies around it. ([Deep Agents](https://docs.langchain.com/oss/python/deepagents/overview), built on top of it, adds filesystem tools, subagents, skills, `AGENTS.md` memory and automatic summarization.)

```python
from langchain.agents import create_agent
from langchain.agents.middleware import (
    ModelCallLimitMiddleware,
    SummarizationMiddleware,
    ToolCallLimitMiddleware,
)

agent = create_agent(
    model=llm,
    tools=[get_stock_price, get_current_weather],
    system_prompt="You are a helpful assistant. Use the tools to answer; never guess prices or weather.",
    middleware=[
        ModelCallLimitMiddleware(run_limit=5),                         # at most 5 model calls per run
        ToolCallLimitMiddleware(tool_name="get_stock_price", run_limit=3),
        SummarizationMiddleware(model=llm, trigger=("tokens", 4000)),  # compress long histories
    ],
)

result = agent.invoke(
    {"messages": [{"role": "user", "content": "What is the weather in Miami?"}]},
    config={"recursion_limit": 10},  # hard cap on graph steps
)
```

Each harness building block has a counterpart here: `system_prompt` is the built-in prompt, `tools` the built-in tools, and middleware covers step limits, context management (`SummarizationMiddleware`, `ContextEditingMiddleware`), human approval (`HumanInTheLoopMiddleware`) and retries.

/// info | System prompts and tool calling
With some models, a custom system prompt replaces the model's default tool-calling instructions, and tool calling gets worse. When you change the `system_prompt`, rerun your [functional tests](../part-04-testing-agents/functional-testing.md) to confirm the agent still calls the right tools.
///

/// warning | Check your LangChain version
Middleware class names and parameters have changed across LangChain 1.x releases. The example above matches LangChain 1.4; check the [LangChain middleware docs](https://docs.langchain.com/oss/python/langchain/middleware) for the version you have installed.
///

## Build, configure or adopt?

| Approach | Choose it when | Examples |
| :--- | :--- | :--- |
| **Build the loop** | The control flow *is* the product: explicit planning, routing, custom state | LangGraph, as in labs 2.1 to 2.5 |
| **Configure a harness in code** | You want a standard tool-calling agent inside your own application | `create_agent`, Deep Agents |
| **Adopt a preset harness** | The agent's job fits an existing harness (coding, research, a personal assistant): you add skills, tools and instructions | Pi, OpenCode, Hermes Agent |

Whichever you choose, the rest of this workshop still applies: you [observe](../part-03-observing-agents/README.md) the agent through traces, [test](../part-04-testing-agents/README.md) its trajectories and answers, and [package](../part-05-packaging-agents/README.md) it for other applications to call.

## References

- [Agent Skills specification](https://agentskills.io/specification)
- [Pi](https://pi.dev) and its design write-up, [What I learned building an opinionated and minimal coding agent](https://mariozechner.at/posts/2025-11-30-pi-coding-agent/)
- [OpenCode documentation](https://opencode.ai/docs/): [tools](https://opencode.ai/docs/tools/), [agents](https://opencode.ai/docs/agents/), [skills](https://opencode.ai/docs/skills/)
- [Hermes Agent documentation](https://hermes-agent.nousresearch.com/docs/): [tools](https://hermes-agent.nousresearch.com/docs/user-guide/features/tools), [skills](https://hermes-agent.nousresearch.com/docs/user-guide/features/skills/)
- [LangChain Deep Agents](https://docs.langchain.com/oss/python/deepagents/overview)
