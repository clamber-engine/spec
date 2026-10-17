# Placements

Titles: `title-1`

How a level says which objects it places where. A level's **placement
list** holds the objects that appear as the view comes near them:
walkers (`../behavior/walker.md`) and the other things standing or
hanging in the level. This file covers how the list is stored, what its
positions mean, and when the objects it places appear. Vectors:
`../vectors/placements/`.

The list doesn't hold the characters (where they start is in
`level-start.md`), the single collectibles
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

Lists with sections (the underwater levels' lists, "Lists with
sections" below) were measured separately, as described there.

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
| 9 | No object. Only ever the first entry of a list: the list has **sections**, and `r` locates its **section table** ("Lists with sections") |
| 15 | One object, as for class 1, that also has to be inside a vertical window to appear ("When placed objects appear") |

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

A class 1, class 2 or class 15 reference leads to part of the original
program. This spec doesn't describe what is there. The importer tells
which kind of object an entry places by **fingerprint** (`../REVIEW.md`,
"The second exception: kind fingerprints"), as in "Kinds" below. Class
15 references are of the same sort as class 1 references: the kind rows
match them in the same way, and every object seen from a class 15 entry
whose reference has a `bunch` or `letter-1` to `letter-4` row was a
bunch or that letter.

### Groups

A group's members are a second list in the same form. It starts at the
location in the same bank as the placement list (`rom-image.md`, "Banks
and locations") with address `r` + 8, and runs up to the next entry of
all zeros. The 8 bytes at `r` are not part of it. In the first level
they are all zero.

The members' positions are their own entries' `x` and `h`. In the first
level's group, the members are class 1 entries and the group entry's
`x` and `h` are its first member's.

### Lists with sections

A list whose first entry has class 9 is divided into **sections**. In
`title-1` the four underwater levels' lists (`../behavior/swimming.md`)
are like this, and so is one more list, which six small areas that are
not underwater share (the **shared list** below). How the view's path
decides which entries place objects is in "In a list with sections"
below.

The entries are numbered from 0 in stored order: the class 9 entry is
entry 0. A **section** is a run of entries, given by the numbers of its
first and its last entry. The class 9 entry's `x` and `h` are 0 in every
such list, and its reference `r` is the address of the list's **section
table**, at the location in the same bank as the placement list
(`rom-image.md`, "Banks and locations").

The section table is a run of **records** of 8 bytes each, up to a
record whose first two bytes are both 0. That record is not part of the
table, and its other six bytes mean nothing. A record is:

| Bytes | Name | Meaning |
|---|---|---|
| 0 | `a1` | Number of the first entry of the record's **first section** |
| 1 | `a2` | Number of the last entry of its first section |
| 2–3 | `x` | The record's **point**: level x (16 bits, low byte first) |
| 4–5 | `h` | The point's height above the bottom edge, as an entry's `h`: level y = `Yb` − `h` |
| 6 | `b1` | Number of the first entry of the record's **second section** |
| 7 | `b2` | Number of the last entry of its second section |

Records are numbered from 0. Each record's second section is the next
record's first section, so a table of `n` records describes `n` + 1
sections, one after another in the list: record 0's first section, then
each record's second section. A record's point marks where its two
sections meet.

In the four underwater lists:

- There are 2 or 4 records, and their heights `h` increase from one
  record to the next.
- The first section starts at entry 1, each section starts with the
  entry after the previous section's last one, and the last section runs
  to the end of the list. Its last number is either the list's last
  entry or the entry of zeros that ends the list. A reader stops at the
  end of the list either way.
- Within a section the entries are in order of `x`, except for one
  entry, which comes after an entry with a larger `x`.
- The classes used are 9 (entry 0 only), 15 (most entries), 1, 2 and 5.

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
| `entrance` | The place the characters come out of when the level begins | `level-start.md` |
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

21 of the first level's 37 references have a kind fingerprint row. They
are used by 48 of its entries (counting group members): 5 small
walkers (all three group members among them), 6 medium walkers, 3 large
walkers, the
thrower, a ball, 4 bunches, the four letters (one each), a barrel, 2
holders, the animal crate, the restart barrel, the end marker, the
entrance,
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
`../behavior/hidden-objects.md` says for its kind. The `entrance` row
was checked as in `../behavior/level-start.md` ("The entrance").

## When placed objects appear

The window and the view position `V` are as in
`../behavior/walker-view.md`: an entry is **in the window** when

    −31 ≤ x − V ≤ 288

with `V` the view's position, rounded down to a whole pixel, as it was
at the start of the frame. While `V` is less than 32, the window is the
one for `V` = 32: an entry is in the window when 1 ≤ `x` ≤ 320. (Seen
in the underwater runs below: 379 appearances of entries with
`x` − `V` > 288 while `V` was less than 32.)

Class 15 entries also have to be in the **vertical window**:

    −31 ≤ y − U ≤ 256

with `y` = `Yb` − `h` the entry's level y and `U` the view's vertical
position (`../behavior/view-vertical.md`: the level y of the picture's
top row), rounded down to a whole pixel, as it was at the start of the
frame.

- **Class 1 and class 2.** The object appears on the first frame on
  which its entry is in the window. On the level's first frame, the
  objects of all entries already in the window are there.
- **Class 15.** The object appears on the first frame on which its
  entry is in the window and in the vertical window.
- **Class 9.** Nothing appears.
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

### In a list with sections

In a list with sections ("Lists with sections" above), only the entries
of the sections that are **open** can place objects, and an entry's
object appears only under the conditions given here. Everything in this
section uses `V` and `U` as they were at the start of the frame, rounded
down to whole pixels, with `V` less than 32 counting as 32.

A record's point (`x`, `y`) is **in the window** when it is in both the
window and the vertical window of a class 15 entry:

    −31 ≤ x − V ≤ 288   and   −31 ≤ y − U ≤ 256

Otherwise it is **past** the left edge (`x` − `V` < −31), the right edge
(`x` − `V` > 288), the top edge (`y` − `U` < −31) or the bottom edge
(`y` − `U` > 256). A point past a side edge and the top or bottom edge
at once counts as past the side edge.

#### Open sections

Which sections are open depends on the path the view has taken, not only
on where it is. An engine keeps two things for this: the **home**
section, and at most one **crossing** record.

The home section is always open. With a table of `n` records and `k`
from 0 to `n`, it is record `k`'s first section (when `k` < `n`), which
is the same as record `k` − 1's second section (when `k` > 0). Records
`k` − 1 and `k`, those of them that exist, are the home section's
**neighbour records**.

When the area begins, `k` is the number of records, counted from
record 0, whose point is not above the view's top row (`y` ≥ `U`);
counting stops at the first record whose point is above it. No record is
crossing.

Each frame, before any object appears:

- **A crossing starts** on a frame on which no record is crossing and a
  neighbour record's point is in the window. That record is the
  crossing record (record `k` − 1 if both points are in the window). Its
  **far** section, the one of its two sections that isn't home, is open
  from this frame on.
- **A crossing ends** on the first frame on which the crossing record's
  point is not in the window. Its far section is no longer open, unless
  it becomes home:
  - if the point is past the left or the bottom edge (the view has gone
    right or up past the point) and the home section is the record's
    first section, the record's second section becomes home (`k` goes up
    by 1);
  - if the point is past the right or the top edge and the home section
    is the record's second section, its first section becomes home (`k`
    goes down by 1);
  - otherwise the home section stays as it was.
