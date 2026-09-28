---
title: 2.6 Context Engineering
description: Managing what goes into an agent's context window
logo: images/ibm-blue-background.png
---

# Context Engineering

/// note | Notebook coming soon
The hands-on notebook for this lab is still in progress. This page covers the concepts; the full write-up is [Context Management for AI Agents](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/recipes/ContextManagement/context_management.md) in the Granite Agent Cookbook.
///

The context window is an agent's entire working memory. Unlike a person, an agent can't glance at a whiteboard or flip back through its notes. Everything it needs has to fit in a fixed token budget: the task description, tool schemas, conversation history, retrieved documents and earlier tool outputs. And everything in that window influences every output.

**Context engineering** is the practice of deciding what goes into that window, what stays out, and what gets moved somewhere else. With small models such as Granite 3B and 8B, it often matters more than the choice of architecture.

## Anatomy of an agent's context

An agent's context comes from three layers:

| Layer | What it holds | Lifetime | In LangGraph |
| :--- | :--- | :--- | :--- |
| **Model context** | The tokens the model sees on this call: system prompt, conversation turns, tool definitions, tool results | One call, unless you carry it forward | A [checkpointer](https://docs.langchain.com/oss/python/langgraph/persistence) replays a thread's history on each call, keyed by `thread_id` |
| **Tool and store context** | State outside the model, injected on demand: graph state, a key-value store, a vector database | One run or thread | Graph state, [stores](https://docs.langchain.com/oss/python/langgraph/persistence#memory-store) |
| **Lifecycle context** | What persists across runs: user preferences, earlier task summaries, project rules | Across threads | A persistent store, or a static `AGENTS.md` file loaded into the system prompt |

Only the model context influences the next output directly. The other two layers are where you *keep* information until it's needed, so it doesn't sit in the window the whole time.

/// info | Progressive disclosure
Loading every tool definition at startup fills the window with instructions the current task doesn't need. *Skills* avoid this: the system prompt lists only skill names and one-line descriptions, and the agent reads a skill's full instructions only when it decides to use it. A static `AGENTS.md` is the opposite trade-off: it's loaded on every call, so it's a fixed cost that grows silently as the file grows.
///

## How context fails

Bigger windows don't solve the problem. Context degrades in four distinct ways, well before the hard token limit:

| Failure mode | What happens |
| :--- | :--- |
| **Poisoning** | A hallucination or wrong inference enters the context and is treated as fact on later turns. Because the model's outputs feed its own input, the error compounds. |
| **Distraction** | The context grows so large that the model over-focuses on its history. Gemini's Pokémon-playing agent stopped making new plans beyond about 100,000 tokens and started repeating earlier actions. |
| **Confusion** | Irrelevant content pulls the model off target. Even a single distractor, a passage on the right topic that doesn't answer the question, measurably reduces accuracy. |
| **Clash** | Contradictory information accumulates: a new tool result contradicts an old one still in the history, or two subagents make incompatible assumptions. |

Knowing which failure you have tells you which fix to reach for.

## Strategies

| Strategy | How it works | Fixes |
| :--- | :--- | :--- |
| **Rolling window (FIFO)** | Keep only the last N messages | Distraction |
| **Compaction** | Replace old history with an LLM-written summary | Distraction |
| **Dynamic tool selection** | Give the model only the tools relevant to this query | Confusion |
| **Pruning** | Cut the irrelevant parts out of tool outputs and documents before they enter the context | Confusion, Poisoning |
| **Structured note-taking** | The agent writes key findings to a scratchpad in its state and reads it back selectively | Distraction, Clash |
| **Filesystem as scratchpad** | The agent writes plans and results to files, and searches them instead of rereading everything | Distraction, Clash |
| **Semantic compression** | Keep only the minimal set of facts future reasoning needs, in a structured form | Distraction, Confusion |
| **RAG** | Retrieve only the relevant chunks from an external corpus at call time | Confusion |
| **Subagents (context quarantine)** | Give each subtask its own clean context window | Clash, Distraction |

### Rolling window

The simplest strategy: when the message list passes a limit, drop the oldest messages. It's trivial to build and gives a predictable token budget. The cost is that early information is gone for good, and the agent can't know what it lost, so it's a poor fit for tasks that need long-range coherence.

### Compaction

Instead of dropping old messages, ask a model to summarize the conversation so far and replace the raw history with the summary. The agent keeps a coherent narrative, but summaries lose information, and compaction adds latency and cost when it runs. Treat the summarization prompt as a component in its own right, with its own tests. LangChain ships this as `SummarizationMiddleware` (see [2.7 Agent Harnesses](agent-harnesses.md#middleware)).

### Dynamic tool selection

Every tool definition costs tokens and gives the model another option to confuse. Research on large tool registries ([RAG-MCP](https://arxiv.org/abs/2505.03275)) found that tool selection accuracy drops sharply beyond about 30 tools with overlapping descriptions. The fix is to retrieve only the relevant tools for each call, which is exactly the [2.4 ToolRAG Agent](toolrag.md) pattern.

### Pruning

Where compaction condenses *everything*, pruning is surgical: it removes only the irrelevant parts of a tool output or document and keeps the exact wording of the rest. Use compaction when all the history is relevant but verbose, and pruning when some of it is clearly irrelevant. It's usually a small LLM step after each tool call; LangChain's `ContextEditingMiddleware` clears old tool results automatically.

### Structured note-taking and the filesystem

Rather than letting tool outputs pile up in the message list, the agent writes what matters to a scratchpad (a dedicated key in the graph state) or to files, and reads back only what it needs. The message list stays short; nothing is lost. For long tasks, rewriting a `todo.md` plan at every step keeps the agent oriented: [Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus) calls this *recitation*.

/// warning | Filesystem access is a trust boundary
Scope file access to a dedicated working directory, never the whole filesystem. In multi-agent systems, isolate each agent's paths, and treat any file path that comes from a tool output as untrusted input.
///

### Semantic compression

A stronger form of compaction: instead of summarizing turns, extract the minimal set of facts that must survive, in a structured form such as JSON or a small knowledge graph. It can be tuned per domain, but it's the hardest strategy to evaluate, because the information loss can be subtle.

### RAG

Retrieve only the relevant chunks from an external corpus at call time instead of loading all knowledge up front. Chroma's context-rot research found that models given about 300 tokens of *focused* context vastly outperformed the same models given the full 113,000-token history, even though the full history contained everything needed.

### Subagents

Give independent subtasks their own context windows, so each subagent works from a clean, focused context and noise from one subtask can't leak into another. This is the idea behind [2.3 Route-and-Solve](route-and-solve.md). Keep subagents to *gathering* information, and leave the synthesis and final decisions to a single agent: tightly coupled subtasks split across agents produce contradictions.

## Choosing a strategy

Context management is use-case specific. These combinations are starting points:

| Scenario | Starting point |
| :--- | :--- |
| Short conversations (under 20 turns) | Rolling window |
| Long-running chatbot (100+ turns) | Compaction and note-taking |
| Tool-heavy research agent | Pruning, note-taking and a filesystem scratchpad |
| Large tool registry (30+ tools) | Dynamic tool selection |
| Knowledge-heavy Q&A | RAG and pruning |
| Parallel, breadth-first research | Subagents, for information gathering only |
| Agent that spans many sessions | Filesystem scratchpad and a persistent store |

## Key takeaways

- **Context isn't free.** Every token influences the output. Budget context like memory.
- **Know your failure mode.** Poisoning, distraction, confusion and clash need different fixes.
- **Separate gathering from deciding.** Subagents are good at the first; a single agent is better at the second.
- **Summarization loses information.** Offloading to a scratchpad or files is safer when details matter.
- **Test at your real context lengths.** Needle-in-a-haystack scores overstate how well models use long context; test with your own history lengths and your own distractors, using [4.1 Functional Testing](../part-04-testing-agents/functional-testing.md).
- **Structure your context.** Typed LangGraph state makes pruning, summarizing and selective retrieval much easier.

## References

- [Anthropic Engineering: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [LangChain Blog: Context Engineering for Agents](https://blog.langchain.com/context-engineering-for-agents/)
- [How Contexts Fail and How to Fix Them](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [Context Rot: How Increasing Input Tokens Impacts LLM Performance](https://www.trychroma.com/research/context-rot)
- [RAG-MCP: Mitigating Prompt Bloat in LLM Tool Selection](https://arxiv.org/abs/2505.03275)
- [Manus: Context Engineering for AI Agents](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)
