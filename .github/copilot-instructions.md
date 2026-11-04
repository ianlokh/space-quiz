# Copilot instructions

## Build, test, lint
None. The whole app is a single `index.html` (inline CSS + JS, no dependencies, no server, no build). Open it directly via `file://` to run it. There is no test runner; verify in a browser (e.g. drive the page with `evaluate_javascript` in the integrated browser canvas). Open with a cache-busting query (`index.html?v=N`) or a new canvas `instanceId` to see edits.

## Architecture
- `Q` is the question array; each entry is `[question, [4 options], correctIndex, funFact]`. Add questions there; the per-question header emoji list lives in `render()` and must match `Q.length`.
- State is three globals: `i` (current question), `score`, `locked` (blocks double answers).
- Flow: `render()` builds the question view via `innerHTML` into `#view` → `answer(n)` applies feedback classes, updates score/progress, reveals `#next` → `finish()` renders results (emoji tier chosen from `score/Q.length`) → "Play again" resets and calls `render()`.
- Theming: CSS custom properties in `:root`, overridden in `@media (prefers-color-scheme: dark)`. Any new colour (including the mission-control `--mc-*` header vars and the `--hex` SVG background) needs both light and dark values.

## Conventions
- Keep it a single dependency-free file; body text 14–16px, system sans-serif stack, narrow centred column (`main` max-width 440px).
- Feedback is class-driven: `.correct` (green pop) and `.wrong` (red shake); animations are disabled under `prefers-reduced-motion`.
- Accessibility: focus is moved programmatically (first option on new question → `#next` after answering → `#again` on results); keys 1–4 select answers; only `#fb` is an `aria-live` region. Preserve this when changing `render()`/`answer()`/`finish()`.
- All question text is passed through `esc()` before going into `innerHTML`.
- Always ensure the closing `</script>` tag survives edits to the end of the file.
