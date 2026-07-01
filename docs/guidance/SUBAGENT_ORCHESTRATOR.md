# PLANNING.md — Agent Strategies

> **Orchestrators or Team leads:** read this before decomposing any non-trivial task. Choose the strategy that matches the work, then follow its rules.

**A clear upfront decomposition determines whether a multi-agent run succeeds or wastes tokens.** Keep agent prompts concise and clear — instruction-following degrades as context grows.

---

## Architectural Constraint — Agent Nesting

> **Sub-agents cannot spawn sub-agents.** This is a hard platform limit, not a permission setting. Only the main conversation thread can spawn agents. Any agent that tries to spawn another agent will silently fall back to doing the work inline.

**What this means in practice:**
- All agent spawning must happen from the main orchestrator (Claude in the main conversation).
- "Team leads" are a coordination role, not a spawning hierarchy. A Team Lead agent reads specialist outputs and integrates them — it does not spawn the specialists.
- If you design a task that requires nested spawning, restructure it: flatten the hierarchy, spawn all agents from the main conversation, and use shared files as the coordination surface.
- The three Team patterns below (Flat Parallel, Dedicated Team Lead, Relay Race) all respect this constraint while providing genuinely different collaboration models.

---

## Quick — Single Agent, Focused Task

**Use when:** the change is small and bounded; one agent can hold the full context.

**How to kick off:** state the goal in one sentence, point to the single file or function. Do not spawn sub-agents — overhead exceeds benefit for small tasks.

**Signal it's too big:** you're listing more than ~3 files, or the change spans both `app.py` and `index.html`.

---

## Team — Orchestrator + Parallel Specialists

**Use when:** the task has multiple **independent** subtasks that can run simultaneously. Based on Anthropic's Opus-orchestrator + Sonnet-workers research pattern and Karpathy's "many small, tool-enabled agents for specific workflows."

**Rules:**
- Each agent owns **one file or one bounded concern** — no cross-agent file overlap.
- Use `temp_working_plan.md` (or TodoWrite) as a shared coordination surface; agents check off work without colliding.
- The main orchestrator synthesizes results after all agents report; agents do not call each other.
- **All agents are spawned directly from the main conversation** — not by other agents (see Architectural Constraint above).

**Signal it's too big for Team:** subtasks have ordering dependencies (A must finish before B starts) — use Divide & Conquer instead.

---

### Personalities

Assign one or more personalities to team members as appropriate:

`ISTJ`, `INTP`, `ENTP`, `ENTJ`, `INTJ`, `ISFP`, `Detail Oriented`, `Creative`, `Red Teamer`, `Intelligence Analyst`, `Recursive Researcher`, `Librarian`, `PhD`

**Prompt phrasing example:**
> "You are an analytical, systems-thinking INTP specialist. Your focus is [X]. Write your findings to `temp_research/intp_findings.md` when done."

> "You are an ENTJ Team Lead. Read the specialist files listed below, integrate their findings, and produce a final synthesis report."

---

### Team Pattern A — Flat Parallel *(fast, simple)*

All specialists run in parallel. Main conversation synthesizes. Best for code tasks, file-parallel work, and independent research tracks where the main conversation can synthesize without a dedicated agent.

```
Main spawns in parallel:
  Agent 1 [personality] — owns concern A
  Agent 2 [personality] — owns concern B
  Agent 3 [personality] — owns concern C
Main reads all three reports and synthesizes.
```

---

### Team Pattern B — Specialists + Dedicated Team Lead *(best simulation of a real team)*

Specialists run in parallel and each writes findings to their own file. Once all complete, a dedicated **Team Lead agent** is spawned — its only job is to read all specialist files and produce the integrated output. The main conversation only handles timing, not content.

This is the closest approximation to a real team: the lead genuinely reads everyone's work and does intellectual integration, rather than the main conversation passing results through.

