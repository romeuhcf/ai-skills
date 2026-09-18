# Explaining: prose, framings, and checking you were understood

## Contents

- Prose, not telegraphese
- The four parts of an explanation
- Framings, and having a second one ready
- When a picture is the explanation
- Clarity checkpoints
- Before you send: the truncation check
- Anti-patterns

## Prose, not telegraphese

The terse budget in `SKILL.md` exists to stop lectures. It is not a licence to write
in fragments. A step whose instructions are a stack of noun phrases costs the
developer more time than the paragraph it replaced, because they have to reconstruct
the sentences you dropped.

The rule is: **short, but whole**. Sentences with verbs. Paragraphs of two or three
sentences rather than bullets of three words. Bullets are for genuinely parallel
items - a list of cases, a list of files - and never for the reasoning that connects
them.

Compare. Telegraphic, and unreadable at speed:

> **Task.** `Partitions` struct. Map, RWMutex, max. `get` implemented. Double check
> after Lock. Test: 100 goroutines, same pointer.

The same content, written:

> Write the `Partitions` type: a map from name to partition, an `RWMutex`, and the
> limit each new partition inherits. Then implement `get`, which returns the
> partition for a name or creates it. The interesting part is that creating it needs
> the exclusive lock, and you have to look in the map a second time after taking it.
> Prove it with a test that calls `get` from a hundred goroutines at once and checks
> they all receive the same pointer.

The second is 30 words longer and takes half the time to act on.

## The four parts of an explanation

When a milestone opens, or when a step introduces something new, the explanation has
four parts, in this order. Skip a part only when it genuinely does not apply, and say
so rather than leaving a hole.

**1. What this makes possible.** A capability, in the developer's terms, not a topic.
"After this, one slow tenant cannot take the whole pool" rather than "concurrency
limiting". This is the sentence they will repeat to someone else.

**2. Why it exists.** The failure it prevents, or the thing that is impossible
without it. Concrete beats general: a scenario with numbers in it lands, a principle
does not.

**3. A short theoretical example.** The idea in miniature, with no project code in
it. Four or five lines, arithmetic if the idea is arithmetic, a sequence of events if
the idea is about ordering. This is where someone who has never met the concept gets
their footing, and it costs almost nothing to include.

**4. A generic implementation sketch, when applicable.** The shape in code, in a
domain that is *not* theirs, so the sketch cannot be pasted into the task. Teaching a
bulkhead for their proxy? Sketch it around a database connection pool. Some
milestones are pure policy and have no sketch; say that instead of inventing one.

## Framings, and having a second one ready

An explanation that did not land does not get better by being repeated. It gets
better by being re-entered from a different side.

Have a second framing before you need it. The usual axes:

| Framing | Enters through |
|---|---|
| Mechanical | What the machine does, step by step, in time |
| Analogical | The same shape in something they already know well |
| Historical | The problem that existed first, and why this was the answer |
| Failure-first | What breaks without it, demonstrated |
| Arithmetic | Small numbers, worked through by hand |

The analogy is the one to reach for when the developer comes from another language:
the fastest route into a Go interface, for someone who writes Ruby, is `Module#prepend`
and `super`. Then say where the analogy breaks, because every analogy does, and the
break is usually the thing worth knowing.

When they say they did not follow, do not restate. Pick a different row of that
table, and say which you are switching to: "let me come at that from the failure
side instead".

## When a picture is the explanation

Some ideas are cheaper to see than to read, and prose about them is a translation the
developer has to undo in their head. Reach for a picture when the idea has a shape:

- **Order in time.** A sequence, a handshake, a shutdown: who acts, in what order,
  and what overlaps. Two goroutines racing over a counter is a picture.
- **Layers wrapping layers.** Middleware, decorators, protocol envelopes. The
  wrapping is the whole point and a list flattens it.
- **A structure before and after.** A slice reallocating, a map growing, a request
  cloned and mutated.
- **Two axes.** Anything where a threshold divides a plane, or where a value moves
  against a baseline.

Do not draw a list, a single formula, or a thing with two parts. A diagram of two
boxes and an arrow costs more attention than the sentence it replaced.

