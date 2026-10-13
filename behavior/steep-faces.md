# Steep faces and walls

Titles: `title-1`, `title-2`

What happens when a grounded player meets ground that rises or falls too
steeply to walk on: being stopped by it, not being able to start walking
toward it from a standstill, being carried down a steep face, and leaving
the ground on one. Frames are logic frames at 60 Hz, numbered as in a
trace. `x`, `y` and `vx` are as in a trace: right and down are positive,
speeds in px per frame. Speeds are whole multiples of 1/256 px per frame
and are written both ways, as in `ground-speed.md`. "Left", "Right" and
"Action" are the neutral inputs `L`, `R`, `A` of the scenario format.

Measured with the lead character, walking, running, standing and
coasting with nothing held, on four steep faces (going down faces that
fall to the right and to the left, and against faces that rise to the
right and to the left) and against one vertical step up, going left.
Jumps, rolls and landings on or against these surfaces are not covered
(see the end).

## Columns, faces and steps

The floor has one height for each 1-px-wide column of `x`. "The
player's column" is the column containing `x` (its whole-pixel part).
"Higher" means higher on screen (smaller `y`).

- A **steep face** is a run of adjacent columns where each column's
  floor is exactly **4 px** higher than the one before it. A **face
  column** is a column whose floor differs by exactly 4 px from a
  neighbouring column's floor.
- A face **rises** in the direction its floor goes up and **falls**
  (is downhill) in the other.
- At its ends a face joins the neighbouring floor by a smaller step: its
  lowest column was 1 px above the floor next to it on both faces
  measured from below, and its highest column 3 px below the floor next
  to it on both faces measured from above. Those neighbouring columns
  are not face columns.
- A **vertical step** is two adjacent columns whose floors differ by
  **64 px**. This is the only step height of this kind that was measured.

The rules below were measured near faces and the vertical step. On flat
floor and on slopes of 1 px per 4 columns they never take effect: there
are no face columns or steps, and the column 5 ahead (below) is never
more than 2 px higher.

## Stopped by a rise

On a grounded frame (walking, running or standing; not a roll), after
the speed for the frame is worked out (`ground-speed.md`, or the face
rule below), the player is **blocked** if the move would take `x` into a
different column that is either:

- a face column of a face that rises in the direction of the move, or
- beyond a vertical step up in the direction of the move.

A blocked frame:

- `x` is set to the left edge of the column the player was in before the
  frame: the fraction of a pixel is dropped and `x` is a whole number.
  The player moves back slightly if moving right and forward slightly if
  moving left.
- `vx` becomes **0**.
- The player stays grounded on the floor they were on.

Even a face column only 1 px above the player's floor blocks: the floor
height of the new column is not what counts, only that it belongs to a
rising face. Columns in front of a face are walked over normally right up
to its first column.

Measured at walking and running speed, and at the low speeds of starting
from rest, into faces rising to the right and to the left and into the
vertical step going left; the speed and the fraction of `x` before the
frame made no difference.

## Can't start toward a rise

On a grounded frame that starts with `vx` exactly **0**, with Left or
Right held (with or without Action held, but not on a frame on which an
Action press starts a roll, `roll-input.md`):

1. Take the column **5 columns ahead** of the player's column in the held
   direction.
2. If that column's floor is **9 px or more** higher than the floor of
   the player's column, the player doesn't start moving: `vx` stays 0
   and `x` doesn't change.
3. If it is 5 px higher or less (or lower), the player starts moving as
   usual (`ground-speed.md`). Differences of 6 to 8 px weren't measured.

This holds on every frame it applies, however long the direction is held
and however often it is released and pressed again. It is why a player
who has been blocked stays where they are while still holding toward the
rise: after the blocked frame `vx` is 0 and the column 5 ahead is inside
the face or past the step. Holding the other direction starts moving away
as usual.

The same rule decides whether a player standing a few pixels in front of
a rise can walk up to it at all. A player who does start moving is only
stopped by the blocking rule above, so they can end up closer to the rise
than they could have started from.

