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

Where the first level's placement list starts in the image is in
`locators.md`.

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
| 8 | No object. The first level's one class 8 entry starts the dimming (`../behavior/dimming.md`) |

Other classes are used by other levels' lists and are not covered.

### Definitions

Entries with the same reference `r` place the same kind of object, with
the same settings (for example a walker's heading and whether it waits a
frame before its first step, `../behavior/walker-view.md`). Different
references can place objects of the same kind with different settings.
The first level's list uses 36 different references in its class 1 and
2 entries, and its group's members use one more. The kind is told by the
reference alone: the same reference given to an entry somewhere else
places the same kind of object there (checked for the references in
"In the first level" below).

A class 1 or class 2 reference leads to part of the original program.
This spec doesn't describe what is there. The importer tells which kind
of object an entry places by **fingerprint** (`../REVIEW.md`, "The
second exception: kind fingerprints"), as in "Kinds" below.

### Groups

A group's members are a second list in the same form. It starts at the
location in the same bank as the placement list (`rom-image.md`, "Banks
and locations") with address `r` + 8, and runs up to the next entry of
all zeros. The 8 bytes at `r` are not part of it. In the first level
they are all zero.

The members' positions are their own entries' `x` and `h`. In the first
level's group, the members are class 1 entries and the group entry's
`x` and `h` are its first member's.

## Kinds

### The fingerprint of a reference

For a reference `r` and each bank block `n` of the image (`rom-image.md`,
"Banks and locations"; 0 to 63 in a 4 MiB image), take the position

    p = 65 536 n + r

and the 64 image bytes at positions `p` to `p` + 63. These bytes may run
on past the end of bank block `n` into the next one. If `p` + 63 is past
the end of the image, block `n` gives no fingerprint. The **candidate
fingerprint** for block `n` is the SHA-256 of those 64 bytes, written as
64 lowercase hexadecimal digits.

`locators.md` lists, in each supported image's section, **kind
fingerprint** rows: a fingerprint and the kind it stands for. If one of
`r`'s candidate fingerprints is in that image's rows, the entry places an
object of that row's kind. In a supported image, at most one candidate
matches a row. If none does, the kind is not covered.

Class 5 and class 8 references are not looked up this way. A group's
members are looked up by their own references.

### Kind names

| Kind | The object | Behavior |
|---|---|---|
| `small-walker` | A small walker | `walker.md`, `walker-contact.md`, `walker-view.md`, `riding-walkers.md` |
| `medium-walker` | A medium walker | `walker.md`, `walker-contact.md`, `riding-walkers.md` |
| `large-walker` | A large walker | as for `small-walker` |
| `thrower` | A thrower | `thrower.md`, `ground-action-enemies.md` |
| `ball` | A ball placed in the level, not thrown (moving when it appears) | `thrower.md`, `ball-contact.md` |
| `bunch` | A bunch | `bunches.md` |
| `letter-1` to `letter-4` | The letter with that number | `letters.md` |
| `barrel` | An object that can be carried and holds nothing | `carrying.md`, `thrown-objects.md` |
| `holder` | An object that can be carried and holds the missing character | `carrying.md`, `holder-breaking.md` |
| `end-marker` | The level's end marker | `level-end.md` |
| `animal-crate` | A crate holding an animal | `animal-crate.md` |
| `restart-barrel` | The restart barrel | `restart-barrel.md` |
| `stinger` | A stinger (flying enemy) | `stinger.md` |
| `tyre` | A tyre that throws the player up | `tyre.md` |
| `hidden-bunch` | A hidden object that the ground action strikes, releasing a bunch | `hidden-objects.md` |
| `hidden-barrel` | A hidden object set off by a long fall onto it, releasing a `barrel` | `hidden-objects.md` |
| `hidden-metal-barrel` | A hidden object set off by a long fall onto it, releasing a metal barrel | `hidden-objects.md` |

The behavior files are in `../behavior/`. A row gives the kind only:
the settings of the object (for example a walker's heading) are not
given by it.

The `holder` reference is the one reference seen to make two objects
appear (see "When placed objects appear"). The holder is one of them;
what the second one is, is not covered.

The `stinger` and `tyre` rows are for references that the first level
doesn't use; where the other levels' placement lists are is not covered
yet. Each of the three `tyre` references was given, in a copy of the
image, to one class 1 entry near the first level's start in place of
its own reference, and placed a tyre there.

### In the first level

20 of the first level's 37 references have a kind fingerprint row. They
are used by 47 of its entries (counting group members): 5 small
walkers (all three group members among them), 6 medium walkers, 3 large
walkers, the
thrower, a ball, 4 bunches, the four letters (one each), a barrel, 2
holders, the animal crate, the restart barrel, the end marker,
15 hidden bunches, a hidden barrel and a hidden metal barrel. The other
references place things that
have no behavior file yet (for example scenery and signs), or that were
not seen.

Checked in the game: every object seen from these 30 entries, in the
runs in "When placed objects appear" and, for the end marker, the
restart barrel and the animal crate, in those of `../behavior/level-end.md`,
`../behavior/restart-barrel.md` and `../behavior/animal-crate.md`, was of
the row's kind. Each of the 17
references was also given, in a copy of the image, to one class 1 entry
near the level's start in place of its own reference. Each time an
object of the row's kind appeared there; with three of the letters'
references (letters 1, 3 and 4), taking the object there took the
letter with that number. A class 2 reference given to a class 1 entry
placed the same kind as in its own class 2 entry. Each of the three
hidden-object references, given to a class 1 entry the same way, placed
a hidden object that was set off and released its item as
`../behavior/hidden-objects.md` says for its kind.

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
- **Class 8.** Nothing appears. For the first level's class 8 entry,
  the first frame on which it is in the window is the dimming's frame T
  (`../behavior/dimming.md`).

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
reader gets from it, and a made-up image with kind fingerprint rows and
the kinds a reader looks up from it: see its `README.md`.

## Not covered

- Where the placement lists of levels other than the first are in the
  image.
- What a definition holds, and the kinds of the references without a
  kind fingerprint row. An object's settings (a walker's heading and
  patrol, whether it takes a first step on its first frame, a placed
  ball's speeds) also belong to the definition and are not covered.
- What class 2 changes compared with class 1: in the runs measured,
  class 2 objects appeared and started like class 1 objects.
- What class 8 entries do other than the first level's (which starts
  the dimming), what a class 8 entry's `h` and reference mean, and the
  classes used only in other levels.
- When objects other than walkers are removed, and whether they come
  back.
- Entries far above or below the view, and when the entry that is out
  of order appears (it is past the furthest point the runs reached).
- Groups in other levels: their members can be of other classes, and the
  group entry's position isn't always its first member's.
- The characters' starting position, the level's left and right ends,
  the view's limits, the level's exit, and other areas entered from the
  level.
