# The animal crate

Titles: `title-1`

An **animal crate** stands on the floor. When the player lands on it
from above, it breaks and the player bounces off it, as off an enemy.
Shortly after, an **animal** comes out where the crate stood and waits
there. When the player lands on the animal from a jump or a bounce, the
player mounts it, and after a short pause rides it. This file covers
breaking the crate, the animal coming out, and mounting. Riding is not
covered yet.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward. The player's **feet line** `F` is as in `view-vertical.md`
(the player's `y` rounded up). "Moving down" on a frame means that the
player's `vy` used for that frame's move is greater than 0. "Right",
"Left" and "Jump" are the neutral inputs `R`, `L`, `J` of the scenario
format.

Measured on the first level's crate with the first character alone, by
inputs only except where said:

- Ranges: the player was put at every whole-pixel offset around the
  crate, and around the animal, one pixel apart, in the air, facing
  either way, and run for one frame (about 113 000 placements). Around
  the crate: falling in a jump (also with the downward speed forced to
  values from 0.125 to 8 px per frame), rising in a jump, and in the
  walking, standing and rolling poses with a downward speed. Around the
  animal: the same, and falling after a bounce, and with the player at
  half-pixel positions.
- The ranges were then checked on 400 runs of random inputs (walking,
  running, jumping, rolling, crouching) around the crate, starting on
  either side of it, 134 205 frames: in every run the crate broke on the
  frame the rules give, the animal came out 18 frames later, and the
  player mounted on the frame the rules give, or not at all when they
  give no frame (400 breaks, all from jumps, 205 facing right; 399
  mounts, 261 from jumps and 138 from the bounce off the crate, 194
  facing right). For each frame of these runs the rules were applied to
  where the player would have been without the crate or the animal (the
  same frame run again with the object moved away).

## The crate

A level's placement list (`../formats/placements.md`) places the crate
with an entry of kind `animal-crate` ("Kinds" there). The crate does
not move. Its **position** (`xc`, `yc`) is the entry's level x, and the
entry's level y plus 1 (see "Positions" there). In the first level the
crate stands on the floor: `yc` is the level y of the floor under it.

Walking, running or rolling through the crate does nothing: the player
keeps their position, speed and action.

## Breaking the crate

Using whole pixels, let

- **`I`** = the player's `x` rounded down, minus `xc`;
- **`D`** = `yc` − `F`: how many px the player's feet line is above
  the crate's position.

The player breaks the crate on frame `n` when, **at the end of frame
`n`**, after the player has moved on that frame:

1. the player is in the air and was moving down on `n`;
2. the player is not rolling;
3. `I` is within the range for the player's facing on `n`, and `D` is
   within 5 to 36 (both ends included):

| Facing | `I` |
|---|---|
| right | −28 to 40 |
| left | −42 to 26 |

Facing left, the range is the facing-right range turned around and
moved 2 px to the left: from −(high end) − 2 to −(low end) − 2.

The ranges were the same in the jump pose at every downward speed
measured, and in the walking and standing poses with a downward speed.
A player rising in a jump never broke the crate, and neither did a
player rolling in the air.

On the frame `n` the crate breaks, the player **bounces** off it:
`n` is the bounce frame **B** of `bounce.md`, and everything there
applies (the move on B, `vy` set to −7.125, the two frozen frames, the
gravity depending on Jump held from B + 3). The trace row for B carries
the `bounce` event. The crate breaks only once.

On B the crate is drawn broken, and a flash and a puff of smoke appear
near it. The broken crate stays where it was and does nothing.

## The animal comes out

On frame **B + 18** the animal appears at `x` = `xc`. For a few frames
it hops: its level y is `yc` minus the height below.

| Frame | Height above `yc` (px) |
|---|---|
| B + 18 | 1.5625 |
| B + 19 | 2.6875 |
| B + 20 | 3.375 |
| B + 21 | 3.625 |
| B + 22 on | 0 |

From B + 22 on, the animal stands at (`xc`, `yc`). It does not move
until it is mounted. It stayed there in a run of 900 frames with no
input. Walking through it does nothing.

## Mounting the animal

For a frame `n`, take the position the player would have at the end of
`n` if the animal weren't there (the player's usual move for that
frame), and with it

