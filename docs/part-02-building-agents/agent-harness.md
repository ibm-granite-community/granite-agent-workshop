# Agent Harnesses and Skills: A Condensed Guide

## 1. The Core Agent Loop

At the center of every autonomous agent is a simple iterative loop:

```python
messages = [system_prompt, user_query]
while True:
    response = model.invoke(messages, tools=tools)  # 1. Model decides
    messages.append(response)
    if not response.tool_calls:                     # 2. Done (no more actions)
        break
    for call in response.tool_calls:                # 3. System executes action
        result = run_tool(call.name, call.args)
        messages.append(tool_message(result, call.id))
```

While the basic loop is simple to implement, a production-ready assistant requires significantly more scaffolding: context pruning, token management, file diffing, command execution, error recovery, and permission boundaries.

---

## 2. Agent Dev Frameworks vs. Out-of-the-Box Agent Harnesses

The ecosystem divides into two main approaches:

| Dimension | Agent Development Frameworks | Out-of-the-Box Agent Harnesses |
| :--- | :--- | :--- |
| **Examples** | LangGraph, AutoGen, CrewAI, LlamaIndex | **Claude Code**, **OpenAI Codex CLI**, **OpenCode**, **Pi**, **Hermes**, **IBM Bob** |
| **What they provide** | Primitives, graph nodes, state schemas, and orchestration abstractions. | Complete ready-to-run runtime: built-in system prompts, toolsets (shell, file read/write/edit, grep), context management, terminal UI, and sandboxing. |
| **Analogy** | **Raw lumber & hardware store**: you cut the wood, assemble the joints, and design custom mechanics from scratch. | **Flat-pack ready furniture**: pre-engineered, assembled in standard configurations, ready for immediate use. |
| **When to use** | When custom branching logic, domain-specific state graphs, or proprietary multi-agent orchestrations *are* the core product. | When you want a developer-ready coding agent immediately to solve tasks, test models (e.g. Granite 5), or automate workflows. |

> **Key Takeaway:** The harness is the runtime scaffolding; the underlying LLM is the reasoning engine. Real-world performance always evaluates the **(harness, model)** pair.

---

## 3. Key Components of an Agent Harness

An out-of-the-box harness typically bundles:

- **Built-in System Prompt**: Tuned instructions defining tool usage patterns, safety guardrails, and output style.
- **Native Tools**: Essential primitives such as file readers/editors, bash executors, glob/grep search, and user prompts.
- **Context Engineering**: Token pruning, history compaction, and memory files to keep long multi-turn sessions focused within context limits.
- **Project Context Files**: Standard instruction files (e.g., `AGENTS.md`, `CLAUDE.md`) automatically injected into every run.
- **Permissions & Sandboxing**: Rules governing when tools execute automatically (`allow`), require human approval (`ask`), or are blocked (`deny`).
- **Extensibility**: Integration hooks, MCP (Model Context Protocol) servers, and custom skills.

---

## 4. Skills: Workflow Prompts as an Addition to Tools

While **tools** define *what actions* an agent can execute, **skills** define *how* an agent should solve specific multi-step tasks.

### What is a Skill?

A skill is an open, standardized specification (originating from Anthropic's Agent Skills standard) where reusable operational know-how is packaged as **predefined workflow prompts**:

```text
my-workflow-skill/
├── SKILL.md          # Required: YAML metadata + step-by-step workflow instructions
├── scripts/          # Optional: Helper scripts the agent can run
├── references/       # Optional: Docs and schemas loaded on demand
└── assets/           # Optional: Templates and example fixtures
```

### `SKILL.md` Anatomy

```markdown
---
name: pr-review-workflow
description: Reviews pull requests against security and style guidelines. Use when asked to review a PR or audit a diff.
---

# Pull Request Review Workflow
1. Inspect the diff using git or file tools.
2. Validate changed dependencies against security policies.
3. Check test coverage for modified logic.
4. Output findings formatted per references/TEMPLATE.md.
```

### Why Skills are Essential: Tools vs. Skills

| Aspect | Tools | Skills |
| :--- | :--- | :--- |
| **Nature** | Executable functions / APIs (e.g., `bash`, `read_file`, MCP tools). | Predefined workflow prompts and structured guidance written in Markdown. |
| **Role** | Gives the agent the capability to interact with the environment. | Directs the agent on *which* tools to use, in what order, and according to what policy. |
| **Context Overhead** | Heavy: Full JSON schema definitions are injected on every turn. | **Lightweight (Progressive Disclosure)**: Only the `name` and `description` (~100 tokens) are loaded initially. The full prompt instructions are loaded only when relevant to the user's request. |

---

## 5. Popular Open-Source Harnesses Compared

| Feature / Harness | **Pi** | **OpenCode** | **Hermes Agent** |
| :--- | :--- | :--- | :--- |
| **Core Philosophy** | Minimalist ("primitives, not features") | Highly configurable coding agent CLI | General-purpose, self-improving assistant |
| **Built-in Tools** | 4 core tools (`read`, `write`, `edit`, `bash`) | ~12 coding tools (file diffs, grep, glob, LSP, web) | Extensive toolsets (terminal, web, memory, cron, messaging) |
| **System Prompt** | Ultra-lean (<1,000 tokens), replaceable | Tailored prompt variants per model family | Persona-driven based on `SOUL.md` |
| **Subagents & Modes** | None built in (relies on `tmux` & skills) | Yes (Build, Plan, Explore subagents) | Yes (Delegation & specialized subagents) |
| **Memory & Learning** | Session-only (`AGENTS.md`) | Session-only (`AGENTS.md`, `CLAUDE.md`) | Persistent memory (`MEMORY.md`, `USER.md`) & self-written skills |
| **Extensibility** | TypeScript extensions, Skills | MCP servers, Skills, Custom Agent configs | MCP servers, Skills (`skill_manage`), Messaging gateways |
| **Permissions** | Container/OS-level isolation | Granular `allow` / `ask` / `deny` per tool | Sandboxing (Docker, SSH, remote sandboxes) |

---

## 6. Summary

- **Development Frameworks** (e.g. LangGraph) allow you to build custom agent loops and bespoke state architectures from scratch.
- **Out-of-the-Box Harnesses** (e.g. Claude Code, Codex, OpenCode, Pi, Hermes, Bob) provide complete, turnkey runtime environments.
- **Tools vs. Skills**: Tools provide raw executable actions (APIs, bash, MCP); Skills provide reusable, predefined workflow prompts that guide the agent step-by-step through complex tasks with minimal token overhead.
