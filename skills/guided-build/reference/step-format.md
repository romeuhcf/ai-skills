# Step format

## Contents

- The template
- A worked example (Rust web server, milestone 2)
- What separates a reference snippet from a solution
- Checkpoint questions that work

## The template

Seven parts. Send 1-2, stop, wait. Send 3-7 after they answer. The whole turn stays
under 200 words outside code blocks - if it will not, the step is too big.

```markdown
**M2 / Step 3** - after this, your server answers a real browser.

**First:** your handler returns `String` today. An HTTP response needs a status
code and headers too. Where do you think those come from, given the signature
hasn't changed?

--- wait ---

**Why this step**

[2-4 sentences on the problem being solved. Not syntax. The reason this
abstraction exists, or the failure it prevents.]

**Your task**

[Prose. Name the file, the function, the behaviour. Never the code.]

**Reference**

[Only the API they cannot be expected to know, with their logic elided.]

**Verify**

Run: `[exact command]`
Passing looks like: `[exact expected output]`

**Checkpoint**

[A question their passing test cannot answer for them.]
```

## Worked example

**M2 / Step 3** - after this, your server answers a real browser.

**First:** your handler returns `String` today, and axum accepts that. An HTTP
response also needs a status code and headers. Where do you think those are coming
from right now?

--- developer answers ---

**Why this step**

One trait converts whatever your handler returns into a response, which is why
`String` works with no ceremony - it fills in `200 OK` for you. To choose a status,
you stop accepting that default and return a type that encodes what you mean.

**Your task**

In `src/routes.rs`, change `get_user` so that a missing user produces a 404 with a
JSON body `{"error": "not found"}`, and a found user produces 200 with the user as
JSON. Do not add an error enum yet - return the two cases directly from the handler.

**Reference**

The two pieces you have not used before:

```rust
use axum::{http::StatusCode, Json};

// A tuple of (StatusCode, Json<T>) is itself a valid return type.
// serde_json::json! builds an anonymous body without defining a struct.
```

**Verify**

Run: `curl -s -o /dev/null -w '%{http_code}\n' localhost:3000/users/999`
Passing looks like: `404`

**Checkpoint**

Your handler now returns two different tuples from two branches. Rust requires both
arms of an `if` to have the same type. Why does this compile?

## Reference snippet vs. solution

The line is whether they could paste it and be done.

| Allowed | Not allowed |
|---|---|
| A signature with an empty body | The body filled in |
| An unfamiliar API call in isolation | That call wired into their control flow |
| A type or trait definition | The `impl` you just assigned |
| A shape with `// your logic here` | A shape where only a literal needs changing |
| The same pattern from a different domain | The same pattern from this domain |

When unsure, move it to a different domain. Teaching middleware for their auth
layer? Show the pattern with a logging middleware.

## Checkpoint questions that work

A good checkpoint cannot be answered by the code passing. It targets the thing they
would have skipped.

- **Prediction:** "What happens if two requests hit this at once?"
- **Deletion:** "Remove the `.await` on line 12. What is the error, and why that one?"
- **Transfer:** "Where else in this file does the same ownership problem exist?"
- **Justification:** "You used `clone()` here. What would it take not to?"
- **Boundary:** "What input breaks this?"

Avoid: "Does that make sense?", "Any questions?", "Understood?" - all three get a
yes that means nothing.
