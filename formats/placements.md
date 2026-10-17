# Placements

Titles: `title-1`

How a level says which objects it places where. A level's **placement
list** holds the objects that appear as the view comes near them:
walkers (`../behavior/walker.md`) and the other things standing or
hanging in the level. This file covers how the list is stored, what its
positions mean, and when the objects it places appear and are
removed. Vectors: `../vectors/placements/`.

The list doesn't hold the characters (where they start is in
`level-start.md`), the single collectibles (their own group list,
`collectibles.md`), or objects made during play (for
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
| `figure` | A golden figure of an animal, adding to the figure count | `figure.md` |
| `barrel` | An object that can be carried and holds nothing | `carrying.md`, `thrown-objects.md` |
| `holder` | An object that can be carried and holds the missing character | `carrying.md`, `holder-breaking.md` |
| `end-marker` | An end marker: the level's end, or the way out of the first level's room | `level-end.md` |
| `entrance` | The place the characters come out of when the level begins | `level-start.md` |
| `wall-return-entrance` | The place the lead comes out of when play comes back to the level from the area behind its first wall exit | `wall-exits.md` ("Coming back from the first wall exit's area") |
| `exit` | An exit that takes the lead out of the area | `exits.md` |
| `wall-exit` | An exit hidden in a wall, opened by a thrown object | `wall-exits.md` |
| `animal-crate` | A crate holding an animal | `animal-crate.md` |
| `restart-barrel` | The restart barrel | `restart-barrel.md` |
| `stinger` | A stinger (flying enemy) | `stinger.md` |
| `tyre` | A tyre that throws the player up | `tyre.md` |
| `stiff-tyre` | A tyre that throws the player up and never squashes | `tyre.md` ("The stiff tyre") |
| `left-launch-barrel` | A barrel that catches the player and shoots them to the left | `launch-barrel.md` |
| `down-launch-barrel` | A barrel that catches the player, shoots them straight down and breaks | `launch-barrel.md` ("The barrel that shoots down") |
| `sign` | A sign (scenery that nothing touches) | `signs.md` |
| `critter` | A small flying creature (scenery that nothing touches) | `critter.md` |
| `extra-life-1`, `extra-life-3` | An extra-life item that stays where it is placed, giving 1 or 3 lives | `extra-lives.md` |
| `rising-extra-life-1`, `rising-extra-life-2` | An extra-life item that rises once it appears, giving 1 or 2 lives | `extra-lives.md` |
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

36 of the first level's 37 references have a kind fingerprint row. They
are used by 66 of its entries (counting group members): 5 small
walkers (all three group members among them), 6 medium walkers, 3 large
walkers, the
thrower, a ball, 4 bunches, the four letters (one each), a figure, a barrel, 2
holders, the animal crate, the restart barrel, the end marker, the
entrance, the wall-return entrance, 2 exits, 2 wall exits (two references, one each),
15 hidden bunches, a hidden barrel, a hidden metal barrel, 2 signs
(two references, one sign each), 2 critters (one reference) and 5
extra-life items (two `rising-extra-life-1`, one `rising-extra-life-2`
and two `extra-life-3`), a stiff tyre and two launch barrels (one of
each kind). The
other references place things that have no behavior file yet (for
example other scenery), or that
were not seen.

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
was checked as in `../behavior/level-start.md` ("The entrance"), and the
two `exit` rows as in `../behavior/exits.md` ("Checks"). The `figure`
reference, given to a class 1 entry near the level's start, placed a
figure that was taken and counted as `../behavior/figure.md` says. Each of
the two `sign` references, given to a class 1 entry near the level's
start, placed a sign there that behaved as `../behavior/signs.md` says.
The `critter` reference, given to a class 1 entry near the level's
start, placed a critter there that appeared and flew as
`../behavior/critter.md` says. Each of the three extra-life references,
given to a class 1 entry near the level's start, placed an extra-life
item there that moved (or stayed), was taken and gave lives as
`../behavior/extra-lives.md` says for its kind. Each of the two `wall-exit`
references, given to a class 1 entry near the level's start, placed a
closed wall exit there that a lead walking through didn't open; in their
own places they were opened and taken over as
`../behavior/wall-exits.md` says. The `stiff-tyre` reference, given to a
class 1 entry near the level's start, placed a stiff tyre there that
caught and threw the player as `../behavior/tyre.md` ("The stiff tyre")
says. The `left-launch-barrel` reference, given to a class 1 entry near
the level's start, placed a launch barrel there that caught the player
and shot them to the left as `../behavior/launch-barrel.md` says. The
`wall-return-entrance` row was checked as in `../behavior/wall-exits.md`
("Coming back from the first wall exit's area"). The `down-launch-barrel`
reference, given to a class 1 entry near the level's start, placed a
launch barrel there that caught the player, shot them down and broke as
`../behavior/launch-barrel.md` says; its own entry was checked as in
`../behavior/wall-exits.md` ("Coming back from the second wall exit's
area").

### In the first level's room

The room the upper exit leads to (`../behavior/exits.md`) has a
placement list of its own (`locators.md`). It has no sections and no
groups: all its entries are of class 1 or class 2. Three of its
references have a kind row: `tyre`, `end-marker` for the room's way out
(`../behavior/level-end.md`, "In the first level's room"), and
`extra-life-1` (`../behavior/extra-lives.md`; given to a class 1 entry
near the first level's start, it placed an item that stayed where it was
placed and gave 1 life); the kinds of the others are not covered. In the
room the picture's left edge is always level x 0
(`../behavior/level-ends.md`, "In the first level's room"), so every
entry is in the window. An object for each of its entries was there
from the room's frame 1 on (frames numbered as in
`../behavior/exits.md`, "Walking in"), and none on frame 0, in each of 8
runs into the room (4 with two characters, with different input before
the upper exit).

### In the first level's scene

The scene the floor exit leads to (`../behavior/exits.md`, "The scene")
has a placement list of its own (`locators.md`). It has 3 entries, of
classes 1 and 2, and no sections or groups. One of its references has a
kind row: it is the room's `end-marker` reference. Nothing held has any
effect in the scene, and the lead walks in from the right and stops left
of that entry's position (`../behavior/exits.md`, "The scene"): in all
the scenes measured there, with random inputs, the marker was never
passed. The kinds of the other two references are not covered. In the scene the picture's
left edge is always the same (`../behavior/level-ends.md`, "In the first
level's scene"), and every entry is in the window. An object was at
each entry's position from the scene's frame 1 on, and none on frame 0
(frames numbered as in `../behavior/exits.md`, "Walking in"). None of
them moved or went away before the scene's last frame. This held in a
run with the lead alone and in one with two characters.

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
  which its entry is in the window, unless an entry stored before it
  holds it back ("Stored order" below). On the level's first frame, the
  objects of all entries already in the window are there.
- **Class 15.** The object appears on the first frame on which its
  entry is in the window and in the vertical window.
- **Class 9.** Nothing appears.
- **Class 5.** All members appear together, on the first frame on which
  the group entry is in the window (subject to "Stored order" below),
  even members whose own `x` is still outside it.
- **Class 8.** Nothing appears. For the first level's class 8 entry,
  the first frame on which it is in the window is the dimming's frame T
  (`../behavior/dimming.md`).

An object appears at its entry's position: level x `x` and level y
`Yb` − `h` (192 of the 230 appearances exactly). Some objects move on
the frame they appear, so on that frame they are already a little away
from it: up to 1 px in the other appearances (a walker's first step is
1/8 px), and in one more, not counted in the 230, 1.2 px to the left and
2.6 px higher.

The objects of one reference were the exception: critters, which fly
about and appear at the top of the picture rather than at their entry
(`../behavior/critter.md`, "Appearing").

One entry can make more than one object appear (seen with one reference:
two objects at the same position).

How a walker is removed, comes back, or stays away once defeated is in
`../behavior/walker-view.md`, how a critter is removed and comes
back in `../behavior/critter.md`, and how an extra-life item is removed
and stays away in `../behavior/extra-lives.md`. When the first level's
other objects are removed is in "When placed objects are removed" below.

### Stored order

In a list without sections, the order in which entries are stored
matters as it does in a list with sections. The rules of "When an entry
appears" (in "In a list with sections" below) hold, with the whole list
as one open section: an entry's object appears on the first frame on
which the entry comes up. That section has no far section, so the home
section never waits. Class 2 entries, like class 1 entries, need only
the window, not the vertical window: they appear however far above or
below the view they are (see "Far above or below the view" under "When
placed objects are removed", "Checks"). This differs from lists with
sections ("Class 1 and class 2 entries in these lists").

In a list stored in order of `x`, this gives the same frames as the
bullets above when each group's first member is at the group entry's
`x` and no member is left of it, as in the first level: the entries in
the window are next to each other in the list, and the lead is the
first of them. It differs when an entry is
stored after one with a larger `x`. Such an entry doesn't appear while
that entry is right of the window, even when it is in the window
itself. Both then appear on the same frame. The first level's list has
one such entry: its `end-marker` entry (class 2) is stored after a
class 1 entry with a larger `x`.

**Checks.** In the first level:

- The end marker, with the view moving right toward it: 20 runs
  (walking and running, random pauses). In every run the marker appeared
  on the first frame on which the class 1 entry stored before it was in
  the window, and not on the frames before, on which the marker's own
  entry was already in the window. In a copy of the image with the two
  entries stored the other way round (5 runs), the marker appeared on
  the first frame on which its own entry was in the window. In a copy
  with the class 1 entry moved 28 px further right but still stored
  first (5 runs), the marker appeared 28 px of view later than in the
  real level, again on the first frame on which that entry was in the
  window.
- The whole list: 56 runs from 7 points along the level, of up to 12 000
  frames each, with random walking, running, jumps and left legs, and
  the player kept from being hurt (knock-outs from pits restart the
  level and the run went on): 273 816 logic frames, 2 536 appearances.
  The rules above gave every frame's appearances except on 58 frames:
  8 frames on which the group appeared while its entry was left of the
  window (under "Not covered"), and 50 on which an entry appeared late
  while many objects were there (see "How many objects can be there"
  below). Taking each entry by its
  own `x` alone (the bullets above without "Stored order") was also
  wrong on 42 more frames: the frames on which the end marker's entry
  was in the window but held back, in the 6 runs that reached it.

### How many objects can be there

Two limits can keep an entry that comes up ("Stored order" above, "When
an entry appears" below) from appearing. They count what is there at
the **start** of the frame, not including the two characters:

- **Places.** There are 12 places. Each placed object takes one while it
  is there, whatever its class, and so do some objects that other
  objects make: the balls a `thrower` throws (`../behavior/thrower.md`)
  and the animal from an `animal-crate` (`../behavior/animal-crate.md`)
  were among them.
- **Pictures.** There are 11 pictures. A class 1 entry's object takes
  one from the frame it appears until the frame it is gone; class 2
  entries' objects take none. Short-lived objects that some events
  make take them too: the flash and each of the four pieces when the
  restart barrel bursts (`../behavior/restart-barrel.md`) took one each.

On each frame, the entries that come up are taken in stored order. Each
takes a free place, and a class 1 entry also a free picture. An entry
for which none is left doesn't appear on that frame, and the entries
stored after it still can. It comes up again on the following frames
while the rules above say so, and appears on the first of them that
starts with what it needs free. A place or picture given up on a frame
(the object removed, taken, broken or defeated) is free from the next
frame on, not on that frame.

In the first level's runs ("Checks" below), the pictures were all taken
on 406 of 140 594 frames, and an entry that came up waited twice: both
times the `large-walker` entry, coming in from the left just after the
restart barrel had burst near it, and both times it appeared on the
frame after the flash went.

**Example (synthetic).** Frame F starts with 11 places and 10 pictures
taken. Entries A (class 1), B (class 2) and C (class 1), stored in that
order, come up on F. A takes the last place and the last picture and
appears; B and C don't. Also on F, the player takes a `bunch`. Frame
F + 1 starts with one place and one picture free; B and C come up
again. B takes the place and appears; C waits again, as it needs a
place too.

**Checks.** In the first level:

- Copies of the image with 14 class 1 entries near the level's start
  (between the left-end exit and the first walkers) replaced by a dense
  row: `bunch` entries 6 px apart, by turns on the floor and high
  enough to need a jump; the same with the last two entries class 2;
  the same 20 px apart; and `sign` entries 6 px apart on the floor. Six
  runs each from the level's start with the random inputs of the
  stored-order runs above: 24 runs, 57 317 frames, 1 086 appearances.
  Entries that came up and didn't appear did so on 5 489 frames, and 58
  appearances came late; the rules above gave every frame's
  appearances. In the copy with two class 2 entries, a class 2 entry
  appeared while a class 1 entry stored before it waited for a picture,
  and the other waited for a place. Controls: with 10 pictures, 90
  frames were wrong; with 12, 4 411; with a picture free on the frame it
  is given up, 46; with a waiting entry holding back the entries stored
  after it, 7.
- The whole list: 28 random runs as in "Stored order" (140 594 frames,
  1 402 appearances), with the number of pictures the short-lived
  objects took read from the measurements. The rules above gave every
  frame's appearances except 5 frames on which the group appeared while
  its entry was left of the window ("Not covered"). Stored order alone
  was wrong on 8 more: the late appearances.

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
- An entry is **clear** when it is a class 9 entry, a class 1, 2, 8 or 15
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

## When placed objects are removed

This section is about the objects placed by the first level's list (a
list without sections) while they are still as they appeared: not
taken, picked up, thrown, broken, opened or set off. What happens to an
object after that is in its kind's behavior file, except for a `barrel`
or `holder` that was picked up and put down again: see "Objects put down
elsewhere" below. Walkers, stingers,
critters, throwers, placed balls and extra-life items have their own
rules (`../behavior/walker-view.md`, `../behavior/stinger.md`,
`../behavior/critter.md`, `../behavior/thrower.md`,
`../behavior/extra-lives.md`).

`V` and `U` are the view's horizontal and vertical positions as in "When
placed objects appear", rounded down to whole pixels, as they were at
the start of the frame. Here `V` is **not** counted as 32 when it is less
than 32. Near the level's left end it can be less than 0.

- An entry is **in the window** when −31 ≤ `x` − `V` ≤ 288, as above.
- An object is **in the vertical window** when −31 ≤ `y` − `U` ≤ 256,
  with `y` the object's own level y. Most of these objects don't move,
  so `y` is their entry's level y. A `barrel` or `holder` whose entry is
  above the floor falls onto it, and its `y` is where it is at the start
  of the frame.

An object that is there is removed on the first frame on which its
entry isn't in the window and the condition for its kind holds:

| Kinds | Removed when the entry isn't in the window and |
|---|---|
| `bunch`, `barrel`, `holder`, `restart-barrel`, `sign`, `hidden-bunch`, `hidden-barrel`, `hidden-metal-barrel` | `x` − `V` ≤ −128, or `x` − `V` ≥ 385, or the object isn't in the vertical window |
| `letter-1` to `letter-4`, `figure`, `animal-crate`, `exit` | always: the object is removed on the first frame on which its entry isn't in the window |

So an object in the first row that is in the vertical window stays, if
it was there, while `x` − `V` is from −127 to −32 or from 289 to 384.
An object above or below the vertical window gets no such margin. This
can also remove an object on a frame on which only `U` changes. If it is
in the margin, it is removed on the first frame on which `y` − `U` is
less than −31 or more than 256.

Except for exits (below), the vertical window plays no part in
appearing. Class 1 and class 2 entries appear by `x` alone ("When placed
objects appear"), even when their objects are far above or below the
picture.

**Exits.** An `exit` is there only while it is in the vertical window
as well. It appears on the first frame on which its entry is in the
window and it is in the vertical window, and it is removed on the first
frame on which either no longer holds, also on a frame on which only
`U` changes. Exits don't move, so `y` is their entry's level y. In the
runs, an exit with `y` − `U` of −32 or less, or of 257 or more, was
never there, and one with `y` − `U` from −27 to 256 was always there
while its entry was in the window. From −31 to −28 it was there in some
runs and not in others (see "Not covered"). An exit is not drawn and
only takes over a player close to it (`../behavior/exits.md`).

The rule doesn't hold before the characters come out of the entrance
(`../behavior/level-start.md`), when nothing can reach an exit: the
first level's exit at the left end, at level y 337, was there from
frame 0 with `U` = 0 (`y` − `U` = 337) and was removed on frame 41, the
frame the entrance bursts. It appeared again on frame 98, the first
frame on which `y` − `U` was 256 as the view came down.

**The entrance.** The `entrance` is removed on frame T, the frame on
which it throws the characters out (`../behavior/level-start.md`). It
doesn't appear again while the level goes on, however far the view goes
from its entry and comes back. It is there again only when the level
begins again.

**The end marker.** An `end-marker` is not drawn and only acts on a lead
at its own position (`../behavior/level-end.md`), which is always in the
window. So whether it is there while its entry is outside the window
makes no difference to play. In the runs it sometimes stayed there with
its entry more than 1 000 px right of `V`, and was sometimes removed as
soon as its entry was outside the window. Either way, when the view
came back it appeared again at the same view position as the first time,
before the lead could reach it.

**Coming back.** An object removed this way appears again, as in "When
placed objects appear", the next time its entry is in the window. This
can happen any number of times.

### Example (synthetic)

Three entries with `x` = 1000: a `bunch` at level y 300 (call it A), a
`bunch` at level y 150 (B) and a `letter-1` at level y 300. All three
objects are there. `U` stays 200, so A and the letter have `y` − `U` =
100 (in the vertical window) and B has −50 (above it). The view moves
left, then back to the right:

| `V` at the start of the frame | `x` − `V` | A | B | `letter-1` |
|---|---|---|---|---|
| 712 | 288 | there | there | there |
| 711 | 289 | there | removed | removed |
| 616 | 384 | there | | |
| 615 | 385 | removed | | |
| 712 (moving right again) | 288 | appears | appears | appears |

With A there and `V` = 650 (`x` − `V` = 350), A stays while `U` goes
down to 44 (`y` − `U` = 256) and is removed on the first frame with `U`
= 43 (257).

### Checks

Every kind in the table other than `exit` was put, by its reference,
into one entry left of the characters' start and one right of it, with
the entry's level y changed to put the object on the floor, at the
vertical window's edges (`y` − `U` of −32, −31, 256 and 257) and far
above and below the picture. A controller walked, ran and jumped the
lead away from the object until it was removed and back until it
appeared, four times per run with three random seeds, never touching
it. The first level's own
entries of most kinds, and its exit at the left end at its own height,
were run in the same way. 330 runs, 271 516 frames: on every frame each
object was there exactly when the rules above say.

- First row: 917 appearances and 702 removals. Removals by edge: at
  `x` − `V` = −128 to −131 and 385 to 388 in the vertical window (never
  at −127 or 384), and 340 inside the margin, all with the object
  outside the vertical window (one of them on a frame on which only `U`
  changed).
- Second row: 393 appearances and 304 removals, all on the first frame
  with the entry outside the window (`x` − `V` from −32 to −35 and 289 to
  292, with the view moving up to 4 px a frame), whether or not the
  object was in the vertical window.
- Removals with `V` less than 32: at `x` − `V` = 385 with `V` = 14 and 15.
  Counting `V` as 32 would have kept the object.
- A `barrel` and a `holder` placed above the floor fell onto it. Testing
  their entry's level y instead of their own would have been wrong in
  7 of their 21 runs each, on 25 to 110 frames per run.

**Far above or below the view.** A `bunch`, a `sign` and a `letter-1`
were each put, by their reference, into the first level's class 2 entry
at its left end and into a class 1 entry right of the characters'
start. The entry's level y was changed to put the object 100, 600 and
2 100 px above the picture's top row and 400, 824 and 1 824 px below
it (`y` − `U` of about −100, −600, −2 100, 400, 824 and 1 824). The
controller above ran three seeds for each entry, kind and height: 108
runs, 32 220 frames. On every frame each object was there exactly when
"When placed objects appear" and the table above say:

- 324 appearances, all on the first frame with the entry in the window
  (`x` − `V` from −31 to −28, with the view moving left), in either
  class and at every height. Each object was at its entry's level y.
- 216 removals, all on the first frame with the entry outside the window
  (`x` − `V` from −32 to −35). The `bunch` and `sign` got no margin,
  being outside the vertical window, and went on the same frames as the
  `letter-1`.
- With the same seed, the frames were the same at all six heights.

**Exits, the entrance and the end marker.**

- Exits: the exit reference was given to the first level's class 2
  entry at its left end and to a class 1 entry right of the characters'
  start, with the entry's level y set to put the exit at the vertical
  window's edges (`y` − `U` of −32, −31, 256 and 257, with `U` as it was
  where the lead walked) and on the floor, and run with the controller
  above, three seeds each: 30 runs. In 27 of them (226 723 frames) each
  exit was there on exactly the frames "Exits" gives. In the other 3,
  in the class 1 entry with `y` − `U` = −31, it never appeared (see "Not
  covered"). With the lead standing still and the entry in the window,
  the exit in the class 2 entry was there down to `y` − `U` = −31 and
  not at −32; in the class 1 entry it was there down to −27 and not at
  −28. Put in the class 1 entry with the entry's class changed to 2, the
  exit was there at −31; put in the class 2 entry with its class changed
  to 1, it was there at −31 too.
- Exits as the view comes down at the start of the level: the first
  level's exit at the left end with its level y changed to 100, 132 and
  168. The exits at 100 and 132 were removed on the first frame with
  `y` − `U` less than −31 (on the frame with −32 for the one at 132),
  with the entry in the window throughout. The one at 168 stayed.
- The entrance: in copies of the image in which the other entry at the
  start point was given a `sign`'s reference, the lead walked until
  the view was 500 and 1 000 px right of the start and came back. The
  entrance didn't appear again; the sign appeared each time the view
  came back over its entry. With a `bunch`'s reference given to the
  entrance's entry after the level had begun, that entry placed nothing
  again either, while the same reference given to the other entry at
  the start point placed a bunch each time. (Earlier runs in which an
  object given the entrance's entry never appeared again came from
  this.)
- The end marker: from near the level's end, the lead walked, ran and
  jumped away from it with the end marker there, until its entry was
  up to 1 273 px right of `V`: 31 runs. In 2 of them the lead came back
  after the end marker had gone, and in both it appeared again at the
  same view position as the first time.

### Objects put down elsewhere

A `barrel` or `holder` can be picked up, carried and put down
(`../behavior/carrying.md`, "Putting down"), so it can be some way from
its entry. Such an object is removed by the first row of the table,
with the object's **own** position in the margin and the **entry's**
in the window: it is removed on the first frame on which

- its entry isn't in the window (−31 ≤ entry's `x` − `V` ≤ 288 doesn't
  hold), and
- its own level x, rounded down to a whole pixel, minus `V` is −128 or
  less or 385 or more, or it isn't in the vertical window (its own `y`,
  as above).

An object that hasn't been moved is at its entry's `x`, so this is the
same rule as above. For one that has been moved:

- If its entry leaves the window first, it stays until its own `x` −
  `V` reaches −128 or 385. It can stay long after an unmoved object at
  its entry would have gone, with the entry far outside the window.
- If its own position reaches the margin first, it stays until its
  entry leaves the window, and is removed on that frame.
- While it is there, its entry places nothing else, even with the entry
  in the window.
- Once it has been removed, it **comes back at its entry**, not where it
  was put down: it appears as in "When placed objects appear", on the
  first frame on which its entry is in the window, at the entry's
  position, as an object that has not been moved. From then on it is
  removed and comes back as above.

**Example (synthetic).** A `barrel` entry at `x` = 1000, with the barrel
put down on the floor at `x` = 800 and `U` such that it stays in the
vertical window:

| The view | `V` at the start of the frame | entry's `x` − `V` | own `x` − `V` | The barrel |
|---|---|---|---|---|
| moving right | 928 | 72 | −128 | there (the entry is in the window) |
| moving right | 1031 | −31 | −231 | there |
| moving right | 1032 | −32 | −232 | removed |
| moving left again | 712 | 288 | | appears at `x` = 1000 |

Had the view moved left instead after the put-down, the barrel would
have stayed while `V` went down past 711 (entry's `x` − `V` = 289) and
been removed on the first frame with `V` = 415 or less (own `x` − `V` ≥
385). Put down at `x` = 1150 instead, with the view moving left, it
would stay while its own `x` − `V` went past 385 (`V` ≤ 765), and be
removed on the first frame with `V` = 711 or less, when its entry
leaves the window.

**Checks.** In the first level, with a `holder` and a `barrel` (the
first level's own entries), and walkers near them given a `sign`'s
reference in copies of the image so that nothing hit the lead: the lead
started carrying the object near its entry, carried it left or right
for 15 to 95 frames, put it down, and then walked left and right (6 legs
of 80 to 600 frames each, random jumps, never Action) with random
seeds: 200 runs with each object (the barrel's runs with the
thrower near it removed by writing to memory, a research-only change).
The objects were put down from 304 px left to 127 px right of their
entry, on flat floor and on slopes. 813 341 frames: on every frame each
object was there exactly when the rules above say, and every
appearance after a removal was at the entry's position.

- 394 of the 1 545 removals were of objects that had been moved. 226
  of them came on the first frame on which the entry left the window
  (entry's `x` − `V` −32 to −34, or 289 or 290), with the own position
  already beyond the margin; 161 on the first frame on which the own
  position reached the margin (own `x` − `V` −128 or −129, or 385 to
  387), with the entry already outside the window; and 7 on a frame on
  which both happened.
- Testing the entry's `x` in the margin instead (the rule for unmoved
  objects) gave a different answer on 51 150 frames, in all 400 runs.
- None of these removals came from the vertical window.
- In a separate run with the holder put down 127 px right of its entry,
  the view went right until the entry's `x` − `V` was −216, with the
  holder still there, and came back: no second holder appeared at the
  entry while the first one was there.

## Example

A list with four entries (the trailing entry of zeros not shown), with
`Yb` = 512:

| `c` | `x` | `h` | `r` | Places |
|---|---|---|---|---|
| 1 | 400 | 175 | `A` | an object of definition `A` at level x 400, level y 337 |
| 5 | 900 | 160 | `G` | the members listed at `G` + 8, all appearing when 900 enters the window |
| 1 | 960 | 200 | `B` | an object of definition `B` at level x 960, level y 312 |
| 2 | 930 | 63 | `C` | an object of definition `C` at level x 930, level y 449 |

With the view moving right and still at `V` = 111 at the start of a
frame, the first entry is outside the window (400 − 111 = 289). It
appears on the first frame that starts with `V` ≥ 112. The group
appears on the first frame that starts with `V` ≥ 612. The fourth entry
is in the window from `V` = 642 on, but the third entry, stored before
it, is right of the window until `V` = 672 (960 − 672 = 288). Both
appear on the first frame that starts with `V` ≥ 672.

## Vectors

`../vectors/placements/` holds a made-up bank fragment, the entries a
reader gets from it and the frames on which their objects appear with
the view moving right (including an entry stored after one with a
larger `x`), and a made-up image with kind fingerprint rows and
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
- What class 2 changes compared with class 1. In the first level's runs,
  class 2 objects appeared and started like class 1 objects, and moving
  a reference from an entry of one class to one of the other changed
  nothing measured: a `bunch` in the first level's class 2 entry at the
  left end, at the vertical window's edges and on the floor (15 runs,
  6 921 frames), appeared and was removed on exactly the frames the
  first row of "When placed objects are removed" gives, and an `exit`
  followed "Exits" in either class. In the lists with sections, class 2
  entries didn't appear outside the vertical window (see "Class 1 and
  class 2 entries in these lists"). Whether that comes from class 2 or
  from the kind of object, and what those objects do, is not covered.
- Exits with `y` − `U` from −31 to −28: there in some runs and not in
  others. An exit given a class 1 entry right of the characters' start
  was never there at −31 or −30, nor at −29 or −28 with the lead
  standing still; with the lead walking, at −29 it was there in one of
  two runs. In the first level's class 2 entry at the left end, and in
  either entry with its class changed, it was there at −31.
- Which class 1 kinds don't appear, or are gone again, outside the
  vertical window, as in "Class 1 and class 2 entries in these lists".
- What class 8 entries do other than the first level's (which starts
  the dimming), what a class 8 entry's `h` and reference mean, and
  classes other than 1, 2, 5, 8, 9 and 15.
- When objects other than walkers are removed in lists with sections.
- What a removed object that had been moved does, other than thrown
  objects (`../behavior/thrown-objects.md`) and a `barrel` or `holder`
  put down elsewhere ("Objects put down elsewhere"): for example an
  object carried while its entry and the object are far apart, and
  objects put down in lists with sections.
- How many objects can be there ("How many objects can be there"):
  which short-lived objects take pictures, and for how long (in the
  first level's runs some kept theirs for a while after they were gone,
  and a short-lived object that came after such a one sometimes held
  two), and which objects made by other objects take places,
  beyond those named there, and how many places a group entry takes
  besides its members' (in the runs it sometimes took one more, not
  always while its members were there). What happens when a group's
  members, or an object that another object makes, find no place or
  picture free. The limits were measured only in the first level's
  list, not in lists with sections.
- In lists with sections: whether entries stored after an open
  section's last entry can appear with it (in the runs, none did); a
  record's point exactly
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
  group's objects appeared where the rules give nothing. In the first
  level's list (one section, "Stored order") the same was seen: its
  group appeared on 8 frames, in 6 runs, while its entry was left of
  the window and its last member wasn't.
- Groups in other levels: their members can be of other classes, and the
  group entry's position isn't always its first member's.
- The level's exit at its end, other than the end marker
  (`../behavior/level-end.md`), and the other areas entered from the
  level, other than the first level's room and scene: which area an
  `exit` leads to (`../behavior/exits.md`, "Where the first level's exits
  lead" and "Not covered"). Where the characters start is in `level-start.md`, and
  the view's limits are in `view-limits.md`, not in the placement list.
