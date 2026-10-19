# Entering a level

Titles: `title-1`

How the characters enter the first level when it begins: they start
hidden at the level's start point, inside the level's **entrance**, which
bursts and throws them out. Where the start point is comes from the
level's data (`../formats/level-start.md`). Which characters are there,
and which one leads, is in `following.md` ("When a level begins again").
Resuming at a restart point is in `restart-barrel.md`. Coming back to
the first level from its room begins in the same way, at another record's
point and out of another entry ("Coming back from the room" below;
`exits.md`, "Where the first level's exits lead").

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
second entry at the start point, of kind `room-return-entrance` ("Coming
back from the room" below). In copies of the image with only one of the
two entries kept, the entrance alone gave every frame below exactly, and
the room-return entrance alone gave the same as having neither.

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
held. What the landing frame starts is in "The landing" below.

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

### The landing

Call **L** the landing frame (the frame carrying `land`). The landing at
the end of the throw works like a landing after a bounce off a walker.
It has no landing frames, and the rules for that case apply with L as
their landing frame: `ground-speed.md` ("After a fall or a bounce",
"After a bounce") and `jump-input.md` ("After a bounce"). In short:

- **A mode starts on L itself**, set by what is held on L:
  - Action and either direction: running at step 1, facing still right,
    even with Left held;
  - otherwise Left: a turn starts on L, so facing changes to left on
    L + 4;
  - Right: walking at step 1;
  - nothing: standing.

  From L + 1 the usual rules apply.
- **Jump** (E = L + 2): a press on L − 10 to L + 1 that is held on
  every frame up to L + 1 starts a jump on L + 2. A press on L + 2 or
  later starts a jump on the frame after the press. Nothing else starts
  one: not a press on L − 11 or earlier, not Jump held without a break
  since before then (for example since the throw), and not a press
  released on L or earlier.
- **Action**: a press on L that is still held on L + 1 starts a roll on
  L + 1. A press in the air isn't remembered, even if it is held through
  L: with a direction held it only starts the run on L. A press on
  L + 1 or later starts a roll on that frame, as on the ground
  (`roll-input.md`).

Examples, on flat floor with nothing else held:

| Held | What happens |
|---|---|
| Left from frame 30 to L + 2 | A turn starts on L. Facing is left from L + 4 |
| Left on L only | The same: the turn goes on with nothing held |
| Left on L + 1 only | A turn starts on L + 1. Facing is left from L + 5 |
| Right with Action from frame 30 on | Running starts on L |
| Jump from L − 10 to L + 1 | Jump starts on L + 2 |
| Jump from L − 11 to L + 8 | No jump |
| Jump on L only | No jump |
| Jump on L + 1 only | Jump starts on L + 2 |
| Action on L only | No roll. Standing starts on L |

### The second character

With two characters, the one that isn't leading stays at the start point,
not moving, up to and including T. From T + 1 up to and including the
lead's landing frame, on each frame it is at the lead's position of the
frame before, rounded down to whole px (`x` and `y`). After that it
follows the lead as in `following.md`. This holds too when the level
begins again after its room ("Coming back from the room" below; measured
in `exits.md`, "Checks", "Two characters").

### The picture

The picture fades in from black at the start of the level, in 15 equal
steps 3 frames apart: after frame `f` the brightness is ⌊`f` / 3⌋
fifteenths of full, up to 15 (full from frame 45). Which picture first
shows each step is not covered.

Where the view is from frame 0 and how it moves through the entrance
are in `view.md` ("When an area begins").

## Coming back from the room

When play comes back to the first level from its room
(`exits.md`, "Where the first level's exits lead"), the level begins as
above, with the room-return record's point in place of the start
record's (`../formats/level-start.md`), and with the entry of kind
`room-return-entrance` in place of the `entrance`: that entry's object
bursts on frame 41 and is removed on T, and everything above holds with
it. The `entrance` entry's object is then there but takes no part.

So each of the two entries acts only for its own way into the level:

| Way into the first level | The entry that bursts and throws the characters | The other entry |
|---|---|---|
| From the level map | `entrance` | `room-return-entrance`: there, does nothing |
| Coming back from the room | `room-return-entrance` | `entrance`: there, does nothing |

An entry that does nothing is not drawn. With no input, entering from
the level map and coming back from the room, the pictures of frames 30,
45, 55, 80 and 110 were the same as in a copy of the image in which the
entry that does nothing was given the reference of a hidden object
(`hidden-objects.md`), which draws nothing.

Without the `room-return-entrance`, coming back from the room is like a
level without its entrance: the characters stay hidden at the point, not
moving.

Measured by coming back from the room as in `exits.md` ("Checks"): 41
runs (one with no input after leaving the room, 40 with random inputs
from frame 1) in each of four images: the supported image, and copies
with only the `room-return-entrance` kept, only the `entrance` kept, and
neither (the other entries at the start point given a hidden object's
reference). The lead was compared on every frame 0 to 89 with the
level's own start from the level map with no input (position, speed,
state and facing):

