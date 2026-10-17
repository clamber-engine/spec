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
are like this, and so is one more list, used by the crystal-cave rooms.
How the view's position chooses the sections that place objects is in
"In a list with sections" below.

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

In a list with sections ("Lists with sections" above), only the sections
**in use** place objects, and the entries are read in the order
described here. Everything in this section uses `V` and `U` as they were
at the start of the frame, rounded down to whole pixels, with `V` less
than 32 counting as 32.

A record's point (`x`, `y`) is **in the window** when it is in both the
window and the vertical window of a class 15 entry:

    −31 ≤ x − V ≤ 288   and   −31 ≤ y − U ≤ 256

Otherwise it is **past** the left edge (`x` − `V` < −31), the right edge
(`x` − `V` > 288), the top edge (`y` − `U` < −31) or the bottom edge
(`y` − `U` > 256). A point past a side edge and the top or bottom edge
at once counts as past the side edge.

#### The current section

One section is always in use: the **current** section. With a table of
`n` records and `k` from 0 to `n`, the current section is record `k`'s
first section (when `k` < `n`), which is the same as record `k` − 1's
second section (when `k` > 0). Records `k` − 1 and `k`, those of them
that exist, are the current section's **neighbour records**.

When the area begins, `k` is the number of records, counted from
record 0, whose point is not above the view's top row (`y` ≥ `U`);
counting stops at the first record whose point is above it.

#### Watching a record

On every frame, before any object appears:

1. If no record is **watched**, the neighbour records are tried, record
   `k` − 1 first. The first one whose point is in the window becomes the
   watched record. From that frame on, the watched record's **other
   section**, the one of its two sections that is not current, is in use
   as well.
2. If a record is watched and its point is still in the window, nothing
   changes.
3. If a record is watched and its point is no longer in the window, the
   record stops being watched and its other section stops being in use.
   If the point is past the left or the bottom edge and the current
   section is the record's first section, the record's second section
   becomes current (`k` goes up by 1). If the point is past the right or
   the top edge and the current section is the record's second section,
   its first section becomes current (`k` goes down by 1). Otherwise the
   current section stays as it was.

So a record is watched from the frame its point comes into the window
until the frame it leaves. While it is watched, both its sections place
objects. The edge it leaves by decides which one stays: past the bottom
or left edge (the view has gone up or right past the point), the second
section; past the top or right edge, the first.

#### Reading a section

After that step, the sections in use are **read**, which is when their
objects appear: first the watched record's other section, if a record is
watched, then the current section. If any object was made while reading
the other section, the current section is not read on that frame, so
its objects appear one frame later at the earliest.

Each section in use has a **reading start**, an entry kept from one
frame to the next. When the area begins, the current section's reading
start is its first entry. When a record starts being watched, the
reading start of its other section is that section's first entry, and
when that section becomes current it keeps its reading start.

An entry is **left of the window** when `x` − `V` < −31. Before a
section is read, its reading start moves:

- If the reading start is left of the window, it moves on one entry at
  a time until it is on an entry that isn't, on the section's last
  entry, or on the entry of zeros that ends the list, whichever comes
  first.
- Otherwise it moves back one entry at a time for as long as the entry
  before it is not left of the window, but not past the section's first
  entry. If it stopped before reaching the section's first entry, and the
  entry before it (left of the window) is a group entry, it moves back
  onto that group entry.

Reading then takes the entries in list order from the reading start,
going on past the section's last entry if it gets there, until it stops:

- **Class 1, 2 or 15.** If the entry is not in the window (left or right
  of it), reading stops. Otherwise, if its object is not there, the
  object is made: always for class 1 and class 2, and for class 15 only
  if the entry is also in the vertical window. Reading goes on.
