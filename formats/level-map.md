# Level map

Titles: `title-1`

Where the stops and paths of the level map are and which direction
takes which path. The terms (stop, point, path, leg, walk, open, map
position `x` and `h`) are those of `../behavior/level-map.md`, which says
how the lead moves along a path. Vectors: `../vectors/level-map/`.

Which paths are open (the player's progress) is not covered here.

## Point ids

Every point of the map, stop or corner, has an **id** from 1 to 255.
The id 0 means "no point".

- Ids 112 to 143 are **corner ids**. A corner is a point a path passes
  through without stopping. The lead never stands at a corner.
- Every other id is a **stop id**.

Not every id is used.

## The point tables

Where the point tables start in the image is in `locators.md` ("Level
map: point tables"). They are seven tables of 256 bytes each, one after
the other, each indexed by the id (byte `i` of a table belongs to id
`i`):

| Table | Byte for id `i` |
|---|---|
| 1 | page of id `i`'s position |
| 2 | `x` of id `i`'s position on its page (0 to 255) |
| 3 | `h` of id `i`'s position |
| 4 | the stop that Right leads to from stop `i` |
| 5 | the stop that Left leads to from stop `i` |
| 6 | the stop that Up leads to from stop `i` |
| 7 | the stop that Down leads to from stop `i` |

### Positions

The **position** of id `i` is the map position, in whole pixels,

    x = 256 × page + (x on its page)
    h = (table 3's byte)

A path's points are the positions of its ids (see "Routes"). A walk's
legs end exactly at them (`../behavior/level-map.md`, "Walking a path").

A stop's **appearance point** is 8 px to the right of its position, at
the same `h`: (`x` + 8, `h`). When the map appears, the lead stands at
its stop's appearance point with both fractions 0
(`../behavior/level-map.md`, "When the map appears"). Walks into a stop
end at its position, not at its appearance point.

### Neighbours

Tables 4 to 7 give each stop's **neighbour** for each direction: the
stop at the other end of the path that direction takes. 0 means the
direction takes no path from that stop. Two directions can have the
same neighbour; they then take the same path. The bytes for corner ids
are 0 in all four tables.

A direction starts a walk only if its neighbour is not 0 and the path to
it is open. Which neighbour a held or remembered direction chooses is
in `../behavior/level-map.md`, "The direction".

## Corner records

Where the corner records start in the image is in `locators.md`
("Level map: corner records"). They are `K` records of 4 bytes each,
one after the other. Record `k` (from 0) belongs to the corner id
112 + `k`. In `title-1`, `K` = 4 (corner ids 112 to 115).

A record is two (destination, next) pairs:

| Byte | Meaning |
|---|---|
| 0 | destination `A` (a stop id) |
| 1 | next point toward `A` (an id) |
| 2 | destination `B` (a stop id) |
| 3 | next point toward `B` (an id) |

A record says: on a walk to `A`, the point after this corner is byte 1;
on a walk to `B`, it is byte 3. The next point is another corner or the
destination itself.

## Routes

A walk from stop `S` to its neighbour `D` follows a **route**: a list of
ids whose positions are the walk's points, in order. Build it like this:

1. **First point.** Look for a corner whose record has `S` as one of its
   destinations with next point `S` itself, and also has `D` as one of
   its destinations. If there is one, the first point is that corner.
   Otherwise the first point is `D`.
2. **Following points.** While the last point is a corner, the next
   point is that corner's next point toward `D` (byte 1 if `D` is its
   destination `A`, byte 3 if `D` is its destination `B`).
3. The route ends with `D`.

So a corner whose record names `S` → `S` sits next to `S`. A walk from
`S` passes through it only if the record also names the walk's
destination. A walk from `S` to any other neighbour goes straight to
that neighbour, in a single leg.

In `title-1` at most one corner fits in step 1 for any pair of a stop
and its neighbour, every route ends at `D`, and none visits a point
twice. An importer may report an image for which that isn't so.

### Example

Made up. Corner 112's record is (20, 20, 30, 113) and corner 113's is
(30, 30, 20, 112). Stop 20's Up neighbour is 30, and stop 30's Left and
Down neighbours are both 20.

- From 20, Up: corner 112 names 20 → 20 and has destination 30, so the
  route is 112, then 112's next toward 30, which is 113, then 113's next
  toward 30, which is 30. Three legs.
- From 30, Left or Down: corner 113 names 30 → 30 and has 20, so the
  route is 113, 112, 20.
- If stop 30 also had a Right neighbour 40, which no record names, the
  route from 30 with Right would be just 40.

## Measurements

- The routes, positions and neighbours were checked on every pair of a
  stop and a non-zero neighbour in the image: 66 stops with neighbours,
  128 walks (one per stop and direction), 138 legs, 7 of the walks
  through corners. Each walk was started from the stop's position with
  the path opened, and the lead's leg ends were compared with the
  route's positions: 0 mismatches.
- The same rules give the points of the 19 walks recorded for
  `../behavior/level-map.md`, which were walked from real progress
  without opening paths: 0 mismatches.
- Appearance points: measured at three stops (the map at the start, and
  after leaving two levels and one place that isn't a level): `x` 8 px
  to the right of the stop's position, same `h`, fractions 0.

## Not covered

- Which paths are open at the start and which ones each finish opens
  (progress), and where progress is kept.
- Which stops are levels and which level each one starts; what the
  other stops (places that aren't levels) do.
- The map's pictures, and the stop names shown on it.
- The map with a second figure walking with the lead (all measurements
  have the lead alone on the map).
- The figures' animation and drawing.
- Other titles.
