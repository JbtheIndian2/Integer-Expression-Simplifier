# reduce() — Integer Expression Simplifier

A single-file HTML app that takes any integer arithmetic expression and walks it
down to a final value one operator-precedence step at a time — parentheses
first, then multiplication/division, then addition/subtraction — highlighting
exactly which sub-expression collapses at each rung of the ladder.

## What it does

1. You type an expression like `(8 - 3) * 4 + 15 / 3 - 2` and hit **SIMPLIFY →**
   (or click one of the example chips).
2. The app sends the expression to the Claude API with a system prompt that
   instructs it to act as a strict integer-arithmetic reduction engine.
3. Claude returns a JSON breakdown: one step per operation, each showing the
   current state of the expression with the next piece to evaluate wrapped in
   `[[ ]]` markers, plus a short plain-English note.
4. The app renders these steps as a vertical "ladder" of cards, with the
   evaluated sub-expression highlighted in gold/teal, ending in a final
   result badge.

## Features

- **Step-by-step precedence trace** — parentheses → `*`/`/` (left to right) →
  `+`/`-` (left to right), matching standard integer order of operations.
- **Integer division truncates toward zero**, as specified in the prompt.
- **Inline highlighting** of the exact sub-expression being reduced at each step.
- **Click-to-run examples** for quick demos.
- **Error handling** for invalid expressions, unparsable model output, or
  network/API failures, all surfaced in a dedicated error box.
- Dark, terminal-inspired UI (JetBrains Mono + Inter), fully responsive down
  to mobile widths.

## How it works technically

- Pure HTML/CSS/JS — no build step, no framework.
- Calls `https://api.anthropic.com/v1/messages` directly from the browser
  using `model: "claude-sonnet-4-6"`.
- The system prompt enforces a strict JSON-only response shape:
  ```json
  {
    "valid": true,
    "steps": [
      { "rule": "PARENTHESES", "expression": "...[[3+4]]...", "note": "..." }
    ],
    "result": 123
  }
  ```
- The client strips any accidental markdown code fences, parses the JSON,
  and renders each step as a "rung" in the ladder. The final rung is styled
  differently (teal accent) and shows the result badge.
- Malformed model output, invalid expressions, or failed requests are caught
  and shown as a readable error message instead of breaking the UI.

## Limitations

- **Integers only** — no decimals, variables, exponents, or other operators
  beyond `+ - * /` and parentheses.
- Accuracy depends on the model's arithmetic — the footer includes a
  reminder to double-check results for graded/academic work.
- Requires network access to `api.anthropic.com`; there's no offline fallback
  or local evaluator.

## Future improvements

- Add an offline/local evaluator to remove dependency on the Claude API.
- Support decimal arithmetic and additional operators like exponentiation.
- Add pre-validation of expressions and clearer syntax error messages.
- Make step labels numbered and add an export/share option for walkthroughs.
- Cache common examples and previous results for faster repeat use.
- Add a dark/light theme toggle and more compact mobile-friendly layouts.

## Usage

Just open the HTML file in a browser (or run it as a Claude Artifact). No
installation, dependencies, or build tools required.
