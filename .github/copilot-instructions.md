# Copilot Instructions

Single-file Korean space quiz web app: everything (HTML, CSS, JS) lives in `index.html`. No build system, package manager, tests, or linter — open the file in a browser to run it.

## Architecture

- Two **parallel arrays** in the `<script>` block, indexed by question number `i`:
  - `Q[i] = [question, options[], correctIndex, shortFact]` — shown inline as feedback after answering.
  - `D[i] = [modalTitle, longDescription]` — shown in the post-answer modal.
  - Adding/removing/reordering a question requires updating **both** arrays at the same index. `Q.length` drives the UI, but the progress bar `aria-valuemax="10"` and the initial "문제 1 / 10" text in the HTML are hardcoded; keep them in sync if the question count changes.
- Flow: `show()` renders a question → `pick(k)` locks input, marks correct/wrong/dim, then after 700ms `openModal()` → `advance()` (modal "next" button) → `show()` or `finish()`. The `locked` flag and the `ov.open` check in `advance()` prevent double-advance.
- `finish()` picks emoji/message by score ratio thresholds (1, .8, .6, .4); `#again` resets `i` and `score`.

## Conventions

- UI text is Korean (`<html lang="ko">`); keep new copy in Korean, friendly `요` tone.
- Styling uses CSS variables in `:root` with a `prefers-color-scheme: dark` override; use the variables (`--accent`, `--good`, `--bad`, …) rather than hardcoded colors. Animations are disabled under `prefers-reduced-motion`.
- DOM access goes through the `$ = id => document.getElementById(id)` helper; text is set via `textContent` (not `innerHTML`) except for clearing the options container.
- Accessibility attributes (`aria-live` on feedback, `aria-hidden` toggled on the overlay, `aria-valuenow` on the progress bar) are maintained in JS — preserve them when changing flow.
- Code style is compact (dense one-liners); match the surrounding density.
