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

## Row grids

Vectors for "Row grids" in the format. They use the same `types.hex`
(`N` = 38).

- `rows-grid.hex`: a row grid of 8 bands, one row per line (band 0
  first): the 128 bytes of the row's 64 cell words, low byte first.
- What the data exercises:
  - Band 2, columns 0–31: column `n` holds type `n` + 1, so every shape
    appears at every position, as in `grid.hex`. Columns 0 and 16 are
    multiples of 16.
  - Column 32: a mirrored cell (band 3) and a cell with bit 15 set
    (band 4). Column 33: bit 6 (band 2), mirrored (band 3), a type of
    `N` or more (band 4), a full cell (band 5).
  - Column 40: full cells in bands 1 and 2, flat ground in band 5.
    Column 41: a cell with a surface in its right half only (band 1)
    over a full cell (band 2), and in band 4 a cell whose right half is
    the band's top line.
  - Columns 48 (a multiple of 16) and 49: the same ground, a full cell
    in band 3 and flat ground in band 5, to show the skipped step 3.
  - Column 63, band 4: flat ground, read from band 3 at level x 4064 to
    4095.

Expected output:

- `rows-surfaces.tsv`: for every level x from 0 to 4095, the surface
  line in each band 0–7, or `none`. From level x 2048 on, band 7's
  cell would lie past the end of the grid; those are `past` (not
  covered).
- `rows-ground-below.tsv`: the ground below for 15 values of `x`. For
  `x` below 2048, every feet line `F` from 1 to 192 (bands 0–5). For
  `x` of 2048 or more, `F` from 33 to 160 (bands 1–4), so the search
  stays inside the grid and off band 0's top line. Each value is a line
  or `none`.
