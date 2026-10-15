# Level graphics

Titles: `title-1`

How the first level's background pictures are stored, and how to turn
them into images: colours and palette rows, tiles, packed tile data,
tile entries, cell pictures, and the two background layers built from
them. The **grid**, its **cells**, **cell words** and **types** are as
in `level-ground.md`: the picture of a cell comes from the same cell
word as its ground. Everything here was checked against the game's own
video output on the first level (see "Evidence").

## Colours

A **colour** is 16 bits, stored low byte first:

| Bits | Meaning |
|---|---|
| 0–4 | Red, 0 to 31 |
| 5–9 | Green, 0 to 31 |
| 10–14 | Blue, 0 to 31 |
| 15 | Ignored |

31 is full intensity, 0 is none; each channel's intensity is its value
divided by 31.

A **palette row** is 16 colours, numbered 0 to 15, stored in order
(32 bytes). Colour 0 of a row is never drawn: a pixel of colour 0 is
transparent, whatever value colour 0 holds.

A **palette set** is 8 palette rows, rows 0 to 7, stored in order
(256 bytes).

## Tiles

A **tile** is 8 × 8 px. Each pixel holds a colour number 0 to 15, the
colour in the palette row that the tile is drawn with (see "Tile
entries").

A tile is stored in 32 bytes as four **planes**. Each plane holds one
bit of every pixel's colour number: plane `p` holds bit `p` (plane 0
the lowest bit). For pixel row `y` (0 = top) of the tile:

- byte 2`y` holds plane 0 of the row, byte 2`y` + 1 plane 1;
- byte 16 + 2`y` holds plane 2, byte 17 + 2`y` plane 3.

In each of these bytes, bit 7 is pixel column 0 (the left pixel) and
bit 0 is column 7. The colour number of the pixel at column `x`, row `y`
is the sum over planes `p` = 0 to 3 of (bit 7 − `x` of the plane's byte
for row `y`) × 2^`p`.

A **two-plane tile** stores planes 0 and 1 only, in 16 bytes, laid out
like the first 16 bytes of a tile; its colour numbers are 0 to 3. The
first level's background layers use only four-plane tiles; two-plane
tiles are listed here for the vectors and for later parts.

A **tile set** is tiles stored one after another, numbered from 0.

## Packed tile data

The first level's tile set is stored packed. Packed data is a **pair
table** followed by a **command stream**. Unpacking produces a run of
bytes, the **output**; its length is not stored, it is whatever the
commands produce.

The pair table is the first 128 bytes: 64 pairs of bytes, pair `k`
being bytes 2`k` and 2`k` + 1.

The command stream starts right after the table. Each command starts
with a **command byte** `b`. Its top two bits (bits 7 and 6) give the
kind of command, and its low six bits give a number `n` from 0 to 63:

| Bits 7–6 | Command |
|---|---|
| 00 | If `b` is 0: the end; unpacking stops. Otherwise the next `n` bytes of the stream are added to the output as they are. |
| 01 | The next byte of the stream is added to the output `n` times. |
| 10 | The next two bytes of the stream are a **position** `q` (low byte first). Then `n` times: the output byte at position `q` is added to the output, and `q` goes up by 1. |
| 11 | Pair `n` of the pair table is added to the output (2 bytes, in order). No further stream bytes. |

Positions count output bytes from 0 (the first byte of the output).
In a copy (`10`), each byte is added before the next is read, so a copy
whose run reaches bytes it has itself just added repeats them: from an
output of `A B C`, a copy of 6 from position 1 adds `B C B C B C`. In a
valid stream `q` is below the output's length when the copy starts;
other streams are not covered.

Every command takes its stream bytes in order after its command byte,
and the next command byte follows them. The bytes after the end command
are not part of the data.

With `n` = 0, a `11` command adds pair 0; the first level's data has
such commands. `01` and `10` commands with `n` = 0 are not in the first
level's data, and what they do is not covered.

The output is the tile set: tile `t` is output bytes 32`t` to 32`t` + 31.
The first level's packed tile data unpacks to 27 968 bytes, which is
874 tiles. Tile 0 is all colour 0.

## Tile entries

A **tile entry** says which tile to draw in an 8 × 8 px square and how.
It is 16 bits, stored low byte first:

| Bits | Meaning |
|---|---|
| 0–9 | Tile number, 0 to 1023 |
| 10–12 | Palette row, 0 to 7: the tile's colour numbers are colours of this row |
| 13 | **Raised**: see "Drawing order" |
| 14 | **Flip left–right**: the tile is drawn mirrored left to right (pixel column `x` shows the tile's column 7 − `x`) |
| 15 | **Flip top–bottom**: the tile is drawn mirrored top to bottom (pixel row `y` shows the tile's row 7 − `y`) |

## Cell pictures

Each type has a **picture**, 32 × 32 px, made of 4 × 4 tile entries.
The **picture table** holds the pictures of types 0, 1, 2, … in order,
32 bytes each: type `t`'s picture is bytes 32`t` to 32`t` + 31 of the
table. In a picture, the 16 entries are stored a row at a time, top row
first, and left to right in each row: the entry for square column `cx`,
square row `cy` (each 0 to 3) is bytes 8`cy` + 2`cx` and 8`cy` + 2`cx` + 1.
It covers the picture's pixel columns 8`cx` to 8`cx` + 7 and rows 8`cy`
to 8`cy` + 7.

A cell shows its type's picture, changed by bits 14 and 15 of its cell
word:

- Bit 14 (**mirrored**, as in `level-ground.md`): the whole 32 × 32
  picture is mirrored left to right.
- Bit 15: the whole picture is mirrored top to bottom. This bit has no
  effect on the ground.
- Both: both.

As tile entries: with bit 14 set, the cell's square (`cx`, `cy`) holds
the picture's entry for (3 − `cx`, `cy`) with its flip left–right bit
changed (0 ↔ 1). With bit 15 set, it holds the entry for (`cx`, 3 − `cy`)
with its flip top–bottom bit changed. With both, the entry for
(3 − `cx`, 3 − `cy`) with both bits changed. The other bits of the entry
are kept.

The first level's grid uses types 0 to 512 for pictures; its picture
table has a picture for each. Type 0's picture is 16 entries of tile 0,
palette row 0: it is empty. Bits 11–13 of a cell word were never set in
the first level's grid; what they do to a picture is not covered.

## The grid picture

The **grid picture** is the image made of all the grid's cells:
column `n`'s cell for band `k` (`level-ground.md`) is drawn at the grid
picture's pixel columns 32`n` to 32`n` + 31 and pixel rows 32`k` to
32`k` + 31 (row 0 is the top of the grid picture). A grid of `N`
columns gives a grid picture 32`N` px wide and 512 px tall. The first
level's grid has 184 columns: 5 888 px.

Each pixel is the colour of its tile's colour number in the tile
entry's palette row, from the level's palette set, or transparent for
colour number 0.

## The layers

The first level's background is two layers in front of a backdrop.
Both are made from the grid picture, with the level's tile set and
palette set.

- The **ground layer** is the grid picture. It moves exactly with the
  view (`view.md`, `view-vertical.md`): grid picture column `c` is at
  level x `c`, and grid picture row `r` is at level y `r` − 1. (In
  pictures with view positions `V` = `Vx`, `Vy`, the picture's top row,
  level y `Vy`, showed grid picture row `Vy` + 1, and its left column,
  level x `Vx`, showed grid picture column `Vx`.)
- The **far layer** is a 512 × 256 px picture that repeats in both
  directions. It is the part of the grid picture made by the grid's
  last 16 columns and bands 0 to 7: far picture pixel (`x`, `y`) is grid
  picture pixel (32(`N` − 16) + `x`, `y`). In the first level that is
  grid picture columns 5 376 to 5 887. How it moves with the view is not
  covered.

## Drawing order

From the back:

1. the backdrop (not covered),
2. the far layer's tiles whose entry is not raised,
3. the ground layer's tiles that are not raised,
4. the far layer's raised tiles,
5. the ground layer's raised tiles.

Transparent pixels show what is behind them. Where characters and
other moving things are drawn in this order is not covered. In the
first level only the ground layer has raised tiles.

## The first level's data

For the first level the importer needs:

- the packed tile data (874 tiles once unpacked),
- one palette set of 8 rows; the layers only use rows 1 to 7 apart from
  type 0's empty picture,
- the picture table (types 0 to 512),
- the grid (184 columns, `level-ground.md`).

None of them changed while the first level was played: no tile and no
colour of palette rows 1 to 7 changed in 5 388 frames of play from eight
places in the level. Changes came only with the picture going dark
(fades, and moving to another area), which is not covered.

## Example (synthetic)

Packed data: a pair table whose pair 5 is `7e 7f` (other pairs any),
then the stream `03 0a 0b 0c 42 ff 85 01 00 c5 00`:

- `03`: 3 bytes as they are: output `0a 0b 0c`.
- `42 ff`: `ff` twice: `0a 0b 0c ff ff`.
- `85 01 00`: 5 bytes from position 1: `0b 0c ff ff 0b` (the last byte
  is the first one this copy added): `0a 0b 0c ff ff 0b 0c ff ff 0b`.
- `c5`: pair 5: `… 0b 7e 7f`, 12 bytes.
- `00`: the end.

A tile whose bytes are all 0 except byte 0 = 0x80 and byte 16 = 0x80 has
colour number 1 + 4 = 5 at its top-left pixel and 0 elsewhere.

Tile entry 0x6A5B: tile 0x25B = 603, palette row 2, raised, flipped
left to right, not flipped top to bottom.

A cell word 0x4003 (type 3, mirrored): if type 3's picture has entry
0x0010 at (0, 0) and 0x4011 at (3, 0), the cell's square (0, 0) holds
0x4011 ^ 0x4000 = 0x0011 and square (3, 0) holds 0x0010 ^ 0x4000 =
0x4010.

More cases, as bytes, are in `../vectors/graphics/`.

## Evidence

- Unpacking the first level's packed tile data as above gave the game's
  874 tiles exactly, byte for byte.
- In 35 saved positions spread over the first level (view x 46 to
  5 036), every tile entry the game showed on the ground layer
  (896 per position) and every entry of the far layer's 512 × 256 px
  picture matched the entries built from the grid, the picture table and
  the cell words as above.
- Drawing the ground layer from those entries, tiles and palette rows
  gave the game's own picture of that layer with no pixel different, at
  six of those positions (256 × 224 px each), always with the one-row
  shift given in "The layers".

## Not covered

- Where the packed tile data, the palette set, the picture table and
  the grid start in an image (see `rom-image.md`); their structure is
  as above.
- How the far layer moves with the view, including different rates for
  different rows of the picture.
- Colours on screen that are not the palette colours: the far layer
  was shown darker than its palette colours, and the backdrop is a
  colour that changes from row to row of the picture.
- The layer that shows counters and other information.
- Characters, walkers, objects and other moving things (their tiles,
  palette rows and order against the layers).
- Fades, and areas other than the first level's main area (rooms
  entered from it).
- Other levels and other titles.
