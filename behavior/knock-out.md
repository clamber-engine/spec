# Knocked out (hit with no other character)

Titles: `title-1`

What happens to the player when they take damage while they are the only
character, from the hit until the level ends. Which contacts cause damage
rather than a bounce (`bounce.md`), what happens with two characters, and
what follows the end of the level are not covered here. Frames are logic
frames at 60 Hz, numbered as in a trace. `x`, `y` and `vy` are as in a
trace: down is positive, speeds in px per frame. Measured with a single
character touching ground enemies from the side: walking or running into
them, standing while an enemy walks into the player from in front or from
behind, on the frame of a jump press, on the take-off frame and one frame
into a jump, on flat ground and on a downhill slope.

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

## Not covered

- Which contacts cause damage, and what happens to the enemy.
- Being hit with two characters (losing one, and the change of lead
  character): see `losing-a-character.md`.
- Any period of protection after a hit.
- Walls, ledges, pits and slopes met during the knock-back flight
  (measured on a slope only where it didn't change the motion, with the
  landings on the slope's floor height).
- Hits during a roll or a bounce, and other causes of losing a life
  (falling out of the level).
- What happens after the last frame: the screen transition, where play
  resumes, and any count of remaining tries.
