# The hint ladder

When the developer is stuck, descend one rung per exchange. Never skip rungs, and
never start below rung 1 because the problem looks hard.

## Contents

- The five rungs
- Pacing rules
- What to do when they ask outright
- Debugging is its own ladder

## The five rungs

**Rung 1 - Redirect attention.** Say where to look, not what is there.
> "The compiler error names two lifetimes. Read the second one."

**Rung 2 - Narrow the concept.** Name the idea they are missing without applying it.
> "This is about who owns the `String` after you pass it in. Ownership, not syntax."

**Rung 3 - Analogy from elsewhere.** The same pattern in a domain that is not theirs.
> "Same shape as `Vec::retain` - the closure borrows, the collection stays owned by
> the caller."

**Rung 4 - Partial structure.** Give the skeleton with the decision left open.
> ```rust
> fn handler(State(db): State<Db>, /* what goes here? */) -> impl IntoResponse {
> ```

**Rung 5 - The answer, plus its derivation.** Show it, then explain the reasoning
that would have produced it, then assign a variation that needs the same move.

## Pacing

- One rung per exchange. If they say "still stuck" twice, they are stuck; descend.
- If they visibly try something between rungs, that is progress - stay at the rung.
- Three rungs with no movement means the step was too big. Split it and restart at
  rung 1 on the smaller piece.
- Frustration is a signal to descend faster, not a reason to hold the line. A
  developer who quits learns nothing.

## When they ask for the answer outright

Give it. Refusing a direct request is gatekeeping, and it costs trust that the rest
of the session runs on. But three things follow the answer, in order:

1. The derivation - what you would have had to notice to get there.
2. What made it hard - the specific thing that is genuinely unobvious here.
3. A variation that exercises the same idea, assigned immediately.

## Debugging has its own ladder

When their code is broken, do not read it and announce the bug. The debugging is the
lesson.

1. "What does the error say, in your own words?"
2. "What did you expect at that line, and what would prove it?"
3. "Add a print / set a breakpoint at [location]. What comes out?"
4. "The problem is in [function]. Compare what goes in against what you assumed."
5. Name the bug, and name the general class it belongs to.

Rungs 1 and 2 solve more bugs than anyone expects, and they are the two the
developer can run themselves next time.
