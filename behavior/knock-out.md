# Knocked out (hit with no other character)

Titles: `title-1`, `title-2`

What happens to the player when they take damage while they are the only
character, from the hit until the level ends. Which contacts with a
walker cause damage rather than a bounce is in `walker-contact.md`, and
when a thrower's ball or the thrower does in `ball-contact.md` and
`thrower-contact.md`. What
happens with two characters, and what follows the end of the level, are
not covered here. Frames are logic frames at 60 Hz, numbered as in a
trace. `x`, `y` and `vy` are as in a trace: down is positive, speeds in
px per frame. Measured with a single character touching ground enemies
from the side: walking or running into them, standing while an enemy
walks into the player from in front or from behind, on the frame of a
jump press, on the take-off frame and one frame into a jump, on flat
ground and on a downhill slope.

## The hit frame

Call **H** the first frame on which the player is hurt (the trace row
carrying the `hurt` event).

- On H the player doesn't move: `x` and `y` are those of H − 1, whatever
  the speed was before, on the ground or in the air.
- From H on, all input is ignored until the level ends, and facing
  doesn't change.

## Frozen frames and the pause

From **H to H + 10** (11 frames) the player doesn't move.

Between frames **H + 7** and **H + 8** the whole game pauses for **32
frames** at 60 Hz: nothing moves or changes on screen, and no logic
frames run. A trace has no rows for the pause (like lag frames), so in a
trace H + 8 directly follows H + 7. An engine that runs in real time
shows the screen of H + 7 for 32 extra frames.

## Knock-back flight, from H + 11

On H + 11 the player is launched with `vy` **−8** and a horizontal speed
of **0.78125** (25/32) px per frame **away from the way they face**
(rightward if facing left, leftward if facing right). The direction comes
from facing alone, not from where the enemy is: a player hit from behind
is pushed toward the enemy's side.

On each frame from H + 11, until the final landing:

1. `vy` increases by **0.4375** (7/16), but never above **6**. On H + 11
   that makes it −7.5625. The cap is lower than the 8 of ordinary falls
   (`jump-arc.md`).
2. The player moves vertically by the new `vy` and horizontally by the
   horizontal speed.
3. The landing rule in `landing.md` applies (moving down, and less than
   5 px above the floor or at or below it). If it is met, the player is
   placed on the floor, then:
   - **first landing:** `vy` becomes **−5** and the flight continues on
     the next frame from the floor;
   - **second landing:** `vy` becomes **−2** and the flight continues
     the same way;
   - **third landing:** the player stops. Horizontal speed becomes 0
     (the horizontal move of this frame still happens).

The horizontal speed doesn't change between H + 11 and the third
landing.

## Lying still

Call **F** the frame of the third landing. The player stays where they
are, not moving, on F and the **153** frames after it. **F + 153** is the
last logic frame of the level; then the level ends.

## An object in the hands

A `barrel` or a `holder` (`carrying.md`) still in the player's hands at
the end of H − 1 is let go on H and falls away on its own. This includes
an object carried with Action held and one whose throw (`carrying.md`,
"Throwing") began on H − 1, so that it hadn't left the hands yet; that
throw never happens. Input from H on makes no difference.

- **On H** the object is where it was held at the end of H − 1 (where a
  held object is relative to the player is not covered, see
  `carrying.md`). Its `vx` becomes **1** (256/256) in the direction the
  player faces on H, and its `vy` becomes **−4** (−1024/256, moving
  up).
- **When it starts moving** depends on the kind:
  - a **barrel** doesn't move from H to H + 10, like the player, and
    moves from **H + 11**;
  - a **holder** moves from **H + 1**.
- **Every frame it moves**, `vy` increases by **0.4375** (112/256), but
  never above **8** (2048/256); then `x` moves by `vx` (exactly 1 px)
  and `y` by the new `vy`. Nothing else changes `vx`. Nothing happens
  to it during the 32-frame pause.
- It **touches nothing**: it passes through floors, faces and walls
  without landing, bouncing, rolling or breaking (a holder keeps the
  missing character, `holder-breaking.md`), and it doesn't touch the
  walker or the player.
