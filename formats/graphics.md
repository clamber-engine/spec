# Level graphics

Titles: `title-1`

How the first level's background pictures are stored, and how to turn
them into images: colours and palette rows, tiles, packed tile data,
tile entries, cell pictures, the first level's background layers and
backdrop, the one fixed picture of the first level's room, and the three
layers of the first level's scene (`../behavior/exits.md`, "Where the
first level's exits lead"). The
**grid**, its **cells**, **cell words** and **types** are as in
`level-ground.md`: the picture of a cell comes from the same cell word
as its ground. Everything here was checked against the game's own video
output on the first level, its room and its scene (see "Evidence").

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
first level's background layers use only four-plane tiles; the front
layer of the first level's scene uses two-plane tiles ("The first
level's scene").

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

The first level's background is three layers in front of a backdrop.
The ground layer and the far layer are made from the grid picture, with
the level's tile set and palette set; the horizon layer has a map and
tiles of its own ("The horizon layer").

- The **ground layer** is the grid picture. It moves exactly with the
  view (`view.md`, `view-vertical.md`): grid picture column `c` is at
  level x `c`, and grid picture row `r` is at level y `r` − 1. (In
  pictures with view positions `V` = `Vx`, `Vy`, the picture's top row,
  level y `Vy`, showed grid picture row `Vy` + 1, and its left column,
  level x `Vx`, showed grid picture column `Vx`.)
