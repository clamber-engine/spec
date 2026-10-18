# Carrying objects

Titles: `title-1`

Picking up an object, carrying it, throwing it and putting it down: what
the player does on each frame, and how a thrown object moves until it
first touches something. Frames are logic frames at 60 Hz, numbered as in
a trace. `x`, `y`, `vx`, `vy` and `facing` are as in a trace: right and
down are positive, speeds in px per frame. "Left", "Right", "Up", "Down",
"Jump", "Action" are the neutral inputs `L`, `R`, `U`, `D`, `J`, `A` of
the scenario format. Measured with the lead character, mostly on flat
ground, with two different objects that can be carried, both resting on
the floor at the start.

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
  holds an object (during a pick-up or after it).
- **P**: the press frame of a jump, as in `jump-input.md`.

## Picking up

A pick-up starts in either of these cases:

- **Moving into the object with Action held**: running into it, sliding
  into it while slowing down with Action still held, or rolling into it.
  Approaching from the left, the pick-up starts on the frame **after** the
  first frame that ends with the player within reach of the object.
- **Pressing Action while standing within reach**: the pick-up starts on
  the press frame, and **no roll starts**. (A press out of reach
  starts a roll as usual, `roll-input.md`; if the roll then reaches the
  object with Action still held, the roll ends and the pick-up starts as
  above.)

Moving into the object without Action held does nothing: the player walks
past it.

Reach depends on the sizes of the player and the object, which this spec
doesn't define (see "Not covered").

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
  frame ("Throwing"), whether or not Down is held.

From G + 15 the player is carrying.

## Carrying

- **Speed**: the speed rule with the targets of `ground-speed.md` with
  Action held: ±3 (±768/256) with a direction, 0 with none or both.
- **Down and Up** have no effect: the player doesn't crouch and the
  target doesn't change.
- **Turns** are as in `ground-speed.md` (8 frames, facing changes on the
  fifth).
- **No rolls**: a roll needs an Action press, and Action is held.
- **Jumps**: a Jump press on P lifts off one frame later than without an
  object. The take-off frame (the row with `jump_start`: `vy` −7, `ground`
  still 1, `y` unchanged) is **P + 2**. Everything else is as for any
  jump: gravity per `jump-arc.md` (the lighter gravity needs Jump held on
  every frame from P), horizontal speed per `air-control.md`, landing per
  `landing.md`. The player keeps holding the object through the jump and
  after landing.
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
makes no difference either (a throw).

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

## Not covered

- **Reach**: how close the player must be to an object to pick it up, and
  the sizes of objects. The two objects measured were reached at
  different distances. Approaching from the right, the pick-up was seen to
  start one to three frames after the player came within reach.
- Picking up in the air (seen to happen just after a take-off close to
  the object, but not when jumping over it), and picking up during other
  actions (turning, crouching, landing, being hit).
- Where the object is drawn or how it collides with anything while it is
  held, and carrying into walls or enemies (when a walker knocks out a
  player alone who carries an object, what the object does is in
  `knock-out.md`, "An object in the hands").
- What a thrown object does when it first touches a character. Bouncing,
  rolling and breaking against walls for an object that holds nothing
  are in `thrown-objects.md`; when an object that holds the missing
  character breaks, also while carried or during a throw, is in
  `holder-breaking.md`. When some objects break
  the game was seen to stop the player for about half a second; this
  isn't specified.
- Putting down on slopes or near walls (on a slope the object was seen to
  end up elsewhere than the rule above), and putting down in the air.
- Jump or Action on Q + 13 of a put-down.
- Facing during a throw in the air, and throwing or putting down during
  a turn.
- Pressing Action within reach while moving (only standing presses and
  moving with Action already held were measured).
- How a trace's `state` names frames of a pick-up, carrying, a throw or a
  put-down (the vocabulary has no names for them yet).
- The other character.
