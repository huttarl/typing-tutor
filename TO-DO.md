# Typing Tutor — To-Do / Backlog

A single running checklist. Finished tasks are checked (`[x]`); unfinished tasks
are unchecked (`[ ]`). No need to move items between sections when they're done —
just check the box. See [`DESIGN.md`](DESIGN.md) for architecture and rationale.

## Pedagogy / content

- [ ] **More reps on foundational levels.** Research says home-row levels need
      more repetition than later ones. Make early levels require more completed
      exercises before advancing (currently a flat 3 across all levels).
- [x] **Optional per-session break timer.** Suggests a break at an
      age-appropriate mark (~5 min under age 8, ~10 min ages 8–11, ~15 min age
      12+). Age captured once per character (stored as birth year); toggleable
      via the level picker; break shown only at exercise boundaries.
- [ ] **Teach punctuation keys** at pedagogically appropriate points (e.g.
      comma and period relatively early; then apostrophe, etc.). Note: comma and
      period are already typeable in the sentence levels, but are not yet
      *taught* with their own drill/finger introduction.
- [ ] **Teach the Shift key explicitly** — decide when (after lowercase letters
      are solid?) and how (dedicated capital-letter levels; teach the
      opposite-hand Shift rule). Note: capitals already appear in sentences and
      the coaching/keyboard already highlight Shift, but there is no dedicated
      Shift-teaching level yet.
- [ ] **Curated Bible passages.** Allow focusing sentence practice on specific
      passages the family wants to learn.
- [ ] **Show purpose of level.** At top of screen during exercises, show a brief
      purpose statement like "Add v and n" or "practice home-row words" or "review
      letters from top two rows." This helps motivation by connecting activity to progress.
      This purpose statement should be separate from, and not replaced by, the
      letter-by-letter instructions during the typing of the exercise.
      (I think it's already being displayed, corresponding to "label", but is
      being replaced by other instructions during typing.)
- [ ] Increased the word bank to include more variety. Use classic authors / poets
such as Shakespeare, Hawthorne, Poe (but make sure the spelling is correct by modern standards).
- [ ] Go through the attributed quotes and remove the ones that dishonor the original quote by cutting it too short. E.g. "It is a truth universally acknowledged." Or else extend them to the full original quote; or at the very least, use an ellipsis instead of just ending it with a period.
- [ ] When practicing "real words" levels, prevent repetition of the same word anywhere within the exercise.
- [x] When practicing actual sentences or longer passages from literature, display the title
and author. (Not sure if the student should be asked to type these.)

## Features

- [ ] **Free-form practice after the structured levels.** Give longer passages
      (a paragraph+) from Dickens, Austen, the Bible, Shakespeare, and classic
      poetry (e.g. Poe). May fetch passages online so the student isn't given
      the same text repeatedly — an *optional, internet-requiring* feature, kept
      separate from the offline core.
- [x] **Reset a character's progress.** Somewhat hidden; requires confirmation.

## Technical / quality

- [ ] **Offline support.** Ensure the app runs fully from a `file://` path with
      no network. Add a local font fallback stack (or bundle fonts) so the
      Google Fonts `<link>` failing offline degrades gracefully. No dynamic data
      fetching in the core app.
- [ ] **Code review** covering: security, intuitive UI, code efficiency, and
      code readability. (E.g. the earlier `SORTED_SENTENCES` slip — a reference
      left after its definition was removed — is the kind of thing a review or a
      lightweight lint/test pass should catch. Consider a small build-time check
      or moving logic into testable functions.)
- [x] **Decide on the third-party banner script.** Whether to include
      `https://keepandroidopen.org/banner.js`; if so, weigh its access to the
      page/localStorage and its conflict with the offline goal (perhaps only on
      a hosted copy, not the offline app the children use).
- [ ] Words from great books shouldn't be stripped of their capitalization.
      And words that require punctuation, like "Mrs.", shouldn't be stripped of it.

## Uncategorized / early history

- [x] Per-character progress via localStorage; no login.
- [x] One-/two-keys-at-a-time progression; row-based groups; corrected so G/H
      are home row and the full top row (incl. q w o p) is one group.
- [x] Real-word review level at the end of each group, with verified word lists.
- [x] Top-row-only word level + top+home word level.
- [x] WEB (modern, public-domain) Bible verses added to sentences; sentences
      pre-sorted shortest→longest in source.
- [x] Finger hint + SVG hand diagram showing the correct finger; Shift
      highlighted for capitals; realistic keyboard layout fixes row alignment.
- [x] Friendlier coaching sentences ("Use your left index finger to press F.")
      plus a home-row positioning hint.
- [x] Keystroke-based accuracy that counts corrected (backspaced) errors.
- [x] Level picker with grouped levels, independent completion tracking, and a
      lock-levels toggle (allows skipping ahead).
- [x] Character grid distinguishes started vs. unused characters.
- [x] Enter advances to the next exercise.
- [x] Save-progress hint; "Continue with" vs "Start with" button text.
- [x] Rename curLi() and getLv() to be more readable, e.g. currentLevelIndex (if that's what it means) and getLevel.
- [x]Allow the beginning flow of selecting a character and starting with that character to be accomplished with a double-click, instead of having to click in two different places (while still allowing the current method of selecting the character and then clicking the Start button).
- [ ] Maybe split out the js code from the HTML file? That would simplify code changes, but
would also degrade portability. Leave it alone for now.
- [x] Adjust color-coding of fingers to avoid the pinkish color which is too close to red and suggests a problem.

