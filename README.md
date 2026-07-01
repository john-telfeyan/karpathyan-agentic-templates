# Karpathyan Agentic Templates

Fork this reusable project scaffold to start a new project with Claude Code or Codex in a tight, human-led loop.

This repository packages opinionated agent instructions, supporting docs, and local settings for projects using Andrej Karpathy's guidance for working with agents:

1. **Discovery:** orient to the repo, read relevant docs, and gather best practices.
2. **Design:** propose approaches, name tradeoffs, and define the verification signal.
3. **Delivery:** implement the smallest useful change and run the check.
4. **Debrief:** review evidence, document the lesson, and commit one logical change.

## Repository map

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Main shared agent instruction template. Fill in project name, goal, stack, workflow rules, and lessons learned. |
| `AGENTS.md` | Codex/OpenAI entry point; imports `CLAUDE.md`. |
| `.claude/settings.json` | Claude Code permission policy for common tools, with explicit denies for risky operations. |
| `.codex/settings.json` | Codex settings copy matching the Claude permission policy. |
| `docs/PROJECT_PLAN.txt` | Project summary, scope, milestones, and plan. |
| `docs/guidance/RELIABILITY_TESTING.md` | Test commands and conventions for the target project. |
| `docs/guidance/SUBAGENT_ORCHESTRATOR.md` | Sub-agent orchestration guidance and handoff patterns. |
| `docs/raw/` | Frozen source documents for the wiki. |
| `docs/wiki/INDEX.md` | Master index for processed project knowledge. |
| `docs/wiki/INGEST_LOG.md` | Append-only ingest log for source-to-wiki updates. |
| `LICENSE` | MIT license. |

## How to Use

1. Fork the template into a new GitHub repo, then clone it locally:

```bash
gh repo fork git@github.com:john-telfeyan/karpathyan-agentic-templates.git --clone --fork-name <new-project-name>
```

2. Initial prompt:

> Help me begin a new project <project name>: The goal of the project is _.
> - Begin by asking me questions to fill out the rest of the `## Project Summary` subsections in `docs/PROJECT_PLAN.txt`.
> - Then you recommend the project plan by breaking the work into milestones.
> - Then identify which documents and data we need to collect in `docs/raw/` so you can update the wiki.
> - Update the wiki, `CLAUDE.md`, and fill in all the `<placeholders>` in documentation.
> - Finally, stop and ask me to review the plan before writing project code.

## Sources Cited

The style is inspired by Andrej Karpathy's AI-coding workflow and Andrew Ng's agentic workflow patterns, with Claude Code-specific mechanics grounded in Anthropic's official documentation.

[1] Anthropic, "How Claude remembers your project," *Claude Code Docs*. Accessed: 2026-07-01. [Online]. Available: https://code.claude.com/docs/en/memory

[2] Anthropic, "Best practices for Claude Code," *Claude Code Docs*. Accessed: 2026-07-01. [Online]. Available: https://www.anthropic.com/engineering/claude-code-best-practices

[3] A. Karpathy, "AI-coding rhythm," *X*, 2025-04-24. Accessed: 2026-07-01. [Online]. Available: https://x.com/karpathy/status/1915581920022585597

[4] A. Karpathy, "Software Is Changing (Again)," *YouTube*, 2025. Accessed: 2026-07-01. [Online]. Available: https://www.youtube.com/watch?v=LCEmiRjPEtQ

[5] A. Ng, "What's next for AI agentic workflows," *Sequoia AI Ascent*, YouTube, 2024. Accessed: 2026-07-01. [Online]. Available: https://www.youtube.com/watch?v=sal78ACtGTc

[6] A. Ng, "Agentic Design Patterns Part 1: Four AI agent strategies that improve GPT-4 and GPT-3.5 performance," *The Batch*, DeepLearning.AI, 2024-03-20. Accessed: 2026-07-01. [Online]. Available: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/

[7] "YouTube video on agentic coding workflows," *YouTube*. Accessed: 2026-07-01. [Online]. Available: https://www.youtube.com/watch?v=h7QJL2_gEXA