| Image | Runs | Frames | Different |
|---|---|---|---|
| Supported | 41 | 3 690 | none |
| Only `room-return-entrance` | 41 | 3 690 | none |
| Only `entrance` | 41 | 3 690 | every run, from frame 51 on: still hidden |
| Neither | 41 | 3 690 | every run, from frame 51 on: still hidden |

Entering from the level map with the same four images, with no input:
the supported image and the copy with only the `entrance` threw the lead
on frame 51 as above; with only the `room-return-entrance`, or neither,
the lead stayed hidden.

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

## In a trace

The trace follows the lead.

- **Frames 0 to T − 1**: state `fall`, `ground` 0, `vx` 0, `vy` 0, `x`
  and `y` those of frame 0, facing right, no events. The characters are
  hidden and not on the ground; the vocabulary's `fall` covers being in
  the air without having jumped.
- **T up to L − 1**: state `fall`, `ground` 0, facing right, no events.
  In particular there is no `bounce`: nothing is bounced off. `vx` and
  `vy` are the speeds of "Frame by frame". On T they are already 2.375
  and −2.375, though the lead only starts moving on T + 1.
- **L**: event `land`, `ground` 1, `vy` 0. The state is the usual
  ground state for the row's `vx` and input: `idle` if `vx` is 0,
  otherwise `run` with Action held and `walk` without. A turn starting
  on L doesn't add an event; the `turn` event comes on L + 4.
- **From L + 1**: as anywhere else.

The scenario `../scenarios/entrance-throw.scn` and its reference trace
cover this. Left is held through the throw and the landing, so the turn
starts on L. Then the lead walks right and jumps.

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

The landing ("The landing"): 80 runs with one character and random
Left, Right, Jump and Action (Jump or Action, never both, in each run)
held for 1 to 12 frames at a time from frame 85 to 130, and 28 runs with
chosen inputs (the examples above and the edges of the Jump window). On
L, 16 runs started standing, 29 walking, 14 running and 21 a turn. The
first jump (40 runs) and the first roll (39 runs) up to L + 18 matched
the rules above, with no differences. Two alternatives each failed: a
mode starting on L + 1 would make every turn on L a frame late, and
remembering a held Action press from the air (as after a jump,
`roll-input.md`, "In the air") would have given 11 rolls that didn't
happen.

## Not covered

- Throws that land before T + 39, and entrances whose throw meets a wall
  or a ceiling.
- Entrances in levels other than the first, other ways a level can
  begin, and how a level's data says which one it uses.
- Whether the `room-return-entrance` appears again after T while play
  goes on (as the `entrance` doesn't: `../formats/placements.md`, "When
  placed objects are removed").
- The flash and pieces of the burst: their motion and when they are
  removed.
- The landing ("The landing") with two characters, and on floor that
  isn't flat.
- Inputs pressed before frame 0 (on the level map or while the level
  loads).