- The **far layer** is a 512 × 256 px picture that repeats in both
  directions. In the first level it is the part of the grid picture
  made by the grid's last 16 columns and bands 0 to 7: far picture
  pixel (`x`, `y`) is grid picture pixel (32(`N` − 16) + `x`, `y`),
  grid picture columns 5 376 to 5 887. It moves at half the view's
  speed across and a quarter of it up and down ("Where the far layer
  is"), and is shown darker than its palette colours ("The far layer's
  colours"). The first level's scene takes its far layer from another
  part of its grid ("The first level's scene").
- The **horizon layer** is a strip 48 px tall that stays at the same
  rows of the picture and moves across in four bands at four rates
  ("The horizon layer").
- The **backdrop** shows where no layer draws. Its colour changes from
  one row of the picture to the next ("The backdrop").

In this section, `E` is the level x of the picture's left edge and `T`
the level y of its top row, both whole pixels, as the ground layer
uses them: `E` is the view's position rounded down and kept within the
level's limits (`view.md`, "The view's state") and `T` likewise
(`view-vertical.md`). The view's position across is kept to 1/256 px;
`f` (0 to 255) is its fraction of a pixel in 1/256 px. A picture is
the one shown after a frame's step, with that frame's `E`, `T` and
`f`. Picture pixel (`i`, `j`) is column `i` (0 to 255, from the left)
and row `j` (0 to 223, from the top).

### Where the far layer is

Picture pixel (`i`, `j`) shows far picture pixel

  ((⌊`E` / 2⌋ + `i`) mod 512, (⌊`T` / 4⌋ + 1 + `j`) mod 256).

So when the view moves 2 px across, the far layer moves 1 px, and when
it moves 4 px up or down, the far layer moves 1 px.

### The far layer's colours

The far layer is never shown in its palette colours. A far-layer pixel
whose palette colour has red, green and blue values `r`, `g`, `b` (0 to
31, "Colours") is shown with the values

  ⌊max(0, `r` − 4) / 2⌋, ⌊max(0, `g` − 4) / 2⌋, ⌊max(0, `b` − 4) / 2⌋.

For example, a palette colour with values 31, 10, 3 is shown as 13, 3,
0. Only the far layer is changed: the ground layer, the horizon layer
and the backdrop are shown in their own colours.

### Row-colour lists

The backdrop's colours and one of the horizon layer's colours are given
row by row by a **row-colour list**: entries of 5 bytes, then a byte 0.

| Bytes | Meaning |
|---|---|
| 0 | A row count `n`, 1 to 127 |
| 1, 2 | A colour number, the same in both bytes: 0 in the backdrop's list, 1 in the horizon layer's |
| 3, 4 | A colour ("Colours") |

A list ends at the first entry whose row count byte is 0; that byte is
the whole of the last entry. The entries give the rows of the picture
from row 0 down: the first entry covers its `n` rows, the next entry
the `n` rows after those, and so on. Rows below the last entry keep the
last entry's colour. In the first level both lists use only row counts
of at most 127, so bit 7 of a row count is never set; what it would do
is not covered.

For example, the list `02 00 00 1f 00`, `01 00 00 e0 03`, `03 00 00 00
7c`, `00` gives rows 0 and 1 colour 0x001F (red), row 2 0x03E0 (green),
rows 3 to 5 and every row below them 0x7C00 (blue).

### Night colour tables

A backdrop row-colour list that takes part in the dimming
(`../behavior/dimming.md`) has a **night colour table**, stored apart from
the list: one colour ("Colours", 2 bytes) for each of the list's first
`m` entries, in the list's entry order. Entries after the table's last
have the night colour black. The table has no terminator; `m` is known
from the level, not from the data. The first level's table has `m` = 71,
fewer than its list's entries, and lies in the image just before the
list (`locators.md`).

For example, with `m` = 3 the table `00 10 00 0c 00 00` gives entries 0, 1
and 2 the night colours 0x1000, 0x0C00 and 0x0000, and every later entry
black.

How the table was found: the backdrop's colours were read from pictures
of the first level while it dimmed, one step at a time (setup method:
research tools), and each entry's final colour and per-step change were
fitted. The final colours were then searched for, as a run of colours,
in the image; the run found lies where `locators.md` says, and with it
as the final colours every picture of 16 runs matched, apart from the
backdrop's lowest rows showing a step one picture early on some
pictures (`../behavior/dimming.md`).

### The backdrop

The backdrop's colour on picture row `j` is the colour the backdrop's
row-colour list gives row `j`, with one change that alternates from
frame to frame: on the pictures after the level's frames 0, 2, 4, …
(even frames) the list's first entry covers its own row count, and on
the pictures after odd frames it covers one row more, so the whole list
is one row lower. The rows don't depend on the view: the backdrop
stays at the same rows of the picture whatever `T` is.

The first level's backdrop list covers 113 rows; its first entry covers
one row, and its last entry (black) also fills every row below the
list.

### The horizon layer

The **horizon block** is stored as:

- a **horizon map**: 6 rows of 32 tile entries ("Tile entries"), 384
  bytes, stored a row at a time, top row first, and left to right in
  each row: the entry for square column `sx` (0 to 31) and square row
  `sy` (0 to 5) is bytes 64`sy` + 2`sx` and 64`sy` + 2`sx` + 1;
- right after the map, the **horizon tiles**: two-plane tiles
  ("Tiles"), stored as they are, not packed, tile 0 first.

The first level's horizon map uses tiles 0 to 18, palette row 0 only,
and no entry is raised or flipped. Tile 0 is all colour 0.

The **horizon picture** is 256 × 48 px: the entry for (`sx`, `sy`)
covers its pixel columns 8`sx` to 8`sx` + 7 and rows 8`sy` to 8`sy` + 7.
Its colours are those of two-plane tiles ("Colours of two-plane tiles")
except colour number 1 of an entry with palette row 0, which comes from
the horizon layer's row-colour list ("Row-colour lists") by picture
row. So in the first level, a horizon pixel with colour number 1 on
picture row `j` has the colour the list gives row `j`; colour numbers 2
and 3 are colours 2 and 3 of the palette set's row 0; colour number 0
is transparent. Unlike the backdrop's list, this list never moves by a
row. The first level's horizon list covers 98 rows, its first entry 68
of them; rows below it keep its last colour.

The horizon picture is at picture rows 64 to 111 whatever the view
does: picture row `j` shows horizon picture row `j` − 64. Across, it
repeats every 256 px and is split into four **bands** of picture rows,
each with its own shift `s`:

| Band `k` | Picture rows | Moves at, of the view's speed |
|---|---|---|
| 0 | 64 to 71 | 1/8 |
| 1 | 72 to 79 | 3/16 |
| 2 | 80 to 87 | 1/4 |
| 3 | 88 to 111 | 5/16 |

Picture pixel (`i`, `j`) in band `k` shows horizon picture pixel
((`i` + `s`ₖ) mod 256, `j` − 64), with

  `s`ₖ = ⌊(`k` + 2) × `E` / 16 + ⌊`f` / 8⌋ / 256⌋ mod 32.

The first level's horizon picture repeats every 32 px across (its map's
square column `sx` holds the same entry as `sx` + 4), so shifts that
differ by a multiple of 32 give the same picture. For example, with
`E` = 100 and `f` = 192 the shifts are ⌊12.5 + 24/256⌋ = 12, ⌊18.75 +
0.09…⌋ = 18, ⌊25.09…⌋ = 25 and ⌊31.34…⌋ = 31.

