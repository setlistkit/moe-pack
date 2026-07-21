# moe. band pack

This is the [setlistkit](../setlistkit) pack for moe. -- the canonical song list and the
normalization policy that maps a taper's messy note ("Rec Chem", "Z0Z", "white lightning ~>")
onto the one name that IS that song.

setlistkit itself ships no band data. The tool is generic; the knowledge lives here, in data
you own, found by a path in config:

```toml
[catalog]
pack = "/path/to/moe-pack"
```

It also stands as a worked example. If you tape another band and want to build a pack, copy
the shape of this one, read `NOTES.md` for how each decision got made, and read `CLAUDE.md`
for the workflow that seeded it.

## The files

| file | what |
|---|---|
| `pack.json` | identity: name, version, `band_name`, where the song list came from |
| `vocabulary.json` | the 304 canonical song names -- the dictionary. Names only, never play-counts (see NOTES) |
| `aliases.json` | 70 hand mappings for spellings fuzzy matching can't reach ("elizabeth reed" -> "In Memory of Elizabeth Reed") |
| `classifiers.json` | the rules for "this entry is not a song" (a setbreak, Al's announcements, a reprise) |
| `protected.json` | `ATL` and `NYC` -- real songs a shape rule would otherwise mistake for junk |
| `corpus.json` | what moe.'s tapes get wrong, and the residue only moe. tapers write |
| `NOTES.md` | the story behind the aliases and classifiers, and one correction to the source data |

Note that `name` is `moe` and `band_name` is `moe.` — the pack's name and the band's name are
not the same string, and the trailing dot is the half that matters. archive.org puts the band
in the title of every item, which is how a `bob.` tape (the Dylan covers band with two moe.
members in it) gets told apart from a moe. one without anybody guessing from the setlist.

### What is in `corpus.json`

Four kinds of thing, every one of them carrying the reason it is there:

- **`drop_dates`** — three nights that are not evidence about moe.: the Warren Haynes Christmas
  Jam, an Everyone Orchestra all-star improv, and the 2025 Halloween show where the band played
  Monty Python. Each costs us the real songs buried in that night, and each says why that trade
  is right.
- **`date_overrides`** — one item, `moe2024-06-14`, which is really set two of 2025-06-14.
  Believing its metadata invented a show that never happened and buried half of a real one.
  The entry carries all six pieces of evidence, plus the near-identical item that is *not*
  mis-dated, because the tell alone does not tell you which case you have.
- **`junk_patterns`** — cover artists and member surnames a taper writes where the song title
  belongs. These drop the entry outright, and `slkit pack lint` holds every one of them against
  all 304 titles, the 70 alias keys and both protected titles on every run.
- **`gear_patterns`** — `kcy`, `nbob`, `pfa`, `ela`, `cf`: lineage-preamble shorthand carried
  over from the old parser with no recorded explanation anywhere. They are here rather than in
  setlistkit precisely because nobody has been able to say what they expand to, and a
  three-letter token nobody can read is the exact shape that once deleted `ATL`.

## Check it before you trust it

```
slkit pack lint --pack /path/to/moe-pack
```

That validates the shape, proves every free-floating rule justifies itself, runs each rule
against the protected titles, and warns when a `corpus.json` fragment reaches a real title.
A rule cannot silently delete a song in any case: setlistkit refuses to let any rule that
drops an entry touch a title the pack claims, whether it claims it in the vocabulary, the
aliases or the protected list.

## License

Public domain, CC0 1.0. A setlist is public knowledge about a public performance, and this
pack keeps it that way -- copy it, learn from it, build your own on top of it. The full text
lives at <https://creativecommons.org/publicdomain/zero/1.0/> (drop it in a `LICENSE` file).