```
Step 1 — Main spawns specialists in parallel:
  Agent 1 [INTP Librarian]            → writes findings to temp_research/librarian.md
  Agent 2 [Red Teamer]                → writes findings to temp_research/red_teamer.md
  Agent 3 [Detail Oriented Analyst]   → writes findings to temp_research/analyst.md

Step 2 — After all complete, main spawns:
  Team Lead [ENTJ]  — reads all three files, integrates, produces final report.
  Prompt: "Read temp_research/librarian.md, temp_research/red_teamer.md, and
           temp_research/analyst.md. You are the team lead. Synthesize their
           findings into a single actionable report, noting where they agree,
           where they conflict, and what the combined picture reveals."
```

**Cost:** Moderate — N+1 agents total (N specialists + 1 team lead). Specialists are parallel so wall-clock time is only slightly longer than Pattern A.

---

### Team Pattern C — Relay Race *(deep compounding, sequential)* ⏱ Time-consuming

Agents run **sequentially**. Each agent reads all previous agents' output files before writing their own contribution — like passing a document around a table where each person reads everything before adding their section. Knowledge compounds with each pass.

Best for: research that benefits from each agent building on and reacting to the last, or analysis tasks where perspective 2 should be informed by what perspective 1 found.

```
Step 1 — Main spawns Agent 1 [Recursive Researcher]:
  "Research [topic]. Write findings to temp_research/pass_1.md."

Step 2 — After Agent 1 completes, main spawns Agent 2 [Red Teamer]:
  "Read temp_research/pass_1.md. Challenge and extend those findings.
   Write your additions and challenges to temp_research/pass_2.md."

Step 3 — After Agent 2 completes, main spawns Agent 3 [PhD]:
  "Read temp_research/pass_1.md and temp_research/pass_2.md.
   Build a synthesis that integrates both perspectives.
   Write to temp_research/pass_3.md."
```

**Cost:** ⏱ Sequential — wall-clock time is the sum of all agents, not the max. Use only when compounding is worth the wait. For 3 agents each taking 2 minutes, this takes 6 minutes vs. 2 minutes for Pattern A.

---

## Divide & Conquer — Sequential Phases with Parallel Leaves

**Only use when:** the user specifically requests this option, or the task is clearly multi-phase with ordering dependencies. If the task isn't complex and multi-phase, confirm with the user first. Modelled on Anthropic's Opus-orchestrator + Sonnet-workers research pattern and Ng's principle that iterative workflows produce significantly better outcomes than single-shot runs.

**How to kick off:**

```
Phase 1 — Research (sequential, one agent):
  Read all relevant files. Write a concise design note to temp_working_plan.md.
  No code yet.

Phase 2 — Build (parallel, main orchestrator spawns one agent per module):
  Each agent owns one independent module identified in Phase 1.

Phase 3 — Integration (sequential, one agent):
  Wire pieces together. Run a manual smoke test.

Phase 4 — Review (parallel, main orchestrator spawns review agents):
  Code review, security review, and docs update simultaneously.
```

**Rules:**
- Never skip Phase 1 — research before building prevents cascading rework.
- Keep phases and checkboxes explicit in `temp_working_plan.md`; agents check off as they go.
- **All parallel agents in Phase 2 and Phase 4 are spawned by the main orchestrator** — phase agents do not spawn sub-agents.
- Prefer Sonnet for leaf/worker agents (fast, cheap); use Opus for the orchestrator when reasoning depth matters.
- If any phase fails, stop and reassess — do not carry a broken Phase 2 into Phase 3.

---

## Critic — Generator + Evaluator Loop

**Use when:** correctness must be explicitly verified before output is accepted — security-sensitive code, tricky logic, anything a one-shot agent could silently get wrong.

Backed by Anthropic's GAN-style harness architecture. A Generator agent produces output; a separate Critic/Evaluator agent reviews it cold against explicit criteria and returns structured feedback; the loop repeats until the Critic passes it. Because the Critic has no memory of generating the output, it catches blind spots the Generator can't see in itself.

