# Placement vectors

Synthetic vectors for `../../formats/placements.md`. All data is made
up; none of it comes from a game.

## Input

- `bank.hex`: part of a made-up bank, 8 bytes per line, each line
  starting with the address of its first byte (hex). The placement list
  starts at address `9008`. `Yb` = 512.

What the data exercises:

- Entries of classes 1, 2, 5 and 8, two entries with the same `x`, and
  two with the same reference.
- A group (entry 3) whose reference is `9050`: its members start at
  `9058`. The 8 bytes at `9050` are not zero here, and are not part of
  the group. One member's `x` is smaller than the group entry's.
- Entry 6 comes after an entry with a larger `x`.
- Heights of 0 and 512, an `x` near the top of the 16-bit range, and a
  reference of `ffff`.
- Filler bytes (`ee`) between and after the lists, which a reader must
  not reach.

## Expected output

- `entries.tsv`: every entry of the list in stored order (`index` from
  0), each group's members right after the group entry (`index` `i.j`
  for member `j` of entry `i`, `group` = `i`). `level_y` = 512 − `h`;
  `r` in hex.
- `appear.tsv`: with the view moving right 3 px a frame, `V` = −40 + 3`n`
  at the start of frame `n` (frame 0 is the level's first frame), the
  frame on which each object appears and its `V`, or `never` within
  frames 0–299. Class 8 places nothing. Entry 6 is left out: when an
  entry that is out of order appears is not covered.

## Kinds

Vectors for "Kinds" in `../../formats/placements.md`.

### Input

- The **image**: 3 bank blocks (196 608 bytes), not stored. The byte at
  position `p` (0 to 196 607) is (37`p` + ⌊`p` / 2048⌋) mod 256. Its
  SHA-256 is
  `a6ba56c90978aa11c57f493b12e84636df5e08f726485164e53537cc69aaa433`.
- `kinds-rows.tsv`: made-up kind fingerprint rows for this image, as a
  `locators.md` section would list them.
- References to look up: `8100`, `9000`, `9010`, `ffe0` and `4000`.

What the data exercises:

- Rows made from bank blocks 0, 1 and 2, and two rows with the same
  kind.
- Reference `ffe0`: in block 1 its 64 bytes run on into block 2; in
  block 2 they would run past the end of the image, so block 2 gives no
  fingerprint.
- Reference `4000` matches no row.

### Expected output

- `kinds-candidates.tsv`: every candidate fingerprint, one line per
  reference and bank block, or `none`.
- `kinds.tsv`: for each reference, the block whose candidate matched a
  row and the row's kind, or `-` and `none`.
