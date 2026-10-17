# Entering a level

Titles: `title-1`

How the characters enter the first level when it begins: they start
hidden at the level's start point, inside the level's **entrance**, which
bursts and throws them out. Where the start point is comes from the
level's data (`../formats/level-start.md`). Which characters are there,
and which one leads, is in `following.md` ("When a level begins again").
Resuming at a restart point is in `restart-barrel.md`.

Frames are logic frames at 60 Hz, numbered as in a trace: frame 0 is the
level's first frame. Horizontal positions are level x as in `view.md`;
vertical positions are **level y** as in `view-vertical.md`, growing
downward. `vx` and `vy` are in px per frame, `vy` positive downward.
"Right", "Left", "Down", "Jump" and "Action" are the neutral inputs `R`,
`L`, `D`, `J`, `A` of the scenario format.

Measured on the first level, entered from the level map: 24 runs with
the entrance, 19 with one character and 5 with two. Inputs held from
frame 0 or from frames 30 to 107 (Right, Left, Jump, Action, Down,
Right with Action, some held to the end, some for a few frames only),
or none. Also in copies of the image: one with the start record moved,
and five in which the start point's entries were changed (see "The
entrance" below).

## The entrance

The first level's placement list has an entry of kind `entrance`
(`../formats/placements.md`, "Kinds") at the start point. Its object
appears on the level's first frames, like the other objects already in
the view when the level begins.

Without it nothing below happens: in a copy of the image in which that
entry's reference was replaced by another kind's, the characters stayed
hidden at the start point, not moving, for as long as the runs went on
(140 frames with one character, 200 with two). The first level has a
second entry at the start point, of a kind without a fingerprint row; in
copies of the image with only one of the two entries kept, the entrance
alone gave every frame below exactly, and the other one alone gave the
same as having neither.

## Frame by frame

Call the start point (`xs`, `ys`). In every run with the entrance, **T**, the
frame on which the characters are thrown out, was frame **51**, and the
entrance burst on frame **41**, also with the start record moved.

| Frame | What happens |
|---|---|
| 0 to T − 1 | Every character that is there is at `x` = `xs`, `y` = `ys`, not drawn, and doesn't move. Inputs have no effect. |
| 41 | The entrance bursts: a flash and four pieces appear. |
| T | The entrance's object is removed. The lead is drawn from this frame, but doesn't move yet: `vx` becomes **+2.375** (2 3/8, to the right) and `vy` **−2.375** (up). |
| T + 1 to T + 14 | The lead moves by `vx` and `vy` every frame, which don't change: up and to the right at 45°. |
| T + 15 on | Each frame, `vy` first increases by **0.4375** (7/16), but never above **8**; then the lead moves by `vx` and `vy`. `vy` reaches 8 on T + 38. |
| T + 39 on, up to and including the landing frame | `vx` changes with the input ("Steering" below). |
| The landing frame | As in `landing.md`. |

From T to T + 38 every input is ignored: `vx` stays 2.375 and nothing
held (directions, Jump, Action, Down) changes the motion. The lead faces
right from frame 0 up to and including the landing frame, whatever is
held. From the frame after the landing, the player is on the ground and
the usual rules apply (`ground-speed.md`, `jump-input.md`): for example,
Left held through the throw started a turn on the frame after the
landing, and Jump held from the landing frame started a jump on the
frame after it.

On frame T + `n`, relative to the start point, for `n` from 0 to 14:

- `x` = `xs` + 2.375 `n`
- `y` = `ys` − 2.375 `n`

From T + 15 on, `y` follows from the `vy` rule above. The top of the arc
is on T + 19: `vy` is −0.1875 on T + 19 and +0.25 on T + 20.

### Steering

From T + 39 up to and including the landing frame, each frame the lead
has a **target speed** set by what is held on that frame, as in
`air-control.md` ("Horizontal speed"): ±2 with Right or Left, ±3 with
Action too, 0 with no direction. Using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ **32**, rounded toward zero to a whole number.
3. If step is 0, the speed becomes the target. Otherwise the speed
   changes by step.
