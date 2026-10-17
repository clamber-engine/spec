# Level ground

Titles: `title-1`

How a level's ground is stored, and how to read from it where the ground
is in each 1-px column. This is the data behind the floor heights,
slopes, steep faces and platforms in `../behavior/` (`slopes.md`,
`steep-faces.md`, `platforms-from-below.md`, `landing.md`), and behind
"the ground below" in `view-vertical.md`. Other behavior
files named below are in `../behavior/` too.

This file covers two kinds of grid. A **column grid** is 16 bands tall
and is stored column by column. Most of the title's ground uses this
kind, including the first two levels. It was checked against the game's
own ground on the first level and the start of the second. A **row
grid** is stored row by row and can be much taller. It is described in
"Row grids" by how it differs from a column grid. A **ceiling grid** is
stored like a row grid but has its own shapes, some of them ceilings.
It is described in "Ceiling grids" by how it differs from a row grid.
Everything before "Row grids" is about column grids.

## Positions

Positions are **level x** and **level y** in whole px. Level y is as in
`view-vertical.md`: it grows downward from the top of the level. Level x
grows to the right from the left edge of the level's grid. A grid can
hold several levels side by side, so a level's positions don't have to
start near 0.

A **line** is a level y used for ground. A surface at line `L` is a
surface that a player stands on with feet line `F` = `L` (feet line as in
`view-vertical.md`).

**Bands** are as in `view-vertical.md`: band `k` holds lines 32`k` + 1 to
32`k` + 32. The grid is 16 bands tall, bands 0 (top) to 15 (bottom), so it
covers lines 1 to 512. On both levels measured, the level's bottom edge
`Yb` (`falling-out.md`) was the bottom of the grid.

## The grid

The grid is a run of **columns**, each 32 px wide: column `n` covers
level x 32`n` to 32`n` + 31. Each column has 16 **cells**, one per band.
A cell is 32 px wide and covers its band's 32 lines.

Storage: the columns one after another from the left. Each column is 16
cell words, top band first. A cell word is 16 bits, stored low byte
first. A column takes 32 bytes.

A **cell word**:

