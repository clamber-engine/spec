# Losing a character (hit with two characters)

Titles: `title-1`

What happens when the player takes damage while the other character is
with them: the character under control (the **hit character**) is
thrown up, lands and runs out of the level, and the **other character**
takes its place and becomes the one under control. Being hit with no
other character is in `knock-out.md`. Which contacts with a walker cause
damage rather than a bounce is in `walker-contact.md` (measured with one
character), and when a thrower's ball does in `ball-contact.md`
(the thrower itself, measured with one character, in
`thrower-contact.md`).

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y` and
`vy` are as in a trace: down is positive, speeds in px per frame. Speeds
and positions are whole multiples of 1/256 px.

The **first character** is the one under control when a game begins;
the **second character** is the one that joins later (how it joins, and
how the other character follows, is in `following.md`). Measured with the
first character as the hit character and the second following it, hit by ground enemies from the
side: walking or running into them, standing facing toward and away from
an enemy that walks into the player from the right or from the left,
while turning, on the frame of a jump press, on the take-off frame,
rising in a jump with the jump input held or already released, and
falling, by inputs alone (90 runs). Mostly on flat ground.

## The hit frame

Call **H** the first frame on which the player is hurt (the trace row
carrying the `hurt` event). Call the hit character's position on H the
**hit spot**.

On H the hit character:

- doesn't move: `x` and `y` are those of H − 1, whatever the speed was
  before, on the ground or in the air;
- gets `vy` **−8** and no horizontal speed;
- faces **right**, whichever way it faced before and whichever side the
  enemy came from.

From H on, all input is ignored until T (below). Nothing pauses and
nothing freezes: there is no pause like the one in `knock-out.md`, and
enemies keep moving on every frame (the thrower and its balls:
`thrower.md`, "While a character is lost").

## The hit character

From **H + 1**, each frame until it lands:

1. `vy` increases by **0.4375** (7/16), but never above **8**. If on
   H − 1 a jump was in progress with the jump input held on every frame
   since its press frame (the press frame included), so that the lighter
   gravity of `jump-arc.md` applied, the increase is **0.28125** (9/32)
   on every frame of this flight instead, whatever the input does after
   H.
2. It moves vertically by the new `vy`. It doesn't move horizontally.
3. The landing rule in `landing.md` applies.

Hit on flat ground with the usual increase, it rises 69.1875 px (highest
on H + 18) and lands back on the floor on **H + 35**. Hit higher up it
lands later.

On the frame after it lands it doesn't move. From the next frame it runs
**right** at **4 px per frame**, following the ground, until it is out of
view, and then it is gone. It plays no further part.

## The other character

From **H to H + 36** the other character doesn't move, even if it was
moving on H − 1.

From **H + 37**, each frame, it moves toward the hit spot. The floor
doesn't hold it: when the hit spot is in the air it rises off the floor
to it, and it comes down through the air to a hit spot below it. The target
is the hit spot with the fraction of `x` dropped, and `y` rounded to a
whole pixel toward the floor if it has a fraction. Each axis moves on
its own:

1. **gap** = target − the character's own coordinate rounded the same
   way (fraction of `x` dropped, `y` rounded toward the floor).
2. If |gap| ÷ 16 is 3 or more, the move is gap ÷ 16 (exact; a multiple
   of 1/16). Otherwise the move is 3 toward the target, or the gap
   itself if that is smaller than 3.
3. The coordinate changes by the move. Its fraction is kept.

When both rounded coordinates equal the target, the run is over. The
next frame is **T**.

On **T** the character is placed half a pixel right of the target `x`
and half a pixel above the target `y`, and starts a hop: it then moves
up by **9.5625** on T + 1, **9.125** on T + 2 and **7.9140625** on
T + 3. After that, each frame it moves up 0.4375 (7/16) less than the
frame before, so the move becomes downward, never more than **8** down.
The landing rule in `landing.md` applies. With no Left or Right held,
and on flat ground, the hop rises 94.7421875 px (highest on T + 21) and
lands on **T + 41** where it started.

### Control returns

- From **T + 1**, Left and Right steer the hop and change facing, as
  they do in the air for that character. Before T + 1 they do nothing.
- Jump presses before the hop lands start nothing by themselves. From
  the hop's landing, jumping follows `jump-input.md` exactly as after any
  landing (the landing lockout, and a press held through it starts a
  jump on E). Measured with presses from 5 frames before the landing to
  4 frames after it.
- From the hop's landing the other character is the character under
  control, in every respect.

### Example (made-up numbers)

The hit character is hit standing on flat ground at `x` = 200.25. The
other character stands on the same floor at `x` = 138. From H + 37 its
`x` is 141.875 (gap 62, move 3.875), 145.5625 (gap 59), 149 (gap 55),
152.1875 (gap 51), 155.1875 (gap 48, move 3), then 3 more each frame
until 200.1875 on H + 56, when the rounded `x` is 200. T is H + 57: `x`
becomes 200.5 and `y` half a pixel above the floor. Its `y` doesn't
change before T, since its gap there is 0.

Vertically, a character 78 px above the target (`y` = target − 78)
moves down by 4.875, then 4.5625 (gap 73), and so on; its last moves
are 3 (from 4.6875 above to 1.6875 above), then 1 (gap 1, to 0.6875
above, which rounds toward the floor to the target).

## In a trace

The trace follows the hit character from H to H + 35, and the other
character from H + 36. `x` and `y` stay relative to the scenario's start
position, so they jump on H + 36 to wherever the other character is.

Rows H to H + 35 (the hit character):

- State `hurt`. Events: `hurt` on H, and `turn` on H too if the hit
  character faced left on H − 1. Facing right throughout.
- `vx` 0. `vy` is the vertical speed applied on that frame (−8 on H).
- `ground` on H as on H − 1, then 0 while in the air. If it lands by
  H + 35: the landing row has `y` on the floor, `vy` 0, `ground` 1 and
  event `land`, and later rows up to H + 35 have `vy` 0, `ground` 1 and
  no events.

Rows from H + 36 (the other character):

- State `swap` from H + 36 until the row before the hop lands. `ground`
  0 on all of these rows, including the run along a floor.
- `vx` and `vy` are the moves made on that frame (0 and 0 on H + 36).
  On T, `vy` is −10 and `vx` 0 (the speed the hop is given there, like
  the speed shown on a bounce frame in `bounce.md`), though the
  placement on T is only half a pixel. On T + 1, T + 2 and T + 3, `vy`
  is −9.5625, −9.125 and −7.9140625.
- Facing is the other character's own. No `turn` on H + 36; `turn` on
  later rows whenever facing changes.
- The hop's landing row: `y` on the floor, `vy` 0, `ground` 1, event
  `land`, and a ground state as usual (`idle`, `walk`, `run`).

## Not covered

- The second character's own movement values are in
  `second-character.md` (walking, running, crouching, jumps, air
  control). Everything else in the other behavior files was measured
  with the first character.
- The first character joining as the other character, and hits while
  the second character is under control with the first one following
  (the second character knocked out with no one left is in
  `knock-out.md`, "The second character (`title-1`)").
- A hit when the other character is already at the hit spot, out of
  view, or facing away from the hit spot when its run begins.
- Ground or walls between the other character and the hit spot.
- Protection from further damage after the take-over is in
  `protection.md`.
- What the hit character's run does at walls, pits and the edges of the
  level, beyond following the ground and falling off ledges.
- Hits during a roll, a bounce or while carrying an object.
