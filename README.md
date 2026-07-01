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
| `AGENTS.md` | Just says `@CLAUDE.md`. |
| `.claude/settings.json` | Claude Code permission policy for common tools, with explicit denies for risky operations. |
| `.codex/settings.json` | Codex settings copy matching the Claude permission policy. |
| `docs/PROJECT_PLAN.txt` | (placeholder shell) Project summary, scope, milestones, and plan. |
| `docs/guidance/RELIABILITY_TESTING.md` | (placeholder shell) Test commands and conventions for the target project. |
| `docs/guidance/SUBAGENT_ORCHESTRATOR.md` | Sub-agent orchestration guidance and handoff patterns. |
| `docs/raw/` | (placeholder shell)Frozen source documents for the wiki. |
| `docs/wiki/INDEX.md` | (placeholder shell) Master index for processed project knowledge. |
| `docs/wiki/INGEST_LOG.md` | (placeholder shell) Append-only ingest log for source-to-wiki updates. |
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

# Bonus: Prompt Inspiration

### Workflow specific
#### Feature Retrospective
```markdown
<TASK> 
Review and summarize (in the chat) every message from me and your response in this entire thread: 
1. In one sentence state the goal(s) 
2. assess if your approach to solve it worked in 1 try or if you had to trouble shoot 
3. deduce the best practice that the next agent could benefit from knowing from the start.  
</TASK> 

<TASK > 
We're going to convert this into a table that provides clear instructions for the next agents and update the best practices table in CLAUDE.md 
For each row, provide guidance for the next agent by thinking about "when you need to `<situation>` you should `<best practice>`, unless `<exception>`." 
and then fill out this table and add it to claude.md;  the columns are: 
Situation, Best Practice , exception (if needed) 
Pause here and propose the updates in the chat for discussion before modifying CLAUDE.md 
</TASK> 

<TASK 3> 
Finally, update the project plan write a 1 page session hand off summarizing the work completed here, challenges overcome, and next steps then provide a one sentence starter prompt pointing the next agent to the handoff and listing the documents it needs to read to get started. (at minimum the project plan ) 
</TASK>
```
### Prompt Catalogues
1. [Claude Command Suite](https://github.com/qdhenry/Claude-Command-Suite)

   **What it is:** The most useful Claude Code prompt library I found.
   **Why it matters:** It contains copyable slash-command prompts for code review, testing, debugging, security, documentation, deployment, refactoring, project setup, and session handoff.
   **Best use:** Mine individual `.md` command files and adapt the ones that match this repo's workflow.

2. [Fabric Patterns](https://github.com/danielmiessler/fabric/tree/main/patterns)

   **What it is:** A large crowdsourced library of reusable task prompts.
   **Why it matters:** The prompts are organized as repeatable patterns for analysis, summarization, extraction, writing, research, security, and decision support.
   **Best use:** Use it for non-code and code-adjacent prompts: summarize docs, extract insights, analyze claims, review decisions, and generate briefs.

3. [prompts.chat / Awesome ChatGPT Prompts](https://github.com/f/prompts.chat)

   **What it is:** A broad community library of copy/paste prompts.
   **Why it matters:** It has many role-based prompts that can be adapted for Claude Code when you want a specific persona or mode of response.
   **Best use:** Pull the useful structures, then rewrite them for coding tasks with files, tests, constraints, and verification steps.


4. [Prompt Engineering Guide](https://github.com/dair-ai/Prompt-Engineering-Guide)

   **What it is:** A prompt engineering reference with techniques, examples, papers, and agent patterns.
   **Why it matters:** It is less Claude Code-specific, but strong for understanding prompt shapes: few-shot examples, reasoning prompts, self-critique, RAG, agents, and evaluation.
   **Best use:** Use it to improve the quality of your own prompt templates rather than copying directly.

5. [Context Engineering Intro](https://github.com/coleam00/context-engineering-intro)

   **What it is:** A practical prompt workflow for turning feature ideas into implementation plans.
   **Why it matters:** It includes reusable prompt files for generating and executing PRPs, or Product Requirements Prompts.
   **Best use:** Adapt its `/generate-prp` and `/execute-prp` pattern for your Discovery -> Design -> Delivery -> Debrief loop.

6. [Tom's Guide: Thinking Prompts for Claude](https://www.tomsguide.com/ai/i-built-a-library-of-thinking-prompts-for-claude-these-are-the-ones-i-use-most)

   **What it is:** A small catalogue of reasoning prompts for Claude.
   **Why it matters:** The prompts elicit useful thinking modes: first principles, contrarian review, expert panel, simplification, idea improvement, structured thinking, and real-world testing.
   **Best use:** Turn these into lightweight review prompts for planning, architecture, and debrief steps.

7. [TechRadar: 5 Claude Prompts](https://www.techradar.com/ai-platforms-assistants/claude/i-use-these-5-clever-claude-prompts-every-day-they-make-me-feel-like-a-genius)

   **What it is:** A short set of creative Claude prompts.
   **Why it matters:** It shows how framing, roles, and constraints can change response quality.
   **Best use:** Good inspiration for explanation prompts, teaching prompts, and stakeholder-friendly summaries.

8. [PromptSource](https://github.com/bigscience-workshop/promptsource)

   **What it is:** A large academic prompt repository for natural-language tasks.
   **Why it matters:** It is not Claude Code-focused, but it is a serious catalogue of prompt templates and prompt variation.
   **Best use:** Use it as reference material if you want to study prompt structure at scale.
   
### Claude Resources
1. [Awesome Claude Code](https://github.com/hesreallyhim/awesome-claude-code)

   **What it is:** The best starting index for Claude Code resources.
   **Why it matters:** It points to skills, hooks, slash commands, agents, plugins, and workflow tools across the Claude Code ecosystem.
   **Best use:** Start here when you want to discover what exists before copying anything into this repo.
   
3. [Anthropic Prompting Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)

   **What it is:** Official Anthropic guidance with sample prompt patterns for Claude.
   **Why it matters:** It shows Claude-native prompting patterns: clear instructions, XML tags, examples, roles, output formatting, tool-use direction, and agentic workflows.
   **Best use:** Treat it as the style guide for prompts you keep in this repo.

4. [Claude Code Templates](https://github.com/davila7/claude-code-templates) / [aitmpl.com](https://aitmpl.com)

   **What it is:** A browse-and-install catalogue for Claude Code agents, commands, settings, hooks, MCPs, and templates.
   **Why it matters:** It is useful when you want installable components instead of hand-copying Markdown files.
   **Best use:** Browse for examples, then distill the parts worth keeping into `docs/guidance/` or `.claude/`.

5. [Contains Studio Agents](https://github.com/contains-studio/agents)

   **What it is:** A role-based library of Claude Code sub-agent prompts.
   **Why it matters:** The agents are organized by real work domains: engineering, design, marketing, product, operations, project management, and testing.
   **Best use:** Use it as inspiration for specialist agents and delegation patterns.

6. [SuperClaude Framework](https://github.com/SuperClaude-Org/SuperClaude_Framework)

   **What it is:** A full Claude Code configuration framework with commands, agents, modes, and workflows.
   **Why it matters:** It shows what a complete Claude Code operating system can look like.
   **Best use:** Study the structure, but be selective; it is heavier than this template needs by default.

7. [Caveman Claude](https://www.businessinsider.com/claude-caveman-speak-prompt-save-tokens-results-worse-2026-5)

   **What it is:** A cautionary prompt-compression experiment that made Claude use stripped-down “caveman” language to save tokens.
   **Why it matters:** It shows that shorter prompts can reduce quality when they remove reasoning context.
   **Best use:** Treat it as a warning: optimize prompts for clarity and verification, not just token count.

Official docs to pair with these:

- [Claude Code skills docs](https://code.claude.com/docs/en/slash-commands)
- [Claude Code sub-agents docs](https://code.claude.com/docs/en/sub-agents)
- [Anthropic prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)
