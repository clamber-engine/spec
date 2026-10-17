# Level start

Titles: `title-1`

Where the characters are when a level begins, and where play resumes at
a level's restart point (`../behavior/restart-barrel.md`). How the
characters then enter the level is in `../behavior/level-start.md`.
Vectors: `../vectors/level-start/`.

## Start records

A **start record** gives one point of a level. It is 4 bytes: two 16-bit
words, each stored low byte first.

| Word | Name | Meaning |
|---|---|---|
| 0 | `x` | Level x |
| 1 | `h` | Height above the level's bottom edge `Yb` |

The point is at level x `x` and level y `Yb` − `h`, with positions as in
`placements.md` ("Positions"): whole px, level y growing downward. On
the first level `Yb` is 512.

## The first level

`locators.md` gives two records for the first level:

- **The start record.** When the first level begins (entered from the
  level map with no restart point reached), both characters that are
  there (`../behavior/following.md`, "When a level begins again") are at
  the record's point on the level's first frame.
- **The restart record.** When play resumes at the first level's restart
  point, the record's point is the restart point (`xr`, `yr`) of
  `../behavior/restart-barrel.md`.

`locators.md` also gives two records for coming back to the first level
from the areas its exits lead to (`../behavior/exits.md`, "Where the
first level's exits lead"). Both points are in the first level, with
`Yb` = 512:

- **The scene-return record.** After the scene that the floor exit leads
  to, the characters walk into the first level from the left, starting
  from this record's point as in `../behavior/exits.md` ("Walking in").
- **The room-return record.** After the room that the upper exit leads
  to, the first level begins as from the level map
  (`../behavior/level-start.md`), with this record's point in place of
  the start record's. In the supported image it holds the same point as
  the start record, but it is a record of its own: in a copy of the
  image with only the room-return record moved (to `x` 64, `h` 330),
  coming back from the room began at the moved point, and entering the
  level from the map still began at the start record's point.

`locators.md` also gives the **wall-return record**, for coming back to
the first level from the area behind its first wall exit
(`../behavior/wall-exits.md`, "Coming back from the first wall exit's
area"). Its point is in the first level, with `Yb` = 512: the lead is
there from the first frame back in the level until it is thrown out of
the level's `wall-return-entrance`. The record, not that entry, decides
where the lead is: in a copy of the image with the record's `x` raised
by 40 and its `h` lowered by 30, coming back began at the moved point and
the lead was thrown from there, with the entry where it was.

`locators.md` also gives the **second wall-return record**, for coming
back to the first level from the area behind its second wall exit
(`../behavior/wall-exits.md`, "Coming back from the second wall exit's
area"). Its point is in the first level, with `Yb` = 512: the lead is
there on the first frame back in the level, and then falls into the
level's `down-launch-barrel`. In the supported image the point is that
entry's position. The record, not that entry, decides where the lead
starts: in copies of the image with only the record moved (its `x`
raised by 20 or 40, or its `h` lowered by 30), coming back began at the
moved point.

`locators.md` also gives the **room's start record**, for the room the
upper exit leads to. The room is an area of its own, with `Yb` = 512 as
in the first level. When the room begins, the characters walk in from
this record's point as in `../behavior/exits.md` ("Walking in"). In a
copy of the image with this record moved, the walk-in into the room
moved with it (`../behavior/exits.md`, "Checks").

`locators.md` also gives the **scene's start record**, for the scene the
floor exit leads to. The scene is an area of its own, with `Yb` = 512
as in the first level. When the scene begins, the lead walks in from
this record's point as in `../behavior/exits.md` ("The scene"). In a
copy of the image with the record's `x` lowered by 40, the lead was 40
px further left on every frame of the scene, and at the same height.

In the first level, the start record's point is also the position of
the level's `entrance` entry (`placements.md`, "Kinds"), and the
restart record's point is the position of its `restart-barrel` entry.
The records, not those entries, decide where the characters are:

- In a copy of the image with the start record's `x` raised by 40 and
  its `h` lowered by 30, both characters began at the new point and
  were thrown from there (`../behavior/level-start.md`), with the
  entrance entry where it was.
- In a copy of the image in which another entry near the level's start
  was given the restart barrel's reference, touching that barrel and
  then being knocked out resumed play at the restart record's point,
  not at the moved barrel (`../behavior/restart-barrel.md`, "The
  restart point").

## Checks

- Entering the first level from the level map: 27 runs, 21 with one
  character and 6 with two, with and without input. 5 of them were in
  copies of the image with the start point's entries changed
  (`../behavior/level-start.md`), and one with the start record moved as
  above. On the level's first frame each character was exactly at the
  start record's point, with no fraction of a px, facing right.
- Resuming at the restart point: the runs of
  `../behavior/restart-barrel.md` ("Resuming"). On the first frame of
  each resume the player was exactly at the restart record's point.

## Vectors

`../vectors/level-start/` holds a made-up bank fragment with start
records at given addresses and the points a reader gets from them.

## Not covered

- Where the start and restart records of levels other than the first
  are, and whether a level has a restart point at all, from the level's
  data.
- Whether a level can make the characters face left on its first frame
  (they faced right in every run).
- Other areas entered from a level other than the first level's room
  and scene (other levels' areas), and where their own start records
  are. Where play is when coming back to levels other than the first.
