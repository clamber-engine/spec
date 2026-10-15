# Sound data vectors

Synthetic vectors for `../../formats/sound.md`. All data is made up;
none of it comes from a game. Written and re-checked against the prose
by the research side's generator, and each sample's outputs also
checked against an emulator's decoding of the same made-up blocks.

## Input

- `bank.hex`: part of a made-up bank, 16 bytes per line, each line
  starting with the address of its first byte (hex). Locations in it
  use bank number `5b`. In this bank:
  - the sample table starts at address `8000` and has samples 0 to 5;
  - the shared sample list starts at `8040`;
  - the sample-set table starts at `8050` and has sets 0 and 1;
  - the song table starts at `8060` and has songs 0 and 1.
  Filler bytes (`ee`) lie between and after the data; a reader must not
  reach them.

What the data exercises:

- Sample 0: one-shot; every shift 0 to 15 with filter 0, then filters
  1 to 3; one left-over byte after the last whole block.
- Sample 1: one-shot; outputs that hit the limits (above 32 767 and
  below −32 768) with filters 2 and 3; no left-over bytes.
- Sample 2: loops from block 0; `s` from 16 384 to 32 767 and below
  −16 384, so outputs change sign when brought into range.
- Sample 3: loops from block 2, with filters 2, 3 and 1 in the loop
  blocks, so the first pass and every loop pass differ.
- Sample 4: loops from block 1 (filter 0, then filter 1), so every loop
  pass equals the loop blocks' part of the first pass; one block and two
  bytes after the last block that are never decoded.
- Sample 5: a single block with shift 14 (steps both negative and not).

## Expected output

- `samples.tsv`: for each sample: number of whole blocks, left-over
  bytes, last block, whether it loops, loop block, number of outputs in
  the first pass, and number of outputs in one loop pass (`-` for
  one-shot samples).
- `decoded.tsv`: each sample's outputs, space-separated: the first pass
  (`first`), then, for looping samples, loop passes 1 to 6 (`loop1` to
  `loop6`), each starting where the one before ended.
- `song-samples.tsv`: the sample numbers each song has, in order:
  shared list, then the song's set.
- `songs.tsv`: each song's base address (hex), length and data (hex).
