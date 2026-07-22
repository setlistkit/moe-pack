# moe. band pack

> This is an example of building an artist pack for [setlistkit](https://github.com/setlistkit/setlistkit/)

This is the [setlistkit](../setlistkit) pack for moe., think it is as the
reference implementation if you are considering writing a builder for your own.
This "pack" has the the canonical song list (which includes covers) and the
normalization policy that maps a taper's messy note ("Rec Chem", "Z0Z", "white
lightning ~>") onto the one name that IS that song.

The point of this is to have a data-driven policy that integrates with
`setlistkit` to auto clean up massive data sets.

`setlistkit` itself ships no band data. The tool is generic; the knowledge lives
here, in data you own, identified by a path in the config file:

```toml
[catalog]
pack = "/path/to/moe-pack"
```

If you tape another band and want to build a pack, copy the shape of this one,
read `NOTES.md` for how each decision got made, and read `CLAUDE.md` for the
workflow that seeded it if you're into that stuff.

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

Note that `name` is `moe` and `band_name` is `moe.` — the pack's name and the
band's name are not the same string, and the trailing dot is the half that
matters. archive.org puts the band in the title of every item, which is how a
`bob.` tape (the Dylan covers band with two moe. members in it) gets told apart
from a moe. one without anybody guessing from the setlist.

That's kind of the magic that this enables in `setlistkit`, sometimes tapers
upload things we wouldn't consider mainline for the band's corpus into the
collection. E.g., moe.stly acoustic sets land in the
[moe.](https://archive.org/details/moe) collection on archive.org, but we don't
want to pull those into the `setlistkit` pipeline, they don't reflect what a
typical night is going to be.

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

## Validation

`setlistkit` is a re-write of my original proof of concept that built
[https://famoe.ly](https://famoe.ly) which was one tangled mess of code and
data. Design principle 1 of the `setlistkit` re-write was 100% separation of
code and data. Thus, there are schemas and very helpull linting and validation
tools included.

```
slkit pack lint --pack /path/to/moe-pack
```

That validates the shape, proves every hand-written rule justifies itself
(matches against at least one raw source item), runs each rule against the,

# License CC0 1.0

Public domain, CC0 1.0. A setlist is public knowledge about a public
performance, and this pack keeps it that way. Copy it, learn from it, build your
own on top of it. The full text of the CC0 1.0 license is available at
<https://creativecommons.org/publicdomain/zero/1.0/>

Respect the API and ToS for websites that provide this kind of data. We don't
ship a pre-made pack for [Phish](https://phish.com/) because we'd be pulling
from the <https://phish.net/> and <https://phish.in/> APIs, and those don't
explicitly say that we can save and re-distribute that data. You can build your
own `setlistkit` artist packs using that data, but they wouldn't be
redistributable.

This pack was crated from the archive.org API using the `politeclient` in the
`setlistkit` tooling to be respectful of their
[bots](https://archive.org/developers/bots.html) and automation policy.

I can not give enough thanks to the hundreds of tapers out there who have put in
the effort to record, process, tag, and upload these live music recordings over
the decades. You keep this alive for fans of all generations and are the only
reason this project was possible.