- On other frames, nothing changes.

So while a record's point is in the window, the entries on both sides of
it can appear, and the edge the view leaves the point by decides which
side stays open.

#### When an entry appears

An entry is **left of the window** when `x` − `V` < −31 (for a group
entry, its own `x`). For each open section, on each frame:

- The section's **lead** is the entry after the last entry of the
  section that is left of the window, or the section's first entry if
  none is. If the section's last entry is left of the window, nothing in
  the section appears on that frame.
- An entry is **clear** when it is a class 9 entry, a class 1, 2 or 15
  entry in the window, or a group entry whose objects are there or whose
  first member is not right of the window and last member (the last in
  the members' list) not left of it.
- An entry of the section **comes up** when it and every entry stored
  between the lead and it are clear, its object (for a group entry, the
  group's objects) is not there, and, for class 15, it is also in the
  vertical window.

Entries that come up appear, all members of a group together, with two
exceptions:

1. **The home section waits** on a frame on which an entry of the far
   section appears: nothing in the home section appears on that frame.
2. **Class 2 entries** outside the vertical window don't appear ("Class
   1 and class 2 entries in these lists").

An entry's object (for a group, its objects) is **there** from the frame
it appears until it is removed. Once it has been removed, the entry can
appear again.

Some consequences:

- An entry stored after one with a larger `x` doesn't appear while that
  entry is right of the window, even when it is in the window itself. In
  the underwater list that has one, the two appeared on the same frame.
- An entry stored before one with a smaller `x` doesn't appear while
  that entry is left of the window. In the underwater lists this doesn't
  happen; in the shared list it does (see "Checks").

#### Class 1 and class 2 entries in these lists

Class 1 and class 2 entries need only the window, not the vertical
window, to come up, so they can come up when the entry is far above or
below the view. What then happens depends on the object, and is not
covered as a rule. In the runs:

- Class 2 entries that came up outside the vertical window didn't
  appear: at the end of the frame there was no object (79 256 times),
  except once, when the player was level with the entry. Inside the
  vertical window, they appeared (79 times). These objects are not
  drawn.
- The entries of one class 1 reference (one entry in each of two of the
  lists) did the same: no object at the end of the frame 1 353 times
  outside the vertical window, and 17 appearances, all inside it.
- One other class 1 entry, coming up outside the vertical window,
  appeared and was gone on the next frame, then came up again: its
  object was there on every second frame (3 731 appearances).

#### Example

A made-up list (in `../vectors/placements/`, see "Vectors") with three
sections: entries 1–6, 7–10 and 11–14, and two records. Record 0 joins
the first two sections at a point at level x 960, level y 724. Record 1
joins the last two at level x 60, level y 324. The view starts at
`V` = 0, `U` = 760 and moves right along the bottom, then up:

| Frame | `V` | `U` | What happens |
|---|---|---|---|
| 188 | 700 | 754 | Record 0's point comes into the window (`y` − `U` = −30): a crossing starts. Entries 7–10 can appear as well as 1–6 |
| 228 | 700 | 674 | Entry 9 (far section, level y 644) appears. Entry 6 (home section, also level y 644) would too, but the home section waits |
| 229 | 700 | 672 | Entry 6 appears |
| 332 | 700 | 466 | The point is past the bottom edge (`y` − `U` = 258): the crossing ends, entries 7–10 become the home section, and 1–6 can no longer appear |

#### Checks

- Every underwater area: 8 areas, two for each of the four lists,
  starting at different points. 3 runs each of up to 40 000 frames,
  with the player moved through the open water along random routes so
  that the view followed, and kept from being hurt. On some frames of
  one run per area the player was moved by up to the whole level in one
  frame. 780 444 frames in all; the view's path changed the home section
  120 times and started 168 crossings. Removals were taken from the
  game. There were 5 751 appearances, 1 824 of them from class 15
  entries and 98 of groups, and the home section waited on 511 frames.
  The entries that appeared matched these rules on every frame but 41:
  the class 2 entry that appeared once outside the vertical window, and
  the 40 frames given under "Not covered" (a group entry just before the
  lead).
- Letting the home section also wait on frames on which a far-section
  entry is clear but doesn't appear (class 2 outside the vertical
  window) changed what appeared on none of these frames.
- Starting the area: each of the 8 areas was entered from the map; the
  home section the rule gives was the game's in all 8, and no record's
  point was in the window. Every record's point was at least 256 px
  above or below the view's top row.
- Kinds: in four of the areas, the objects from class 15 entries with a
  `bunch` or letter row were all of that kind (11 appearances).
- The shared list: three of its areas, 66 294 frames, run the same way
  until the player left the area. No section changed. One class
  2 entry appeared once outside the vertical window, and nothing else
  differed. In this list some entries are stored before entries with a
  smaller `x`: taking the lead as the first entry that isn't left of the
  window instead would have been wrong on 98 frames.

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
the kinds a reader looks up from it. It also holds a made-up list with
sections, its section table, a view path, and the entries that appear
on each frame: see its `README.md`.

## Not covered

- Where the placement lists of levels other than the first are in the
  image.
- What a definition holds, and the kinds of the references without a
  kind fingerprint row. An object's settings (a walker's heading and
  patrol, whether it takes a first step on its first frame, a placed
  ball's speeds) also belong to the definition and are not covered.
- What class 2 changes compared with class 1: in the first level's runs,
  class 2 objects appeared and started like class 1 objects. In the
  lists with sections, class 2 entries didn't appear outside the vertical
  window (see "Class 1 and class 2 entries in these lists"). Whether that
  comes from class 2 or from the kind of object, and what those objects
  do, is not covered.
- Which class 1 kinds don't appear, or are gone again, outside the
  vertical window, as in "Class 1 and class 2 entries in these lists".
- What class 8 entries do other than the first level's (which starts
  the dimming), what a class 8 entry's `h` and reference mean, and
  classes other than 1, 2, 5, 8, 9 and 15.
