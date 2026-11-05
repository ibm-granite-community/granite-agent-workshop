---
title: 5. Packaging Agents
description: Package an agent's tools and the agent itself so other applications can call them
logo: images/ibm-blue-background.png
---

# Packaging Agents

An agent that only runs inside a notebook is hard for anything else to use. This part packages the travel-planning agent behind two open protocols, one for each boundary it crosses:

| Lab | Protocol | What crosses the boundary | Use it when |
| :--- | :--- | :--- | :--- |
| [5.1](mcp.md){ .lab-run } | **Model Context Protocol (MCP)** | Tools: the agent calls a server's functions | Several agents share a tool, or the tool is owned by another team |
| [5.2](a2a.md){ .lab-demo } | **Agent2Agent (A2A)** | Tasks: an agent delegates work to another agent | Agents are built, deployed or owned separately and must collaborate |

<span class="lab-legend lab-legend-run">Run during the workshop</span> <span class="lab-legend lab-legend-demo">Demoed by the instructor</span>

<style>
  .md-typeset tr:has(a.lab-run) > td { background-color: rgba(36, 161, 72, 0.15); }
  .md-typeset tr:has(a.lab-demo) > td { background-color: rgba(15, 98, 254, 0.12); }
  .md-typeset .lab-legend { display: inline-block; padding: 0.1rem 0.6rem; margin-right: 0.5rem; border-radius: 0.2rem; font-size: 0.75rem; }
  .md-typeset .lab-legend-run { background-color: rgba(36, 161, 72, 0.15); }
  .md-typeset .lab-legend-demo { background-color: rgba(15, 98, 254, 0.12); }
</style>

Both labs use the same scenario, a trip to Atlanta on 2026-10-25, so you can compare calling a tool with delegating to an agent.

For more background on deploying AI agents to production, see [CI/CD for agentic development](https://github.com/ibm-granite-community/granite-agent-cookbook/blob/main/cicd_agent-dev-blog.md) in the Granite Agent Cookbook.
