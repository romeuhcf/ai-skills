# Levelling: finding out what they already know

## Contents

- Why this is one message, not a conversation
- The three probes
- Writing a probe that is worth asking
- Turning answers into plan decisions
- Recording the baseline
- Re-levelling mid-course
- Levelling does not stop at intake
- Worked example
- Anti-patterns

## Why this is one message, not a conversation

Getting the level wrong costs the whole session in one of two ways. Too high and
they grind, ask you to write it, and stop reading the questions. Too low and they
skim, answer "continue" instead of thinking, and the checkpoints stop working as
checkpoints.

Both failures look the same from the outside: the developer stops engaging with the
questions. By the time you notice, several milestones are already built on a wrong
assumption.

So the assessment happens once, up front, in a single message, and costs the
developer about two minutes. It is not a quiz and it is not a conversation. Three
probes, asked together, answered together.

## The three probes

Exactly three, one per axis. Fewer and you are guessing; more and it reads as an
exam and they start skipping.

| Axis | What it predicts | Shape of the question |
|---|---|---|
| **Language** | Which mental model they will import from the language they already know, and therefore which bugs they will write | A behaviour question about the target language that their previous language answers differently |
| **Domain** | Whether the milestones can assume the problem or must teach it | A specific question about the thing being built, not about code |
| **Recovery** | How much verification scaffolding each step needs | What they actually do when something does not work |

The recovery probe is the one people leave out, and it is the one that changes the
most about how you teach. Someone who reads compiler output needs a command and
nothing else. Someone who does not needs the expected output spelled out every
time, and needs to be shown that the tool already told them.

## Writing a probe that is worth asking

A probe earns its place only if a different answer changes the plan. Before asking,
finish this sentence: "if they answer X, I will _____ instead."

If the sentence cannot be finished, the question is trivia. Drop it.

Rules:

- **Never a self-rating.** "How comfortable are you with pointers, 1 to 5" measures
  confidence, which is uncorrelated with knowledge and inflated under observation.
- **It has a right answer**, and you can tell a real one from a plausible one.
- **It is answerable in one or two sentences.** If it needs a paragraph, it is a
  milestone, not a probe.
- **No looking it up.** Say so. A looked-up answer measures nothing.
- **"No idea" is a valid answer, and say that too.** The most useful reply you can
  get is an honest blank, and developers will bluff if the framing punishes not
  knowing.
- **Aim at the level you suspect, not the floor.** A probe everyone passes told you
  nothing. If they clear it easily, that is information; the plan moves up.

## Turning answers into plan decisions

Do not compute a score. Each probe maps to one decision:

| Probe | Answer | Plan decision |
|---|---|---|
| Language | Confident and correct | Skip the language-basics milestones entirely; start at the domain |
| Language | Correct by analogy to their other language | Keep one short basics milestone, aimed at where the analogy breaks |
| Language | Blank | Two basics milestones before the domain, with the domain named in each so it does not feel like a detour |
| Domain | Explains the mechanism | Milestones can assume it; spend the budget on the language |
| Domain | Knows the words, not the mechanism | Keep it, note it in **Shaky**, and put the explanation in the milestone that needs it, not before |
| Domain | Blank | The first milestone demonstrates the problem before solving it |
| Recovery | Reads the error, forms a hypothesis | Verification is one command per step; stop there |
| Recovery | Reruns, or asks someone | Every step states the exact passing output, and early steps deliberately induce one failure so they read a real message |

State the conclusion in one line before the plan, in plain language, so they can
correct it: "Taking you as fluent in Ruby, new to Go, comfortable with HTTP. Two
short Go milestones first, then straight into the proxy."

That sentence is also the cheapest way to catch a bad read. Developers will correct
an explicit claim about themselves far more readily than they will volunteer a
correction to a plan.

## Recording the baseline

The baseline goes in `LEARNING.md` under its own heading, with the date and the
actual answers, not your interpretation:

```markdown
## Baseline (2026-09-09)

- **Go:** none. Comes from Ruby, 10 years.
- **HTTP:** reads `curl -v` output; could not say what `Accept: */*` does.
- **Recovery:** reads compiler errors, does not run tests before asking.
- **Read:** two Go basics milestones first, then the proxy.
```

It earns its place later. When a gap shows up in milestone 6, the baseline says
whether it is a regression, a thing they never knew, or a thing you assumed away.
Without it you are guessing about your own guess.

## Re-levelling mid-course

The baseline is a hypothesis, like the plan. Revise it out loud when the evidence
contradicts it:

- **Clearing steps without using the hints** - the read was low. Merge steps and
  raise the ceiling, and say so.
- **Every step needs three rungs of the ladder** - the read was high, or a
  prerequisite is missing. Find the prerequisite before slowing down; it is usually
  one specific thing, not general difficulty.
- **They stop answering the checkpoints and just say "next"** - the questions are
  either too easy or too abstract. Ask them which, directly. Do not guess, and do
  not keep asking questions into silence.

Edit the baseline in place and date the revision. Do not delete what it said before.

## Levelling does not stop at intake

The three probes tell you where they stand on the language, the domain and their
tooling. They tell you nothing about a **form** that shows up in milestone 6: a
table-driven test, a build system, a query language, a protocol, a new kind of file.

So when a step introduces a form rather than an API, spend one line before the
step's opening question:

> "Have you written a table-driven test before, in any language?"

Two words of answer changes the next message completely, and the cost of guessing is
high in both directions. Guess high and the opening question presupposes exactly the
thing the step exists to teach - which reads as a quiz they are failing before the
lesson starts. Guess low and you explain something they have done for a decade.

An alternative that costs nothing: state the assumption instead of asking, in half a
line, and let them correct it.

> "Assuming you have not written a table-driven test before - "

Either way, put it in the same message as the step. It is not a separate turn, and
it is not the intake assessment again. Record what you learn in the baseline.

## Worked example

Goal: a reverse proxy in Go. Developer writes Ruby.

> Three quick ones before I write the plan. Answer from memory - "no idea" is a
> perfectly good answer to any of them, and more useful to me than a guess.
>
> 1. In Go, a function returns `(value, error)` and you ignore the error. What does
>    the compiler do?
> 2. A client sends `Accept-Encoding: gzip` and a proxy forwards the request without
>    it. What changes about the response the client gets?
> 3. Last time a build failed with an error you did not recognise - what was your
>    first move?

Why each one:

1. **Language.** Ruby raises; Go returns. The answer reveals whether they expect
   exceptions. If they say "it won't compile", they are guessing from Rust or from
   the unused-variable rule - which is itself useful, and a good sign.
2. **Domain.** Not "what is a header". A specific consequence, which separates
   reading `curl -v` from knowing what the fields do.
3. **Recovery.** Open-ended on purpose. "I read the error" and "I paste it into
   Claude" lead to different step formats.

Their answers: "no idea"; "no idea, I only know how to read curl -v"; "I read the
error, usually it's a typo".

Read, stated in one line: fluent elsewhere, new to Go, thin on HTTP mechanics, good
at reading tool output. Plan: two Go basics milestones, HTTP taught inside the
proxy milestones rather than before them, verification as a single command per step.

## Anti-patterns

- Asking them to rate themselves.
- Spreading the probes across several turns. It becomes an interview.
- A fourth and fifth probe because they were interesting.
- Asking something whose answer would not change the plan.
- Keeping the read to yourself. An unstated assumption cannot be corrected.
- Treating a blank as a failure. It is the cheapest, cleanest data point available.
- Re-probing later because you doubt the baseline. Watch the work instead; it is
  better evidence than another question.
- Opening a step with a question that only makes sense once the step is taught.