On some pictures the lowest bands already show the shifts of the next
frame's `E` and `f`: band 3 alone, bands 2 and 3, or bands 1 to 3. In
a run of 2 708 pictures this happened on 268 of them (196, 57 and 15
times). Which pictures do it is not covered; the rule above, with the
frame's own `E` and `f`, gives every other picture exactly.

Synthetic vectors for this section: `../vectors/graphics/` ("Background
layers of the first level").

## Drawing order

From the back:

1. the backdrop ("The backdrop"),
2. the horizon layer ("The horizon layer"),
3. the far layer's tiles whose entry is not raised,
4. the ground layer's tiles that are not raised,
5. the far layer's raised tiles,
6. the ground layer's raised tiles.

Transparent pixels show what is behind them. In the first level only
the ground layer has raised tiles. Where characters and other moving
things are drawn in this order is given in "Moving things and raised
tiles".

## Moving things and raised tiles

**Moving things** here are the two characters and the animal when
ridden. (Walkers and objects follow a different rule: "Walkers and
objects and raised tiles".) Against the ground layer they
are in one of two **orders**, and the order is a state of play, not a
property of a tile or of a thing:

- **In front** (ordinary play). Every pixel of a moving thing is drawn
  over the ground layer, whether the tile under it is raised or not.
- **Behind** (a walk-out, below). A pixel of a moving thing is drawn
  over the ground layer's tiles that are not raised, as in front, but is
  **hidden wherever a raised tile has a pixel that isn't transparent**.
  On tiles that are not raised the thing shows as usual. The only
  per-tile information an importer needs is the tile entry's raised bit
  ("Tile entries"); the order is the same for the lead, the other
  character and the animal.

The order is in front except in these walk-outs, where it becomes behind
at the picture of the frame given and stays behind until the area
changes:

- **At the end of a level** (`../behavior/level-end.md`): from the
  picture of `T`, the first frame of the walk-out. The picture of `T` − 1
  and every earlier one has the moving things in front. With the lead
  alone, with both characters and with the lead riding the animal, from
  that picture on every pixel of a moving thing that lies on a pixel of
  a raised tile that isn't transparent is hidden, the animal and the
  rider together, with one exception seen: in one run riding the animal,
  one pixel stayed shown in each of 9 pictures, and why is not known
  (the other runs had none). Pixels over transparent pixels of raised
  tiles stay shown.
- **Through an exit** (`../behavior/exits.md`): from the picture of
  `T` + 1, where `T` is the frame the exit takes the lead over. The
  picture of `T` itself still has the moving things in front (the lead
  and the animal alike when ridden; the other character as well). The
  rule holds for the exit on the floor near the first level's left end
  and for the exits in the walls, on foot and ridden, for the pixels
  over raised-tile pixels that aren't transparent. In these runs no
  part of a moving thing lay over a transparent pixel of a raised tile,
  so they show nothing about those pixels: whether the thing shows
  through them in an exit is not covered.

A moving thing that is not on a raised tile at that picture is not
changed by it: it is hidden when it comes to one (the other character,
following the lead's path, was hidden once it reached the raised tiles,
later than the lead). At the level end, transparent pixels of raised
tiles hide nothing, as in "Drawing order".

In the exit on the floor near the first level's left end, the lead is
not drawn at all from the picture of `T` + 19 on, in four runs; that is
a matter of the walk-out, not of this order. The order changes the
pixels of the picture only, never positions, events or timing.

For an engine: draw the ground layer's raised tiles over the moving
things from the picture given until the area changes, and the moving
things over the ground layer's other tiles throughout. An engine that
hides a moving thing completely, or hides one kind (the lead but not the
animal) or all of them one picture early or late, differs from the
original on the pixels of that picture.

## Walkers and objects and raised tiles

Walkers and objects (`../formats/placements.md`, "Kinds") do not follow
the **order** of "Moving things and raised tiles". Each **kind** has one
order against the ground layer's raised tiles, and it does not change
with the state of play, in particular not at the picture of `T` of a
level's end:

- **In front**, at every picture: `small-walker`, `medium-walker`,
  `large-walker`, `thrower`, `bunch`, `barrel`, `holder`, `animal-crate`, `figure`,
  `restart-barrel` and `down-launch-barrel`. Every pixel is drawn over
  the ground layer, raised tiles included.
- **Behind**, at every picture: `tyre` and `sign` (every pixel over a
  pixel of a raised tile that isn't transparent is hidden). A pixel over
  a tile that isn't raised, or over a transparent pixel of a raised
  tile, is drawn as usual; the rule is the one given for "Behind" above.
- **Partly hidden**, at every picture: `letter-2`. Over a pixel of a
  raised tile that isn't transparent, about 4 in 5 of its pixels are
  hidden and the rest shown, before and from the picture of `T` alike;
  which ones is not covered.

For an engine: keep the order with the kind, a fixed table of the kinds
above, and apply the level end's change of order to the characters and
the animal only. An object of a behind kind drawn in front, or of an
in-front kind hidden from the picture of `T`, differs from the original
on the pixels over raised tiles.

A `ball`, a `critter` and a `stinger` put on the raised tiles in the
same way never had a pixel over a pixel of a raised tile that isn't
transparent, so no order was found for them. Kinds not named (`letter-1`,
`letter-3`, `letter-4`, the extra lives, the hidden kinds, the exits and
the entrances) were not tested.

## Screen pictures

Some areas show one fixed picture instead of layers made from the grid.
The first level's room is one of them. Its background is a **screen
picture**, made from:

- a **screen map** of 32 × 32 tile entries ("Tile entries"), 2 048
  bytes. The entries are stored a row at a time, top row first, and left
  to right in each row: the entry for square column `sx`, square row `sy`
  (each 0 to 31) is bytes 64`sy` + 2`sx` and 64`sy` + 2`sx` + 1. It covers
  the picture's pixel columns 8`sx` to 8`sx` + 7 and rows 8`sy` to
  8`sy` + 7, so the screen picture is 256 × 256 px;
- a tile set of 1 024 tiles, stored as they are, not packed (32 bytes
  per tile, tile 0 first);
- a palette set.

Each pixel is the colour of its tile's colour number in the entry's
palette row, or transparent for colour number 0, as in "The grid
picture".

In an area with a screen picture:

- The screen picture is the only background layer. There is no far
  layer, and the grid's cells (`level-ground.md`) give the area's ground
  only: their pictures are not shown.
- The picture doesn't move. Its pixel column `c` is always at level x
  `c`, and its pixel row `r` is always at level y `r`, with no shift of a
  row as the ground layer has ("The layers"). The 256 × 224 px picture
  on screen shows the screen picture's rows 0 to 223.
- Transparent pixels show the **backdrop**, one colour on the whole
  picture: colour 0 of palette row 0 of the area's palette set.
- From the back, the order is the backdrop, the screen picture's tiles
  that are not raised, then its raised tiles. Where characters and other
  moving things are drawn in this order is not covered, as in "Drawing
  order".

## The first level's data

For the first level the importer needs the following. Where each one
starts in the image is in `locators.md` (the grid's row is listed under
`level-ground.md`).

- the packed tile data (874 tiles once unpacked),
- one palette set of 8 rows; the layers only use rows 1 to 7 apart from
  type 0's empty picture,
- the picture table (types 0 to 512),
- the grid (its first 184 columns, `level-ground.md`),
- the backdrop's row-colour list and the horizon layer's row-colour
  list ("Row-colour lists"), and the backdrop's night colour table
  ("Night colour tables"),
- the horizon block: the horizon map and, right after it, the horizon
  tiles (tiles 0 to 18 are used).

No tile changed while the first level was played: none in 5 388 frames
of play from eight places in the level. Changes came only with the
picture going dark (fades, and moving to another area), which is not
covered.

The colours of rows 1 to 7 were the palette set's colours in the same
5 388 frames, and in every one of 56 saved positions in the level with
the view's left edge at level x 4 585 or less. Near the level's right
end they were not: in all six saved positions with the view's left
edge at level x 4 774 to 5 120, the colours of rows 1 to 7 were
different and mostly darker, and in two of the six they were different
from the other four. Those colours were not found in the image
as palette rows: they are the dimming (`../behavior/dimming.md`), which
changes every colour of rows 1 to 7 step by step from the palette set's
colours near the level's right end. The palette set is the level's
colours before the dimming.

## The first level's room

For the room the upper exit leads to, the importer needs the following.
Where each one starts in the image is in `locators.md`.

- The tile set (1 024 tiles), stored in two parts: tiles 0 to 986 (31 584
  bytes) at one location, and tiles 987 to 1 023 (1 184 bytes) at
  another.
- The screen map.
- The palette set (8 rows).

The room's grid and type table are in `level-ground.md`. At every check
in "Evidence" the game held exactly these tiles, this screen map and
these 8 palette rows. The fade-in and the walk-in are in
`../behavior/exits.md`.

## The first level's scene

The scene the floor exit leads to (`../behavior/exits.md`, "The scene")
has three background layers: a ground layer and a far layer made from
its grid, as in "The layers", and a **front layer** made from a screen
map of its own. For the scene the importer needs the following. Where
each one starts in the image is in `locators.md`.

- The packed tile data ("Packed tile data"). It unpacks to 28 064
  bytes, which is 877 tiles. Tile 0 is all colour 0.
- The palette set (8 rows).
- The picture table ("Cell pictures"). The scene's grid uses types 0 to
  435 for pictures, and the table has a picture for each. Type 0's
  picture is empty, as in the first level. Bits 11 to 13 of the grid's
  cell words are never set.
- The grid (`level-ground.md`, "The first level's scene").
- The **front tile set**: two-plane tiles ("Tiles"), stored as they
  are, not packed, tile 0 first. The front map uses tiles 0 to 43.
- The **front map**: 32 × 32 tile entries, 2 048 bytes, laid out as a
  screen map ("Screen pictures"). Its entries' tile numbers are tiles of
  the front tile set.

### Colours of two-plane tiles

A two-plane tile is drawn with the colours of the palette set counted
as one run of 128 colours, from colour 0 of row 0 to colour 15 of row
7. A tile entry with palette row `p` gives colour number `c` (1 to 3)
the colour `4p` + `c` of that run: colour (4`p` + `c`) mod 16 of row
⌊`p` / 4⌋. Colour number 0 is transparent. So entries with palette rows
0 to 3 take their colours from the set's row 0, and entries with rows 4
to 7 from its row 1. The scene's front map uses palette rows 0 and 4.
Synthetic vectors: `../vectors/graphics/` ("Front layer").

### The pictures

- The **front picture** is the 256 × 256 px picture made from the front
  map and the front tile set as in "Screen pictures", with the colours
  of two-plane tiles above.
- The scene's **far picture** is the part of the grid picture made by
  the grid's first 8 columns and bands 0 to 7: far picture pixel (`x`,
  `y`), with `x` and `y` from 0 to 255, is grid picture pixel (`x`,
  `y`).
- The ground layer is the grid picture, as in "The layers".

### Where the layers are

The scene's view doesn't move (`../behavior/level-ends.md`, "In the
first level's scene"): on every frame the picture's left edge is at
level x `E` and its top row at level y `Vy`, the values given there.
Screen pixel column `i` (0 to 255) and row `j` (0 to 223), counted
from the picture's top left, shows:

- on the ground layer, grid picture pixel (`E` + `i`, `Vy` + 1 + `j`),
  with the one-row shift of "The layers";
- on the far layer, far picture pixel (`i`, `j` + 5);
- on the front layer, front picture pixel (`i`, `j` + 1).

The front layer doesn't depend on the view: in a copy of the image with
the scene's view-limit record changed so that the view was somewhere
else, it was still front picture pixel (`i`, `j` + 1). The far layer
did move with the view there; how is not covered, so the far layer's
place above holds for the scene's view as stored in the image.

### Drawing order in the scene

From the back:

1. the far layer's tiles that are not raised,
2. the ground layer's tiles that are not raised,
3. the far layer's raised tiles,
4. the ground layer's raised tiles,
5. the front layer's raised tiles.

Transparent pixels show what is behind them. This is the order of
"Drawing order" with the front layer's raised tiles in front of
everything. In the part of the far picture on screen no pixel is
transparent, so the backdrop is never seen in the scene, and no tile is
raised. Every front picture pixel on screen that isn't transparent comes
from a raised entry. Where characters and other moving things are drawn
in this order is not covered, as in "Drawing order".

On screen, every layer shows the palette set's own colours (the first
level's far layer, by contrast, is shown darker: "The far layer's
colours").

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

In a screen map, bytes 130 and 131 are the entry for square column 1,
square row 2 (130 = 64 · 2 + 2 · 1). If they are `05 64`, the entry is
0x6405: tile 5, palette row 1, raised, flipped left to right, not
flipped top to bottom. It covers the picture's pixel columns 8 to 15 and
rows 16 to 23, that is level x 8 to 15 and level y 16 to 23 in an area
with a screen picture.

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
- The first level's backdrop, far layer and horizon layer, in three runs
  from the level's start: about 450, 670 and 2 710 frames (waiting and
  walking right, jumps, and a run to the level's right end), every
  picture after frame 45, 3 830 pictures in all, up to the start of the
  dimming. Shown together, with the ground layer, characters and objects
  not drawn, every pixel was as given in "The layers" (by "Where the
  far layer is", "The far layer's colours", "The backdrop", "The
  horizon layer" and "Drawing order"), except on the horizon rows of
  the pictures that showed the next frame's horizon shifts (332 of the
  3 830; with those shifts, no pixel was different). In the first run
  each of the three was also shown alone, on 456 pictures each: no
  pixel was different on the backdrop and the far layer, and the
  horizon layer differed only on those same pictures. The views of
  those runs covered `E` from 0 to 4 722 and `T` from 0 to 264.
- The first level's room, entered by the upper exit with the lead alone
  and with two characters: 6 runs of 600 logic frames with made-up input
  (walking, running, jumping and rolling, three runs each way),
  checked every 50th frame (72 pictures). The game's own
  picture of its background layer matched the screen picture drawn as
  in "Screen pictures" from the image's tile set, screen map and palette
  set, with every one of the 56 368 non-transparent pixels the same in
  each picture (4 058 496 pixels, none different), and with no shift of
  a row. The game's tiles and screen map were the image's, byte for
  byte, and its colour rows 0 to 7 were the palette set.
- In the same pictures, the game showed nothing on any other background
  layer, and the transparent pixels showed one colour. In a copy of the
  image with colour 0 of the room's palette row 0 changed, the backdrop
  was the new colour.
- Where characters and objects are drawn in the room: in 4 runs of 600
  logic frames (two with two characters), each of 14 800 objects drawn
  was at the point that `sprites.md` ("Where the picture is drawn")
  gives with the picture's left edge at level x 0 and its top row at
  level y 0.

- The first level's scene, entered by the floor exit with the lead
  alone and with two characters: in each run, every 25th logic frame
  from frame 50 to frame 350 (13 pictures per run, at full brightness),
  the game's own picture of each background layer alone matched the
  layer drawn from the image's data as in "The first level's scene",
  with no pixel different (per picture: 40 796 non-transparent pixels on
  the ground layer, 57 344 on the far layer, 9 753 on the front layer).
  Each layer matched at the row offsets given there and at no other
  (offsets of up to 2 px either way in both directions were tried; the
  next best had 1 060 to 20 357 pixels different). The game's picture
  of all three layers together, with characters and objects not drawn,
  matched the order of "Drawing order in the scene" on every pixel of
  the same 26 pictures.
- At each of those 26 pictures the game's tiles were the scene's
  unpacked tile data byte for byte, its colour rows 0 to 7 were the
  scene's palette set, and the front layer's map and its tiles 0 to 43
  were the image's front map and front tile set. Every tile entry the
  game showed on the ground layer (896 per picture) matched the entry
  built from the grid and the picture table, and all 1 024 entries of
  the far layer matched the grid's first 8 columns, bands 0 to 7.
- In a copy of the image with the scene's view-limit record changed
  (its left and right limits and the entry's start lowered by 64, its
  top and bottom lowered by 24): at 3 pictures (frames 100, 200 and
  300), the ground layer matched with the moved view and the front
  layer as before, each with no pixel different, and the far layer
  matched at none of the offsets tried.
- Moving things against the ground layer's raised tiles, in the first
  level, by pictures with only the ground layer, only the moving things
  and both shown (setup method: research tools). Ordinary play: 500
  frames of walking right from the level's start and 330 frames of
  waiting and then walking left with jumps, near the floor exit. Of the
  moving-thing pixels on a raised tile with a pixel that isn't
  transparent, every one was shown (about 6 600 and 32 650 pixels), and
  none on a tile that isn't raised was ever hidden, in any run below.
  Whether a raised tile's pixel is transparent was taken from the tile
  data (colour number 0), counting a pixel only when it and its eight
  neighbours agree, because the scroll the pictures were made with is
  known only at the end of each frame; pixels where they disagree are
  left out of the counts. Level end, 4 runs from before the marker (the
  lead alone from two places, both characters, the lead riding the
  animal), 58 to 61 pictures from the switch in each. Pixels of moving
  things on a pixel of a raised tile that isn't transparent: hidden from
  the picture of `T` on in all but 9 (28 651, 46 969, 63 818 and 28 966
  pixels hidden; the 9 are one pixel in each of 9 pictures of the riding
  run, always at the same place on screen, shown, and not explained),
  and all shown before it (400, 53, 2 841 and 80 pixels). Pixels of
  moving things on a transparent pixel of a raised tile: none was ever
  hidden, 18, 58, 248 and 31 were shown from the picture of `T` on (and
  0, 53, 331 and 57 before it); about 3 100 more pixels from the picture
  of `T` on and 1 000 before it, near the edges of the tiles' shapes,
  were left out. Exits, 4 runs: the floor exit with
  the lead alone and with both characters, and the wall exit on foot and
  ridden, 37 to 47 pictures from the switch: no pixel on a pixel of a
  raised tile that isn't transparent was shown on the picture of `T` + 1
  or after, all earlier ones were shown (6 673, 57, 2 677 and 12 071
  pixels before; 9 129, 14 691, 2 321 and 6 611 hidden). In these runs
  no moving-thing pixel was on a transparent pixel of a raised tile in
  any picture, so the exits say nothing about transparent pixels. In
  each, the last pictures are those just before the area changed.
- Walkers and objects against the ground layer's raised tiles, in the
  first level at its end (setup method: research tools, used to put one
  object of each kind below on the raised tiles beside the end marker,
  at the height of the lead's standing place). The level was run to the
  end with the lead walking right (pictures with only the ground layer,
  only the moving things and both shown, as in the bullet above). For
  each kind, the pixels it adds to a run without it, over the 20 pictures
  before the picture of `T` and the 14 from it (the lead is not yet at
  the object in them), on pixels of
  raised tiles that aren't transparent, shown / hidden. Before, then
  from `T`: `medium-walker` 17 778 / 0 and 11 494 / 0; `small-walker`
  11 475 / 0 and 7 322 / 0; `thrower` 22 212 / 0 and 15 336 / 0;
  `barrel` 19 677 / 0 and 12 956 / 0; `holder` 19 822 / 0 and 13 024 /
  0; `animal-crate` 33 474 / 0 and 21 066 / 0; `figure` 9 538 / 0 and
  5 846 / 0; `restart-barrel` 19 308 / 0 and 10 887 / 0;
  `down-launch-barrel` 32 508 / 0 and 22 005 / 0; `bunch` 5 160 / 0 and
  3 539 / 0; `letter-2` 1 099 / 5 115 and 820 / 3 127; `tyre` 0 / 12 180
  and 0 / 7 250; `sign` 0 / 13 419 and 0 / 7 044 (the level's other sign
  was not run); `large-walker` 29 688 / 0 and 18 493 / 0. A `ball`, a
  `critter` (at two heights) and a `stinger` (at seven heights) added
  no pixel on raised tiles in either part. Pixels over tiles that
  aren't raised were never hidden.

## Not covered

- Sprite colours during the dimming near the first level's right end
  (`../behavior/dimming.md`).
- Where other levels' packed tile data, palette sets and picture
  tables start in an image, and which areas other than the first
  level's room have a screen picture.
- How the far layer and the horizon layer move in areas other than the
  first level.
- In the first level: where the backdrop's night colours for the
  dimming are stored (`../behavior/dimming.md`); the picture while
  the level fades in at its start (the pictures after frames 0 to 44); which
  pictures show the next frame's horizon shifts ("The horizon layer");
  `f` while the view's position is outside the level's limits; and
  whether the backdrop's frame-to-frame alternation keeps the same
  order after a knock-out or another area.
- The layer that shows counters and other information.
- Characters, walkers, objects and other moving things: their tiles
  and palette rows. The order of the characters and the animal against
  the layers is given for the ground layer's raised tiles; against the far layer, the horizon layer
  and the backdrop it is not covered.
- The order against the raised tiles of the kinds not named in
  "Walkers and objects and raised tiles", and of walkers and objects in
  exits and in areas other than the first level's own ground; the
  pixels over transparent pixels of raised tiles in an exit; the order of the characters and the animal against the raised
  tiles in areas other than the first level's own ground, and in its
  room and its scene (as the room and the scene sections say); the order
  in the walk-in at an area's start; other exits than those named in
  "Moving things and raised tiles"; riding through an exit leading left
  on the floor.
- Fades, and the pictures of areas other than the first level, its
  room and its scene.
- Whether other areas with a screen picture have tile sets of other
  sizes, a far layer or a moving picture.
- In the scene: whether the image's bytes right after the front map
  belong to the front layer (the scene shows nothing from them), where the front layer's tiles that are not
  raised are drawn (none is seen), and how the front layer's raised
  tiles go against the ground layer's raised tiles (they never meet on
  screen). Which part of a grid makes the far layer in other areas: the
  first level and its scene take it from different ends of their
  grids.
- Other levels and other titles.
