# Typing Tutor — To-Do / Backlog

A single running checklist. Finished tasks are checked (`[x]`); unfinished tasks
are unchecked (`[ ]`). No need to move items between sections when they're done —
just check the box. See [`DESIGN.md`](DESIGN.md) for architecture and rationale.

## Pedagogy / content

- [x] **More reps on foundational levels.** Replaced the flat "3 exercises"
      gate with mastery-based progression (`masteryGoal`/`passStreak`): advance
      only after 3 consecutive exercises clear a per-group accuracy bar (Home row
      95%, Top/Bottom 92%, rest 90%); a miss resets the streak. The higher bar on
      foundational rows means they take more attempts, self-adjusting per student.
      NOTE: the "research says" claim was overstated — this rests on
      mastery-learning/overlearning principles, not a specific cited study; verify
      sources before leaning on the claim in docs.
- [x] **Optional per-session break timer.** Suggests a break at an
      age-appropriate mark (~5 min under age 8, ~10 min ages 8–11, ~15 min age
      12+). Age captured once per character (stored as birth year); toggleable
      via the level picker; break shown only at exercise boundaries.
- [x] **Teach punctuation keys** at pedagogically appropriate points (e.g.
      comma and period relatively early; then apostrophe, etc.). Note: comma and
      period are already typeable in the sentence levels, but are not yet
      *taught* with their own drill/finger introduction.
- [x] **Teach the Shift key explicitly** — decide when (after lowercase letters
      are solid?) and how (dedicated capital-letter levels; teach the
      opposite-hand Shift rule). Note: capitals already appear in sentences and
      the coaching/keyboard already highlight Shift, but there is no dedicated
      Shift-teaching level yet.
- [ ] **Curated Bible passages.** Allow focusing sentence practice on specific
      passages the family wants to learn.
- [x] **Show purpose of level.** At top of screen during exercises, show a brief
      purpose statement like "Add v and n" or "practice home-row words" or "review
      letters from top two rows." This helps motivation by connecting activity to progress.
      This purpose statement should be separate from, and not replaced by, the
      letter-by-letter instructions during the typing of the exercise.
      (I think it's already being displayed, corresponding to "label", but is
      being replaced by other instructions during typing.)
- [ ] Increased the word bank to include more variety. Use classic authors / poets
such as Shakespeare, Hawthorne, Poe (but make sure the spelling is correct by modern standards).
- [x] Go through the attributed quotes and remove the ones that dishonor the original quote by cutting it too short. E.g. "It is a truth universally acknowledged." Or else extend them to the full original quote; or at the very least, use an ellipsis instead of just ending it with a period. This will require checking quotes.
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
- [x] Words from great books shouldn't be stripped of their capitalization.
      And words that require punctuation, like "Mrs.", shouldn't be stripped of it.
- [ ] When learning / practicing individual characters, I don't think it makes sense to have more than 2 in a row of the same character. Because that almost never happens in English... or in coding.
Except for ellipses...!
- [ ] When presenting whole sentences and passages, make sure not to repeat the same passage
      twice in a row.
- [ ] We'll probably have to check our quotes. I found at least one misquote. Consider wikiquotes to be authoritative.
- [x] Maybe add a "Redo" button at the bottom next to "Next" that allows the user to redo the exercise they just did, if desired.
      Re-runs the same prompt (and citation); counts as a normal attempt (affects the streak like any exercise). Hidden after a level-up.
- [ ] Maybe add a confetti/star "explosion" over the page when the student completes an
exercise with 3 stars. Or a small "celebration" for each 3-star exercise complete, and a bigger celebration for completing a level. Both with appropriate sound effect.
      - enforce a pause after completing a level, before displaying the next one. This makes sure the student is aware of having completed something! That pause could be enforced simply by means of the "celebration."
- [x] Level 4 says "Add A and ;", but the first two exercises have only one 'a' and no ';'
      Not sure if this is a more general problem, but if you're adding certain keys,
      the new keys should (both/all) be used frequently in the exercise.
      Fixed in the drill generator: it now computes each level's newly-added keys (vs. the
      previous level) and weights them to ~half of each drill. Also restored ';' to drills —
      the old letter-only filter dropped it, so "Add A and ;" never practiced it.
- [ ] Add a link from the home page to the source code repo.
- [ ] The first time the 'i' key is introduced, it's un-greyed-out but its outline isn't color coded according to what finger you're supposed to use. I'm sure it's not just 'i' either.
- [ ] When a prompt wraps at a space, and it's time to type that space, the usual cursor that shows what to type next is not visible.
- [x] Add more common punctuation: hyphens, colons & semi-colons, apostrophes and double-quotes, question mark, exclamation mark, slash. Put them into the lessons & exercises,
and add some sentences from literature that include these punctuation.
- [x] Add a favicon.
- [x] The "Bottom + home row words" level includes some words with letters from the top row, and some words with
      only one or zero letters from the bottom row. Instead, the words in this level should not include letters from the top row
      at all, and should include at least two letters from the bottom row.
      Rebuilt the `bottomHome` list (33 words) — script-verified: no top-row letters, ≥2 bottom-row letters each.
      (Broader principle "each exercise should drill the level's keys" also applies to the random-drill levels — see the
      "Level 4 says 'Add A and ;'…" item, still open.)
- [x] Put the "real words" (now "Mixed words") level before the punctuation level.
      It comes before the Capitals lesson too, so it's kept all-lowercase via the
      `allowCaps` filter and a no-punctuation key set; `Mr.`/`Mrs.` are practiced
      on the Comma-and-period level instead.
- [x] Save vertical space by:
      - shortening the hand diagrams; only the fingers really matter. Shorten the bottom of the palms.
      - reducing space between the typing row (where the cursor is) and the keyboard diagram.
      - putting the accuracy and level boxes beside the hand diagrams instead of below them.
      - Getting rid of the bottom box:
            - Put the "*** Excellent!" message, along with "Enter or click Next -->" into the instruction box (where it says "Use your left pinky to ...")
            - Put the Next button next to the "Level" box
- [ ] Make the sound for wrong key less startling.
- [x] Make the "level - n stars" display on the start screen for each character clearer. Two of
      us users thought "14⋅23⭐" was saying "14/23", that is, the character is on level 14 of 23.
      The star could be more contrasty with the background; the number of stars could be more separated from the level number, e.g. "14 (23⭐)"; or we could even remove the number of stars.
- [x] visible streak indicator: the student isn't told "2 of 3 in a row." With a streak that resets on a miss, that opacity matters more. A small "🔵🔵⚪" on the result screen would make progress legible

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

