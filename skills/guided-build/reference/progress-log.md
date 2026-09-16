# LEARNING.md, the progress log

One file at the project root. It is the memory of the whole effort, and the first
thing to read at session start. It belongs in version control.

## Contents

- Update rules
- The template
- What "mastered" means here

## Update rules

Write to it:

- when a step completes (move the marker, one line in the timeline);
- when a milestone completes (mark it, note what they can now do);
- when a decision is made (axum over actix, threads over async) with the reason;
- when something goes wrong in a way worth revisiting - the log's most valuable
  section is the one about gaps;
- when the plan changes;
- when the work contradicts the baseline, revised in place with a date.

Rules:

- Edit in place; do not append a new copy of the plan each session.
- Absolute dates, never "yesterday" or "last session".
- Record what actually happened. If they built something unassigned, it goes in.
- Confirm the write in one short line so they know it is saved.
- Never record a step as done when its checkpoint was not answered.

## The template

```markdown
# Learning log: [goal]

**Goal:** [the concrete, checkable version]
**Path:** [from-scratch | idiomatic-stack, and why]
**Started:** YYYY-MM-DD
**Now:** M2 / Step 3

## Baseline (YYYY-MM-DD)

From the three intake probes. Their answers, not your interpretation. Never
deleted; revised in place with a date if the work contradicts it.

- **Language:** none. Comes from Ruby, ten years.
- **Domain:** reads `curl -v`; could not say what `Accept: */*` does.
- **Recovery:** reads compiler errors; does not run the tests before asking.
- **Read:** two language milestones first, then the real goal.

## Milestones

- [x] M1. It runs - completed YYYY-MM-DD
- [ ] M2. Typed routes and JSON  <- current
  - [x] S1. Path extractor
  - [x] S2. JSON response body
  - [ ] S3. Status codes for the not-found case  <- next
- [ ] M3. Errors
- [ ] M4. State
- [ ] M5. Tests

## Decisions

- YYYY-MM-DD - axum over actix-web. Wanted the tower ecosystem and the extractor
  model; actix's actor legacy is a second thing to learn.
- YYYY-MM-DD - `Arc<Mutex<HashMap>>` for state rather than a database. The lesson is
  shared ownership, not SQL.

## Solid

Things demonstrated by working code plus a correct checkpoint answer.

- YYYY-MM-DD - `#[derive(Serialize)]` and what serde generates.
- YYYY-MM-DD - why the handler must be `async` even with nothing to await.

## Shaky

Things to revisit. Delete an entry only when re-demonstrated.

- YYYY-MM-DD - lifetimes on struct fields. Got it working by cloning; did not have a
  reason not to clone.
- YYYY-MM-DD - `?` in a function returning `impl IntoResponse`. Worked by accident.

## Timeline

### YYYY-MM-DD
Completed M2/S1 and M2/S2. Wrote the path extractor unaided. Struggled with the
`Json` wrapper on the return type - three rungs down the ladder before it clicked
that `Json` is a wrapper, not a function. Checkpoint on serde derive: answered well.
```

## What "solid" means here

Two conditions, both required:

1. Working code they wrote.
2. A correct answer to a checkpoint question the code could not answer for them.

Code that works without the second condition goes in **Shaky**. That is not
pedantry - it is the entire difference between this and vibe coding, and the log is
where the distinction is kept honest.
