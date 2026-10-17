# Level ground vectors: `title-2`

Synthetic vectors for "In `title-2`" in
`../../../formats/level-ground.md`. All data is made up; none of it
comes from a game.

## Input

- `types.hex`: the type table, one type per line (type 0 first): the
  four bytes of the left half word and then the right half word, each
  low byte first, in hex. `N` = 40 (the number of lines).
- `map.packed.hex`: the map, packed as in
  `../../../formats/nibble-packing.md` (32 bytes per line). Unpacked, it
  is 38 map columns of 16 cell words (1216 bytes).
- The level's grid is map columns 1 to 36 (`m` = 1, 36 columns), so it
  covers level x 256 to 1407. Map columns 0 and 37 are full of cells of
  type 34 (full cells); they are not part of the grid and must not show
  up in the results.

What the data exercises (grid columns; grid column `n` is map column
`n` + 1):

- Columns 0–31: band 8 holds type `n` + 1 and band 9 type `n` + 1
  mirrored. Types 1–32 have shape 2(`t` − 1) in the left half word and
  2(`t` − 1) + 1 in the right one (type 32's right half word is 0), so
  every shape from 0 to 62 appears at every position, both ways round.
  Band 10 holds types 33 to 38 in turn, some mirrored and some with bit
  15 set in the cell word.
- Types 33–38 use bit 14 (passable from below) and bit 15 (flipped) in
  half words: type 33 = 0x4005, 0xC00C; type 34 = 0x0001, 0x0001 (a full
  cell, rise 31); type 35 = shape 61 in the left half only; type 36 =
  0x800C, 0x4011; type 37 = 0x0039, 0x803B; type 38 = 0x003C, 0x8030.
  Type 39 has no ground.
- Columns 32–35: full cells under full cells and under empty cells, full
  cells in bands 0 and 1 ("above the level" both ways), a cell of type 45
  (`N` or more), a cell with bit 15 set, ground in band 15 and band 14,
  and a column whose bands 0 and 1 hold a half-full cell and a full cell.

## Expected output

- `surfaces.tsv`: for every level x from 256 to 1407, the level y of the
  surface in each band 0–15 (line + 256), or `none`.
- `passable.tsv`: for every level x from 256 to 1407 and each band 0–15,
  `yes` if the surface is passable from below, `no` if it isn't, or
  `none` if there is no surface.
- `ground-below.tsv`: the ground below for 12 values of level x (3, 17
  and 30 px into grid columns 32–35) and every feet level y from 240 to
  780 (feet lines −16 to 524). Each value is a level y, `above` (above
  the level) or `none` (no ground).
