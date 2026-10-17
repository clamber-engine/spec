# Level ground

Titles: `title-1`, `title-2`

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
**ceiling grid**. Every area on a ceiling grid is underwater all over
except one, where the player rides instead (`../behavior/swimming.md`,
"Where the player swims"). It is stored like a row grid, but its shape
numbers are its own and half of them are ceilings. Some cells have ground above a
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

## In `title-2`

This section says how `title-2`'s ground differs from the grids above.
Anything it doesn't mention works as above. Most of it was first
measured on `title-2`'s first level, which uses a column grid. "Areas
and layouts", "Row grids", "The grid's first cell" and "Shapes 63 to
79" (all "`title-2`") were measured on every level entry that uses a
column grid or a row grid (42 of the 48).

### Positions (`title-2`)

Level x and level y are in whole px, growing to the right and downward
as above, but the grid doesn't start at 0. The grid's left edge is at
level x 256 and its top line (line 1) is at level y 257:

- grid column `n` covers level x 256 + 32`n` to 256 + 32`n` + 31, and
  the px column within the grid for level x `x` is `x` − 256;
- line `L` is level y `L` + 256, so a surface at line `L` is one that a
  player stands on with its feet at level y `L` + 256.

The player's position is given in the same level positions.

### The map and the grid (`title-2`)

A level's ground comes from a **map**, stored packed
(`nibble-packing.md`). In a column grid (layout 0 in "Areas and layouts"
below), the unpacked map is a run of **map columns**,
each 16 cell words, top band first, stored as a column of a column grid
is (32 bytes, cell words low byte first). The level's grid is a run of
consecutive map columns: grid column 0 is map column `m`, grid column 1
is map column `m` + 1, and so on, for as many columns as the level has.
Map columns before and after them are not part of the level's grid.
"Areas and layouts" says how `m` and the number of columns are given,
and how a row grid is taken from its map.

Cell words are as for column grids above: bits 0–13 the type, bit 14
mirrored, bit 15 no effect on the ground. A cell word of 0, or a type of
`N` or more, has no ground.

### The type table (`title-2`)

Each type has four bytes: the **left half word**, then the **right half
word**, each 16 bits stored low byte first. Type `t`'s half words are at
bytes 4`t` to 4`t` + 3 of the table.

| Bits | Meaning |
|---|---|
| 0–7 | The half's **shape**, as in "Shapes"; 0 = no ground |
| 8–13 | 0 in every type of the first level; some other levels set bit 8; see "Not covered in `title-2`" |
| 14 | **Passable from below** |
| 15 | **Flipped**: if set, the half's shape is mirrored left to right |

"The surface in one cell" then works as above with half words in
place of half bytes, and with the px column taken from the grid's left
edge: `p` = (`x` − 256) mod 32 in grid column ⌊(`x` − 256) / 32⌋. The
surface in band `k` is at line 32`k` + 32 − `s`, that is at level y
32`k` + 288 − `s`.

Shapes 0 to 62 are exactly as in "Shapes". The first level's types use
shapes from 0 to 60 only. Other levels also use shapes 63 to 79: see
"Shapes 63 to 79 (`title-2`)".

### Passable from below (`title-2`)