| Bits | Meaning |
|---|---|
| 0–13 | The cell's **type**, a number from 0 to 16383 |
| 14 | **Mirrored**: if set, the cell's ground is mirrored left to right |
| 15 | No effect on the ground (it flips the cell's picture top to bottom, `graphics.md`) |

A cell word of 0 is an empty cell with no ground. Types are shared with
the cells' pictures (`graphics.md`), so a grid uses more types than
have ground. A grid comes with a **type table** of `N` entries for types
0 to `N` − 1. A cell whose type is `N` or more has no ground.

## The type table

Each type has two bytes, one for each half of the cell: first the
**left half** (the cell's 16 px columns 0–15), then the **right half**
(columns 16–31). Type `t`'s bytes are bytes 2`t` and 2`t` + 1 of the
table.

A **half byte**:

| Bits | Meaning |
|---|---|
| 0–5 | The half's **shape**, 0 to 63 (see "Shapes"); 0 = no ground |
| 6 | **Passable from below** (see "Ground passable from below"); no effect on where the ground is |
| 7 | **Flipped**: if set, the half's shape is mirrored left to right |

## The surface in one cell

To find the surface in level x column `x` (whole px) in band `k`:

1. Take the cell word in column ⌊`x` / 32⌋, band `k`. If it is 0, or its
   type is `N` or more, there is no surface.
2. Let `p` = `x` mod 32, the px column within the cell. If the cell is
   mirrored, `p` ← 31 − `p`.
3. Take the type's left half byte if `p` < 16, otherwise its right half
   byte. Let `c` = `p` mod 16. If the half is flipped, `c` ← 15 − `c`.
4. Look up the half's shape at position `c` (next section). It gives a
   **rise** `s` from 0 to 31, or no surface.
5. The surface is at line 32`k` + 32 − `s`. Rise 0 is the band's
   bottom line and rise 31 is its top line.

So mirroring a cell swaps its halves and mirrors each one. Flipping a
half mirrors only that half. Mirroring a cell and flipping a half both
apply when both are set; together they put the half back the right way
round (but in the other half's place).

## Ground passable from below

A surface found in a cell is **passable from below** if bit 6 is set in
the half byte that its shape came from in step 3: the left or right half
as chosen after the cell's mirroring. Flipping a half doesn't change
it, and neither does bit 15 of the cell word.

A cell's **ground** in a 1-px column is the lines below its surface,
down to and including the bottom line of its band: for a surface at
line `S` in band `k`, lines `S` + 1 to 32`k` + 32. A surface on the
band's bottom line (rise 0) has no ground in its cell.

What this changes is in `../behavior/platforms-from-below.md`: a
platform's top in a column is a surface that is passable from below,
and its underside is the bottom line of the top's band. A rising player
whose feet line would end a frame in the ground of a surface that isn't
passable from below is stopped.

On the first level every surface with open space under it was passable
from below. Many floors were too, but not all. For ground with nothing
under it, being passable from below changed nothing that was seen. With
bit 6 cleared on the floors that have no open space under them, three
runs of 1800 frames along one route through most of the level gave
exactly the same frames as before. With bit 6 cleared everywhere, the
same runs only differed from the first frame on which the player rose
into a platform from below.

## Shapes

A shape gives a rise for each position `c` from 0 (the left of the half)
to 15 (the right). Position `c` + 1 is never lower than `c`, so unflipped
shapes rise to the right or are flat.

Each shape number belongs to a family. Within a family, each step up in
shape number raises the whole shape by the same amount. Work out the
value `v` from the table. If `v` < 0 there is no surface at that position.
If `v` > 31 the rise is 31. Otherwise the rise is `v`.

| Shapes | Slope | `v` at position `c` |
|---|---|---|
| 0 | — | no surface anywhere |
| 1–8 | flat | 31 − 4(shape − 1) |
| 9–16 | 1 px per 4 px | ⌊`c` / 4⌋ + 4(shape − 9) |
| 17–25 | 1 px per 2 px | ⌊`c` / 2⌋ + 4(shape − 18) |
| 26–35 | 2 px per 3 px | ⌈2(`c` + 1) / 3⌉ + 4(shape − 28) |
| 36–46 | 1 px per px | `c` + 4(shape − 39) |
| 47–53 | 2 px per px | 2`c` + 8(shape − 50) |
| 54–58 | 4 px per px | 4`c` + 16(shape − 57) |
| 59 | 4 px per px, then flat | the smaller of 4`c` − 16 and 15 |
| 60 | flat, then 4 px per px | the larger of 4`c` − 16 and 15 |
| 61 | flat | 31 |
| 62 | flat | 15 |
| 63 | — | not used by any grid; not defined here |

Examples: shape 5 is flat at rise 15 (the line in the middle of the band). Shape 12 rises
from 12 to 15 over the half. Shape 17 has no surface on positions 0–7
and rises from 0 to 3 on 8–15. Shape 46 rises from 28 to 31 on 0–3 and
stays at 31 after that. Shape 26 uses the 2-per-3 steps 1, 2, 2, 3, 4,
4, …: it has no surface on positions 0–9 and rises 0, 0, 1, 2, 2, 3 on
10–15.

The steep faces of `steep-faces.md` are the 4-px-per-px shapes (54 to
60). The 1-in-4 slopes of `slopes.md` are 9 to 16.

## The ground below a point

The behavior files use "the ground below" a player (or other object):
the surface that the player stands on or will land on next. For level x
column `x` and feet line `F`, it is found from at most four cells of
column `x`. Let `k` be the band of `F`, taken as band 0 if `F` is above
line 1 and as band 15 if `F` is below line 512.

1. **The feet's band.** If band `k` has a surface in column `x` and it is
   not the band's top line, that surface is the ground below. This holds
   even when the surface is **above** `F` (a point inside the ground has
   the ground's surface in its band as the ground below).
2. **A surface at the top of the band.** If band `k`'s surface is its top
   line (rise 31):
   - if band `k` − 1 has a surface in column `x`, that one is the ground
     below (only one band up is looked at, even if that surface is also
     at its band's top);
   - otherwise the ground below is band `k`'s top line.
   If `k` is 0, or the surface found in band `k` − 1 is line 1, the
   ground below is **above the level**: treat it as higher than any
   point in the level.
3. **No surface in the feet's band.** The ground below is the surface in
   band `k` + 1, or if there is none, in band `k` + 2. Bands below 15
   have no surface. If neither has one, there is **no ground** below.

This is the reach in `view-vertical.md` ("its band is at most 2 more than
the band of `F`"). Further down than two bands, nothing is found.

Checked against the game along one route through most of the first
level: on 1674 frames in the air, the ground below the player matched
these rules every time, including 286 frames with no ground. On 5497
frames on the ground, the player's feet line was the ground below every
time. It also matched on 136 frames at the start of the second level.
The rules were also checked against the game's own way of finding the
ground below, for every column of every grid of this kind, with no
differences.

## Example (synthetic)

A grid of one column with type table entries:

- type 1: left half byte 0x05 (shape 5), right half 0x05.
- type 2: left half 0x0C (shape 12), right half 0x8C (shape 12 flipped).

Column 0, band 10 holds a cell word 0x0001 (type 1), band 11 holds
0x4002 (type 2, mirrored), the other bands 0.

- `x` = 3, band 10: `p` = 3, left half, shape 5, rise 15 → line 32·10 +
  32 − 15 = 337.
- `x` = 3, band 11: mirrored, `p` = 28, right half (flipped), `c` = 12 →
  15 − 12 = 3, shape 12 at 3: ⌊3/4⌋ + 12 = 12 → line 352 + 32 − 12 = 372.
- `x` = 20, band 11: mirrored, `p` = 11, left half (not flipped), `c` =
  11, rise ⌊11/4⌋ + 12 = 14 → line 370.
- Ground below `x` = 3, `F` = 300 (band 9): band 9 is empty, band 10 has
  337 → 337.
- Ground below `x` = 3, `F` = 360 (band 11): band 11 has 372, below `F`
  → 372.
- Ground below `x` = 3, `F` = 380 (band 11): 372 again, above `F`.
- None of these surfaces is passable from below: bit 6 is clear in all
  four half bytes. A half byte of 0x45 would be shape 5, passable from
  below; with type 1 = 0x45, 0x05, the surface at `x` = 3 in band 10 would
  be passable and the one at `x` = 20 would not. Its ground at `x` = 3 is
  lines 338 to 352.

More cases, as bytes, are in `../vectors/level-ground/`.

## Row grids

A row grid uses the same cell words, type table, half bytes and shapes
as a column grid: "The type table", "Ground passable from below" and
"Shapes" apply unchanged. What differs is how tall the grid is, how its
cells are stored, and two steps of "The ground below a point".

Positions are as in "Positions". Level y is measured from the top of the
grid's band 0.

### Bands and storage

A row grid has `R` bands, band 0 at the top. `R` can be far more than
16. The one row grid measured had 116 bands (3712 px). Band `k` holds
lines 32`k` + 1 to 32`k` + 32, as before.

Storage: the bands one after another from band 0. Each band is a **row**
of 64 cell words, so a row covers level x 0 to 2047. Cell words are as
in "The grid", 16 bits, low byte first. A row takes 128 bytes and the
grid 128`R` bytes.

The cell for level x column `x` in band `k` is cell word number
64`k` + ⌊`x` / 32⌋, counted from the start of the grid. For level x of
2048 or more this word lies in a later row. For example, the cell for
level x 2048 to 2079 in band `k` is the first cell of band `k` + 1's
row. The surface found there still belongs to band `k`: its line is
32`k` + 32 − `s`, as in step 5 of "The surface in one cell". Level x
from 2048 to 4095 was checked in the game this way.

"The surface in one cell" is otherwise unchanged.

### The ground below a point in a row grid

For level x column `x` and a feet line `F` of 1 or more, let `k` be the
band of `F`. `F` is not limited to the grid's bands. Steps 1 to 3 of
"The ground below a point" apply, with two changes:

- **Step 2 has no "above the level".** If band `k`'s surface is its top
  line and band `k` − 1 has a surface in column `x`, that surface is the
  ground below. This is true even when it is band `k` − 1's top line,
  and even when it is line 1. If band `k` − 1 has no surface there, the
  ground below is band `k`'s top line. Band 0's top line is not covered.
- **Step 3 is skipped in every 16th column.** In step 3 (no surface in
  the feet's band), if ⌊`x` / 32⌋ is a multiple of 16, there is no ground
  below. Bands `k` + 1 and `k` + 2 are not looked at, even if they
  have ground. These are level x 0 to 31, 512 to 543, 1024 to 1055 and
  so on, and also 2048 to 2079 and so on, past the first row. In every
  other column, step 3 is as before.

Step 1 is unchanged. As before, nothing is found further down than two
bands, and the surface found in the feet's band can be above `F`.

The skipped step 3 shows when falling. In one of those columns, a
player falling towards ground one or two bands below has no ground
below until the feet line reaches the ground's band. Then step 1 finds
the ground, so the landing itself is not changed.

No half byte in the row grid measured had bit 6 set, so none of its
ground is passable from below.

### Checks

The row grid measured holds nine rooms (parts of levels). In each, the
ground where the player starts matched these rules. Runs through three
of the rooms gave 3797 frames in the air and 1857 frames on the ground. Frames on ropes are not counted. In the air, the
ground below matched these rules on every frame. That includes 18 frames
in a skipped column, 3 of them with ground one or two bands below that
was not found. On the ground, the feet line was the ground below on
every frame.

The player was also placed in the air at 4800 points, at level x 0 to
4095 and feet lines in bands 1 to 113. The points were chosen so that
every step of the rules came up. Where the player was still in the air
after one frame, the ground below matched every time: 3191 points.
Where the player landed in that frame, the landing line was the ground
below the feet line at the end of the frame every time: 1609 points.
In 23 of them that was one line below where the player was placed,
because the player had been placed on a band's bottom line.

The rules were also checked against the game's own way of finding the
ground below, for every column and band of the grid, and every third
feet line. There were no differences.

### Example (synthetic)

A row grid of 3 bands. Its type table has type 1 as in the column
grid example (shape 5 in both halves: flat at rise 15) and type 3 with half
bytes 0x01, 0x01 (shape 1 in both halves: flat at rise 31, the band's
top line). Band 2's row holds type 1 at columns 16 and 17. Band 1's row
holds type 3 at column 17. Every other cell word is 0. As cell words:
number 64·2 + 16 = 144 and 145 hold 0x0001, and number 64 + 17 = 81
holds 0x0003.

- Surface at `x` = 550 (column 17), band 2: rise 15 → line 32·2 + 32 −
  15 = 81. Band 1: line 33.
- Ground below `x` = 550, `F` = 10 (band 0): band 0 is empty, band 1
  has 33 → 33.
- Ground below `x` = 520 (column 16, a multiple of 16), `F` = 10 (band
  0): band 0 is empty, so step 3 is skipped → no ground. With `F` = 70
  (band 2): band 2 has 81 → 81.
- `x` = 2048 + 550 = 2598 is column 81 = 64 + 17. In band 0 it reads
  cell word 81, the one stored at column 17 of band 1's row, and gives a
  band-0 surface at its top line: line 1. In band 1 it reads cell word
  145 (rise 15) → line 32 + 32 − 15 = 49.
- Ground below `x` = 2598, `F` = 40 (band 1): 49. With `F` = 60: 49
  again, above `F`.

More cases, as bytes, are in `../vectors/level-ground/` (the `rows-`
files).

## Ceiling grids

The levels where the player swims use a third kind of grid, a
**ceiling grid**. It is stored like a row grid, but its shape numbers are
its own and half of them are ceilings. Some cells have ground above a
line instead of below it. "The ground below a point" changes to match.

What is the same as in a row grid: "Bands and storage" (64 cell words
per row, the cell for level x 2048 or more lies in a later row), the
cell word, the type table, mirroring and flipping, and steps 1 to 3 of
"The surface in one cell". Positions are as in "Row grids". The one
ceiling grid measured had 391 bands (12 512 px). No half byte in it had
bit 6 set.

### Halves in a ceiling grid

Take the half byte and the position `c` as in steps 1 to 3 of "The
surface in one cell". Let `n` be the half's shape number, bits 0–5 of the
half byte. The half is one of three kinds:

- **Empty**: the cell word is 0, its type is `N` or more, or `n` is 0.
- A **floor half**: `n` is 1 to 31. It may have a surface, as in a column
  grid.
- A **ceiling half**: `n` is 32 to 63. It always has a **ceiling value**
  `u`, a whole number of 31 or less. `u` can be below 0.

Floor shapes `n` from 1 to 30 give a value `v` at position `c`:

| `n` | Slope | `v` at position `c` |
|---|---|---|
| 1–4 | flat | 31 − 8(`n` − 1) |
| 5–12 | 1 px per 4 px | ⌊`c` / 4⌋ + 4(`n` − 5) |
| 13–16 | 1 px per 2 px | ⌊`c` / 2⌋ + 8(`n` − 13) |
| 17–21 | 1 px per px | `c` + 8(`n` − 18) |
| 22–26 | 4 px per px | 4`c` + 16(`n` − 25) |
| 27 | 4 px per px, then flat | the smaller of 4`c` − 16 and 15 |
| 28 | flat, then 4 px per px | the larger of 4`c` − 16 and 15 |
| 29 | flat | 31 |
| 30 | flat | 15 |

Shapes 0 and 31 have no `v`.

- **Floor half**, shape `n`: if `n` has no `v`, or `v` < 0, the half has no
  surface. Otherwise its rise is `v`, or 31 if `v` > 31. The surface is
  at line 32`k` + 32 − rise, as in a column grid.
- **Ceiling half**, shape `n`: take floor shape `n` − 32 at the same
  position. If it has a `v` of 1 or more, then `u` = 31 − `v`, with `v` not
  limited to 31. Otherwise `u` = 31. The half's **ceiling line** in band
  `k` is 32`k` + 32 − `u`. `u` = 0 gives the band's bottom line. A `u` below
  0 gives a line below the band.

So ceiling shape 32 + `m` is floor shape `m` turned upside down. Floor
shape 3 is flat at rise 15. Ceiling shape 35 is `u` = 16 everywhere, with
its ceiling line on line 32`k` + 16. Ceiling shapes 32 and 63 are `u` = 31
everywhere. Ceiling shape 33 is `u` = 0 everywhere. Ceiling shape 53 is
`u` = 7 − `c`, which is below 0 from position 8 on.

How a ceiling acts on a swimming player is not covered. This file only
gives the ground below a point.

### The ground below a point in a ceiling grid

For level x column `x` and a feet line `F` of 1 or more, let `k` be the
band of `F`. Look at the half that column `x` uses in band `k`. That is
the **feet's half**.

1. **A ceiling at the feet.** If the feet's half is a ceiling half with
   `u` of 1 or more, its ceiling line is the ground below.
2. **A floor at the feet.** If the feet's half is a floor half with a
   surface below band `k`'s top line, that surface is the ground below.
   Either way it can be above `F`.
3. **A floor at the top of the band.** If the feet's half is a floor
   half whose surface is band `k`'s top line (rise 31), look at band
   `k` − 1:
   - a ceiling half: the ground below is its ceiling line minus 32, 32
     lines above the ceiling line;
   - a floor half with a surface: that surface, even on its band's top
     line;
   - otherwise (empty, or a floor half without a surface): band `k`'s
     top line.
   Nothing is ever "above the level", and band 0's top line is not
   covered, as in a row grid.
4. **Nothing at the feet.** If none of these applies, the feet's band
   has nothing. The feet's half is empty, a floor half without a surface,
   or a ceiling half with `u` of 0 or less. Look at band `k` + 1:
   - If exactly one of the feet's half and band `k` + 1's half is a
     ceiling half, then: if band `k` + 1's half is empty or has shape
     32, the ground below is band `k`'s bottom line, 32`k` + 32.
     Otherwise there is no ground.
   - Otherwise, if band `k` + 1's half is a floor half with a surface, or
     a ceiling half with `u` of 0 or more, that line is the ground below.
   - Otherwise, the same goes for band `k` + 2, whatever kind the feet's
     half is: a surface, or a ceiling line with `u` of 0 or more, is the
     ground below. If it has neither, there is no ground.

Unlike a row grid, no column skips step 4. As before, nothing is
found further down than two bands.

In step 4, a ceiling half with `u` below 0 is like a floor half without a
surface: it has nothing, but it still counts as a ceiling half when the
kinds are compared. In step 3 it gives a line from its `u` all the same.

### Checks

The player was placed in the water, standing still, at 3793 points in one
level and 3783 in another, at level x 0 to 4095 and feet lines in bands
2 to 388. The points were chosen so that every case in the steps above
that the level's ground has came up, most of them 150 times. After one
frame, the ground below matched these rules at the point where the player was
placed or where the player ended the frame, every time.

The grid's own types do not use every shape. They were replaced by
made-up half bytes, every shape number and flipped or not, in four
copies of the game. 8247 more points, at least 14 for every case of the
rules, and every shape number at the feet: 8238 matched. In the other
9, the feet were on a ceiling line or 1 px below one. In those, the
value matched the ground below a point 1 to 3 px above the feet. That
is the swimming player's reaction to the ceiling, which is not
covered.

The ground where the player starts matched in all nine of the rooms that
use this grid. The rules were also checked against the game's own way of
finding the ground below, for every column and every seventh feet line
of the grid, with its own types and with the four made-up type tables.
That is about 7.2 million values each, with no differences.

### Example (synthetic)

A ceiling grid of 3 bands. Its type table has type 1 with half bytes
0x03, 0x03 (floor shape 3: flat at rise 15), type 2 with 0x23, 0x23
(ceiling shape 35: `u` = 16) and type 3 with 0x21, 0x21 (ceiling shape
33: `u` = 0). Band 1's row holds type 2 at column 0 and type 3 at column
1. Band 2's row holds type 1 at column 0. Every other cell word is 0.

- `x` = 5, `F` = 40 (band 1): ceiling with `u` = 16 → ceiling line 32 +
  32 − 16 = 48. With `F` = 60: 48 again, above `F`.
- `x` = 5, `F` = 70 (band 2): floor at rise 15 → line 81.
- `x` = 5, `F` = 10 (band 0): band 0 is empty and band 1 is a ceiling
  half of shape 35 → no ground, even though band 2 has a floor.
- `x` = 37 (column 1), `F` = 40 (band 1): a ceiling with `u` = 0, so
  nothing at the feet. Band 2 is empty → band 1's bottom line, 64.
- `x` = 37, `F` = 10: band 1 is a ceiling half of shape 33 → no ground.
- `x` = 2048 + 5 = 2053, `F` = 8 (band 0): this reads cell word 64, the
  first cell of band 1's row, a ceiling with `u` = 16 → line 0 + 32 −
  16 = 16.

More cases, as bytes, are in `../vectors/level-ground/` (the
`ceiling-` files).

## The first level's data

Where the first level's grid and type table start in the image is in
`locators.md`. Both are column-grid data as above:

- The grid is 1 310 columns. The first level uses columns 0 to 183
  (level x 0 to 5 887); the columns after them hold other levels'
  ground.
- The type table has `N` = 234 types.

## Not covered

- Whether being passable from below matters for anything other than
  the player (walkers, carried and thrown objects).
- Some levels may also use a second grid.
- In ceiling grids: what ceilings do to a swimming player, and from
  which point near the feet a swimming player's ground below is taken
  when the player moves or meets a ceiling during a frame. Band 0's top
  line in step 3 and bands past the grid's last band, as for row grids.
  Bit 6, which no half byte in the grid measured has.
- In row grids: band 0's top line in step 2, a feet line of 0 or less,
  and bands past the grid's last band in step 3. All the rooms measured
  were inside the grid.
- Where the grids, type tables and `N` of levels other than the first
  are stored, which part of a grid each of them uses, and the first
  level's left and right ends within its 184 columns.
- Level x outside the grid.
- Anything other than ground and cell pictures (walkers, objects,
  limits of the view). Cell pictures are in `graphics.md`.
