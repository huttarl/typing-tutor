# Typing Tutor

A free, progressive touch-typing tutor for children — built as a single
self-contained HTML file. No login, no accounts, no server, no build step.
Open `typing-tutor.html` in a browser and start typing.

## What it does

- **Progressive, finger-by-finger curriculum.** Keys are introduced one or two
  at a time (never a whole row at once), grouped by physical row: home row →
  top row → bottom row. Each group ends with real-word practice using only the
  letters learned so far.
- **Real reading material.** As more letters are unlocked, practice text moves
  from letter drills to real words and then complete sentences drawn from Jane
  Austen, Charles Dickens, and the Bible (World English Bible — public domain,
  modern English). Sentences progress from short to long.
- **Accuracy first, speed later.** Early levels are scored on accuracy alone.
  Words-per-minute is hidden until all letters are learned and the student has
  practiced real sentences — so beginners focus on correct technique, not speed.
  Accuracy counts every mistake, even ones fixed with backspace, to discourage
  reflexive correcting.
- **On-screen guidance.** A finger reminder names the correct hand and finger
  for each key; an SVG hand diagram lights up the right finger; and a full
  keyboard diagram highlights the next key (and the Shift key for capitals).
- **Pick-a-character profiles (no login).** Up to several students share one
  browser. Each chooses an animal character, and their progress is saved
  separately on that device. Nothing is sent anywhere.
- **Level picker with skip-ahead.** Levels are grouped and show completion
  status. A "lock levels" setting (on by default) keeps students on the path;
  turn it off to jump to any level — useful for a student who already has some
  skills, or for testing.
- **Age-aware break reminders.** Each student enters their age once (stored as
  a birth year, so it stays current). After an age-appropriate amount of
  practice (~5–15 minutes), the app gently suggests a break at a natural
  stopping point. Toggleable.
- **Audio feedback** for correct and incorrect keystrokes.

## Getting started

1. Download `typing-tutor.html`.
2. Open it in any modern web browser (desktop, with a physical keyboard).
3. Pick a character and begin. Progress saves automatically in that browser.

No installation or internet connection is required to run it. (An internet
connection is only used, if available, to load nicer fonts; the app falls back
to system fonts offline.)

## Privacy

There are no accounts and no network calls for app data. All progress is stored
locally in the browser via `localStorage`, keyed to the chosen character. To
keep progress separate, each student should consistently pick their own
character.

## For developers

The entire app — HTML, CSS, and JavaScript — lives in `typing-tutor.html` with
no dependencies or build tooling. The curriculum, word lists, and sentences are
plain data tables near the top of the script and are meant to be easy to edit.

See [`DESIGN.md`](DESIGN.md) for the architecture and the rationale behind the
curriculum and scoring decisions, and [`TO-DO.md`](TO-DO.md) for the backlog.

## Status

A work in progress (not yet released). Planned work (see [`TO-DO.md`](TO-DO.md) for the full list)
includes more repetition on the foundational levels, teaching punctuation and
the Shift key explicitly, full offline support, and an optional free-form mode
with longer passages from classic literature and poetry.

## License

See [`LICENSE`](LICENSE). The bundled sentence material is from public-domain
sources (Austen, Dickens, the World English Bible, etc.).
