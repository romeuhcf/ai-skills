# Designing the milestone plan

## Contents

- Rules for a good milestone
- Rules for a good step
- Sequencing: running program first
- Worked example: HTTP server in Rust, both paths
- Adjusting the plan mid-course

## Rules for a good milestone

A milestone is the smallest increment that delivers one new concept, and it ends with
something the developer can run and show. Usually 3-8 steps; fewer is fine and often
better.

- **It has a demo.** "Ownership and borrowing" is not a milestone. "The server keeps
  shared state across requests without cloning it per request" is.
- **It introduces exactly one new concept.** Two means it is two milestones. This is
  the rule that gets broken most, and the cost is that when the developer struggles
  you cannot tell which of the two lost them.
- **It is a minimum deliverable increment.** If half of it could ship on its own and
  be useful, that half was the milestone and the rest is the next one.
- **It opens with an explanation, not a task.** What it makes possible, why it
  exists, a short theoretical example, and a generic implementation sketch in another
  domain when one applies. Then a clarity checkpoint. See `reference/explaining.md`.
- **It survives being stopped after.** If they walk away at the end of M3, they have
  a smaller working thing, not a half-thing.
- **It is named for the capability, not the topic.** "Serves JSON on two routes",
  not "Handlers and routing".

## Rules for a good step

- One change, in one place, with one way to check it.
- 5-20 minutes of the developer's time. Longer means split it.
- The verification is a command, never a judgement. `cargo test`, `curl`, `cargo
  check` - something that passes or fails.
- The step is writable with what they now know plus exactly one new thing.

## Sequencing

Get to a running program in milestone 1, always, even if it is trivial. A developer
with something running has a place to put every later idea. A developer three
milestones into theory has nowhere to put anything.

Then deepen along the axis that keeps producing checkable results:

1. Make it work for the happy path.
2. Make it fail correctly.
3. Make it testable.
4. Make it hold state.
5. Make it fast or concurrent, if the goal calls for it.

Correctness before ergonomics. Ergonomics before performance. Introduce
abstractions only after the pain they solve has been felt - the developer should
have written the duplicated code before extracting it.

## Worked example: HTTP server in Rust

Which plan depends on the answer to the depth-or-speed intake question.

### Path A - from scratch (understanding what a framework does)

- **M1. It accepts a connection.** `TcpListener`, accept loop, read the bytes, print
  them. Demo: `curl` prints raw bytes in the server's terminal.
- **M2. It speaks HTTP.** Parse the request line by hand, write a hardcoded valid
  response. Demo: a browser renders it. Concepts: the protocol as text, `&str`
  slicing, why parsing is where servers get exploited.
- **M3. It routes.** Match on path and method, 404 for the rest. Demo: two working
  routes and a correct 404.
- **M4. It handles more than one client.** Threads or an async runtime, chosen
  deliberately. Demo: two slow requests overlapping. Concepts: `Arc`, `Send`, why
  the accept loop was a bottleneck.
- **M5. It is tested.** Integration tests against a server on an ephemeral port.
  Demo: `cargo test` green.
- **M6. Errors stop panicking.** A real error type, `?` through the handlers,
  responses that say what went wrong. Concepts: `Result`, `From`, `thiserror`.

### Path B - idiomatic stack (shipping on axum/tokio)

- **M1. It runs.** `cargo add axum tokio`, one route, `#[tokio::main]`. Demo: `curl`
  gets a string back. Concepts: async as a keyword, the runtime as a thing that must
  exist.
- **M2. It has typed routes.** Path and query extractors, JSON in and out with
  serde. Demo: a POST that round-trips a struct. Concepts: extractors as trait
  impls, derive macros.
- **M3. It answers correctly when things go wrong.** `IntoResponse`, status codes, a
  404 that means it. Demo: `curl -w '%{http_code}'` matches the case.
- **M4. It holds state.** `State` extractor, `Arc<Mutex<_>>` or a pool. Demo: data
  written by one request read by the next. Concepts: shared ownership across tasks,
  why the mutex is not free.
- **M5. It is tested.** `tower::ServiceExt::oneshot` against the router, no network.
  Demo: `cargo test` green, fast.
- **M6. It is observable.** `tracing`, a middleware layer, structured logs. Concepts:
  middleware as composition, why `tower::Layer` looks the way it does.

Both paths reach a server the developer can explain. Path A produces someone who
knows what axum is doing; path B produces someone who can ship this week. Say that
trade-off out loud during intake rather than choosing for them.

## Adjusting mid-course

The plan is a hypothesis. Revise it, out loud, and update `LEARNING.md`:

- **They are flying.** Merge steps, or raise the ceiling of the remaining
  milestones. Do not just go faster through material that is now beneath them.
- **They are grinding.** The step was too big or the prerequisite is missing. Split
  it, or insert a milestone that builds the missing piece.
- **They want a detour.** Follow it if it is adjacent - curiosity is the resource
  the whole thing runs on. Record it in the log as an unplanned milestone so the
  plan stays honest.
- **The goal changed.** Rewrite the plan from the current step forward. Keep the
  completed history; do not rewrite what already happened.