Measured standing at every 1/16 px across the boundary in front of a
face rising to the right, a face rising to the left and a vertical step
(rising to the left), and at the positions where players came to rest
by themselves.

## Speed on a face

A frame **starts on a face** when the player is grounded and their
column before the frame is a face column. On such a frame the target
speed of `ground-speed.md` is replaced by:

| Held (Action not held) | Target, face falling to the right | Target, face falling to the left |
|---|---|---|
| Nothing | +1.5 (+384/256) | −1.5 (−384/256) |
| The uphill direction | +1.5 (+384/256) | −1.5 (−384/256) |
| The downhill direction | +3.5 (+896/256) | −3.5 (−896/256) |

That is 1.5 px per frame downhill, plus the normal walking target when
the downhill direction is held. A held direction that points uphill is
ignored. Then the speed rule of `ground-speed.md` applies unchanged
(gap ÷ 8 rounded toward zero, or the target itself when that is 0) and
`x` changes by the new `vx`.

- The frame on which the player moves **onto** a face from other floor
  doesn't start on a face, so it uses the normal target. The face target
  applies from the next frame.
- The face target also applies on the frame on which the player leaves
  the ground from a face (frame L below), because that frame started on a
  face.
- A player can't stand still on a face: with nothing held, the speed
  moves toward 1.5 px per frame downhill.
- Facing didn't change on any measured frame that started and ended on
  face columns, including up to 10 frames in a row with the uphill
  direction held. Whether a longer hold turns the player on a face isn't
  covered.

## Following a face down, or leaving it

