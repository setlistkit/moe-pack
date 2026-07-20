# moe-pack — agent instructions

This repo is a [setlistkit](../setlistkit) band pack: the canonical song list and normalization
policy for moe., kept as data the tool discovers by config path. It is also a worked example, so
these instructions are committed on purpose (see the `.gitignore` note) -- someone building a
pack for their own band should be able to read this and follow the same path.

## What lives here, and what doesn't

- **Committed:** the five pack JSON files, `NOTES.md` (the why), `README.md`, this file.
- **Never committed:** AI planning docs, design notes, scratch. `.gitignore` keeps
  `docs/specs/`, `docs/plans/`, `docs/design/` out of history. Agent instruction files
  (`CLAUDE.md`, `AGENTS.md`) are the deliberate exception, because they ARE the lesson.
- **License:** CC0 1.0 (public domain). A setlist is public fact.

## Conventions

- **Commits:** terse, why-first, no `feat:`/`fix:`/`chore:` prefixes, no AI co-author trailer.
  Match the setlistkit repo's voice.
- **The pack must lint clean before you trust it:** `slkit pack lint --pack .`. A pack that
  fails lint can silently delete a real song, which is the one thing this format exists to stop.

## How the pack was seeded (one time)

The vocabulary and aliases were extracted once from the old `moe-tour-predictions` repo's
`research/songnorm.py`, then became maintained files with no live dependency:

- **`vocabulary.json`** = the `build_vocab()` name list -- names ONLY, never the play-counts or
  base rates (those are a tracker's derived work, not ours to ship).
- **`aliases.json`** = the `ALIAS` dict, flat, keys sorted.
- **`classifiers.json`** = the `NON_SONG` regex split into its top-level alternatives. A pattern
  that anchors an end (`^x`, `x$`) ships as a bare string; a free-floating one ships as
  `{ "pattern", "why", "must_not_match" }` with the justifying prose moved over verbatim.
- **`protected.json`** = the real songs a shape rule would wrongly delete. `TLH` was dropped
  from the old list here -- see NOTES; it's a checksum tool, not a song.

You do not re-run that extraction. The files are the source of truth now.

## Extending it

- **New alias:** add `"normalized key": "Canonical Name"`. The target MUST already be in
  `vocabulary.json` or lint errors (that's the "Hi & Lo" silent-loss guard). Keys are written
  in normalized form; the loader normalizes them again anyway.
- **New non-song rule:** if it anchors an end, a bare string is fine. If it floats free, it MUST
  carry a `why` explaining the night it became necessary, and a `must_not_match` list of real
  songs it has to leave alone. Anchor when you can; a free-floating rule can reach into a title.
- **New protected title:** only for a real song a shape rule would eat. Verify it against the
  data first -- do not protect something you can't find in the corpus (that's how `TLH` got in).
- After any change, run the lint. Then update `NOTES.md` if the change carries a story worth
  keeping.
