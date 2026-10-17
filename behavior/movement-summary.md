# Movement summary

Titles: `title-1`

A one-page index of how the playable characters move, for building a
movement preset. Every value here is defined exactly, with its
conditions and edge cases, in the file named next to it; where this page
and that file seem to differ, the file is right. Frames are logic frames
at 60 Hz. `vx` and `vy` are in px per frame, right and down positive, as
in a trace. Speeds are whole multiples of 1/256 px per frame. Values
are for the first character; the second character's differences are at
the end.

## One rule for horizontal speed

On the ground and in the air, every frame: the **target** speed comes
from what is held on that frame, and the speed moves toward it by one
eighth of the gap, rounded toward zero to 1/256 (the speed becomes the
target when that step would be 0). The player then moves by the new
speed. (`ground-speed.md`, `air-control.md`)

| Held | Target |
|---|---|
| Left or Right | ±2 |
| Left or Right, with Action held | ±3 |
| No direction, or both | 0 |

So there is no separate acceleration, braking, skid, turning or air
rate: starting from rest gives 0.25 on the first frame, stopping and
reversing use the same rule, and the air uses the same targets. Slopes
of 1 px in 4 don't change any of this (`slopes.md`).

| Subject | Summary | File |
|---|---|---|
| Starting from rest | `vx` 0.25, 0.46875, 0.66015625, … toward the target | `ground-speed.md` |
| Running | Action held with a direction; an Action *press* starts a roll instead | `ground-speed.md`, `roll-input.md` |
| Facing on the ground | A turn lasts 8 frames, facing changes 4 frames in; from standing it starts at once. While walking or running it starts only on a turn check of the stride cycle (0 to 7 frames' delay walking, 0 to 12 running). Speed is never affected | `ground-speed.md` |
| Crouch (Down) | Target 0 from the second frame Down is held until 10 frames after the crouch ends; a crouch lasts at least 13 frames, even after a shorter press | `ground-speed.md` |
| Steep faces and walls | Blocking, being carried down faces, leaving the ground on them, landing on or next to them (when the push starts), rolls on and into them (a blocked roll stays stopped until it pushes off) | `steep-faces.md` |

## Jumping and falling

| Subject | Summary | File |
|---|---|---|
| Press to take-off | Press on P, take-off (`jump_start`) on P + 1, also during a roll | `jump-input.md` |
| Take-off speed | `vy` −7 on the take-off frame, which doesn't move yet | `jump-arc.md` |
| Gravity | 0.28125 per frame while Jump has been held since the press, 0.4375 once released (rising and falling alike); fastest fall 8 | `jump-arc.md` |
| Variable height | `apex_height` 52.5 px (tap) to 83.625 px (held 26 frames or more); holding longer still lengthens the fall | `jump-arc.md` |
| Standing, walking, running jumps | Same vertical motion; horizontal by the one rule above | `jump-arc.md`, `air-control.md` |
| Jump distances | 85.5 px standing with Right, 98 px walking, 147 px running (held to landing) | `air-control.md` |
| Landing | A falling frame that ends less than 5 px above the floor lands on it | `landing.md` |
| Modes after landing | No turns for 7 frames, facing follows the input; Action and a direction held run from the frame after landing; otherwise walking or standing from the 7th frame after landing | `ground-speed.md` |
| Jump after landing | Earliest on the third frame after the landing frame; a press made in the air shortly before, and held, is remembered until then | `jump-input.md` |
| Walking off a ledge | Moves down 3.4375 px on the first airborne frame, then gravity 0.4375 up to 8 | `walk-off.md` |
| Jumping after leaving a ledge | Where the ground simply ends, a press on the first airborne frame still jumps (take-off on the next frame); no later press does. At a ledge with a face at the top, the last chance is the frame before the player starts down the face | `walk-off.md` |

## Rolling

| Subject | Summary | File |
|---|---|---|
| Starting | An Action press on the ground starts a roll that frame; in the air, a late held press starts one on landing | `roll-input.md` |
| Speed | Target 1 (slow, no direction held), 3 (normal) or 4 (fast, after running recently), same speed rule | `roll-speed.md` |
| Length | 29 frames; about 69 px from standing and 83 px from walking for a normal roll | `roll-input.md`, `roll-speed.md` |
| Extending | Action held throughout carries a normal or slow roll on into running; otherwise a stop of 17 frames follows | `roll-input.md`, `roll-speed.md` |
| Steering | Holding the opposite direction reverses the roll at once | `roll-speed.md` |
| After the roll | Standing after a stop; walking (not running) after carrying on, whatever is held | `ground-speed.md` |
| Jumping out | A jump can start on any roll frame; from take-off the normal targets apply, starting from the roll's speed; no stop follows | `roll-speed.md` |
| Off a ledge | The roll continues through the fall at roll speed and lasts until 29 frames after landing (it ends on landing after a fall of exactly 30 frames); a roll leaving within 8 frames of its start hovers first; a jump can start on any frame of that fall | `rolling-off-ledges.md` |
| Before a ledge | A roll slower than 1.0625 px per frame stops when the ground ends within 15 px ahead | `rolling-off-ledges.md` |
| Into walkers | Defeats them; the roll carries on and speeds up | `rolling-into-walkers.md` |

## Bouncing off enemies

| Subject | Summary | File |
|---|---|---|
| Bounce speed | `vy` −7.125 on the bounce frame, whatever it was before | `bounce.md` |
| Freeze | The player and every other object don't move on the two frames after the bounce | `bounce.md` |
| Height | `bounce_height` 54.5 px without Jump, up to 86.71875 px with Jump held from the third frame after the bounce for 25 frames or more | `bounce.md` |
| Which contacts bounce | Walkers: `walker-contact.md` | `walker-contact.md` |

## Swimming

In underwater levels the player swims instead (`swimming.md`): the same
kind of target rule for `vx` with targets ±2 and one thirty-second of
the gap per frame (from rest to 2 takes 106 frames), facing follows
Left or Right at once, and a Jump press sets `vy` to −2 (−2.5 with Up,
−1 with Down). `vy` then grows by 12/256 per frame up to a sinking
limit of 1.25 (2 while Down is held). A move across that would end 4 px
or more in the ground is a wall; 1 to 3 px is a step up, or a push down
under a ceiling. Rising into a ceiling stops the rise.

## Ropes

| Subject | Summary | File |
|---|---|---|
| Catching | From a jump only, when the feet line is no more than 24 px below the rope's end and the player is within 12 px of it (4 px when moving at 0.25 or more); not the rope just left until out of its reach for a frame | `ropes.md` |
| Climbing | Targets up 1.5, down 2.5 (2.5 and 3.5 with Action), speed moves an eighth of the way per frame; no higher than 41 px below the view's top | `ropes.md` |
| Turning | Holding the direction faced swings round the rope in 8 frames, facing changes on the 5th | `ropes.md` |
| Jumping off | Jump pressed with Left or Right: `vy` −6.125 (more with Up, less with Down), then a jump with air targets ±3 | `ropes.md` |
| The end | With the feet line more than 24 px below the rope's end the player lets go and falls, facing kept | `ropes.md` |

## Second character

Walking 2.25, running 3.375, take-off `vy` −8.3515625, held-jump gravity
0.3515625, and 13 frames of crouch after release. Rolls last 24 frames
with sizes 1.125, 3.375 and 4.5 (a steered slow roll goes toward 3), and
carry on into running until D + 6. Everything else is the same
(`second-character.md`).

## Scenarios with reference traces

| Scenario | Exercises |
|---|---|
| `run-jump-flat` | Walking, rolling into a run, a full-height jump |
| `walk-off-ledge` | A fall after walking off a ledge |
| `roll-flat` | A roll and the stop after it |
| `bounce-walker` | A standing jump onto a walker and a full-height bounce |
| `slope-dip` | Walking down and up 1-in-4 slopes, a jump from a slope |
| `swim-strokes` | Swimming in open water: a plain, an Up and a Down stroke, sinking with and without Down, speeding up, slowing down and turning |

## Not covered

What each file lists under "Not covered", and in particular: every
title other than `title-1`.
