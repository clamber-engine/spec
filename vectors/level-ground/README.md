# Level ground vectors

Synthetic vectors for `../../formats/level-ground.md`. All data is made
up; none of it comes from a game.

## Input

- `types.hex`: the type table, one type per line (type 0 first): the left
  half byte, then the right half byte, in hex. `N` = 38 (the number of
  lines).
- `grid.hex`: the grid, one column per line (column 0 first): the 32
  bytes of the column's 16 cell words, top band first, low byte first.
  37 columns, level x 0 to 1183.

What the data exercises:

- Columns 0–31, band 8: column `n` holds type `n` + 1. Types 1–32 have
  shape 2(`t` − 1) in the left half and shape 2(`t` − 1) + 1 in the right
  half, so every shape from 0 to 62 appears at every position (type 32's
  right half is 0: shape 63 is not defined).
- Column 32: a mirrored cell (band 8) and a cell with bit 15 set (band 9)
  of a type whose right half is flipped.
- Column 33: a type with bit 6 set in both halves (right half flipped),
  plain (band 8) and mirrored (band 9); a type of `N` or more (band 10);
  a full cell (band 11).
- Columns 34 and 35: full cells (rise 31) under empty cells, under full
  cells and under a cell with a surface in one half only. Full cells in
  bands 0 and 1 give "above the level". The search two bands down finds
  ground in band 15 from band 13, and none from band 12.
- Column 36: a type with bit 6 set in its left half only, plain
  (band 8), mirrored (band 9), with bit 15 set (band 10) and mirrored
  with bit 15 set (band 11); band 12 holds the type of column 33.

## Expected output

- `surfaces.tsv`: for every level x from 0 to 1183, the surface line in
  each band 0–15, or `none`.
- `passable.tsv`: for every level x from 0 to 1183 and each band 0–15,
  `yes` if the surface is passable from below, `no` if it isn't, or
  `none` if there is no surface.
- `ground-below.tsv`: the ground below for 12 values of `x` (in columns
  32–35) and every feet line `F` from −8 to 520. Each value is a line,
  `above` (above the level) or `none` (no ground).