- It is **removed** on the first frame on which, after the move, its `x`
  is less than 0, or its `y` rounded down is 256 or more below `U`, the
  view's vertical position at the start of the frame, rounded down as in
  `../formats/placements.md` ("When placed objects are removed"). If
  neither happens first, it is gone when the level ends.

This is the same motion as an object let go during an exit's walk-out
(`exits.md`, "An object in the lead's hands"), starting from H instead
of `T` + 1, and for a barrel held still for 10 more frames.

A barrel let go by a player facing left, relative to where it was held
at the end of H − 1 (a holder: the same rows 10 frames earlier):

| Frame | `x` | `y` | `vy` | Note |
|---|---|---|---|---|
| H to H + 10 | 0 | 0 | −4 | not moving yet |
| H + 11 | −1 | −3.5625 | −3.5625 | first move |
| H + 12 | −2 | −6.6875 | −3.125 | |
| H + 19 | −9 | −16.3125 | −0.0625 | highest point |
| H + 20 | −10 | −15.9375 | 0.375 | falling |
| H + 38 | −28 | 65.375 | 8 | at the cap from here |

Mirror image (`x` positive) when facing right.

Measured: 108 knock-outs by a small walker in the first level with the
player alone and carrying an object, each followed until the object was
removed (4 440 object frames): 46 with the barrel (23 facing left and 17
facing right with Action held, standing, walking either way or turning
at the hit; 6 with the throw begun on H − 1) and 62 with a holder (31
facing each way). On every frame from H to the removal, the object's
`x`, `y`, `vx` and `vy` were as the rules above give, to 1/256 px, and
it was removed on the frame they give; the holder kept the missing
character throughout. In those runs the barrel was removed below the
picture 44 to 48 frames after H (the view moving differently) and the
holder 38 frames after H. In 36 more runs that put
the walker on the player during a throw, from the frame after the throw
began to 20 frames after it with the barrel and to 22 with a holder, the
object still in the hands hit the walker and broke
(`holder-breaking.md`), and the player was not hurt.

## In a trace

- State `hurt` on every row from H to the last frame. Event `hurt` on H.
- H to H + 10: `vx` and `vy` 0; `ground` as on H − 1.
- H + 11 until F: `ground` 0; `vx` and `vy` are the speeds applied on
  that frame. The first and second landings are rows with `y` on the
  floor, `ground` 0, no event, and `vy` −5 or −2 (the value set there,
  like the speed shown on a bounce frame in `bounce.md`).
- F: `y` on the floor, `vx` 0, `vy` 0, `ground` 1, event `land`.
- After F: `vx` 0, `vy` 0, `ground` 1, no events.
- No other events (`leave_ground`, `turn`, …) occur from H on.

## Worked values

Hit while walking right on flat ground. `x` and `y` relative to the
position on H − 1:

| Frame | `x` | `y` | `vy` | Note |
|---|---|---|---|---|
| H to H + 10 | 0 | 0 | 0 | 32-frame pause between H + 7 and H + 8 |
| H + 11 | −0.78125 | −7.5625 | −7.5625 | launched |
| H + 12 | −1.5625 | −14.6875 | −7.125 | |
| H + 28 | −14.0625 | −69.1875 | −0.125 | highest point |
| H + 45 | −27.34375 | −7 | 6 | at the cap since H + 42 |
| H + 46 | −28.125 | 0 | −5 | first landing |
| H + 47 | −28.90625 | −4.5625 | −4.5625 | |
| H + 66 | −43.75 | −8.125 | 3.75 | |
| H + 67 | −44.53125 | 0 | −2 | second landing |
| H + 71 | −47.65625 | −3.625 | −0.25 | |
| H + 72 | −48.4375 | 0 | 0 | third landing (F) |
| H + 225 | −48.4375 | 0 | 0 | last frame (F + 153) |

