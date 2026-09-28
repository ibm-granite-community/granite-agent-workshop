---
title: About Granite
description: What IBM Granite is, and what the Granite 4.2 models this workshop runs on can do
logo: images/ibm-blue-background.png
---

# About Granite

## What is IBM Granite?

[IBM Granite](https://www.ibm.com/granite) is a family of open-source large language models developed by IBM for enterprise and research use. Granite spans more than chat models -- the family also includes vision, document-processing (Docling), speech, embedding, safety (Guardian), and time-series models -- but this workshop uses the Granite language models. They range from compact, edge-deployable sizes up to large-scale reasoning models, and every one of them is released under the [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0), so there are no usage restrictions for commercial or academic work.

## Granite 4.2

The workshop runs on the [Granite 4.2](https://huggingface.co/collections/ibm-granite/granite-42-language-models) generation, released by IBM in August 2026 in three sizes -- `3b`, `8b`, and `30b` parameters. All three are dense, all-attention transformer models: unlike Granite 4.0's hybrid Mamba-2/transformer Mixture-of-Experts design, Granite 4.1 and 4.2 use every parameter on every token, which keeps their behavior straightforward to reason about while you're building your first agent in [Lab 2.1](part-02-building-agents/function-calling.md).

Granite 4.2 is IBM's first generation to combine native reasoning with tool calling in one model. Each model can emit a step-by-step chain of thought inside `<think>...</think>` tags before its final answer, with three selectable modes:

- **Full thinking** (the default) -- the model reasons at length before answering, which helps most on math, coding, and multi-step logic.
- **Low-effort reasoning** -- a shorter chain of thought for simpler queries, trading some depth for latency.
- **Non-thinking** -- the model answers directly, with no visible reasoning step.

The 8B and 30B models also received additional agentic reinforcement learning for tool calling, code editing, terminal use, and web search -- the same skill this workshop's agents lean on when deciding which tool to invoke and when.

| Model | Parameters | Context | Typical role |
| :--- | :--- | :--- | :--- |
| [`granite-4.2-3b`](https://huggingface.co/ibm-granite/granite-4.2-3b) | 3B | 128K | Fast, local iteration (this workshop's Ollama default) |
| [`granite-4.2-8b`](https://huggingface.co/ibm-granite/granite-4.2-8b) | 8B | 128K | General-purpose enterprise agent work (this workshop's Replicate model) |
| [`granite-4.2-30b`](https://huggingface.co/ibm-granite/granite-4.2-30b) | 30B | 128K, extendable to 512K | Flagship reasoning, long-context agentic workflows |

All three natively support a 128K-token context window, and the 30B model has a documented long-context extension to 512K. All three also support tool calling and 12 languages (including English, German, Spanish, French, and Japanese).

## Which model this workshop uses

Every notebook defines the same `get_llm()` helper, which picks the model for you instead of hardcoding a model name:

- **Replicate (hosted):** `ibm-granite/granite-4.2-8b`, overridable with the `REPLICATE_MODEL` environment variable. Used whenever `REPLICATE_API_TOKEN` is set in the environment, and as the fallback when Ollama doesn't have the model.
- **Ollama (local):** `granite4.2:3b`, overridable with the `GRANITE_MODEL` environment variable. Used when no `REPLICATE_API_TOKEN` is set and the model is pulled locally.

The Replicate path also sets `chat_template_kwargs: {"enable_thinking": False}`, so this workshop runs Granite in non-thinking mode by default -- the labs are about the mechanics of an agent loop (tool calls, routing, retrieval), not about reading a model's chain of thought. If you want to see Granite's reasoning step for yourself, try flipping `enable_thinking` to `True` and re-running a notebook.

## Learn more

- [IBM Granite on Hugging Face](https://huggingface.co/ibm-granite) -- the full model portfolio (language, vision, speech, embeddings, and more)
- [Granite 4.2 language models collection](https://huggingface.co/collections/ibm-granite/granite-42-language-models) -- every 4.2 size and quantized variant
- [`granite-4.2-30b` model card](https://huggingface.co/ibm-granite/granite-4.2-30b) -- architecture, benchmarks, and intended use for the flagship model
- [Granite 4.2 documentation](https://www.ibm.com/granite/docs/models/granite4-2) -- IBM's own reference for the generation