- When objects other than walkers are removed, and whether they come
  back.
- Entries far above or below the view in lists without sections, and
  when the first level's entry that is out of order appears (it is past
  the furthest point the runs reached).
- Whether the rules of "When an entry appears" (leads, clear entries)
  also hold for lists without sections, taken as one section
  covering the whole list. They were only measured on lists with
  sections.
- In lists with sections: whether entries stored after an open
  section's last entry can appear with it (in the runs, none did); what happens
  when an object can't appear because too many objects are already
  there; a record's point exactly
  on the view's top row when the area begins, and a record's point in
  the window on the area's first frame (neither happened in the runs);
  and the shared list beyond the checks above (no section changed in its
  runs).
- In lists with sections, when the entry just before a section's lead
  is a group entry: on some such frames the section's appearances came
  later than "When an entry appears" gives, and the group's objects
  sometimes appeared again after being removed while its entry was left
  of the window and its members still reached into it. In the runs this
  happened on 40 frames in 4 of the 8 areas: on 24, entries of the
  section appeared one frame later than the rules give; on 16, the
  group's objects appeared where the rules give nothing.
- Groups in other levels: their members can be of other classes, and the
  group entry's position isn't always its first member's.
- The level's exit, the object at the first level's left end
  (`../behavior/level-ends.md`, "Not covered"), and other areas entered
  from the level. Where the characters start is in `level-start.md`, and
  the view's limits are in `view-limits.md`, not in the placement list.
