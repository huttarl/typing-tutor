# Typing Tutor — Design & Architecture

A web-based touch-typing tutor for children, built as a single self-contained
HTML file. No login, no backend, no build step. Designed to be usable by several
students on one shared browser, each under a chosen animal "character."

## Goals & constraints

- **No accounts / privacy-friendly.** Students pick an animal character instead
  of logging in. Progress is stored locally per character.
- **Runs from a single file.** `typing-tutor.html` contains all HTML, CSS, and
  JS. The only external resource is the Google Fonts stylesheet (graceful
  fallback to system fonts if offline — see To-do).
- **Accuracy first, speed later.** Following typing-pedagogy research, the early
  levels score on accuracy only; words-per-minute (WPM) is hidden until all
  letters are learned and the student has typed real sentences for a level.
- **Desktop / physical keyboard.** Assumes a real keyboard (touch typing).

## File layout

Everything lives in `typing-tutor.html`:

- `<style>` — design tokens (CSS variables) and component styles.
- Two "screens" toggled by an `.active` class:
  - `#pick-screen` — character selection.
  - `#lesson-screen` — the actual typing practice.
- `#modal-mount` — where the level-picker modal is injected.
- `<script>` — all logic (data tables, state, rendering, input handling).

## Data model (the source-of-truth tables)

- **`ANIMALS`** — the selectable characters (id, name, emoji).
- **`KEY_INFO`** — for each key: which `hand` ('L'/'R') and `finger`
  (0=pinky … 3=index, 4=thumb) types it. Drives the finger hint and hand
  diagram. Follows standard touch-typing finger assignments (e.g. `m` = right
  index).
- **`K_HOME` / `K_TOP` / `K_BOTTOM` / `K_PUNCT`** — cumulative key sets, built
  up with `.concat()` so the level table stays readable and consistent.
- **`LEVELS`** — ordered array; each level has the `keys` available, a `label`,
  and flags: `wordLevel`, `wordKey` (which `GROUP_WORDS` list), `sentenceLevel`,
  `showWpm`, `wpmStars`.
- **`GROUP_WORDS`** — pre-generated, curated word lists per review level,
  restricted to the letters learned by that point. Keys: `home`, `topOnly`,
  `topHome`, `bottomHome`, `all`.
- **`WORD_BANK`** — larger mixed vocabulary (Austen/Dickens flavored) for the
  "Real words" level, filtered at runtime to available letters.
- **`SENTENCES`** — complete sentences from Austen, Dickens, and the World
  English Bible (WEB, public domain, modern English). **Kept pre-sorted
  shortest→longest in source** so sentence levels can slice it into difficulty
  tiers without sorting at runtime.
- **`LEVEL_GROUPS`** — maps contiguous level ranges to named groups for the
  level-picker UI (Home row, Top row, Bottom row, Real words, Real sentences).
- **Derived indices** — `SENT_LEVEL_FIRST`, `WPM_SHOW_FROM`, `WPM_STARS_FROM`
  are computed from `LEVELS` via `findIndex`, so reordering levels keeps them
  correct automatically.

## Curriculum design

Keys are introduced **one or two at a time** (never a whole row at once), which
matches how touch typing is taught. The physical rows are grouped:

1. **Home row** — F J, then D K, S L, A ;, then the G/H index stretch, then a
   home-row word level.
2. **Top row** — E I, R U, T Y, W O, Q P, then a *top-row-only* word level, then
   a *top + home* word level. (Many words use only `q w e r t y u i o p`.)
3. **Bottom row** — V N, C M, B, X Z. Bottom-row-only words are impossible, so
   the review level uses *home + bottom* letters. Bottom row is taught last.
4. **Real words** — mixed vocabulary from the full bank.
5. **Real sentences** — three levels, drawing progressively longer sentences;
   WPM appears on the 2nd and counts toward the score on the 3rd.

Word-review lists were verified by script so that (a) every word is typeable
with the letters learned by that level, and (b) each list actually exercises the
newly introduced keys.

## Scoring

- **Stars (0–3) per exercise.** Accuracy-only until the final sentence level,
  where WPM also factors in.
- **Accuracy is keystroke-based and counts corrected errors.** Every forward
  keystroke is counted; a character that is wrong *at the moment it is typed*
  counts against accuracy even if the student fixes it with backspace. This is
  deliberate: the goal is correct typing without looking back, so reflexive
  backspacing should not hide mistakes. (State: `keystrokes`, `keyErrors`,
  `prevTypedLen`.)
- **Level-up.** Completing 3 exercises at ≥2 stars marks the level complete and
  advances. (See To-do: early home-row levels should likely require more reps.)

