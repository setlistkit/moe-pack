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
| `pack.json` | identity: name, version, where the song list came from |
| `vocabulary.json` | the 304 canonical song names -- the dictionary. Names only, never play-counts (see NOTES) |
| `aliases.json` | 70 hand mappings for spellings fuzzy matching can't reach ("elizabeth reed" -> "In Memory of Elizabeth Reed") |
| `classifiers.json` | the rules for "this entry is not a song" (a setbreak, Al's announcements, a reprise) |
| `protected.json` | `ATL` and `NYC` -- real songs a shape rule would otherwise mistake for junk |
| `NOTES.md` | the story behind the aliases and classifiers, and one correction to the source data |

## Check it before you trust it

```
slkit pack lint --pack /path/to/moe-pack
```

That validates the shape, proves every free-floating rule justifies itself, and runs each
rule against the protected titles so a rule can never silently delete a real song.

## License

Public domain, CC0 1.0. A setlist is public knowledge about a public performance, and this
pack keeps it that way -- copy it, learn from it, build your own on top of it. The full text
lives at <https://creativecommons.org/publicdomain/zero/1.0/> (drop it in a `LICENSE` file).