On every grounded frame, after the horizontal move (and if the player
wasn't blocked), compare the floor of the new column with the floor of
the column before the frame:

- **8 px lower or less**: the player stays grounded and `y` becomes the
  new column's floor height. `ground` stays 1 and, as on any grounded
  frame, `vy` in a trace is 0. A new column 1 px higher (as at the end
  of a face, or on a gentle slope) is followed the same way; higher
  floor ahead is either blocked (above) or not covered.
- **11 px lower or more**, or **no floor** in the new column: the player
  leaves the ground. This frame is the leave frame **L** of
  `walk-off.md`: `y` moves down by exactly 3.4375 px whatever the floor
  below, `vy` ends at 0.00390625, and the fall continues as
  `walk-off.md` describes, with horizontal speed by `air-control.md`
  from L + 1 on and the landing rule of `landing.md`.

Drops of 9 and 10 px can't happen on a face whose columns are 4 px
apart and weren't measured. On such a face the rule means: crossing at
most **2 columns** in a frame keeps the player on the face; crossing 3 or
more makes them leave it. At the speeds above that depends on the speed
and on the fraction of `x`, so a player walking onto a face usually
follows it for a few frames and then leaves it.

## Leaving a face onto floor

When a grounded player moves off the low end of a face onto other floor
and stays grounded, then on the **first two frames** that start on a
column that isn't a face column:

- a held direction pointing **back up the face** (opposite to the face's
  downhill direction) is ignored: the target speed is 0, as if no
  direction were held;
- a held direction pointing the other way (the way the face falls)
  applies normally.

From the third such frame the normal target applies. Measured moving
left off the foot of a face falling to the left, with Right held (three
runs: target 0 on two frames, then the normal target) and with Left held
(two runs: normal target from the first frame). Leaving a face moving
right in this way, and facing on these frames, are not covered.

## Examples

Synthetic floors. `y` is down-positive, as in a trace.

**Walking into a rise.** Flat floor at `y` = 0 for columns up to 199. A
face rises to the right from column 200: column 200 at `y` = −1, 201 at
−5, 202 at −9, and so on (4 px per column). Walking right at +2 with
Right held, `x` = 193.5 before frame 1:

| Frame | `x` | `vx` (1/256) | Note |
|---|---|---|---|
| 1 | 195.5 | 512 | |
| 2 | 197.5 | 512 | |
| 3 | 199.5 | 512 | |
| 4 | 199 | 0 | Blocked: 201.5 is in face column 201 |
| 5 on | 199 | 0 | Right still held: column 204 is 17 px higher |

**Standing in front of the same rise**, Right held from frame 1:

- At `x` = 196.875 (column 196): column 201 is 5 px higher, so the
  player starts walking, as on flat floor: `x` is 197.125, 197.59375,
  198.25390625 and 199.078125 on frames 1 to 4 (`vx` 64, 120, 169, 211 in
  1/256). On frame 5 the move would reach 200.046875 (column 200), so the
  player is blocked: `x` 199, `vx` 0. They now stand closer to the rise
  than they could have started from.
- At `x` = 197 (column 197): column 202 is 9 px higher, so `vx` stays 0
  and `x` stays 197 on every frame.

**Coasting down a face.** Flat floor at `y` = 0 for columns up to 299; a
face falling to the right from column 300 (`y` = 2, then 6, 10, … for
columns 300, 301, 302, …). Nothing held, `x` = 299 and `vx` = 1
(256/256) before frame 1:

| Frame | Column before the frame | `vx` (1/256) | `x` | `y` | Note |
|---|---|---|---|---|---|
| 1 | 299 | 224 | 299.875 | 0 | Normal target 0 |
| 2 | 299 | 196 | 300.640625 | 2 | Onto the face: normal target 0 |
| 3 | 300 | 219 | 301.49609375 | 6 | Face target +384 |
| 4 | 301 | 239 | 302.4296875 | 10 | |
| 9 | 306 | 308 | 307.98046875 | 30 | |
| 10 | 307 | 317 | 309.21875 | 38 | Crossed 2 columns: 8 px drop, followed |
| 11 | 309 | 325 | 310.48828125 | 42 | |

`vx` keeps rising toward 384 and the player follows the face, crossing
one or two columns per frame.

**Walking down the same face.** Right held, `x` = 297.75 and `vx` = 512
before frame 1:

| Frame | `vx` (1/256) | `x` | `y` | `ground` | Note |
|---|---|---|---|---|---|
| 1 | 512 | 299.75 | 0 | 1 | |
| 2 | 512 | 301.75 | 6 | 1 | Onto the face: normal target |
| 3 | 560 | 303.9375 | 14 | 1 | Face target +896; 8 px drop, followed |
| 4 | 602 | 306.2890625 | 17.4375 | 0 | 3 columns: leaves (L), `y` + 3.4375 |
| 5 | 591 | 308.59765625 | 17.87890625 | 0 | Air: normal target +512 |

From frame 5 on, `vy` and the landing follow `walk-off.md` and
`landing.md`.

## Not covered

- Rolls on, onto or into faces and walls, and Action held on a face
  (with or without a direction).
- Jumping from a face, and jumps from a standstill next to a rise (on
  the press frame the player stays put as above, but the take-off frame
  was seen to move). One case is measured: walking down the face at the
  top of a ledge, a press whose next frame starts on the face gives no
  jump (`walk-off.md`).
- The frames after landing on a face from the air. Contact with faces
  and walls while airborne is in `air-walls.md`, except falls into pits
  that carry the player below the floor of the column they are in.
- Moving left off the low end of a face that ends at a drop rather than
  at floor: the player was seen to leave the ground while still over the
  face's last column. (Moving right off such an end, the rule above
  holds.)
- Leaving a face onto floor while moving right, and facing on the frames
  after leaving a face.
- Vertical steps of other heights, steps up of 9 to 63 px in general,
  faces steeper or less steep than 4 px per column, and a vertical step
  rising to the right.
- Differences of 6 to 8 px in the "can't start" rule.
- How a trace's `state` names frames on a face (the vocabulary has no
  name for being carried down a face).
