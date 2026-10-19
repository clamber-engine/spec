# Level graphics vectors

Synthetic vectors for `../../formats/graphics.md`. All data is made up;
none of it comes from a game. Hex files are bytes in hex, separated by
spaces and line breaks (line breaks have no meaning).

## Packed tile data

`packed/NAME.in.hex` is packed data (the 128-byte pair table, then the
command stream, then two bytes `5a 5a` that come after the end command
and are not part of the data). `packed/NAME.out.hex` is its output.
Every case uses the same pair table.

- `empty`: the end command only; the output is empty.
- `literals`: `00` commands of 63, 1 and 2 bytes, including a literal 0.
- `fills`: `01` commands of 1, 63 and 5 bytes.
- `copy`: `10` commands that don't reach their own bytes, of 16, 63 and
  1 bytes, from positions 8, 0 and 94.
- `copy-overlap`: `10` commands that repeat bytes they have just added
  (a copy of 30 from position 0 of a 3-byte output, and others).
- `pairs`: `11` commands for every pair 0 to 63, then 63 and 0 again.
- `mixed`: about 4 000 bytes of output from random commands of all four
  kinds.

## Tiles

`tiles.hex`: four tiles (32 bytes, one tile per line), then one
two-plane tile (16 bytes, last line). `tiles.txt`: each tile's colour
numbers in hex, one pixel row per line, top row first. Tile 0 holds
colour `x` + 8 × (`y` mod 2) at column `x`, row `y`, so every colour
number appears; tile 1 is all 0, tile 2 all 15, tile 3 random.

## Colours

`palette.hex`: a palette set (8 rows of 16 colours, one row per line).
`palette.tsv`: every colour's row, number in the row, and red, green and
blue values. Colours 5 and 6 of row 0 have bit 15 set (it is ignored).

## Tile entries

`entries.tsv`: tile entries in hex and their fields (tile number,
palette row, raised, flip left–right, flip top–bottom).

## Cell pictures

`pictures.hex`: a picture table of types 0 to 5, one picture (32 bytes)
per line. Type 0 is empty; type 5's entries have flip left–right set in
every other square. `grid.hex`: a grid of 8 columns, one column per line
(32 bytes: 16 cell words, top band first); only band 0 is used, holding
the cell words `0000`, `0001`, `4002` (mirrored), `8003` (bit 15),
`c004` (both), `0005`, `4005` and `c005`. `cells.tsv`: for each 8 × 8 px
square of band 0 (grid picture pixel column `x` and row `y` of its
top-left pixel), the tile entry it holds, in hex.

## Screen pictures

`screen.hex`: a screen map (32 rows of 32 tile entries, one row per
line, each entry low byte first). Its entries use tiles 0 to 3 of
`tiles.hex` only; every 7th entry is 0, and entries 1, 2, 3 and 33 are
tile 0 in palette row 1 with no flip, flip left–right, flip top–bottom
and both. `screen.tsv`: for each square, its square column and row, the
pixel column and row of its top-left pixel, and its entry in hex.
`screen-pixels.tsv`: pixels of the screen picture drawn from
`screen.hex`, `tiles.hex` and `palette.hex`: pixel column and row, the
square's entry, the colour number, the palette row, and the colour in
hex or `transparent`. The first 256 rows cover squares 1 to 3 and 33
completely.

## Front layer (two-plane tiles)

`front-tiles.hex`: four two-plane tiles (16 bytes, one tile per line).
Tile 0 holds colour number `x` mod 4 at column `x` of every row; tile 1
is all 0, tile 2 all 3, tile 3 random. `front.hex`: a front map (32
rows of 32 tile entries, one row per line, each entry low byte first)
using those tiles; entries 0 to 7 are tile 0 in palette rows 0 to 7
with no flip, and entries 8, 9 and 10 are tile 0 in palette row 0 with
flip left–right, flip top–bottom and both. `front-pixels.tsv`: pixels
of the front picture drawn from `front.hex`, `front-tiles.hex` and
`palette.hex` as in `../../formats/graphics.md` ("The first level's
scene"): pixel column and row, the square's entry, the colour number,
the entry's palette row, then the palette set's row and colour the
pixel takes (4 × palette row + colour number, counted through the set)
and that colour in hex, or `-`, `-` and `transparent`. The first 176
rows cover entries 0 to 10 along pixel rows 0 and 5.

## Background layers of the first level (far layer, backdrop, horizon)

For `../../formats/graphics.md`, "The layers". Made with the palette set
of `palette.hex`.

- `far-place.tsv`: for left edges `E`, top rows `T` and picture pixels
  (`i`, `j`), the far picture pixel shown ("Where the far layer is").
- `far-colours.tsv`: palette colours in hex with their red, green and
  blue values, and the colour the far layer shows instead ("The far
  layer's colours"): ten chosen colours, then colours 16 to 127 of
  `palette.hex` (bit 15 cleared).
- `rows.hex`: two row-colour lists, each followed by `5a 5a` (not part
  of the list): first a backdrop list (colour number 0; row counts 1, 3,
  127, 1 and 2), then a horizon list (colour number 1; row counts 68, 1,
  5 and 24). One entry per line; the line with `00` holds the end byte.
  `rows.tsv`: for picture rows 0 to 223, the backdrop's colour on the
  pictures after even frames and after odd frames ("The backdrop"), and
  the horizon layer's colour for colour number 1.
- `horizon.hex`: a horizon block: the horizon map (6 lines of 64 bytes,
  one square row per line) and then six two-plane tiles (one per line).
  Tile 0 is all 0, tile 1 all colour 1, tile 2 all 2, tile 3 all 3, tile
  4 a diagonal of colour 1 from the top left and of colour 2 from the
  top right, tile 5 random. Every entry is palette row 0 with no flip.
- `horizon-shifts.tsv`: band shifts `s0` to `s3` for view values `E`
  and `f` ("The horizon layer").
- `horizon-pixels.tsv`: picture pixels for two views, one after an even
  frame and one after an odd frame: the horizon picture pixel shown (or
  `-` outside rows 64 to 111), its colour number, and the colour on
  screen, using the lists of `rows.hex` and colours 2 and 3 of row 0 of
  `palette.hex`; transparent horizon pixels and rows outside the horizon
  show the backdrop.
