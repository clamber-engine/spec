# Carrying objects

Titles: `title-1`

Picking up an object, carrying it, throwing it and putting it down: what
the player does on each frame, and how a thrown object moves until it
first touches something. Frames are logic frames at 60 Hz, numbered as in
a trace. `x`, `y`, `vx`, `vy` and `facing` are as in a trace: right and
down are positive, speeds in px per frame. "Left", "Right", "Up", "Down",
"Jump", "Action" are the neutral inputs `L`, `R`, `U`, `D`, `J`, `A` of
the scenario format. Measured with the first character leading, mostly
on flat ground, with two different objects that can be carried, both
resting on the floor at the start. The second character's timing and
speeds are in "The second character".

Speeds are whole multiples of 1/256 px per frame, and this spec writes
them both ways (768/256 = 3 px per frame). **The speed rule** below means
the rule of `ground-speed.md` (gap to the target, step = gap ÷ 8 rounded
toward zero, the target itself once the step is 0, then move by the new
speed). It is the same rule in the air (`air-control.md`).

**Holding** an object needs Action held the whole time. The player holds
it from the first frame of a pick-up until the frame Action is let go.

Frame names used here:

- **G**: the first frame of a pick-up.
- **Q**: the first frame on which Action is not held while the player
  holds an object (during a pick-up or after it) (with one exception: during a
  pick-up, Down held on that frame moves Q to a later frame, see "During
  the pick-up").
- **P**: the press frame of a jump, as in `jump-input.md`.

## Picking up

A pick-up starts on a frame **f** when all of these hold:

- Action is held on f (pressed on f or held from before);
- the player ended f − 1 on the ground (`ground` 1 in its trace row),
  not holding an object and not knocked out (`knock-out.md`);
- the player was **within reach** of the object at the end of f − 1
  (see "Reach").

So, in practice:

- **Moving into the object with Action held** (running into it, sliding
  into it while slowing down with Action still held, rolling into it),
  the pick-up starts on the frame **after** the first frame that ends
  with the player within reach. This is so from either side.
- **Pressing Action while standing within reach**, the pick-up starts on
  the press frame, and **no roll starts**. (A press out of reach
  starts a roll as usual, `roll-input.md`; if the roll then reaches the
  object with Action still held, the roll ends and the pick-up starts as
  above.)
- **Landing** within reach with Action held, the pick-up starts on the
  frame after the landing frame. A jump's take-off frame still ends with
  `ground` 1, so a player who takes off within reach with Action held
  picks the object up on the next frame, in the air; jumping over the
  object from further away doesn't.

Moving into the object without Action held does nothing: the player walks
past it.

### Reach

Reach is decided from the positions at the end of the frame before (f −
1), in whole pixels: `x` rounded down and `y` rounded up, for the player
and for the object. With **X** = the object's `x` minus the player's
`x` and **E** = the object's `y` minus the player's `y`, the player is
within reach when X is in the range for the player's facing and E is in
its range (both ends included):

| Object | X, facing right | X, facing left | E |
|---|---|---|---|
| `barrel`, and `holder` looking still | −7 to 38 | −33 to 12 | −22 to 32 |
| `holder` shaking | −10 to 43 | −36 to 17 | −21 to 31 |

(`barrel` and `holder` are the kinds of `formats/placements.md`: an
object that holds nothing and one that holds the missing character.)
E is positive when the object is lower than the player.

**Facing**: the player's facing on f itself (its trace row), except on
the frame on which a turn changes facing (`ground-speed.md`, "Facing and
turns": the fifth frame of the turn), which still uses the facing of
f − 1. For example, Action pressed during the first four frames of a
turn, out of reach, starts a roll facing the old way (`roll-input.md`);
on the next frame the player faces the held direction, and that facing
decides reach.

**The holder's look**: a `holder` lying at its place in the level shakes in a cycle
of 40 frames. With **A** the frame it appears (`formats/placements.md`),
it looks still on the frames where (frame − A) mod 40 is 9 to 13 or 34
to 38, and shakes on the others. When still, it looks the same as a `barrel`
(the same picture). Reach uses its look at the end of f − 1.

Measured on the first level with the lead: about 590 000 controlled
frames, in which the object was moved to every X from −50 to 50 and every
E from −30 to 40 next to a player standing still, just before a frame
with Action pressed: a `barrel` and the `holder`, both facings, the
`holder` at each of the 40 frames of its cycle; every frame gave the
ranges above. The `holder`'s look was logged on every frame for 130
frames from its appearance, for 8 different appearance frames. Then 6 000
runs of random input (standing, walking, running, rolls, turns and jumps,
with Action held or not; 1 359 547 frames) from 6 starts: each object
at its place in the level with the player on either side, and each
object put down. All 4 782 pick-ups started on the frame predicted by the
rule above, and none started on any other frame. The same check with the
player's facing taken from f − 1 on every frame, or from f on every
frame, misses pick-ups; with either X range made 1 px narrower or wider
on one side, it misses dozens.

### During the pick-up

The pick-up lasts **15 frames**, G to G + 14. Starting on the ground:

- **G to G + 9**: the target speed is 0, whatever is held, and the speed
  rule applies. A player running into the object at 3 px per frame slows
  down over these frames.
- **G + 10 to G + 14**: the target follows the input again, as in
  `ground-speed.md` with Action held: ±3 (±768/256) with a direction,
  0 without.
- Facing doesn't change. If the direction opposite to facing is held, a
  turn (`ground-speed.md`, "Facing and turns") starts on G + 15.
- A Jump press doesn't jump, and holding Jump on doesn't make a jump
  later (measured with Jump pressed on G + 2 and held for 20 frames).
- Letting go of Action on any of G + 1 to G + 14 is a throw with Q on that
  frame ("Throwing"), unless Down is held on that frame. With Down held,
  nothing changes on that frame: the pick-up goes on as above, and the
  throw starts, with Q, on the first later frame on which Down isn't held
  (the first frame of carrying, G + 15, still counts as the pick-up for
  this). If Down is held on every frame up to and including G + 15, there
  is no throw: the pick-up ends as usual and the object is put down from
  G + 16 ("Putting down", with Q = G + 16).

From G + 15 the player is carrying.

## Carrying

- **Speed**: the speed rule with the targets of `ground-speed.md` with
  Action held: ±3 (±768/256) with a direction, 0 with none or both.
- **Down and Up** have no effect: the player doesn't crouch and the
  target doesn't change.
- **Turns** are as in `ground-speed.md` (8 frames, facing changes on the
  fifth). A turn starts on the frame the direction opposite to facing is
  held without the other one, whether the player is standing or moving:
  there are no turn checks while carrying (`player-frames.md`,
  "Carrying").
- **No rolls**: a roll needs an Action press, and Action is held.
- **Jumps**: a Jump press on P lifts off one frame later than without an
  object. The take-off frame (the row with `jump_start`: `vy` −7, `ground`
  still 1, `y` unchanged) is **P + 2**. Everything else is as for any
  jump: gravity per `jump-arc.md` (the lighter gravity needs Jump held on
  every frame from P), horizontal speed per `air-control.md`, landing per
  `landing.md`. The player keeps holding the object through the jump and
  after landing.
- **Ground passable from below** (`../formats/level-ground.md`) doesn't
  touch a carried object: when the player rises into a platform from
  below (`platforms-from-below.md`), the object stays in the hands and
  in the same place relative to the player as on any other frame of a
  jump. Where the player is stopped by ground that isn't passable from
  below, the object is stopped with it and stays in the hands. Measured
  in `title-1` with a carried `barrel`: 230 runs of walking and jumping
  under a platform, on the first level and on a copy with every surface
  made not passable from below, 160 frames each (36,800 frames per
  copy). The barrel stayed in the hands on every frame of both copies,
  including the 698 frames with the player inside the platform, and was
  always at the place relative to the player that the same jump frame
  has elsewhere.
- Letting go of Action on **P + 1 or P + 2** cancels the jump: the player
  doesn't take off, and that frame is Q of a throw from the ground. Let go
  on P + 3 or later, the throw happens in the air.

## Throwing

Letting go of Action (frame Q) throws the object, unless the player is on
the ground and Down is held on Q (see "Putting down"). Up makes no
difference.

### The player

The throw lasts **43 frames**, Q to Q + 42:

- **Q to Q + 21**: the target speed is 0 whatever is held, on the ground
  and in the air, and the speed rule applies.
- **Q + 22 to Q + 42**: the target follows the input again (`ground-speed.md`
  on the ground, `air-control.md` in the air; Action is no longer held, so
  ±2 with a direction).
- On the ground, facing doesn't change before Q + 43, even if the
  opposite direction is held from Q on. If it is held on Q + 43, a turn
  starts on that frame.
- In the air, vertical motion is not affected: the jump goes on exactly as
  in `jump-arc.md` and lands as in `landing.md`. Landing doesn't end the
  throw; its remaining frames are on the ground.

Jump and Action during the throw:

| Input | Result |
|---|---|
| Jump pressed on Q + 22 to Q + 42 | A jump as in `jump-input.md` (take-off on the next frame). The throw ends. |
| Jump pressed before Q + 22 | Nothing, now or later. |
| Action pressed on Q + 22 to Q + 42 | A roll starts on the press frame. The throw ends. |
| Action pressed on Q + 15 to Q + 21 and still held on Q + 22 | A roll starts on Q + 22. |
| Action pressed on Q + 15 to Q + 21 and let go before Q + 22 | Nothing. |
| Action pressed on Q + 1 to Q + 14 | Nothing, even if held through Q + 22. |

From Q + 43 the player is free as usual.

### The object

The object leaves the player's hands on **Q + 21**. On that frame:

- its `x` is the player's `x` on Q + 21 with the fraction dropped;
- its `y` is 54 px above the player's `y` on Q + 21, rounded to a whole
  pixel toward the floor if the player's `y` has a fraction (in the air);
- its `vx` is **16** (4096/256) in the facing direction, and its `vy` is
  **+1** (moving down);
- it hasn't moved yet.

Nothing else changes these: not the player's speed, the input on Q, Up,
or whether the player is in the air.

From Q + 22 on, each frame, until the object first touches something:

1. `vx` follows the speed rule toward a target of **3.5** (896/256) in the
   direction it was thrown.
2. `vy` increases by **0.4375** (112/256), but never above **8**.
3. The object moves by the new `vx` and `vy`.

What happens when it touches the floor, a wall or a character depends
on the object: see `thrown-objects.md` and "Not covered".

## Putting down

If Down is held on Q while the player is on the ground, the object is put
down instead of thrown. Down can be pressed on Q or held from earlier. In
the air, Down on Q makes no difference (a throw). During a pick-up, Down
held on the frame where Action is let go delays the throw, or turns it into
a put-down, as in "During the pick-up".

- **Q to Q + 13**: the target speed is 0 whatever is held, and the speed
  rule applies.
- **From Q + 14** the player is free as usual (Down still held is a
  crouch, as in `ground-speed.md`).
- **The object**: on Q + 13 it is 16 px in front of the player (in the
  facing direction) from the player's `x` on Q + 13 with the fraction
  dropped, and 3 px above the floor. From Q + 14 it rests on the floor
  there and doesn't move.
- **Picking it up again**: an Action press within reach on Q + 14 or later
  picks it up on the press frame. An Action press on Q + 2 to Q + 13 that
  is still held on Q + 14 picks it up on Q + 14.

Measured on flat floor only.

## The object in the hands

During a pick-up, a throw and a put-down on the ground, standing on flat
floor, the object's position on each frame is fixed relative to the
player's: **ahead** is how many pixels the object's `x` is in front of
the player's `x` (in the facing direction; negative behind), both with
the fraction dropped, and **up** how many pixels the object's `y` is
above the player's, both rounded up. Both are taken at the end of the
frame. Walkers use this position (`thrown-objects.md`, "During a
pick-up, a throw or a put-down").

| Frame | Pick-up (G + n) ahead, up | Throw (Q + n) ahead, up | Put-down (Q + n) ahead, up |
|---|---|---|---|
| n = 0 | where it lies | 2, 46 | −2, 39 |
| 1, 2 | 20, 3 | −6, 47 | 4, 38 |
| 3, 4 | 20, 15 | −18, 48 | 20, 33 |
| 5, 6 | 20, 27 | −30, 44 | 20, 27 |
| 7, 8 | 20, 33 | −40, 39 | 20, 15 |
| 9, 10 | 4, 38 | −38, 39 | 20, 3 |
| 11, 12 | −2, 39 | −36, 38 | 16, 3 |
| 13, 14 | −2, 39 | −32, 39 | 16, 3 on n = 13; on the floor from 14 |
| 15, 16 | carried | −28, 41 | |
| 17, 18 | | −24, 45 | |
| 19, 20 | | −7, 54 | |
| 21 | | 0, 54 | |

So in a throw the object goes back over the player's head and is
brought forward again, and on Q + 21 it is at the position "The object"
above gives. In a pick-up it rises in front of the player and is brought
back to above its head; a put-down is the same in reverse. Where the
object is carried before Q and from G + 15 depends on the player's
pose and isn't covered.

Measured in the runs by inputs of `thrown-objects.md` ("During a
pick-up, a throw or a put-down"), facing either way, on every frame up
to the first hit or hurt of each run: 13 to 96 runs for each frame and
facing, all with the values above. The pick-ups were of an object lying
where a put-down leaves it (16 px in front of the player).

## Worked examples

All values are computed from the rules above.

**A thrown object**, thrown facing right; `x` and `y` from its position
on Q + 21, speeds in 1/256 px per frame:

| Frame | `x` | `y` | `vx` | `vy` |
|---|---|---|---|---|
| Q + 21 | 0 | 0 | 4096 | 256 |
| Q + 22 | 14.4375 | 1.4375 | 3696 | 368 |
| Q + 23 | 27.5078125 | 3.3125 | 3346 | 480 |
| Q + 24 | 39.3828125 | 5.625 | 3040 | 592 |
| Q + 25 | 50.2109375 | 8.375 | 2772 | 704 |
| Q + 30 | 92.71875 | 28.6875 | 1860 | 1264 |
| Q + 35 | 123.0859375 | 59.9375 | 1393 | 1824 |
| Q + 36 | 128.28515625 | 67.5 | 1331 | 1936 |
| Q + 37 | 133.2734375 | 75.5 | 1277 | 2048 |

From Q + 37, `vy` stays at 8 px per frame.

**A throw while running**: carrying at full speed to the right
(`vx` 768/256), Action let go on Q, Right held throughout; `x` from the
player's position on the frame before Q:

| Frame | `vx` (1/256) | `x` |
|---|---|---|
| Q | 672 | 2.625 |
| Q + 1 | 588 | 4.921875 |
| Q + 10 | 179 | 16.21875 |
| Q + 21 | 43 | 20.046875 |
| Q + 22 | 101 | 20.44140625 |
| Q + 23 | 152 | 21.03515625 |
| Q + 42 | 480 | 49.7890625 |
| Q + 43 | 484 | 51.6796875 |

On Q + 22 the target becomes walking speed (+512/256): Action is no
longer held.

**A pick-up at full running speed**, Right and Action held; `x` from the
player's position on the frame before G:

| Frame | `vx` (1/256) | `x` |
|---|---|---|
| G | 672 | 2.625 |
| G + 1 | 588 | 4.921875 |
| G + 9 | 204 | 15.51953125 |
| G + 10 | 274 | 16.58984375 |
| G + 14 | 477 | 22.984375 |
| G + 15 | 513 | 24.98828125 |

## The second character

With the second character under control (`second-character.md`), a
pick-up, carrying, a throw and a put-down go as above with these
differences. G, Q and P are as above; "the speed rule" and its targets
are as above, with the second character's values where they differ.

| | First character | Second character |
|---|---|---|
| Pick-up frames | G to G + 14 | **G to G + 9** |
| Target 0 from G | to G + 9 | **to G + 10** |
| Carrying from | G + 15 | **G + 10** (standing on G + 10, whatever is held) |
| Target while carrying, with a direction | ±3 (768/256) | **±3.375** (864/256), on the ground and in the air |
| Throw frames | Q to Q + 42 | **Q to Q + 39** |
| Throw: target 0 | Q to Q + 21 | **Q to Q + 18** |
| Throw: target follows the input | Q + 22 to Q + 42 | **Q + 19 to Q + 39** (±2.25, 576/256, with a direction) |
| Free again after a throw | Q + 43 | **Q + 40** |
| Put-down frames, target 0 | Q to Q + 13 | **Q to Q + 9** |
| Free again after a put-down | Q + 14 | **Q + 10** |

- **Letting go of Action during the pick-up** is as for the first
  character, with the second character's frames: a throw on the frame
  itself on G + 1 to G + 9 and on G + 10, and with Down held on that frame
  the throw waits for the first frame without Down (G + 10 counts as the
  pick-up for this); with Down held on every frame up to and including
  G + 10, the object is put down from G + 11. The throw's timing after Q
  is the table's.
- **G + 10** is a frame of standing with the object, and the target is
  still 0 on it whatever is held. From G + 11 the target follows the
  input, and a held direction starts moving or a turn as from any frame
  of standing (`player-frames.md`, "Carrying").
- **Jumps while carrying** take off on P + 2 as above, with the **first
  character's** jump values, not the second character's: `vy` −7 on the
  take-off frame, gravity 0.28125 (72/256) while Jump is held since the
  press and 0.4375 (112/256) otherwise, at most 8. The horizontal target
  in the air is ±3.375 (864/256) with a direction.
- **Down and Up** have no effect while carrying, as above.
- During a throw and a put-down facing doesn't change, even if the
  other direction is held. After a throw, Q + 40 is a frame of standing,
  of walking (the facing direction held) or the first frame of a turn
  (the other one held), as after walking. After a put-down, Q + 10 is a
  frame of standing whatever is held.

Measured in `title-1` on flat floor with a `barrel`, the first character
following: 240 runs of random input (pick-ups at the end of a roll,
running into the object and standing next to it; then standing, moving,
turns, jumps, Down, each with Action held, ending in a throw or a
put-down) and 180 runs of slow drifts with the object, 284 028 frames in
all. On every frame `vx` matched the speed rule with the targets above:
5 401 pick-up frames (to G + 10), 10 760 throw frames, 1 510 put-down
frames, 207 051 frames of carrying on the ground and 7 360 frames in the
air with a direction held. In the air `vy` matched the jump values above
on all 16 383 frames. With the zero target ending one frame earlier or
later, 14 to 62 frames differ. Of 466 Jump presses made while carrying on
the ground outside a landing's frames, all took off on P + 2. Facing
never changed during a throw or a put-down, also with the other
direction held.

### Reach and letting go of Action, second character

**Reach** for a `barrel` is the same for the second character as for the
first, in the same terms ("Reach"): X from −7 to 38 facing right and −33
to 12 facing left, E from −22 to 32, with the same facing rule. The pick-up
starts on the same frames as for the first character ("Picking up"): a
press while standing within reach starts it on the press frame without a
roll, and moving into the object with Action held starts it on the frame
after the first frame that ends within reach.

Letting go of Action during the pick-up is in the list above ("Letting go
of Action during the pick-up"); a direction held does not change it.

Measured in `title-1` with the second character leading, from a start
prepared with a setup method (an object on the opening floor), for a
`barrel` only (the `holder` is not covered here, below). The player stood
still, facing each way, at every X from −50 to 50 and every E from −40 to
30 from the object, just before a frame with Action pressed: every
position gave the ranges above. Then 600 runs of
random input (standing, walking, running, rolls, turns and jumps, with
Action held or not; 214 528 frames), both facings at the start: all 366
pick-ups started on the frame the rule predicts, and no other frame
started one. For letting go of Action, runs with Action let go on each of
G + 1 to G + 17 and Down held on 0, 1, 2, 3, 5, 8 or 99 frames from that
frame, or on every frame from G + 1, with no direction or either one held
(408 runs for each character): in all of them the throw or put-down
started on the frame the rule gives, and in the throws the object left the
hands on the frame the throw's timing gives (Q + 21 for the first
character and Q + 18 for the second).

### The object, second character

- **Leaving the hands: Q + 18.** On that frame the object's `x` is the
  player's `x` with the fraction dropped, its `y` is **9 px** above the
  player's `y` rounded up to a whole pixel (the rounding toward the
  floor that the first character's 54 px also uses; `y` grows downward):
  on the ground that is 9 px above `y`, and in the air a player at `y`
  100.25 releases the object at `y` 92 (101 - 9), 8.75 px above the
  exact `y`, while at `y` 100 it is 91. Its `vx` is 16 (4096/256) in the facing direction and its `vy` is +1
  (moving down), and it hasn't moved yet. It is thrown in the direction
  the player faces on Q. Nothing else changes these: not the player's
  speed, Up, Left or Right pressed on Q, or whether the player is in the
  air.
- **Flight.** From Q + 19 the object moves by the first character's
  rules ("The object" above: the speed rule toward 3.5 in the throw
  direction, `vy` + 0.4375 up to 8, then the move), and the worked
  example "A thrown object" applies with Q + 18 in place of Q + 21. It
  starts only 9 px above the player's `y`, so on flat floor it touches
  the floor within a few frames; touching, bouncing and rolling are in
  `thrown-objects.md`.

**In the hands.** On the ground, with the player's `y` the same as on
the two frames before (so not on a slope, in a take-off or a landing),
ahead and up are as defined in "The object in the hands" (at the end of
the frame; a negative up is below the player's `y`). At the start of a
pick-up the object lay from 7 px behind to 13 px in front of the player
and 0 or 3 px above its `y`; the table starts one frame later:

| Frame | Pick-up (G + n) ahead, up | Throw (Q + n) ahead, up | Put-down (Q + n) ahead, up |
|---|---|---|---|
| n = 0 | where it lies (above) | 8, 11 | 12, 8 |
| 1 | 12, −8 | 4, 13 | 12, 8 |
| 2 | 16, −7 | 4, 13 | 14, 3 |
| 3 | 16, −7 | −7, 11 | 14, 3 |
| 4 | 16, −1 | −7, 11 | 16, −1 |
| 5 | 16, −1 | −7, 11 | 16, −1 |
| 6 | 14, 3 | −16, 9 | 16, −7 |
| 7 | 14, 3 | −16, 9 | 16, −7 |
| 8 | 12, 8 | −16, 9 | 12, 3 |
| 9 | 12, 8 | −16, 9 | 12, 3 |
| 10 | carried | −16, 9 | on the floor there |
| 11 to 13 | | −15, 9 | |
| 14, 15 | | −12, 9 | |
| 16 to 18 | | 0, 9 | |

(On Q + 18 the object is released at 0, 9.) On the ground rows where
the player's `y` did change, `ahead` was the same and `up` was the
same or 1 px more, never less. So the object goes up in
front of the player and over its head in a pick-up and comes back in a
throw, as for the first character, but much lower: it is never more
than 13 px above the player's `y`, and below it for part of the
pick-up and put-down. In a put-down the object is 12 px in front of the
player and 3 px above the floor on Q + 9 and rests there from Q + 10.

Throws, pick-ups and put-downs of "The object, second character" were
measured in `title-1` with the second character leading, by inputs
alone, from a start prepared with a setup method (an object ahead on the
opening floor): 300 runs of a pick-up, random carrying (standing,
walking either way, jumps) and a throw or a put-down with Up, Left,
Right or Down held on Q or nothing. In 245 throws the object left the
hands on Q + 18 every time, at the player's `x` with the fraction
dropped and 9 px above the player's `y` on the ground (178 throws); in the
air (67) it was 9 px above the player's `y` rounded up to a whole
pixel (for example `y` 100.25 gave 92), thrown in the
facing direction on Q every time. Over the flights, `vx` followed the
speed rule toward 3.5 on all 5 674 frames; 831 frames before the first
touch matched the air rules in position and both speeds, and at all 188
first touches that were bounces `vy` became minus half the fall speed,
rounded down. The positions in the hands held in all rows on the ground with the
player's height unchanged for three frames, except the first frame of
one put-down (object 12 px behind the player): 300 pick-ups, 108 to 178
throws for each of Q to Q + 18 and 44 to 46 put-downs for each frame;
the other ground rows (172 of the throw rows) had the same `ahead` and
an `up` of 1 px more in some.
The same runs with the first character leading (74 throws) gave
Q + 21 and 54 px every time and the first character's table above.

## Not covered

- Reach for other objects that can be carried, for an object that is
  moving (thrown, bouncing or rolling), and with two objects within reach
  at once. The `holder`'s look while it is carried and after it is put
  down, and for a `holder` already there on the level's first frame.
- Picking up with Down or Up held, while being hit with both characters
  there, and Action held during a throw or a put-down with another
  object within reach.
- Where the object is while it is carried (before Q and from G + 15;
  during a pick-up, a throw and a put-down on flat ground, see "The
  object in the hands"; on slopes and in the air it wasn't measured),
  where it is drawn or how it collides with anything while it is held,
  and carrying into walls or enemies (walkers meeting the object during
  a pick-up, a throw or a put-down are in `thrown-objects.md`, "During a
  pick-up, a throw or a put-down"; when a walker knocks out a
  player alone who carries an object, what the object does is in
  `knock-out.md`, "An object in the hands"; a carried object meeting
  the thrower or a ball is in `thrown-objects.md`, "Hitting the
  thrower" and "Hitting a ball", and one meeting a large walker in "A
  carried object").
- What a thrown object does when it first touches a character, beyond
  `thrown-objects.md`, "Meeting a character" (an object that holds
  nothing passes through both characters). Bouncing, rolling and
  breaking against walls for an object that holds nothing are in
  `thrown-objects.md`; when an object that holds the missing
  character breaks, also while carried or during a throw, is in
  `holder-breaking.md`. When some objects break
  the game was seen to stop the player for about half a second; this
  isn't specified.
- Putting down on slopes or near walls (on a slope the object was seen to
  end up elsewhere than the rule above), and putting down in the air.
- Jump or Action on Q + 13 of a put-down.
- With the second character, the first frame of a put-down: in one
  put-down the object was 12 px behind the player on that frame instead
  of the table's value. What set that put-down apart wasn't found, so
  the table has this one exception.
- Facing during a throw in the air, and throwing or putting down during
  a turn.
- Pressing Action within reach while moving (only standing presses and
  moving with Action already held were measured).
- How a trace's `state` names frames of a pick-up, carrying, a throw or a
  put-down (the vocabulary has no names for them yet).
- With the second character under control: the reach for a `holder`
  (it holds the missing character, so none lies on the level while both
  are there), pressing Action within reach while moving, Down or Up held
  while the pick-up starts, Jump and Action during a throw, picking a put-down
  object up again, where the object is while carried and in the hands
  on slopes and in the air, a pick-up of an object lying elsewhere than
  where a put-down leaves it, and everything above that was measured
  only with the first character (slopes, walls, platforms).
- The other character.