Bit 14 marks the half's surface as passable from below, as bit 6 does
in a column grid above (the half chosen after the cell's mirroring).
In the first level's grid, the cells whose half words have bit 14 set
are all platforms with open space under them.

Checked on one such platform: a player jumping straight up from the
floor 68 px below it rose through it and landed on it. With bit 14
cleared in that platform's types, in a copy of the image, the same jump
was stopped below the platform and the player fell back to the floor.
Where exactly a rising player is stopped by ground that isn't passable
from below is not covered.

### The ground below a point (`title-2`)

For level x `x` and feet at level y `y`, let `F` = `y` − 256 (the feet
line) and use the grid's px column `x` − 256. The rules are those of
"The ground below a point" above, with one difference: if `F` is 0 or
less (feet at level y 256 or above), the feet's band `k` is taken as
band **15**, not band 0. As above, `k` is band 15 if `F` is more than
512. Results are lines; the ground below is at level y line + 256.
"The grid's first cell (`title-2`)" below applies too.

### Areas and layouts (`title-2`)

Each map comes with an **area record**. The map can hold several
**areas** (parts of levels), and each area's grid is a rectangle of the
map. The record holds 16-bit words, low byte first:

1. the map's width in px;
2. the map's height in px;
3. then 10 bytes for each area, in order: the left edge, right edge,
   top edge and bottom edge of the area's rectangle, and the area's
   **layout** number.

Edges are in px from the map's top-left corner and are multiples of 32.
The right and bottom edges are the first px past the rectangle, so the
rectangle has (right − left) / 32 columns and (bottom − top) / 32 rows of
cells. The layout says how the map is stored and what kind of grid the
area has:

| Layout | Map stored | The area's grid |
|---|---|---|
| 0 | Column by column: each map column is (map height) / 32 cell words, top first | A column grid of 16 bands, as above |
| 4, 5, 6, 7 | Row by row: each map row is (map width) / 32 cell words, left first | A row grid ("Row grids (`title-2`)") |
| 1, 2, 3, 8 | Column by column | Not covered |

The area's grid is the rectangle: grid column `n` is map column
left / 32 + `n`, and band `k` is map row top / 32 + `k`. Layouts 4, 5, 6
and 7 give the same ground inside the rectangle. In layout 0 the map was
16 cells tall and the rectangle was the map's whole height in every area
measured, so a layout-0 area's grid is map columns left / 32 to
right / 32 − 1: that is `m` in "The map and the grid" above.

For a row map, the map's height in the record can be more than the
unpacked map holds; only the rows of the rectangle are used.

Positions are as in "Positions (`title-2`)" for both kinds of grid: grid
column 0 starts at level x 256 and band 0's top line is at level y 257.

### Row grids (`title-2`)

A row grid has as many bands as its rectangle has rows: `R` = (bottom −
top) / 32. Band `k` holds lines 32`k` + 1 to 32`k` + 32 (level y
32`k` + 257 to 32`k` + 288), as before, and band 0 is at the top. The
grids measured had 8 to 408 bands. The cell for level x `x` in band `k`
is in grid column ⌊(`x` − 256) / 32⌋ and band `k`. "The surface in one
cell" works as in "The type table (`title-2`)".

**The ground below a point in a row grid.** For level x `x` and feet at
level y `y`, let `F` = `y` − 256 and let `k` be the band of `F`. Steps 1
to 3 of "The ground below a point" apply, with the same change to step 2
as in `title-1`'s row grids ("The ground below a point in a row grid"),
but with no change to step 3:

- **Step 2 has no "above the level".** If band `k`'s surface is its top
  line and band `k` − 1 has a surface in column `x`, that surface is the
  ground below, even when it is band `k` − 1's top line. If band `k` − 1
  has no surface there, the ground below is band `k`'s top line.
- **Step 3 is never skipped.** Unlike `title-1`'s row grids, bands `k` +
  1 and `k` + 2 are looked at in every column.

Step 1 is unchanged. As before, nothing is found further down than two
bands, and the surface found in the feet's band can be above `F`.
Results are lines, and the ground below is at level y line + 256.
"The grid's first cell (`title-2`)" applies too.

Only points whose search stays inside the rectangle are covered: `F`
from 1 to 32`R`, with no step looking at a band above band 0 or below
band `R` − 1. So a surface at band 0's top line, with `F` in band 0, is
not covered.

### The grid's first cell (`title-2`)

In both kinds of grid, the cell in grid column 0 and band 0 (level x 256
to 287, lines 1 to 32) **never has ground**, whatever its cell word.
The rules above treat it as a cell word of 0. Many levels have a
non-empty cell there, often a full cell at the top-left corner of the
grid.

### Shapes 63 to 79 (`title-2`)

These shapes are used in both kinds of grid. Some of them depend on the
point being looked up, not only on the position in the half. Let `F` be
the feet line whose ground below is being found, and let **`h`** = 31 −
((`F` − 1) mod 32), the height of `F` above the bottom line of its own
band: 0 on the band's bottom line, 31 on its top line. The same `h` is
used in every band that the rules look at (bands `k` − 1, `k` + 1 and `k`
+ 2 as well as `k`). With `c` the position 0–15 in the half, after the
flips of "The surface in one cell", the rise is:

| Shape | Rise |
|---|---|
| 63 | 19 |
| 64 | 15 |
| 65 | 31 for `c` from 0 to 3; no surface for 4 to 15 |
| 66 | 31 for `c` from 0 to 3; 15 for 4 to 15 |
| 67 | 31 if `c` is 0 to 3 and `h` ≥ 8; otherwise no surface |
| 68 | 31 if `c` is 0 to 3 and `h` ≥ 16; otherwise no surface |
| 69 | 31 if `c` is 0 to 3 and `h` ≥ 24; otherwise no surface |
| 70 | 31 for `c` from 0 to 3; 19 for 4 to 15 |
| 71 | 31 |
| 72 | 15 |
| 73 | 2`c` for `c` from 0 to 7; 15 for 8 to 15 |
| 74 | 15 for `c` from 0 to 7; 2`c` for 8 to 15 |
| 75 | 31 if `h` ≥ `c`; otherwise no surface |
| 76 | 31 if `h` ≥ `c` + 16; otherwise no surface |
| 77 | 31 if `h` ≥ 17; otherwise no surface |
| 78 | 31 if `h` ≥ 4`c`; otherwise no surface |
| 79 | 31 for `c` from 0 to 7; no surface for 8 to 15 |

So with shapes 75 to 78 (and 67 to 69 in their first 4 positions), a
point high enough in its band finds the band's top line in the cell
(rise 31), and a lower point finds nothing there. The cell acts as
ground above a sloping or level line. A rise of 31 from these shapes
works like any other in step 2 of "The ground below a point".

No type in any level measured uses a shape above 79.

### Example (synthetic, `title-2`)

A type table with `N` = 3: type 1 has half words 0x0005, 0x0005 (shape 5,
flat at rise 15); type 2 has half words 0x400C, 0x800C (left: shape 12,
passable from below; right: shape 12 flipped). The map has 4 columns;
the level's grid is map columns 1 and 2. Map column 1 holds 0x0001 in
band 10 and 0x4002 in band 11; map column 2 holds 0x0002 in band 3.
Every other cell word is 0.

- Level x 259 (grid px column 3, grid column 0, map column 1), band 10:
  `p` = 3, left half, shape 5, rise 15 → line 32·10 + 32 − 15 = 337,
  level y 593.
- Level x 259, band 11: mirrored, `p` = 28, right half (flipped), `c` =
  15 − 12 = 3, shape 12 at 3: ⌊3/4⌋ + 12 = 12 → line 372, level y 628.
  Its half word is the right one, which doesn't have bit 14: not
  passable from below.
- Level x 276 (`p` = 20), band 11: mirrored, `p` = 11, left half word
  0x400C: shape 12 at 11 → rise 14, line 370, level y 626, passable
  from below.
- Level x 300 (grid column 1, map column 2), band 3: `p` = 12, left
  half, shape 12 at 12 → rise 15, line 113, level y 369.
- Ground below level x 259 with feet at level y 556 (`F` = 300, band
  9): band 9 is empty, band 10 has line 337 → level y 593.
- Ground below level x 259 with feet at level y 250 (`F` = −6): the
  feet's band is taken as band 15, which is empty in this column, and
  there are no bands below it → no ground. (With the `title-1` rule it
  would be band 0, and the search would go on to bands 1 and 2.)

More cases, as bytes, are in `../vectors/level-ground/title-2/`.

### Example (synthetic, row grid, `title-2`)

A type table with `N` = 5: type 1 has half words 0x0005, 0x0005 (flat at
rise 15); type 2 has 0x0001, 0x0001 (a full cell, rise 31); type 3 has
0x004B, 0x004B (shape 75 in both halves); type 4 has 0x0049, 0x004A
(shapes 73 and 74). The map is 6 cells wide and 6 tall (map width 192,
height 192), stored row by row. The area record gives left 32, right
160, top 32, bottom 160 and layout 4, so the grid is map columns 1 to 4
and map rows 1 to 4: `R` = 4 bands, grid columns 0 to 3, level x 256 to
383. In grid terms: band 0 column 0 holds type 2; band 1 column 0 holds
type 2; band 2 column 0 holds type 1; band 1 column 1 holds type 3; band
3 column 1 holds type 1; band 2 column 2 holds type 4. Every other cell
of the rectangle is 0. Every cell around the rectangle (map row 0 and
5, map column 0 and 5) holds type 2, and none of it is part of the grid.

- Ground below level x 260 with `F` = 40 (band 1): band 1 is full (rise
  31, its top line), so step 2 looks at band 0. That is the grid's first
  cell, which has no ground, so the ground below is band 1's top line,
  line 33 (level y 289). Without the first-cell rule it would be band
  0's top line, line 1.
- Level x 260 with `F` = 70 (band 2): band 2 has line 32·2 + 32 − 15 = 81
  → level y 337.
- Level x 300 (grid column 1, `p` = 12, left half, shape 75 at `c` =
  12), band 1:
  - `F` = 40: `h` = 31 − 7 = 24 ≥ 12, so the rise is 31: step 2, band 0
    has nothing in column 1 → line 33 (level y 289).
  - `F` = 52: `h` = 31 − 19 = 12 ≥ 12 → line 33 again.
  - `F` = 53: `h` = 11 < 12, so band 1 has no surface: step 3, band 2 has
    nothing in column 1 and band 3 has line 32·3 + 32 − 15 = 113 → level
    y 369.
  - `F` = 20 (band 0, which has nothing in column 1): step 3 looks at
    band 1 with `h` = 31 − 19 = 12 → rise 31 → line 33. With `F` = 21,
    `h` = 11 and band 1 has nothing; band 2 has nothing either → no
    ground.
- Level x 325 (grid column 2, `p` = 5, left half, shape 73 at 5): rise 10
  → line 32·2 + 32 − 10 = 86 (level y 342). Level x 330 (`p` = 10): rise
  15 → line 81. Level x 345 (`p` = 25, right half, shape 74 at `c` = 9):
  rise 18 → line 78 (level y 334).

More cases, as bytes, are in `../vectors/level-ground/title-2/` (the
`rows-` files).

### Checks (`title-2`)

The map unpacked by `nibble-packing.md` was the same, byte for byte, as
the game's own unpacked map, and the level's grid was the same as the
game's grid for all its columns.

These rules were checked against the game's own ground lookups. Every
lookup made by any object at a level x inside the grid, on 48 000
frames, was compared. On each frame the player was put at a random
place in the first level (often near a surface, sometimes above the
grid's top line or below its bottom line) and moved for one frame with
random buttons held. On 24 000 of the
frames the grid was used as it is; there were 115 198 lookups. On the
other 24 000, six random cells of the game's grid near the player were
changed first (random types, mirrored or not, some of `N` or more, many
filling their band), and the rules were applied to the changed grid;
there were 117 050 lookups. There were no differences in either. Every
step of the rules came up, including both ways of reaching "above the
level" and feet lines of 0 or less and of more than 512.

Shapes 1 to 62 were also compared, at all 16 positions, with the game's
own rule for each shape: no differences.

**Areas, row grids, the first cell and shapes 63 to 79.** Each of the 48
level entries was loaded. 42 use layout 0, 4, 5, 6 or 7: 20 column
grids and 22 row grids (8 to 408 bands, rectangles up to 96 columns
wide, all four row layouts). For every row-grid area the grid built from
its unpacked map and area record as in "Areas and layouts" was the same
as the game's own grid, cell for cell (83 624 cells).

The rules were then checked against the game's own ground lookups, as
above, on all 42 entries. In each entry there were 1500 frames with the
player put at a random point of the area's rectangle (often near a
surface), 1500 frames with six random cells near the player changed
first (including types with shapes 63 to 79 and full cells), and 1500
frames of play from the entry's start with random buttons (stopped when
the player left the area). There were 447 575 lookups in all, 213 835
of them in row grids. There were no differences. Every step of the
rules came up in both kinds of grid. Every shape from 63 to 79 came up,
in 86 to 5 324 lookups each. 8 876 lookups in row grids looked at a
band outside the rectangle; these were not compared (not covered).
Without "The grid's first cell", 10 lookups in 300-frame runs of six
entries differed, and all of them looked at that cell.

### The first level's data (`title-2`)

Where the first level's packed map and type table start in the image is
in `locators.md`. The map unpacks to 29 568 bytes (924 map columns). The
first level's grid is map columns 1 to 223 (223 columns, level x 256 to
7 391). The type table has `N` = 170 types.

### Not covered in `title-2`

- Layouts 1, 2, 3 and 8. Layout 8 is used by some levels whose half
  words have bit 8 set, and the ground below there doesn't follow these
  rules for every object. Layouts 1 to 3 weren't seen.
- Two level entries whose area doesn't use its grid for the ground at
  all, and one area in which no ground is found below a fixed depth for
  some objects.
- Where other levels' maps, type tables and area records are, and which
  area of its record a level entry uses.
- In a row grid: feet lines of 0 or less, and any search that looks at a
  band above band 0 or below the bottom band (including a surface at
  band 0's top line found from band 0).
- Bits 8–13 of a half word, and shape numbers above 79. What shapes 63 to
  79 change for movement, apart from the ground below.
- What being passable from below changes for anything other than the
  player, and the way a rising player is stopped by ground that isn't.
- Level x outside the level's grid columns (in a row grid, outside the
  rectangle's columns).
- Cell pictures (`graphics.md` is `title-1` only).
