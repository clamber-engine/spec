# Level ground

Titles: `title-1`

How a level's ground is stored, and how to read from it where the ground
is in each 1-px column. This is the data behind the floor heights,
slopes, steep faces and platforms in `../behavior/` (`slopes.md`,
`steep-faces.md`, `platforms-from-below.md`, `landing.md`), and behind
"the ground below" in `view-vertical.md`. Other behavior
files named below are in `../behavior/` too.

This file covers one kind of grid: 16 bands tall. Most of the title's
ground uses this kind, including the first two levels. It was checked
against the game's own ground on the first level and the start of the
second. The other kinds are listed under "Not covered".

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
| 15 | No effect on the ground |

A cell word of 0 is an empty cell with no ground. Types are shared with
the cells' pictures (not covered here), so a grid uses more types than
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

## Not covered

- Whether being passable from below matters for anything other than
  the player (walkers, carried and thrown objects).
- What bit 15 of a cell word means.
- The other kinds of grid: one for levels taller than 512 px, and
  another kind. Some levels may also use a second grid.
- Where a level's grid, type table and `N` are stored, and which part
  of a grid each level uses (its left and right ends). These belong to a
  later part of the level format.
- Level x outside the grid.
- Cell pictures, and anything other than ground (walkers, objects,
  limits of the view).