**Rules:**
- Define the acceptance criteria *before* the Generator runs — the Critic scores against these, not general vibes.
- Critic returns structured feedback: `PASS` or `FAIL + specific issues`. No vague "looks good."
- Cap iterations (e.g. 3 rounds) to prevent infinite loops; escalate to human if cap is hit.
- Prefer the same model tier for both agents so neither has an inherent reasoning advantage.
- Both Generator and Critic are spawned from the main conversation, sequentially.

**Signal it's overkill:** the task is low-stakes and reversible — use Reflect instead.

---

## Reflect — Single-Agent Self-Critique Loop

**Use when:** a one-shot response isn't enough but spawning a second agent is overkill. Medium-complexity tasks where you'd normally read your own output over once before committing.

The agent produces a draft, then explicitly critiques it against a checklist (correctness, edge cases, project conventions), then revises. No second agent. Cited by Andrew Ng as producing meaningfully better outcomes than single-shot for iterative polish work.

**How to kick off:** append to any agent prompt —
```
After producing your output, critique it against these criteria: [list].
Then revise and output the final version only.
```

**Rules:**
- The checklist must be explicit — "check for correctness" is too vague; "verify every data-* attribute referenced in JS exists in the HTML template" is not.
- One revision pass is usually enough; two is the max before the loop stops adding value.
- Do not use Reflect as a substitute for tests — it catches reasoning errors, not runtime failures.

**Signal to upgrade:** the task is security-sensitive or has a hard correctness bar — use Critic instead.

---

## Pipeline — Explicit States and Transitions

**Use when:** the workflow must be deterministic, auditable, and fault-tolerant. Production-quality changes where you need traceability, retry logic, or a human-in-the-loop gate.

Define the states upfront as a state machine in `temp_working_plan.md`. Each state has: input, action, output, and a transition condition. Agents execute one state at a time and write their result before moving on. A failed state retries or halts — it never silently advances.

**How to kick off:**
```
States:
  1. VALIDATE   — confirm inputs are present and well-formed → pass/fail
  2. PLAN       — write design note → temp_working_plan.md
  3. BUILD      — implement → code diff
  4. VERIFY     — run checks → pass/fail
  5. DONE       — summarise changes
Transition: each state must explicitly output its result before the next begins.
Retry limit: 2 per state. On third failure, HALT and report.
```

**Rules:**
- Write the state list before any agent runs — the pipeline is the plan.
- Each transition must be explicit; no agent should assume the previous state succeeded.
- Each state agent is spawned from the main conversation — state agents do not spawn sub-agents.
- Use for anything touching `app.py`'s file-write logic or any change that could corrupt catalogue data — reversibility is limited there.

**Signal it's overkill:** the task is straightforward and reversible — use Quick, Reflect, or Team.

---

## Swarm — Emergent Coordination Through Debate

**Use when:** the problem is open-ended or creative and benefits from multiple competing perspectives converging on a solution — no single "right" decomposition exists upfront.

All agents are spawned from the main conversation with different personas or constraints. They propose, challenge, and negotiate via their returned reports. A moderator (the main orchestrator, or a dedicated synthesis agent spawned last) converges on a result.

**Assign personas from:** `ISTJ`, `INTP`, `ENTP`, `ENTJ`, `INTJ`, `ISFP`, `Detail Oriented`, `Creative`, `Red Teamer` (see Team section for prompt phrasing).

**Rules:**
- Set a convergence rule upfront: time-boxed rounds (e.g. 3 exchanges) or explicit vote/consensus signal — otherwise debate loops indefinitely.
- Swarm is expensive; use it only when the value of diverse perspectives outweighs the token cost.
- One agent (or the main orchestrator) must own the final synthesis — do not let the swarm output be a merge of all proposals.
- Not suitable for tasks with a clear correct answer — use Critic or Pipeline for those.
- All swarm agents are spawned from the main conversation. True peer-to-peer agent debate is not possible; agents return reports to the orchestrator, not to each other.

