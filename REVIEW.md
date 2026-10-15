# Gatekeeper review checklist

A spec change describes **what happens**, never **how the original
program does it**. Before approving, confirm the change contains none of:

- [ ] Memory addresses, bank/offset locations, ROM offsets, or pointers,
      except in a locator table as allowed below
- [ ] Routine names, labels, or variable names from any disassembly
- [ ] Step-by-step paraphrases of code ("load X, compare with Y, branch...")
- [ ] Bytes copied from the ROM, including as "examples" (vectors must be synthetic)
- [ ] Assets or transcriptions of creative content (level layouts, graphics, music)
- [ ] Original character names, item names, or other trademarked terms

And that it does contain:

- [ ] Behavior in observable, engine-neutral terms (units, frames, conditions)
- [ ] Format descriptions as structure (fields, encodings, algorithms in prose)
- [ ] Synthetic vectors for any decoder it specifies

## The one exception: locator tables

Director decision (spec #21). An importer can't find data in an image
without knowing where it starts, so a format spec may include a
**locator table**, and nothing else location-shaped:

- It lives in `formats/locators.md`, with one section per supported
  image, identified by the image's hash as `rom-image.md` defines it.
- Each row names **one kind of data** (for example "level placement
  lists: table of per-level entries") and **where that data starts** in
  that image, in the form `rom-image.md` defines. Counts and entry sizes
  belong in the format spec for that kind of data, not here.
- Only starting points of **data** described by a format spec. Never the
  location of code, routines, or anything the importer doesn't read as
  data.
- No bytes from the image, no labels or names from any disassembly, and
  no comments about how the original program uses the data.

When reviewing a locator table, check each row against these rules; a
row that fails any of them fails the change.