### Cheapest medium first

**1. ASCII, inline.** No permission, no tooling, no round trip. It survives being
pasted into `LEARNING.md`, into a commit message, into a chat with a colleague, and
it renders in every terminal. This is the default, and it is enough surprisingly
often:

```text
t=0   goroutine A   Load() -> 9    admite
t=0   goroutine B   Load() -> 9    admite      <- ambas viram 9
t=1   A             Add(1) -> 10
t=1   B             Add(1) -> 11              <- limite era 10
```

**2. A tool already on the machine.** Check before promising anything, because these
vary per box:

```sh
for t in dot mmdc d2 plantuml; do command -v $t >/dev/null && echo "$t"; done
```

On the machine this was written on, `dot`, `mmdc` and `d2` were present and
`plantuml` was not. Graphviz is the safest of them:

```sh
printf 'digraph { rankdir=LR; client -> proxy -> upstream }' > /tmp/d.dot
dot -Tsvg /tmp/d.dot -o /tmp/d.svg      # verified: renders, ~2.5KB
```

Write the source in a temporary directory, never in their project, and hand them the
file path plus the source that produced it. The source is the part they can change.

**3. Hand-written SVG**, when the layout matters more than the graph structure - an
annotated timeline, a threshold on a plane.

**4. A published artifact**, if your client offers one. This is the only medium worth
asking about first, because it costs a round trip and leaves something outside the
repository. Worth it for a concept the whole plan rests on, or one they will come
back to across sessions. Not worth it for a single step.

### Rules

- **Ask before 3 and 4, never before 1.** ASCII is part of writing clearly.
- **The picture explains, it does not decide.** It never replaces the task, the
  verification command, or the checkpoint question.
- **If it explains something durable, it goes into `LEARNING.md`** next to the entry
  it belongs to. ASCII is the only medium that survives there intact, which is the
  second reason to prefer it.
- **Draw the mechanism, not the metaphor.** A diagram whose boxes are your analogy
  rather than their code teaches the analogy.

## Clarity checkpoints

Two different questions, easy to confuse, and only one of them belongs here.

The **checkpoint** in the step loop tests understanding of code that already passes.
It has a right answer, and it comes after the work.

A **clarity checkpoint** asks whether the explanation landed, and it comes right
after the explanation, before any work. It has no right answer.

Ask it after opening a milestone, after introducing a concept, and after any
explanation longer than a few sentences. Make it specific enough to be answerable:

- "Does that land, or do you want it from the failure side instead?"
- "Which of those two parts should I go slower on?"
- "Is the analogy helping or getting in the way?"

Never "does that make sense?", "any questions?" or "understood?". All three get a
polite yes that means nothing, which is why they are already banned in
`step-format.md`.

And when the answer is "no", that is information about your explanation, not about
them. Switch framing and thank them for saying it, in about four words.

## Before you send: the truncation check

Read the message back before sending. Three failures are common enough to check for
by name:

1. **The dangling lead-in.** A sentence that announces something and then stops:
   "Now, the question that is still open - two goroutines, max 10, in flight 9:" and
   nothing follows. The reader is left holding a colon. This happens when a message
   is edited down and the payload is what got cut.
2. **The unanswered question.** You asked something two messages ago, they answered
   something else, and you moved on. Either drop it out loud or ask it again, whole.
3. **The orphan reference.** "the second one", "that approach", "as above" - when the
   thing referred to is no longer on screen for them.

The check is one pass, and the question it answers is: if someone read only this
message, would they know exactly what to do next?

## Anti-patterns

- A task written as noun phrases with the verbs removed.
- Repeating an explanation in the same framing, only slower.
- "Does that make sense?" instead of a specific clarity checkpoint.
- Ending on a colon, a dash, or a lead-in with nothing after it.
- A theoretical example that is really the task with the names changed.
- An implementation sketch in the developer's own domain, which they can paste.
- Explaining a concept before the moment it is needed, which is still the more common
  failure than explaining too little.
- A diagram of two boxes and an arrow, which costs more than the sentence it replaced.
- Promising a rendering tool without checking the machine has it.