**Signal it's overkill:** the task has a known solution space — use Team or Divide & Conquer instead.

---

## Round Table — Two-Pass Debate *(richest output, most expensive)* ⏱💰

**Use when:** the problem is complex enough that specialists genuinely benefit from seeing and responding to each other's work — not just parallel independent takes, but real back-and-forth. Best for high-stakes decisions, design debates, or research where blind spots in one perspective get caught by another.

Two full passes of all agents, plus a final integration agent. Like a real meeting: everyone presents first, then everyone responds to everyone else, then a lead writes the summary.

```
Pass 1 — All specialists in parallel (each writes to their own file):
  Agent 1 [INTP]        → temp_research/round1_intp.md
  Agent 2 [Red Teamer]  → temp_research/round1_redteam.md
  Agent 3 [PhD]         → temp_research/round1_phd.md

Pass 2 — Each specialist reads ALL other Pass 1 files, then responds (parallel):
  Agent 4 [INTP, pass 2]
    "Read round1_redteam.md and round1_phd.md. Where do they challenge
     your perspective? Where do they add something you missed? Write
     your refined position to temp_research/round2_intp.md."

  Agent 5 [Red Teamer, pass 2]
    "Read round1_intp.md and round1_phd.md. Respond and refine.
     Write to temp_research/round2_redteam.md."

  Agent 6 [PhD, pass 2]
    "Read round1_intp.md and round1_redteam.md. Respond and refine.
     Write to temp_research/round2_phd.md."

Integration — Team Lead reads all 6 files:
  Agent 7 [ENTJ Team Lead]
    "Read all six files in temp_research/. Synthesize the full debate —
     what converged, what remained contested, and what the combined
     picture recommends."
```

**Cost:** ⏱💰 Most expensive — 2N+1 agents total, two parallel rounds plus integration. For 3 specialists: 7 agents, two parallel batches plus one sequential integration. Only use when the debate between perspectives will genuinely produce a better outcome than independent parallel takes. If specialists' domains don't meaningfully overlap or conflict, use Pattern B (Dedicated Team Lead) instead.

**Rules:**
- Always write intermediate files to `temp_research/` — this is the shared coordination surface that makes the debate possible.
- Pass 2 agents must be instructed to *respond to* the other files, not just read them — otherwise Pass 2 degrades into Pattern A with extra steps.
- The integration agent should explicitly call out where perspectives converged vs. remained contested — that tension is the most valuable output.
- Clean up `temp_research/` after the final report is produced.

---

| Signal | Strategy | Cost |
|---|---|---|
| One file, clear outcome | Quick | Minimal |
| One agent, needs a revision pass | Reflect | Low |
| Multiple independent files, same feature | Team — Pattern A (Flat Parallel) | Low |
| Team needs a real lead to integrate specialist work | Team — Pattern B (Dedicated Team Lead) | Moderate |
| Research that compounds; each perspective builds on the last | Team — Pattern C (Relay Race) | ⏱ Moderate-slow |
| Correctness must be explicitly verified | Critic | Moderate |
| Multi-phase; some steps depend on prior ones | Divide & Conquer | Moderate |
| Deterministic, auditable, fault-tolerant flow | Pipeline | Moderate |
| Complex debate; specialists benefit from responding to each other | Round Table | ⏱💰 High |
| Open-ended; benefits from competing perspectives in one pass | Swarm | Moderate-high |
| Unclear scope | Write a one-paragraph goal statement first, then pick | — |

> **Golden rule (Karpathy):** prefer many small focused agents over one sprawling context. A well-scoped 3-agent Team beats a single bloated session every time.

> **Hard limit:** only the main conversation can spawn agents. Design all strategies accordingly — flatten any hierarchy that requires nested spawning.
