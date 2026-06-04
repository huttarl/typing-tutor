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
- `#modal-mount` — where modals (level picker, age entry) are injected.
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
  and flags: `wordLevel` (draw from `WORD_BANK`), `allowCaps` (let a `wordLevel`
  include capitalized words), `wordKey` (which `GROUP_WORDS` list), `sentenceLevel`,
  `phrases` (a curated list for a capitals/punctuation-teaching level), `showWpm`,
  `wpmStars`.
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
4. **Mixed words** — common vocabulary from `WORD_BANK`. It comes before the
   Shift and punctuation lessons, so it must stay all-lowercase: its key set has
   no punctuation and it omits `allowCaps`, so the filter drops any word with a
   capital (`Darcy`) or a mark it can't yet type (`Mr.`).
5. **Capitals** — a dedicated Shift lesson (curated `phrases` of capitalized
   words, spanning both Shift keys), taught before capitals are needed downstream.
6. **Punctuation** — the common marks taught a few at a time, each level via a
   curated `phrases` list (see Data model): comma/period (which also practices the
   abbreviations `Mr.`/`Mrs.`), apostrophe (`can't`, `men's`),
   question/exclamation, quotation marks (dialogue), hyphen, then
   colon/semicolon/slash. Cumulative `K_PUNCT_1..4` key sets unlock the marks
   progressively; shifted marks (`" ? ! :`) ride on a base key (`' / 1 ;`) and
   are coached with the opposite-hand Shift, just like capitals.
7. **Real sentences** — three levels, drawing progressively longer sentences;
   WPM appears on the 2nd and counts toward the score on the 3rd.

Proper nouns (`Darcy`, `Scrooge`, months, places like `United States`) are drilled
as Shift practice in the Capitals lesson — multi-word names exercise Shift several
times in a row. The `WORD_BANK` keeps its capitalized entries too, but the mixed-word
filter excludes them (no `allowCaps` level yet); they're available for a future one.

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
  exercises, birthYear }`. `completed` is an array of independently-tracked
  completed level indices (migrated from an older `maxLevel`-only format on
  load). `birthYear` is captured once (see Break timer) and preserved by every
  save.
- `localStorage` key `tt2_settings` stores global settings: `lockLevels` and
  `breakReminders` (both default true).
- **Level locking** (`isLevelUnlocked`): when on (default), a level is
  selectable if it's the first level, already completed, or the one right after
  any completed level. When off, any level is selectable (for testing or for a
  student who already has skills). Toggle lives in the level picker.
- **Reset a character** (`confirmResetProgress`): a de-emphasized link at the
  bottom of the level picker deletes the active character's `tt2_<characterId>`
  key after an inline two-step confirm (`requestResetProgress` /
  `cancelResetProgress`, gated by `resetConfirmShown`). It then returns to the
  pick screen with that character re-selected, so the full new-user flow (age
  modal → level 1) replays — useful for testing or starting over. Global
  `tt2_settings` is intentionally left untouched. This is accident-resistant
  (hidden, muted, confirm-gated), **not** secure against a determined student.

## Break timer (age-based)

- On first use of a character, an age modal (`openAgeModal`) asks the student's
  age via a −/+ stepper. We store **birth year** (`current year − age`), not the
  age, so it stays correct over time without manual updates. There is no age
  verification.
- `breakMinutesForAge` maps age to a suggested session length before a break:
  ~5 min under age 8, ~10 min ages 8–11, ~15 min for 12+. Younger children
  fatigue and accumulate errors faster, so their sessions are shorter.
- The session timer (`beginSessionTimer`, `maybeSuggestBreak`) is checked at
  exercise boundaries only — never mid-typing — so a break note appears at a
  natural stopping point on the result screen. Toggleable via `breakReminders`
  in the level picker.

## Coaching

- `renderFingerReminder` shows a friendly per-key instruction ("Use your left
  index finger to press F."), spelling out punctuation key names and, for
  capitals, telling the student to hold the opposite-hand Shift. It is larger on
  the home-row group and smaller from the bottom-row group onward.
- A positioning hint (`#position-hint`) appears only while learning the home row
  ("Place your left index finger on F and your right index finger on J. Feel the
  little bumps…"), where hand placement matters most.

## Input handling

- The real `<input>` is visually hidden; the styled prompt box shows progress.
- A global `keydown` handler keeps focus on the input during an active exercise,
  routes `Enter` to "next exercise" when a result is showing, and `Escape` to
  close the level picker. While any modal is open it stays out of the way.
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
small render() consolidation or a framework could be considered (see TO-DO.md:
code review).

---

The running backlog — both unfinished and completed tasks — lives in
[`TO-DO.md`](TO-DO.md).
