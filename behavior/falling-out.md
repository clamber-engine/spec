# Falling out of the level

Titles: `title-1`

What happens when the player falls below the bottom of the level, for
example into a gap in the floor with nothing under it: the frame on which
the fall ends the life, and what happens until the level ends. Frames are
logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx` and `vy` are
as in a trace: down is positive, speeds in px per frame.

Measured with the first character leading, at one gap in the floor of
one level: alone (24 runs) and with the second character following (8
runs). The player went in by walking off the gap's edge and then
stopping, turning back or pressing buttons while falling, or by jumping
into it with or without turning back. Separately, the fall was started
at 2560 chosen heights and downward speeds in the gap to find the exact
limit.

## The level's bottom

Positions here are **level y** as in `view-vertical.md`: in px, growing
downward, measured from the top of the level. A level has a **height**
`Yb`: the level y of its bottom edge. The view never shows anything
below it (`view-vertical.md`). How `Yb` is stored belongs to the level
format (not yet specified).

Below a gap in the floor the player falls as in any other fall (as in
`jump-arc.md` and `air-control.md`: `vy` grows by the usual amount each
frame up to 8, and input steers as usual). Nothing stops the fall at
`Yb`: the player passes below the bottom edge and keeps falling.

## The fall-out frame

Call **Z** the first frame that ends with the player's level y
**greater than `Yb` + 48**, after the player has moved on that frame.
Ending exactly at `Yb` + 48 is not enough; `Yb` + 48 + 1/256 is.

- On Z the player moves as on any falling frame. With `vy` at 8, the
  player's level y on Z is from `Yb` + 48 + 1/256 to `Yb` + 56.
- From Z on, all input is ignored until the level ends.
- From Z + 1 on, the player stays out of sight below the picture until
  the level ends. (On every measured fall the player was already below
  the picture on Z.)
- From Z + 1 on, the view doesn't move. In every measured fall the view
  had already reached its bottom limit (`view-vertical.md`) before Z.
- There are no pauses: logic frames run on every 60 Hz frame from Z to
  the end.

## The end of the level

The last frame is **Z + 96**: 97 frames from Z, counting Z. After it the
level ends; what follows (the screen transition, where play resumes, and
any count of remaining tries) is not covered, as for `knock-out.md`.

In all 32 runs, Z + 96 was the last frame, whatever the input, the way
the player went in and the speed on the way down.

## With two characters

Falling out ends the level in the same way when the other character is
present: Z is found the same way from the lead character, and Z + 96 is
the last frame. The other character isn't lost first and doesn't take
over. It keeps following the lead's path after Z; in every run it went
into the gap after the lead and fell out of sight too.

## In a trace

- Z is an ordinary `fall` row.
- Z + 1 to Z + 96: `x` and `y` as on Z, `vx` 0, `vy` 0, `ground` 0, state
  `fall`, no events. The vocabulary has no state for these frames yet;
  `fall` is used until it does.
- There are no rows after Z + 96.

## Example (made-up numbers)

A player falls with `vy` at 8 in a level whose `Yb` is 400. Frame 300
ends at level y 396.5, above the bottom edge. Frame 301 ends at 404.5,
below the edge, and nothing happens. Frames 302 to 306 end at 412.5 to
444.5. Frame 307 ends at 452.5, greater than `Yb` + 48 = 448: Z is
frame 307. Input is ignored from frame 307, and frame 403 (Z + 96) is
the last frame of the level.

Had frame 306 ended at exactly 448, Z would have been frame 307 at 456,
not frame 306.

## Not covered

- The second character leading, with the first one following, and the
  other character falling out while the lead stays in the level.
- Other gaps and other levels. The rule was measured against one
  bottom edge only.
- Which floors and walls stop a player who is below the floor of the
  column they are in (inside a gap, the player was seen to pass under
  the floors on either side) and where the sides of a gap stop a fall.
- Falling out during a roll, a bounce (`bounce.md`), while carrying an
  object (`carrying.md`) or while knocked out (`knock-out.md`).
- What enemies, objects and sound do from Z to the end.
- Leaving the level through its left or right end.