- **`I`** = that `x` rounded down, minus `xc`;
- **`D`** = `yc` − the feet line of that position.

The player mounts the animal on frame `n` (from B + 22 on) when:

1. the player was moving down on `n`;
2. the player is in the air after a jump or after a bounce (`bounce.md`,
   including the bounce off the crate), and not rolling;
3. `I` is within the range for the player's facing on `n`, and `D` is
   within 5 to 45 (both ends included):

| Facing | `I` |
|---|---|
| right | −27 to 19 |
| left | −26 to 20 |

The left range is the right one moved 1 px to the right; it is not the
right one turned around.

A player rising in a jump never mounted. A player put in the air in the
walking pose (as after walking off a ledge) or rolling never mounted,
whatever the position.

Call the frame the player mounts **M**.

- **On M**, the player first moves as usual, horizontally too (the
  frame's air control, `air-control.md`, changes `vx` as on any airborne
  frame). Then the player is moved by whole pixels so that `x` rounded
  down is `xc` and the feet line `F` is `yc`: the fractions of `x` and
  `y` are kept. `vy` becomes 0. `vx` keeps its value from the move.
- **M + 1 to M + 21**: the player doesn't move; `x`, `y` and `vx` stay as
  they were at the end of M. Inputs have no effect: Jump pressed on
  M + 5 did not start a jump, and Left held from M + 10 changed nothing
  before M + 23.
- **M + 22**: the player still doesn't move.
- **From M + 23** the player rides the animal: `y` is `yc` (the
  fraction is dropped) and the player is on the ground. Riding is not
  covered.

## Example

A crate at `xc` = 1000, `yc` = 400, on a floor at level y 400. The
player jumped while walking left and is falling, facing left, with
`vx` −2, Left held, Jump not held. The positions are those of a
measured break, moved to this crate.

| Frame | `x` | `y` | `vy` | `I` | `D` | What happens |
|---|---|---|---|---|---|---|
| 0 | 1018.5 | 353.4375 | 5.34375 | 18 | 46 | |
| 1 | 1016.5 | 359.21875 | 5.78125 | 16 | 40 | |
| 2 | 1014.5 | 365.4375 | −7.125 | 14 | 34 | The crate breaks; `bounce` (the move used `vy` 6.21875) |
| 3 | 1014.5 | 365.4375 | −7.125 | | | Frozen (`bounce.md`) |
| 4 | 1014.5 | 365.4375 | −7.125 | | | Frozen |
| 5 | 1012.5 | 358.75 | −6.6875 | | | |
| 20 | | | | | | The animal appears at `x` 1000, `y` 398.4375 |
| 24 | | | | | | The animal stands at (1000, 400) |

Mounting: later, the player falls toward the animal facing right, with
`vx` 2 and Right held. A frame ends with the player at `x` = 988.25,
`y` = 353.5, `vy` 5.8125 (`F` = 354, `D` = 46: too high). On the next
frame `vy` becomes 6.25 and the player would end at `x` = 990.25,
`y` = 359.75 (`I` = −10, `F` = 360, `D` = 40): the player mounts on
that frame and is put at `x` = 1000.25, `y` = 399.75 (`F` = 400), with
`vy` 0 and `vx` 2. The player stays there for 22 more frames; riding
begins on the 23rd frame after the mount, with `y` = 400.

## Not covered

- Riding: everything from M + 23 on (moving, turning, jumping, what the
  animal does to enemies, getting off or being knocked off).
- Other levels, other crates, and the kind of animal: the first level's
  crate holds one animal; how a crate's animal is chosen, and other
  animals, are not covered.
- Mounting while the animal hops (B + 18 to B + 21); it did not happen
  in the runs.
- A crate not standing on the floor, and an animal coming out above the
  floor.
- The second character: whether a following character breaks the crate
  or mounts the animal.
- Breaking the crate while bouncing off an enemy, carrying an object,
  being hit, swapping, or during the Down + Action ground action; and
  objects thrown at the crate or the animal.
- What happens to the animal when it leaves the view and comes back. The
  crate is removed and comes back as in `../formats/placements.md`
  ("When placed objects are removed").
- How the flash, the smoke and the broken crate are drawn, where they
  are, and when they are removed; the sounds.
- The trace state of the player while mounting.
