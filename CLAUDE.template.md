<!--
  CLAUDE.template.md — minimalist starter, distilled from Anthropic's official
  memory guidance + Andrej Karpathy and Andrew Ng's primary advice.

  HOW TO USE THIS TEMPLATE:
  - Copy to CLAUDE.md at your repo root, then fill every <...> placeholder.
  - Delete any section you don't need. Keep the whole file under ~200 lines
    (Anthropic: shorter files get followed more reliably).
  - Anything inside an HTML comment like this is STRIPPED before the file is
    loaded into context, so usage notes here cost zero tokens. Use comments for
    notes-to-humans; use plain text for instructions you want Claude to read.
  - Write concrete, verifiable rules ("run `make test`", not "test your code").
  - This file is guidance, not enforcement. For things that MUST happen at a
    fixed moment (e.g. before every commit), use a hook, not a rule here.
  - Bootstrap or refresh with `/init`; tune it like a prompt as you go.
-->

# CLAUDE.md — <PROJECT NAME>

<!-- Orientation. One paragraph. Put the most important rules in the first ~40
     lines — Claude pays most attention to the top of the file. -->
**What this is:** <one sentence: what it does and who it's for>. It is NOT
<explicit non-goal / scope boundary>. **Current goal:** <the MVP slice or milestone>.

## Stack (authoritative)

| Layer | Choice |
|---|---|
| Language / runtime | <e.g. Python 3.11> |
| Framework | <e.g. FastAPI / none> |
| Data / storage | <e.g. Postgres / flat files> |
| Tests | <e.g. pytest> |
| Lint / format | <e.g. ruff + black> |
| Package manager | <e.g. uv / pnpm> |

Don't add a dependency without saying why in the commit message.

## Commands

```
<make setup>     # install deps, validate env
<make dev>       # run locally
<make test>      # run the test suite
<make lint>      # lint + typecheck
<make ci>        # everything CI runs — must pass before a change is "done"
```

## Operating Rules

1. **Production-quality, minimal.** Simplest thing that works. No over-engineering,
   no speculative abstraction. If 200 lines could be 50, rewrite it.
2. **Smallest verifiable step.** Make one concrete change at a time. If a change
   can't be verified, it's too big — split it.
3. **No secrets in the repo.** Keys/tokens live in `.env` (git-ignored); commit only
   `.env.example` with names. Read via env vars; fail loudly if one is missing.
4. **Reuse before you write.** If it happens twice, it's a function. Three lines
   reusing what exists beats twenty lines of new infrastructure.
5. **Fix root causes, not symptoms.** Stop bad state where it originates; never patch
   over it with compensating code.
6. **Surgical edits.** Touch only what the task needs; match the surrounding style.
   Don't refactor what isn't broken. Mention unrelated dead code, don't delete it.
7. **Surface, don't hide.** State assumptions. If a request is ambiguous, ask 1–3
   short questions. Push back once if an approach is clearly wrong, then proceed if confirmed.
8. **Commits:** imperative subject < 60 chars; blank line; body explains *why*, not
   *what*. One logical change per commit.
9. **NEVER push to `main`/`master`. NEVER open a PR unless asked.** Work on a feature branch.

## Workflow (the loop)

<!-- Karpathy's AI-coding rhythm + Ng's iterative/eval-driven agentic workflow,
     compressed. The order matters: agree on approach BEFORE writing code. -->

1. **Orient** — read this file and the relevant doc below. Pull the needed context in first.
2. **Plan** — for anything non-trivial, propose a couple of approaches with trade-offs and
   state how you'll verify the result. Pause for a decision before writing code.
3. **Build** — the smallest reversible step. Make it work → right → fast, in that order.
4. **Verify** — run tests/lint and tie the change to a falsifiable outcome (a passing test,
   a number, a screenshot). Reflect on failures and iterate, don't guess.
5. **Document** — update the affected doc in the *same* change.
6. **Commit** — a clear message describing the *why*.

## Project map / where to look

```
<src/>        <live code — edit here>
<tests/>      <test suite>
<docs/>       <design notes / knowledge>
README.md     <public front door>
```

| When you're working on… | Read first |
|---|---|
| <subsystem A> | <docs/...> |
| Roadmap / "what's next" | <docs/plan.md> |

## Guardrails

- **Don't break CI.** Green is the baseline.
- **Ask before architectural changes** — new dependency, stack change, schema change.
- **When intent is ambiguous on a hard-to-reverse change, ask** rather than guess.
- <Project-specific invariant — e.g. "every DB query is scoped by tenant_id">

<!-- OPTIONAL ADD-ONS — uncomment a section only if the project needs it.

## Docs / knowledge wiki (Karpathy LLM-wiki pattern)
Source of truth for *what we're building* lives in `docs/`. Code wins over docs;
if they disagree the doc is stale — fix it in the same change. Structure:
`docs/raw/` (frozen sources) -> `docs/wiki/` (one concept per page, [[cross-linked]])
-> `docs/index.md` (master index — start here).

## Writing style (for a non-engineer reader)
Answer first, then the why. Spell out each acronym on first use. Name jargon in
plain words, then the term in parentheses. Short paragraphs and bullets over walls of text.

## Hard-won lessons (Situation -> Best Practice -> Exception)
Append environment/harness traps here as you hit them — things you can't infer from code.
| Situation | Best Practice | Exception |
|---|---|---|
| <long build/download> | <run in background; act on completion> | <a human glance is faster> |
-->