Mirror image when facing left. On flat ground the sequence takes 226
rows from H to the last frame, plus the 32-frame pause; hits in the air
or onto lower ground take longer only by the extra flight time.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character alone (the other character hadn't joined yet), touched by the
walkers of the first level (`walker-contact.md`, "In `title-2`"): while
standing (facing toward the walker and away from it), turning, walking
and running into it, on the frames G and G + 1 of a jump, rising just
after take-off, and on the landing frame, with the walker on the
player's right (in front or behind) and on their left, on flat and on
uneven floor, with many patterns of input from the hit on. Every walker
measured was heading left. Speeds are whole multiples of 1/256 px per
frame, written both ways. **H** is the hit frame, as above. The landing
rule (reaching the floor or going below it lands, with no margin) and
the floor are as in `walk-off.md` ("Falling (`title-2`)") and
`slopes.md` ("In `title-2`"); the gravity of the knock-back is its own.

### The hit frame (`title-2`)

1. On H the player first moves exactly as they would have on that frame
   without the hit: the same horizontal move, and the same vertical move
   or floor height.
2. Then `vx` is set to **−1** (−256/256) and `vy` to **−5.5**
   (−1408/256), and the player faces **right**.

In every measured hit the player was then knocked to the **left** and
faced right, whichever way they faced before and whichever side the
walker touched them from. Since every walker measured was heading left,
whether a walker heading right knocks the player the other way isn't
known.

From H on, all input is ignored until the level ends, and the player
faces right on every frame.

### The pause (`title-2`)

Between **H + 1** and **H + 2** the game runs no logic frames for **6**
frames at 60 Hz (7 in 5 of 104 measured hits). The player doesn't move
during them. A trace has no rows for them, so in a trace H + 2 directly
follows H + 1. An engine that runs in real time shows the screen of
H + 1 for 6 extra frames.

Walkers don't pause in the same way: each walker moves on H, doesn't
move on H + 1, makes one move during the pause, and moves on every frame
from H + 2. So by H + 2 each walker has moved as far as if there had been
no pause and no stop.

### Knock-back flight (`title-2`)

From **H + 1**, on each frame:

1. The player moves horizontally by `vx` (−1).
2. In the air, `vy` increases by **0.234375** (60/256), but never above
   **8**, and the player moves vertically by the new `vy`. On H + 1, if
   the player was on the ground on H, the move starts from the higher of
   their `y` on H and the floor height under their new `x`, as on the
   first airborne frame of a jump (`slopes.md`, "Jumping from a slope
   (`title-2`)"). On flat floor that is their `y` on H.
3. The `title-2` landing rule applies. After landing, the player stays on
   the ground and keeps moving left by 1 px per frame, `y` following the
   floor, until the hop.

Hit standing on flat floor, the player rises 61.8125 px (highest on
H + 23) and lands on **H + 46**, 46 px to the left.

### The hop (`title-2`)

Call **P** the first frame from **H + 48** on which the player is on the
floor: H + 48 itself if they landed earlier, otherwise the frame on which
they land. P starts like any other frame of the flight, and at its end
`vy` is set to **−2.5** (−640/256). From **P + 1**, on each frame:

1. `vx` moves toward 0 by **one eighth** of itself, rounded toward 0 (in
   256ths: 256 becomes 224, then 196, 172, 151, 133, …). When one eighth
   of `vx` rounds to 0, `vx` becomes 0 instead. `vx` is −7/256 on
   P + 31 and 0 from **P + 32** on.
2. The player moves horizontally by the new `vx`, on the ground and in
   the air.
3. Vertically as in the flight (step 2 above, with P in place of H: P + 1
   starts from the higher of their `y` on P and the floor under their new
   `x`). After the hop lands, the player stays on the ground, `y`
   following the floor.

From P + 32 the player doesn't move. On flat floor the hop rises
12.109375 px (highest on P + 10) and lands on **P + 21**, and the player
comes to rest 7.24609375 px left of where they were on P.

### Leaving the level (`title-2`)

The player is in the level up to the **later** of **H + 189** and
**P + 140**, and is gone on the frame after it. Then the level ends: no
more frames of the level run. With an early landing (P = H + 48 or
H + 49), the last frame is H + 189; with P = H + 50 it is H + 190; and
so on.

### In a trace (`title-2`)

- State `hurt` on every row from H to the last frame with the player.
  There is no row for the frame on which the player is gone.
- H: event `hurt`, and `turn` if the player faced left on H − 1. `vx`
  −1 and `vy` −5.5 (the values set there); `ground` 1 if the player is
  on the ground after step 1 of the hit frame, else 0.
- In the air: `ground` 0; `vx` and `vy` are the speeds applied on that
  frame.
- A landing row: `y` on the floor, `ground` 1, event `land`, and `vy` 0,
  unless it is P.
- P: `vy` −2.5 (the value set there, like the speed shown on a bounce
  frame in `bounce.md`), `ground` 1, and event `land` if the player
  lands on P.
- Other rows on the ground: `vy` 0, `ground` 1, `vx` as applied.
- No other events (`leave_ground`, `jump_start`, `turn`, …) occur after
  H.

### Example (`title-2`)

Hit standing on flat floor (P = H + 48). `x` and `y` relative to the
position on H:

| Frame | `x` | `y` | `vx` | `vy` | Note |
|---|---|---|---|---|---|
| H | 0 | 0 | −1 | −5.5 | 6-frame pause between H + 1 and H + 2 |
| H + 1 | −1 | −5.265625 | −1 | −5.265625 | |
| H + 2 | −2 | −10.296875 | −1 | −5.03125 | |
| H + 23 | −23 | −61.8125 | −1 | −0.109375 | highest point |
| H + 46 | −46 | 0 | −1 | 0 | lands |
| H + 47 | −47 | 0 | −1 | 0 | |
| H + 48 | −48 | 0 | −1 | −2.5 | P |
| H + 49 | −48.875 | −2.265625 | −0.875 | −2.265625 | |
| H + 50 | −49.640625 | −4.296875 | −0.765625 | −2.03125 | |
| H + 58 | −53.22265625 | −12.109375 | −0.2734375 | −0.15625 | highest point |
| H + 69 | −54.79296875 | 0 | −0.07421875 | 0 | lands |
| H + 79 | −55.24609375 | 0 | −0.02734375 | 0 | last move |
| H + 80 | −55.24609375 | 0 | 0 | 0 | |
| H + 189 | −55.24609375 | 0 | 0 | 0 | last frame |

### Evidence (`title-2`)

104 hits, each followed until the level ended: 44 at one walker (9
standing, turning and walking cases, 27 around jumps, 1 with the walker
on the player's left, 7 with input held or pressed after H), 42 walking
and running at two walkers on uneven ground later in the level, and 18
in which the player was moved up partway through the flight (by 4 to 96
px, without changing anything else) so that they landed, and P came,
later (P from H + 48 to H + 59). On 19,711 frames from H + 1 to the frame
the player is gone, the flight, the slide, the hop, the landings, facing
and the frame the player leaves match this section with 0 mismatches,
including 2 frames at the cap of 8. For 86 of the hits, the same run was
repeated with the walkers moved out of the way: the position on H was
the same as in that run every time. Input after H (directions held,
jump, Action, Down and Up, held or tapped) changed nothing.

### Not covered in `title-2`

- Walkers heading right, other kinds of enemies, and hits during a roll,
  a bounce or while carrying an object.
- Being hit with two characters (losing one, and the change of lead
  character), and the second character.
- Still being in the air on H + 48 was measured only with landings on the
  floor below the hit (no walls, ledges or pits met in the flight).
- Protection after a hit: none applies, since this hit ends the level.
- What happens after the level ends.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Which contacts with other kinds of enemies cause damage (walkers:
  `walker-contact.md`).
- Being hit with two characters (losing one, and the change of lead
  character): see `losing-a-character.md`.
- Protection after a hit: none applies here, since this hit ends the
  level. Protection after a hit with two characters is in
  `protection.md`.
- Walls, ledges, pits and slopes met during the knock-back flight
  (measured on a slope only where it didn't change the motion, with the
  landings on the slope's floor height).
- Hits during a roll or a bounce. Falling out of the level is in
  `falling-out.md`.
- Objects other than a `barrel` or a `holder` in the hands at the hit,
  hits during a throw other than on the frame after it began, and how
  walkers and other objects move from H to H + 10.
- What happens after the last frame: the screen transition and any count
  of remaining tries. Where play resumes after a restart point has been
  reached is in `restart-barrel.md`; otherwise, where play begins.