- **Class 5.** If the group's objects are there, reading goes on.
  Otherwise, if its first member is right of the window (`x` − `V` > 288)
  or its last member (the last in the members' list) is left of it,
  reading stops. Otherwise all the members' objects are made and reading
  goes on.
- **Class 9.** Reading goes on.
- **The entry of zeros** that ends the list: reading stops.

An entry's object is **there** from the frame it is made until it is
removed. Once it has been removed, the entry can make it again.

Some consequences:

- An entry that comes after one with a larger `x` is not reached while
  that entry is right of the window, even when it is in the window
  itself. In the underwater list that has one, the two appeared on the
  same frame.
- A group entry just left of the window, whose objects are gone and
  whose members are all left of the window, stops reading on the frames
  on which it is the reading start. With the reading start moving back
  onto it on one frame and on past it on the next, the entries after it
  can appear one frame later than they otherwise would.
- In the runs, no entry after a section's last entry was ever made.

#### Class 1 and class 2 objects in these lists

Class 1 and class 2 entries are read by the window alone, so their
objects are made even when the entry is far above or below the view.
What happened to them then, in the runs, depends on the object, and is
not covered as a rule:

- Every class 2 object made outside the vertical window was gone again
  by the end of the same frame (79 256 times), except once, when the
  player was level with the entry. Made inside the vertical window, they
  stayed (79 times). These objects are not drawn.
- The objects of one class 1 reference (one class 1 entry in each of two
  of the lists) did the same: 1 353 made and gone on the same frame, 17
  that stayed, all of those inside the vertical window.
- The object of one other class 1 entry, made outside the vertical
  window, was gone on the frame after it was made. It was then made
  again, so it was there on every second frame (3 731 appearances).

An object that is gone again on the frame it was made still counts as
made: while such an object is made from the other section on every
frame, the current section isn't read at all.

#### Example

A made-up list (in `../vectors/placements/`, see "Vectors") with three
sections: entries 1–6, 7–10 and 11–14, and two records. Record 0 joins
the first two sections at a point at level x 960, level y 724. Record 1
joins the last two at level x 60, level y 324. The view starts at
`V` = 0, `U` = 760 and moves right along the bottom, then up:

| Frame | `V` | `U` | What happens |
|---|---|---|---|
| 188 | 700 | 754 | Record 0's point comes into the window (`y` − `U` = −30). Entries 7–10 are in use as well as 1–6 |
| 228 | 700 | 674 | Entry 9 (other section, level y 644) appears. Entry 6 (current section, also level y 644) would too, but the current section isn't read |
| 229 | 700 | 672 | Entry 6 appears |
| 332 | 700 | 466 | The point is past the bottom edge (`y` − `U` = 258): entries 7–10 become the current section, and 1–6 are no longer in use |

#### Checks

- Every underwater area: 8 areas, two for each of the four lists,
  starting at different points. 3 runs each of up to 40 000 frames,
  with the player moved through the open water along random routes so
  that the view followed, and kept from being hurt. On some frames of one run per area the player
  was moved by up to the whole level in one frame. 780 444 frames in
  all, with 120 changes of the current section and 168 records starting
  to be watched. On every frame, the current section, the watched record
  and the reading start were as described here. The entries whose
  objects were made, and the ones still there at the end of the frame
  (as in "Class 1 and class 2 objects in these lists"), matched on every
  frame but one: the class 2 object that stayed once outside the
  vertical window. Removals were taken from the game. That is 5 751
  appearances, 1 824 of them from class 15 entries and 98 of groups. The
  current section wasn't read on 6 038 frames, on 5 526 of them only
  because of objects gone at once. On 2 of those frames an object of the
  current section was due, and it appeared one frame later.
- Starting the area: each of the 8 areas was entered from the map; `k`
  and the reading start on the first frame were as described. Every
  record's point was at least 256 px above or below the view's top row.
- Kinds: in four of the areas, the objects made from class 15 entries
  with a `bunch` or letter row were all of that kind (11 appearances).
- The crystal-cave rooms' list: three of its rooms, 66 294 frames, run
  the same way until the player left the room. No section changed. One
  class 2 object stayed once outside the vertical window, and nothing
  else differed.

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
sections, its section table, a view path, and on each frame the sections
in use, the reading start and the entries that appear: see its
`README.md`.

## Not covered

- Where the placement lists of levels other than the first are in the
  image.
- What a definition holds, and the kinds of the references without a
  kind fingerprint row. An object's settings (a walker's heading and
  patrol, whether it takes a first step on its first frame, a placed
  ball's speeds) also belong to the definition and are not covered.
- What class 2 changes compared with class 1: in the first level's runs,
  class 2 objects appeared and started like class 1 objects. In the
  lists with sections, every class 2 object was gone again at once
  outside the vertical window (see "Class 1 and class 2 objects in these
  lists"). Whether that comes from class 2 or from the kind of object,
  and what those objects do, is not covered.
- Which class 1 kinds are gone again outside the vertical window, as in
  "Class 1 and class 2 objects in these lists".
- What class 8 entries do other than the first level's (which starts
  the dimming), what a class 8 entry's `h` and reference mean, and
  classes other than 1, 2, 5, 8, 9 and 15.
- When objects other than walkers are removed, and whether they come
  back.
- Entries far above or below the view in lists without sections, and
  when the first level's entry that is out of order appears (it is past
  the furthest point the runs reached).
- Whether the reading rules of "In a list with sections" also hold for
  lists without sections, read as one section covering the whole list.
  They were only measured on lists with sections.
- In lists with sections: what happens when an object can't be made
  because too many objects are already there; a record's point exactly
  on the view's top row when the area begins, and a record's point in
  the window on the area's first frame (neither happened in the runs);
  and the crystal-cave rooms' list beyond the checks above (no section
  changed in its runs).
- Groups in other levels: their members can be of other classes, and the
  group entry's position isn't always its first member's.
- The characters' starting position, the level's left and right ends,
  the view's limits, the level's exit, and other areas entered from the
  level.