- Entities, moving platforms and the other character.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character alone and no enemies nearby. Two steep faces were measured, both
rising to the right: one **64 px** high (16 face columns, with flat floor
at its foot and at its top) and one **96 px** high (24 face columns, flat
floor at its foot). The player walked, ran, rolled, turned, stopped and
started again against both faces from the left. They went down the 64 px
face to the left from the floor at its top, walking, coasting with
nothing held and holding the uphill direction. There were also random
sequences of Left, Right and nothing on and around both faces. Speeds
are whole multiples of 1/256 px per frame, written both ways. "The
speed rule" is the one of `ground-speed.md` ("In `title-2`"): the
target is chosen from the frame's input, and the gap is divided by 8,
rounded toward zero, or the target itself is taken when that is 0.

### Faces (`title-2`)

Columns, steep faces, face columns, rising and falling are as defined
above in "Columns, faces and steps". Both measured faces have the
`title-1` shape. Each face column is exactly 4 px higher than the one
below it. The lowest face column is 1 px above the floor at the foot,
and the highest face column is 3 px below the floor at the top.

### Stopped by a rise (`title-2`)

The `title-1` rule ("Stopped by a rise") holds unchanged. On a grounded
frame the player is **blocked** if the move would take `x` into a
different column that is a face column of a face rising in the direction
of the move. Then:

- `x` is set to the left edge of the column the player was in before the
  frame, a whole number;
- `vx` becomes **0**;
- the player stays grounded on the floor they were on.

A face column only 1 px above the player's floor blocks, as in
`title-1`. This held at every speed measured: starting from rest,
walking, running, turning, rolling (the roll goes on and is blocked again)
and on the first frame after landing on a face. The move that is tested
uses the speed from the speed rule, before the limits of "Speed on a
face" below.

### Starting toward a rise (`title-2`)

`title-1`'s rule "Can't start toward a rise" does **not** apply. A player
with `vx` 0 who holds toward a rise starts moving as on flat floor and is
blocked again as soon as a move would cross into the face column. So a
player holding toward a face repeats a short cycle at its foot. After a
block at whole-pixel `x`:

| Held | Frames of the cycle | `x` on those frames | `vx` (1/256) |
|---|---|---|---|
| The direction of the rise | 3 | `x`, `x` + 0.28125, `x` + 0.80859375 | 0, 72, 135 |
| That direction and Action | 2 | `x`, `x` + 0.421875 | 0, 108 |

The frame after the last one in the table is blocked again and starts the
cycle over. Releasing the direction and pressing it again starts the same
cycle from rest. The cycle held for as long as the direction was held,
up to 80 frames in the measurements.

### Face frames (`title-2`)

A **face frame** is a frame that starts with the player grounded (the
frame right after a landing included) and `x` in a face column. The
rules below are written for the measured faces, which fall to the left,
so "downhill" is to the left there.

### Speed on a face (`title-2`)

1. **Pull.** On every frame that **follows** a face frame, the target of
   the speed rule is moved **1 px per frame (256/256) downhill**. This
   applies whatever is held, whether or not the frame is itself a face
   frame, and also if the player has just left the ground. Then the speed
   rule applies as usual. The first face frame on a face therefore uses
   the normal target, and the first frame after the last face frame
   still has the pull.

   | Held (Action not held) | Target with the pull |
   |---|---|
   | Nothing | 1 downhill (256/256) |
   | The downhill direction | 3.25 downhill (832/256) |
   | The uphill direction | 1.25 uphill (320/256) |

2. **Limits.** On a face frame, after the speed rule and after the
   blocking test above:
   - a speed of more than **2.25** px per frame downhill (576/256) becomes
     exactly **2** downhill (512/256);
   - a speed of less than **0.75** downhill (192/256), including 0 and any
     uphill speed, becomes exactly **0.75** downhill (192/256).

   Then `x` changes by the new `vx`.

So a player can't stand still on a face or walk up it:

- **Holding downhill**, `vx` settles into alternating **2** and
  **2.15625** (512/256 and 552/256): from 2, the pull gives 2.15625; from
  2.15625, the speed rule gives more than 2.25, which the limit turns
  into 2.
- **Holding nothing**, `vx` moves toward 1 downhill by the speed rule and
  reaches it exactly, from either side.
- **Holding uphill**, `vx` drops by the speed rule toward 1.25 uphill and
  is held at 0.75 downhill by the limit.

### Following a face down, or leaving it (`title-2`)

On a face frame, after the horizontal move, compare the floor of the new
column with the floor of the column before the frame:

- **8 px lower or less** (at most two face columns crossed, or onto the
  floor at the foot, 1 or 5 px lower): the player stays grounded and `y`
  becomes the new column's floor.
- **More than 8 px lower** (9 and 12 px were measured): the player leaves
  the ground. On this frame (L) `y` moves down by **exactly 8 px**, not
  by 3.4375 as in `walk-off.md`, and `vy` ends at **0.00390625** (1/256).
  From L + 1 `vy` increases by **0.4375** (112/256) each frame, so it is
  113/256 after L + 1 (not 368/256 as after a walk-off in `title-2`). The
  player moves down by the new `vy`. Horizontal speed follows
  `air-control.md` ("In `title-2`"), with the pull on L + 1 because L was
  a face frame. The player lands by the `title-2` landing rule of
  `walk-off.md`.

Because the alternation of 2 and 2.15625 lowers the fraction of `x` by
0.15625 every two frames, a player holding downhill sooner or later
crosses three columns in one frame on a long enough face, and leaves the
ground there. All four departures from a face that were measured
happened this way. Three crossed three face columns (12 px). One went
from the third face column from the bottom onto the floor at the foot
(9 px).

### Getting onto a face from above (`title-2`)

The frame that moves the player from the floor at the top onto the face
isn't a face frame. Whether the player stays grounded on it depends on how
far below the top floor the new column is:

- a move that ends in the **highest face column** (3 px below the top
  floor) keeps the player grounded on it, and the next frame is a face
  frame;
