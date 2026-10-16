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

## The second exception: kind fingerprints

Director decision (spec #23, revised after the gatekeeper's review in
#25 and #27). Where an image's data identifies what kind of object
something is only through a reference into the original program, the
importer tells kinds apart by **fingerprint**, and no value from the
original program crosses the firewall:

- A format spec states, in its own words, that the reference leads to
  part of the original program, and how the importer turns the reference
  into a position in the image (using `rom-image.md`).
- It defines the fingerprint exactly: how many bytes the importer reads
  at that position and which hash it computes over them. The hash is
  **SHA-256**, written out in full (64 hexadecimal digits), over **at
  least 32 bytes**, so that the input can't be recovered by trying every
  possibility. A fingerprint whose input could be reconstructed from the
  spec, the vectors or other fingerprint rows (for example because the
  span is mostly predictable) is not allowed.
- `formats/locators.md`, in the section for each supported image, may
  then list **fingerprint → engine-neutral kind** rows (kinds from the
  behavior specs, for example `walker`, `barrel`).
- The spec contains hashes only. Never the reference values themselves,
  never bytes from the image, never what the program does there, no
  labels or names from any disassembly, no original object names.

- Rows are only for kinds whose fingerprints are distinct in the
  supported image.
- Relation to the locator exception above: this exception publishes no
  locations at all and no bytes. The bytes the importer hashes may be part
  of the original program rather than data; they are used only as hash
  input on the player's machine and never appear in the spec.

A row or description that fails any of these rules fails the change.
