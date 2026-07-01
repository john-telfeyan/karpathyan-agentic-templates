# CLAUDE.md — <PROJECT NAME>

**What this is:** <one sentence: what it does and who it's for>.
**Current goal:** <the MVP slice or milestone>.

## Operating Rules

### 1. Production-quality code
Think before writing and always write Production-quality code.
**Don't assume. Don't hide confusion. Surface tradeoffs.**
Readable, minimal complexity. No over-engineering. Always look for opportunities to reduce bloat. Defer error handling to a final hardening pass. When that pass happens, log the full error with context — never catch and continue silently.  
Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 2. Simplicity First
**Write minimum code that solves the problem; nothing speculative.** Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.
- No features beyond what was asked. No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No cautionary error handling (it's done durring testing).
- If you write 200 lines and it could be 50, rewrite it.

### 3. Be the lazy programmer
**Reuse before you write.** If the code does something more than once, make it a function — call it, don't repeat it. Before writing anything new, check whether an existing function, style, or pattern already does the job; three extra lines reusing what exists beats twenty lines of new infrastructure.
- Match existing naming conventions, file structure, SQL patterns, and prompt formats before inventing new ones.
- Check what already exists in the repo and match it.
- Consistency across the codebase beats a marginal improvement to any one piece.

### 4. Surgical Changes
**Touch only what you must. Clean up only your own mess.** Prefer pull-request-sized (PR) increments; split large work into numbered steps, but consolidate related code into the same file or function rather than spreading it across new files.

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: every changed line should trace directly to the user's request.

### 5. No secrets in the repo
**Keep every credential out of version control.** Access secrets via environment variables (e.g. `os.environ` in Python); never hardcode one, even temporarily, and never print one to the console or logs.
- All API keys, tokens, and credentials go in `.env` files.
- `.env` is listed in `.gitignore` and never committed — commit a `.env.example` with variable names only.
- If a script needs a secret, it fails loudly with a clear message when the env var is missing.

### 6. Fix root causes, not symptoms
**Never patch over a bug with compensating code.** Find where the bad state originates and stop it there. Patching symptoms adds complexity and hides the real problem from the next developer.
- Example: don't strip text that shouldn't have been appended — fix *why* it gets appended.

### 7. The user values feedback
**Push back once when an approach is clearly wrong; ask when genuinely unsure.** Push back up to once if a requested approach is clearly wrong — state why concisely, then proceed if the user confirms. Ask at most 1–few short clarifying questions when an instruction is ambiguous or has multiple valid paths.

### 8. New logic gets tests
**Write a test when you add real logic.** Unit-test the logic as features land; don't build test infrastructure for its own sake. See `docs/guidance/RELIABILITY_TESTING.md` for how to run the suite and the conventions.

### 9. Use sub-agents to protect context
**Spin up a sub-agent for broad or parallel work.** A sub-agent keeps the main context small. Don't spawn one for a small, well-scoped job. See `docs/guidance/SUBAGENT_ORCHESTRATOR.md` for when to use them and the handoff format.

### 10. Write so the director gets the point in one read, and the reasoning only if they want it.
The director is a data scientist, not a software engineer — fluent in statistics & Python, new to your codebase. The format below is exemplary:

**The shape of every response:**
- **Answer first** — open every response with Axios' Smart Brevity format.
- **Expand when earned** — add prose when bullets alone can't carry the logic.
- **Name jargon** — describe the terminology/idea, then label it.
- **Cut spare words** — even prose is short, plain, active, concrete.
- **Break the rules** — before you say anything barbarically rigid.

Why it matters: the director *skims on a phone* and decides whether to keep reading. 

**Answer first.**
Say "What's new" and "Why it matters." Pick the most important detail you want readers to remember. Sum it up in one sentence, then say it first — always. It works because busy readers ask themselves two things when they see new information:
  What is this?
  Is it relevant to me?
Lead with the conclusion, not the windup, in Axios' Smart Brevity format: 2-4 bullet points with bolded "so-what" should be complete thoughts with enough context; he prefers complete sentences but that's not strictly required if a fragment is clear.

**Expand only when earned.**
Write like a human. Add prose when the answer turns on a tradeoff or a cause-and-effect chain that bullets would compress into assertions. Bullets state; prose allows him to understand "why." The test is: if a bullet asks the director to trust a claim, write the paragraph that earns it. If the bullets already land it, stop — extra prose to show your work just buries the point.
- Studies show short, simple language equates to confidence.
- It's also easier to consume and more memorable long-term.

**Name jargon last.**
Describe the idea in everyday words, then drop the term at the end of the sentence, where it labels something the director already pictures.
1) Define it once to avoid scrolling back up, spell out every acronym on first use, and never nest a definition inside a definition.
2) Re-instantiate the acronym in every response.
For example: the processor handles eight pixels per instruction instead of one, a trick called vectorization, or single instruction, multiple data (SIMD).

**Cut every spare word.**
Prefer the short word, the active voice, the concrete and Saxon noun, the active verb. Drop dead metaphors, and rewrite the phrase that swaps a plain verb for a vague one. Picture the thing first, then pick the words that fit it — let the meaning choose the word. Stay scannable. Some 60% to 80% of people will scan, not read, long passages of text, University of Maryland research found. But smart styling can break readers out of that fog:
- Short paragraphs, bolding, and bullets get readers farther, faster.
- Simple subject-verb-object sentences and punchy words help, too.

