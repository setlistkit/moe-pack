# Notes: the why behind the moe. pack

The JSON files are what setlistkit reads. This file is what they can't hold: the story of why
each awkward entry is the way it is. Every one of these cost a debugging session at least
once, and a clean-room rewrite would drop them silently. They came from comments in the old
`moe-tour-predictions` repo's `songnorm.py`; this is where they live now.

## The vocabulary

`vocabulary.json` is 304 canonical song names, seeded once from public setlist data (setlist.fm's
year-stats, the cleanest name list to hand). **Names only.** A setlist -- the songs played,
their order, the segues -- is public knowledge about a public performance. What a tracker
*computes on top of* it, the play-frequency averages and base rates, is that tracker's derived
work, and none of it is in here. That line is the whole reason setlistkit exists.

## Aliases

An alias exists when two spellings are one song and no amount of fuzzy matching will connect
them, because the strings are too far apart or a real song sits between them.

**The phantom song.** `"bud" -> "Bring You Down"`. This one used to map `bud -> Bud` on the
belief that Bud was a separate recurring original. It minted a song that occurs exactly once in
the whole corpus, and that once is 2023-07-20, where two tapers cover the same night and the
same slot between Billy Goat and Nebraska: one writes "04 - Bud", the fuller one writes
"09. Bring You Down". B(ring yo)U D(own). One song, not two.

**Shorthand.** Setlist writers and tapers drop the qualifier and write what people say out loud:
nobody says "Waiting for the Punchline" twice in a row, they say "Punchline". Every shorthand
target is the canonical setlist.fm spelling, and eight of these pairs were being drawn as two
separate songs on the range plot -- "In Memory of Elizabeth Reed" four rows above "Elizabeth
Reed". Fuzzy matching cannot and should not try these: "kyle" against "kyles song" is a long way
apart as a string, and a cutoff loose enough to catch it would merge things that differ by one
real word. Shorthand is knowledge about the band, so it is written down by hand.

**Misspellings that sit just outside the cutoff.** "lazurus" -> "Lazarus", "crashing" ->
"Crushing". A couple of these are subtler: "Rickey Martin" and "Eyed Serpent" used to ride in on
a fuzzy match against a MISSPELLING that setlist.fm itself carried. Folding those bad spellings
into their real songs removed the anchors, and both then fell just outside the 0.9 cutoff (0.880
and 0.800) and became songs of their own. Written down by hand, which is where knowledge about
the band belongs.

**One Breathe.** "breathe in the air" and "breathe reprise" both map to "Breathe". setlist.fm
splits the Pink Floyd cover into "Breathe (In the Air)" and "Breathe (Reprise)" -- both are Dark
Side of the Moon, both are the same cover, and holding them apart gives two songs with three
plays each instead of one with six. NOT folded in: "Just Breathe", which is Pearl Jam, a
different song that happens to share a word.

**The ampersand that belongs.** "hi and lo" -> "Hi & Lo". The `&` is part of the title; the
setlist.fm vocab happens to miss it.

## Classifiers: is this even a song?

There is exactly one answer to that question, and it lives in `classifiers.json`. The old repo
had two -- the duration pipeline knew "Al.nouncements" and "Encore Break" were not music while
the predictor did not, and 79 entries of stage business sat in the play history across 67
nights. One pack gives everyone one answer.

A rule that pins an end of the string (`^setbreak$`, `intros$`) ships as a bare string -- it is
narrow by construction and can't reach into a title. A rule that floats free (`nounc`, `reprise`)
can reach into the middle of a real song, so it MUST carry a `why`. A few worth calling out:

- **`nounc`** catches Al's between-song announcements in every spelling a taper reaches for:
  "al.nouncements", "Al.nouncments", "al.nouncement". Matching "unceme" instead would miss the
  spelling with no 'e' after the 'c'. And **`nownc`** is the one that STILL got past it:
  "Alnowncements", squashed to "alnowncements", with a 'w' where the 'u' belongs. It had become
  a song with n=1 and a median of 85 seconds.
- **`intro`** is a minefield. There is an anchored `^intro` and `intro$` and a plural `intros$`,
  but a bare free-floating "intro" is deliberately OFF LIMITS -- it would swallow any real song
  with Intro in its name. setlist.fm's own dictionary carries a bare "Intro" (1 of its 313
  entries) as a data-entry habit for walk-on music; the rule drops that entry too, on purpose.
- **`reprise`** takes the setlist at its word. "Moth (reprise)" is real music but it is not a
  performance of the song, and it drags the median down exactly like the unlabelled reprises we
  have to catch by ear.
- **`introduction`** is a hype MC introducing the band. He does it with very consistent timing,
  which is what makes it dangerous: it looks like a tight, reliable 90-second opener, and a
  length model would believe it.

## One correction to the source data: TLH

The old `songnorm.py` protected three all-caps titles from a shape rule that once deleted them:
`ATL`, `TLH`, `NYC`, described in a comment as "all real moe. songs." Two of those are right.
The third is not.

- **ATL** is a real song. It's in the setlist.fm list and it appears in the corpus. Protected.
- **NYC** is shorthand for **New York City**, which is a real song; the alias handles the
  mapping, and protecting `NYC` keeps a taper's `(NYC)` from being dropped before the alias can
  reach it. Protected.
- **TLH** is not a song. It never appears as a setlist entry anywhere in the corpus, and the old
  repo's `parse_archive.py` lists `tlh` alongside `ffp`, `md5` and `shntool` -- it's **Trader's
  Little Helper**, the FLAC-checksum tool tapers run. It got grouped with ATL and NYC because it
  LOOKED like them, a bare three-letter all-caps token. It's dropped from `protected.json`.

(For the record: a passing suggestion that TLH stood for "The Last Hurrah" turned out to be
made up -- that title appears nowhere in the data. Checked against the corpus before believing
it, which is the whole point.)

## Three rules deleted for having no reason

`a\s+team` and `home\s+team` (junk) and `cf` (gear) came over from the old parser's word lists
and shipped here carrying an admission instead of an explanation: nobody could say what they
were for. Each one's `why` ended by asking a future reader to check it against the corpus and
delete it if it turned out to be dead.

They are deleted now, without waiting for that check.

The rule this pack follows from here: **a filter that cannot say why it exists does not get
grandfathered in.** Keeping one is not the cautious option, it is the expensive one. Every rule
in this file DROPS what it matches, which means it removes an entry without recording that
anything was there, and the vocabulary guard only protects titles the pack already names -- so
the only thing an unexplained rule can eat is a song nobody has written down yet. That is
precisely the class of loss nothing downstream can detect or recover. "We are not sure what this
does" and "this silently deletes data" are a bad pair to hold at once, and the tie goes to
deletion.

`a\s+team` also turned out to be wider than it looked. Bounded by non-word edges it still matches
"a team" inside any ordinary phrase -- `what a team` matches -- which is a lot of reach for a
rule with no stated purpose.

The four remaining unexplained gear tokens (`kcy`, `nbob`, `pfa`, `ela`) are deliberately kept.
They are not in the same position: what is missing for them is the *expansion*, not the reason.
Each one has a recorded observation behind it -- it appeared in this band's taper lineage
preambles, which is a preamble and not a setlist. That is a why, even if a thin one. `cf` went
with the other two because it is two letters, which is the shape that once deleted ATL and NYC.

**Back pocket.** If unexplained junk starts showing up in the corpus later -- a title that reads
like a taper's aside, something involving the words "team", or a stray `cf` in a lineage line --
this is the first place to look. The deletions were a deliberate call made without evidence
either way, and re-adding one with an actual example attached would be a better entry than any
of the three that were removed.