- a move that ends in a lower face column (7 or 11 px below) makes the
  player leave the ground as when walking off a ledge (`walk-off.md`, "In
  `title-2`": frame L moves down 3.4375 px).

Drops of 4 to 6 px from ordinary floor weren't measured. Walking at the
top speed of 2.25 px per frame, whether the move ends in the highest face
column depends on the fraction of `x`. Running (Action held), 20 of 21
measured moves skipped it and left the ground.

### Facing (`title-2`)

On face frames the player faces downhill. A player facing uphill turns
round on that frame, without the turn of `ground-speed.md` ("In
`title-2`"). The one exception measured is the first frame
after landing on a face: facing doesn't change on it, and the player turns
on the next frame (seen in 3 landings). Holding the uphill direction on a
face never turned the player uphill.

### Examples (`title-2`)

Synthetic floor, `y` down-positive: `y` = 0 for columns up to 199. A face
rises to the right over columns 200 to 215 (`y` = −1, −5, −9, …, −61,
that is −1 − 4k for column 200 + k). Flat floor at `y` = −64 from column
216.

**Walking into the face.** Right held, `x` = 193.5 and `vx` = 576/256
before frame 1:

| Frame | `x` | `vx` (1/256) | Note |
|---|---|---|---|
| 1 | 195.75 | 576 | |
| 2 | 198 | 576 | |
| 3 | 198 | 0 | Blocked: 200.25 is in face column 200 |
| 4 | 198.28125 | 72 | Starts again from rest |
| 5 | 198.80859375 | 135 | |
| 6 | 199.55078125 | 190 | Still column 199 |
| 7 | 199 | 0 | Blocked |
| 8 | 199.28125 | 72 | |
| 9 | 199.80859375 | 135 | |
| 10 | 199 | 0 | Blocked, and so on every 3 frames |

**Running into the face.** Right and Action held, `x` = 192.25 and
`vx` = 864/256 before frame 1: `x` 195.625 and 199 on frames 1 and 2, then
blocked at 199 on frame 3, 199.421875 (`vx` 108) on frame 4, blocked on
frame 5, and so on every 2 frames.

**Coasting down.** Nothing held, `x` = 217.5 (on the top floor), `vx` =
−320/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | Note |
|---|---|---|---|---|
| 1 | 216.40625 | −64 | −280 | |
| 2 | 215.44921875 | −61 | −245 | Onto the highest face column (3 px) |
| 3 | 214.609375 | −57 | −215 | Face frame, normal target 0 |
| 4 | 213.75 | −53 | −220 | Pull: target −256 |
| 5 | 212.875 | −49 | −224 | |
| 6 | 211.984375 | −45 | −228 | |
| 7 | 211.08203125 | −45 | −231 | Same column |
| 18 | 200.6796875 | −1 | −248 | |
| 19 | 199.70703125 | 0 | −249 | Onto the floor at the foot |
| 20 | 198.70703125 | 0 | −256 | Pull once more: gap 7 ÷ 8 is 0, so the target |
| 21 | 197.83203125 | 0 | −224 | Normal target 0 |

**Walking down, then holding uphill.** Left held for frames 1 to 4, then
Right; `x` = 219.75 (top floor) and `vx` = −576/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | Note |
|---|---|---|---|---|
| 1 | 217.5 | −64 | −576 | |
| 2 | 215.25 | −61 | −576 | Onto the highest face column |
| 3 | 213 | −53 | −576 | Face frame: normal target, 8 px down, followed |
| 4 | 211 | −45 | −512 | Pull: −608, limited to −512 |
| 5 | 209.40625 | −37 | −408 | Right held: target +320 |
| 6 | 208.16796875 | −33 | −317 | |
| 7 | 207.23828125 | −29 | −238 | |
| 8 | 206.48828125 | −25 | −192 | −169 limited to −192 |
| 9 to 17 | … | | −192 | Down the face at 0.75 px per frame |
| 17 | 199.73828125 | 0 | −192 | Onto the floor at the foot |
| 18 | 199.23828125 | 0 | −128 | Pull once more: target +320 |
| 19 | 199.08203125 | 0 | −40 | |
| 20 | 199.2265625 | 0 | 37 | |
| 21 | 199.6328125 | 0 | 104 | |
| 22 | 199 | 0 | 0 | Blocked: back at the foot |

If Left had stayed held, frame 5 would have moved 2.15625 px from
`x` = 211 to 208.84375, three columns and 12 px down, and the player
would have left the ground there, 8 px lower (−37).

### Evidence (`title-2`)

527 runs (70 312 frames) at the two faces. 30 813 frames that started
grounded inside the mapped floor were checked frame by frame against the
rules above (`x`, `vx`, `y` and whether grounded), with 0 differences.
That is 3 250 blocked frames, 1 650 face frames, 161 frames after a face
frame that weren't face frames, 34 departures from the ground and 25 718
frames on ordinary floor near the faces. Among the face frames, 364 met
the upper limit and 339 the lower one. 120 of the runs were random input
recorded after the rules were first written. On first check they showed
2 differences, both corrected in the rules above: the blocking test comes
before the limits, and it applies on the first frame after a landing.
Frames in a turn weren't checked frame by frame. More than 300 blocked
frames were seen during turns, with the result the blocking rule gives.

### Not covered in `title-2`

- Faces that rise to the left, faces steeper or less steep than 4 px per
  column, and vertical steps.
- Action held or rolling on a face. Rolls and runs that reach the face
  from the top floor were seen to leave the ground there, as above.
- Jumps from a face, and the frames of a jump or fall that meet a face
  (walls in the air, `air-walls.md`), apart from the landings on a face
  above.
- Longer falls after leaving a face than the four measured, which landed
  on the floor at the foot within 11 frames.
- The second character, enemies and moving platforms.
- How a trace's `state` names face frames.