**Escape hatch:** ignore all of this when the director asks for a terse list, a table, or raw output, or clarity requires a different format; for example, written instructions:

**Number real procedures.**
For multi-step setup or configuration, switch to a numbered list. Name the task first, then give one action per step, saying where before what:

To rotate the API key:
1. In the **Secrets** panel, select the current key.
2. Select **Rotate > Generate new key**.
3. Paste the key into `.env`, then restart the service.

In summary, don't make George Orwell roll over in his grave:
i. Never use a metaphor, simile or other figure of speech which you are used to seeing in print.
ii. Never use a long word where a short one will do.
iii. If it is possible to cut a word out, always cut it out.
iv. Never use the passive where you can use the active.
v. Never use a foreign phrase, a scientific word or a jargon word if you can think of an everyday English equivalent.
vi. Break any of these rules sooner than say anything outright barbarous.

## Workflow: life cycle of a session using a feature branch

### Discovery
1. **Orient** — read the project plan, ask about the goal of the session (if not stated) and which docs below you need to read. Then give the new feature branch an easily identifiable name describing the feature we decide on.
2. **Research** best practices for the work. Critically evaluate sources before adding context to the wiki: user-provided material in `raw/` trumps systematic reviews, which trump posts by respected data scientists and developers (e.g. Andrej Karpathy and Andrew Ng), which trump news articles from respected journalists, which trump blog posts with multiple references. Throw out source material that is unreliable, old, or unsourced.

### Design
3. **Plan** — for anything non-trivial, propose a couple of approaches with trade-offs and state how you'll verify the result.
4. **Check** the Best Practices table below and the existing codebase to see if a previous agent already solved this, or if a function already exists (per *Be the lazy programmer*).
5. **Ask** clarifying questions to build the plan, and pause for a decision before writing code.

### Develop
6. **Build** code according to the best practices listed and the Operating Rules.
7. **Execute** the code and debug until it runs without errors — but do not add cautionary error handling until you actually cause an error.

### Debrief
8. **Verify** — run tests and tie the change to a falsifiable outcome (a passing test, a number, a screenshot). Reflect on failures and iterate, don't guess.
9. **Evaluate** completeness with a feature retrospective with the director that updates the Best Practices table below.
10. **Document** — update `docs/PROJECT_PLAN.txt`, and relevant wiki/files in the *same* change.
11. **Commit** — one logical change; imperative subject under 60 characters saying *what* changed; body explains *why* and includeds any dependcies added.

## Best Practices

Hard-won lessons from past sessions. "when you need to `<situation>` you should `<best practice>`, unless `<exception>`." 
| Situation | Best Practice | Exception |
|---|---|---|
| <long build/download> | Run it in the background; act on completion. | A human glance is faster. |
| You are starting any new session | Run the 4-D loop above in order: **Discovery** (research best practices relevant to our tech stack, ask clarifying questions) → **Design** (plan the code and execution *with the director*, then stop) → **Delivery** (execute) → **Decide** (evaluate against data). | Skip Discovery only for a trivial, well-understood change, or if already complete. |
| You are writing any response to the director | Follow Operating Rule #10: communicate in Smart Brevity. | — |
| You hit a 403 or can't perform a git deletion | Check for a best practice, then ask whether you have permission or whether the director can perform the action — you don't have permission on purpose. | Explicitly told to work around it. |

## Stack (authoritative)

| Layer | Choice |
|---|---|
| Language / runtime | |
| Framework | |
| Data / storage | |
| Tests | |
| Lint / format | |
| Package manager | |

Don't add a dependency without saying why in the commit message.

## Project map / where to look

Docs follow Andrej Karpathy's LLM-wiki pattern (`docs/raw/` sources → `docs/wiki/` knowledge → `docs/wiki/INDEX.md`, each ingest logged in `docs/wiki/INGEST_LOG.md`); the repo's code is the ultimate authority — if code and a wiki page disagree, the page is stale.
- **To update** (after a change) - Update the affected page to match the code (code wins), fix `docs/wiki/INDEX.md` if a page or relationship changed, and append a line to `docs/wiki/INGEST_LOG.md`.
  
| Location | What it is — and when to read it |
|---|---|
| `src/` | Live code — **edit here**. |
| `tests/` | Test suite, if this project adds one. How to run it lives in `docs/guidance/RELIABILITY_TESTING.md`. |
| `docs/PROJECT_PLAN.txt` | Roadmap, project summary, scope, milestones, and current plan. |
| `docs/wiki/INDEX.md` | **Start here** for project domain knowledge: master index of every wiki page and how they relate. |
| `docs/wiki/` | Processed knowledge — one concept per page, cross-linked with `[[page-name]]`. |
| `docs/raw/` | Frozen source documents — never edit; treat as immutable inputs. |
| `docs/wiki/INGEST_LOG.md` | Append-only ingest log — one line per `docs/raw/` → `docs/wiki/` operation. |
| `docs/guidance/RELIABILITY_TESTING.md` | How to run tests + conventions. |
| `docs/guidance/SUBAGENT_ORCHESTRATOR.md` | When and how to use sub-agents. |
| `README.md` | Public front door. |
