
# CLAUDE.md — <PROJECT NAME>

**What this is:** <one sentence: what it does and who it's for>.

## Operating Rules

<*This should use the word doc wording see comment>

## Workflow: Life cylce of a session using Feature-branch:

### Discovery: <* may need to fix numbering>
1. **Orient** — read the project plan and ask about the goal of the session (if not stated) and which docs below you need to read. Then give the new feature-branch an easily identifyable name describing on the feature we decide on.
2. **Research** best practices on the project  Critically evaluate sources before adding context to the wiki: User provided material in raw trumps systematic reviews trump posts by respected data scientistis and developers (for example Andrej Karpthy and Andrew Ng) trump news articles from respected journalists trumps blog posts with multiple references. throw out unreliable source material if it's unreliable, old, or unsourced.
### Design:
4. **Plan** — for non-trivial , propose a couple of approaches with trade-offs and
   state how you'll verify the result.
5. **Check** the best practices table below and existing code base to see if a previous agent has already figured out how to complete each task or if a function exists as per the <*update this>
6. **Ask** clarifying questions to build the plan and pause for a decision before writing code.
## Develope:
7. **Build** code according to the best practices listed and Operating Rules
8. **Execute** the code and debug until it runs without errors but do not add error cautionary handleling until you actually cause an error. 
## Debrief:
8. **Verify** — run tests and tie the change to a falsifiable outcome (a passing test,
   a number, a screenshot). Reflect on failures and iterate, don't guess.
9. **Document** — update the affected doc in the *same* change.
10. **Evaluate** completeness by conducting a feature retrospective with the director that updates the best practices table. (See bellow)
11. **Commit** — a clear message describing the *why*.

## Best Practices:
Hard-won lessons from past sessions. 
| Situation | Best Practice | Exception |
|---|---|---|
| <long build/download> | <run in background; act on completion> | <a human glance is faster> |
| You are starting any new session | Run the 4-D loop above in order: **Discovery** (research best practices relevant to our tech stack ask clarifying questions) → **Design** (plan the code and execution *with the director*, then stop) → **Delivery** (execute) → **Decide** (evaluate against data). | Skip Discovery only for a trivial, well-understood change or if already complete. |
| You are writing any response to the director. | Follow Operating Rule #10: Communicate in smart brevity | |
| You hit any 403 or cant perform git deletion | Check for a best practice then ask if you have permission or if the director can perform this action; you don't have permission on purpose | Explicitly told to work around | 

-->
## Stack (authoritative)

| Layer | Choice |
|---|---|
| Language / runtime |  |
| Framework |  |
| Data / storage | |
| Tests |  |
| Lint / format |  |
| Package manager | |



## Project map / where to look

```
<src/>        <live code — edit here>
<tests/>      <test suite>
<docs/>       <design notes / knowledge>
<* explain wiki/raw>

```