4. The lead moves by the new speed.

This is the rule of `air-control.md` with 32 in place of 8, so the speed
changes four times more slowly than in a jump. With nothing held, the
speed goes from 2.375 (608/256) to 589/256 on T + 39, then 571/256 on
T + 40.

### The second character

With two characters, the one that isn't leading stays at the start point,
not moving, up to and including T. From T + 1 up to and including the
lead's landing frame, on each frame it is at the lead's position of the
frame before, rounded down to whole px (`x` and `y`). After that it
follows the lead as in `following.md`.

### The picture

The picture fades in from black at the start of the level, in 15 equal
steps 3 frames apart: after frame `f` the brightness is ⌊`f` / 3⌋
fifteenths of full, up to 15 (full from frame 45). Which picture first
shows each step is not covered.

## Example

The start point is at `xs` = 100, `ys` = 300, and the floor below is
flat at level y 380. Nothing is held.

| Frame | `x` | `y` | `vx` | `vy` | What happens |
|---|---|---|---|---|---|
| 0 to 50 | 100 | 300 | | | Hidden |
| 51 (T) | 100 | 300 | 2.375 | −2.375 | Thrown; drawn from here |
| 52 | 102.375 | 297.625 | 2.375 | −2.375 | |
| 65 (T + 14) | 133.25 | 266.75 | 2.375 | −2.375 | |
| 66 (T + 15) | 135.625 | 264.8125 | 2.375 | −1.9375 | |
| 70 (T + 19) | 145.125 | 261.4375 | 2.375 | −0.1875 | Top of the arc |
| 71 | 147.5 | 261.6875 | 2.375 | 0.25 | |
| 89 (T + 38) | 190.25 | 340.875 | 2.375 | 8 | |
| 90 (T + 39) | 192.55078125 | 348.875 | 589/256 | 8 | Steering from here: (0 − 608) ÷ 32 → −19 |
| 91 | 194.78125 | 356.875 | 571/256 | 8 | (0 − 589) ÷ 32 → −18 |
| 92 | 196.9453125 | 364.875 | 554/256 | 8 | |
| 93 | 199.04296875 | 372.875 | 537/256 | 8 | 7.125 px above the floor: still in the air |
| 94 | 201.078125 | 380 | 521/256 | 0 | Lands (`landing.md`): 380.875 would be past the floor |

On frame 95 the ground rules apply: with nothing held the speed becomes
521 − 65 = 456/256 (`ground-speed.md`).

## Checks

A model of the rules above, fed only the inputs of each run, gave the
lead's `x`, `y`, `vx`, `vy` and facing on every frame from 0 to the frame
before the landing, in all 24 runs with the entrance (19 with one
character, 5 with two, including the one with the start record moved):
no differences. The landing frame's `vx` and `x` also matched in the 9
runs checked for it (nothing held; Right, Left, Right with Action held
from frame 0; Right or Left from frame 95; Jump on the landing frame;
Down; Left with two characters). The second character's rule above
matched on every frame of all 5 runs with two characters and the
entrance.

Controls: starting the steering on T + 38 or on T + 40, using 16 instead
of 32, or starting gravity on T + 14 each gave differences on 68 to 168
frames of 4 runs.

## Not covered

- Throws that land before T + 39, and entrances whose throw meets a wall
  or a ceiling.
- Entrances in levels other than the first, other ways a level can
  begin, and how a level's data says which one it uses.
- What the second entry at the start point is (in the runs it did
  nothing).
- The flash and pieces of the burst: their motion and when they are
  removed.
- What a trace shows on frames 0 to T − 1 and during the throw (states
  and events).
- Where the view starts and how it moves while the characters are
  hidden.
- Inputs pressed before frame 0 (on the level map or while the level
  loads).
