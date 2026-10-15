# Placements

Titles: `title-1`

How a level says which objects it places where. A level's **placement
list** holds the objects that appear as the view comes near them:
walkers (`../behavior/walker.md`) and the other things standing or
hanging in the level. This file covers how the list is stored, what its
positions mean, and when the objects it places appear. Vectors:
`../vectors/placements/`.

The list doesn't hold the characters, the single collectibles
(`../behavior/collectibles.md`), or objects made during play (for
example the pieces of a broken object or a defeated walker, and short
effects).

Measured on the first level, with the first character leading: about
12 800 frames from the level's first frame and from points along the
level, through the level and back and forth, as in
`../behavior/walker-view.md`. The first level's list has 68 entries,
counting the members of its one group. Objects from 62 of them were
seen: 230 appearances during the runs, plus the objects already there on
the level's first frame. Of the other six, one places no object (class
8) and five are past the furthest point the runs reached.

## Positions

Positions are **level x** and **level y** in whole px, as in
`level-ground.md`. An entry gives a level x and a **height** `h`: how far
the position is above the level's bottom edge `Yb`
(`../behavior/falling-out.md`). Its level y is

    level y = Yb − h

On the first level `Yb` is 512, the bottom of the grid
(`level-ground.md`). For a walker, the level y is its feet line
(`../behavior/view-vertical.md`): every walker seen appeared at its
entry's `h` and walked off along the floor from there.

## Storage

A placement list is a run of **entries** of 8 bytes each. An entry is
four 16-bit words, each stored low byte first:

| Word | Name | Meaning |
|---|---|---|
| 0 | `c` | The entry's **class** (below) |
| 1 | `x` | Level x |
| 2 | `h` | Height above the bottom edge (see "Positions") |
| 3 | `r` | The entry's **reference**, a 2-byte address (meaning by class) |

An entry whose four words are all 0 ends the list. The entries are in
order of `x`, smallest first, with one exception in the first level: one
entry near the level's right end comes after an entry with a larger
`x`. A reader must not assume the order. Entries can share an `x` (and a whole
position).

## Classes

| `c` | What the entry places |
|---|---|
| 1 | One object. `r` is the address of the object's **definition** |
| 2 | One object, as for class 1 |
| 5 | A **group** of objects. `r` locates the group's members |
| 8 | No object. Not covered |

Other classes are used by other levels' lists and are not covered.

### Definitions

Entries with the same reference `r` place the same kind of object, with
the same settings (for example a walker's heading and whether it waits a
frame before its first step, `../behavior/walker-view.md`). Different
references can place objects of the same kind with different settings.
The first level's list uses 36 different references in its class 1 and
2 entries. Where definitions are stored, what they hold and which kind
of object each one places are not covered.

### Groups

A group's members are a second list in the same form. It starts at the
location in the same bank as the placement list (`rom-image.md`, "Banks
and locations") with address `r` + 8, and runs up to the next entry of
all zeros. The 8 bytes at `r` are not part of it. In the first level
they are all zero.

The members' positions are their own entries' `x` and `h`. In the first
level's group, the members are class 1 entries and the group entry's
`x` and `h` are its first member's.

## When placed objects appear

The window and the view position `V` are as in
`../behavior/walker-view.md`: an entry is **in the window** when

    −31 ≤ x − V ≤ 288

with `V` the view's position, rounded down to a whole pixel, as it was
at the start of the frame.

- **Class 1 and class 2.** The object appears on the first frame on
  which its entry is in the window. On the level's first frame, the
  objects of all entries already in the window are there.
- **Class 5.** All members appear together, on the first frame on which
  the group entry is in the window, even members whose own `x` is still
  outside it.

An object appears at its entry's position: level x `x` and level y
`Yb` − `h` (192 of the 230 appearances exactly). Some objects move on
the frame they appear, so on that frame they are already a little away
from it: up to 1 px in the other appearances (a walker's first step is
1/8 px), and in one more, not counted in the 230, 1.2 px to the left and
2.6 px higher.

The objects of one reference were the exception. They move about
rather than stand, and both entries with that reference had their
object away from the entry's position when it appeared: 90 px to the
left at the same height (there on the level's first frame), and 46 px
to the left and 59 px higher (appearing on the frame its entry entered
the window).

One entry can make more than one object appear (seen with one reference:
two objects at the same position).

How a walker is removed, comes back, or stays away once defeated is in
`../behavior/walker-view.md`. For the other objects it is not covered.

## Example

A list with three entries (the trailing entry of zeros not shown), with
`Yb` = 512:

| `c` | `x` | `h` | `r` | Places |
|---|---|---|---|---|
| 1 | 300 | 175 | `A` | an object of definition `A` at level x 300, level y 337 |
| 5 | 900 | 160 | `G` | the members listed at `G` + 8, all appearing when 900 enters the window |
| 1 | 900 | 200 | `B` | an object of definition `B` at level x 900, level y 312 |

With the view moving right and still at `V` = 11 at the start of a
frame, the first entry is outside the window (300 − 11 = 289). It
appears on the first frame that starts with `V` ≥ 12. The group and the
third entry both appear on the first frame that starts with `V` ≥ 612.

## Vectors

`../vectors/placements/` holds a made-up bank fragment and the entries a
reader gets from it: see its `README.md`.

## Not covered

- Where a level's placement list is in the image (as for the other
  format specs, see `rom-image.md`).
- What a definition holds and which kind of object (walker, container,
  and so on) each reference places. Some objects take a first step on
  their first frame and others don't, and some start away from their
  entry's position; this belongs to the definition.
- What class 2 changes compared with class 1: in the runs measured,
  class 2 objects appeared and started like class 1 objects.
- Class 8 entries (no object appeared for the one in the first level)
  and the classes used only in other levels.
- When objects other than walkers are removed, and whether they come
  back.
- Entries far above or below the view, and when the entry that is out
  of order appears (it is past the furthest point the runs reached).
- Groups in other levels: their members can be of other classes, and the
  group entry's position isn't always its first member's.
- The characters' starting position, the level's left and right ends,
  the view's limits, the level's exit, and other areas entered from the
  level.