## Progress & persistence

- `localStorage` key `tt2_<characterId>` stores `{ level, completed[], stars,
  exercises }`. `completed` is an array of independently-tracked completed level
  indices (migrated from an older `maxLevel`-only format on load).
- `localStorage` key `tt2_settings` stores global settings (currently
  `lockLevels`).
- **Level locking** (`isLevelUnlocked`): when on (default), a level is
  selectable if it's the first level, already completed, or the one right after
  any completed level. When off, any level is selectable (for testing or for a
  student who already has skills). Toggle lives in the level picker.

## Input handling

- The real `<input>` is visually hidden; the styled prompt box shows progress.
- A global `keydown` handler keeps focus on the input during an active exercise,
  routes `Enter` to "next exercise" when a result is showing, and `Escape` to
  close the level picker.
- Audio feedback uses the Web Audio API (oscillator) — a soft tick for correct
  keys, a lower buzz for errors. No audio files needed.

## Keyboard & hand diagrams

- **Keyboard** (`renderKeyboard` + `KB_ROWS`) renders a realistic ANSI layout,
  including modifier/structural keys (Tab, Caps, Shift, Enter, Backspace, space)
  with proper unit widths. The real key widths produce the correct row stagger,
  so the bottom row aligns naturally (no margin hacks). Unlearned keys are shown
  locked/greyed. The current key is highlighted in its finger color; home-row
  keys carry a finger-colored border.
- **Capital letters** highlight the letter key *and* the Shift key on the
  opposite hand from the letter.
- **Hands** (`drawHand` / `updateHands`) draw two SVG hands; the correct finger
  on the correct hand lights up for the current key. The SVG `viewBox` is
  cropped so the hands aren't clipped and there's no excess whitespace.

## Rendering pattern

There is no framework. State lives in module-scoped variables; functions
re-render the relevant DOM section (avatar grid, prompt, keyboard, hands,
result) when state changes. This is fine at this scale; if complexity grows, a
small render() consolidation or a framework could be considered (see To-do:
code review).

---

# TO-DO LIST

This is the running to-do / backlog. Items roughly in priority order within
each section.

## Pedagogy / content

- [ ] **More reps on foundational levels.** Research says home-row levels need
      more repetition than later ones. Make early levels require more completed
      exercises before advancing (currently a flat 3).
- [ ] **Optional per-session break timer.** Gently suggest a break at an
      age-appropriate mark (~5 min under age 8, ~10 min ages 8–11, ~15 min age
      12+). Optional/toggleable.
- [ ] **Teach punctuation keys** at pedagogically appropriate points (e.g.
      comma and period relatively early; then apostrophe, etc.).
- [ ] **Teach the Shift key explicitly** — decide when (after lowercase letters
      are solid?) and how (dedicated capital-letter levels; teach
      opposite-hand Shift rule).
- [ ] **Curated Bible passages.** Allow focusing sentence practice on specific
      passages the family wants to learn.

## Features

- [ ] **Free-form practice after the structured levels.** Give longer passages
      (a paragraph+) from Dickens, Austen, the Bible, Shakespeare, and classic
      poetry (e.g. Poe). May fetch passages online so the student isn't given
      the same text repeatedly — this would be an *optional, internet-requiring*
      feature, kept separate from the offline core.

## Technical / quality

- [ ] **Offline support.** Ensure the app runs fully from a `file://` path with
      no network. Add a local font fallback stack (or bundle fonts) so the
      Google Fonts `<link>` failing offline degrades gracefully. No dynamic data
      fetching in the core app.
- [ ] **Code review** covering: security, intuitive UI, code efficiency, and
      code readability. (E.g. the recent `SORTED_SENTENCES` slip — a reference
      left after its definition was removed — is the kind of thing a review or a
      lightweight lint/test pass should catch. Consider adding a small
      build-time check or moving logic into testable functions.)

## Done (for reference)

- Per-character progress via localStorage; no login.
- One-/two-keys-at-a-time progression; row-based groups; corrected so G/H are
  home row and the full top row (incl. q w o p) is one group.
- Real-word review level at the end of each group, with verified word lists.
- Top-row-only word level + top+home word level.
- WEB (modern, public-domain) Bible verses added to sentences; sentences
  pre-sorted shortest→longest in source.
- Finger hint + SVG hand diagram showing the correct finger; Shift highlighted
  for capitals; realistic keyboard layout fixes row alignment.
- Keystroke-based accuracy that counts corrected (backspaced) errors.
- Level picker with grouped levels, completion state, and a lock-levels toggle.
- Character grid distinguishes started vs. unused characters.
- Enter advances to the next exercise.
