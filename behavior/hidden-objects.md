# Hidden objects

Titles: `title-1`

A **hidden object** is placed in a level like any other object but isn't
drawn. The player walks, jumps and stands through it, and it never hurts.
It is **set off** in one of two ways, depending on its kind: by the
ground action (`ground-action.md`), or by the player coming down onto it
after a long fall. When it is set off, it releases an **item**. The item
flies out on a short arc, lands, and becomes a bunch (`bunches.md`) or a
barrel. This file covers when each kind is set off, what happens on that
frame, and how the item moves until it becomes a bunch or a barrel.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward. The player's **feet line** `F` is as in `view-vertical.md`
(the player's `y` rounded up). `vy` is as in a trace: down is positive,
in px per frame. **P**, **t**, **attack frames** and **pause frames** are
as in `ground-action-enemies.md`. **B** is a bounce frame as in
`bounce.md`.

Measured on the first level with the first character alone, by inputs
except where said:

- **Being struck by the ground action.** The player was put at every
  whole-pixel offset around a hidden object, one pixel apart, at the end
  of each frame from P + 15 to P + 34 (offsets −80 to 80), facing either
  way, with direction −1, 0 and +1 (19 320 placements). For the vertical
  range, the object was moved up and down instead.
- **Being landed on.** The player was put above the object at chosen
  heights and offsets, facing either way, and let fall. To reach the
  lower end of the vertical range, the player was moved sideways onto the
  object partway down a fall. The object was raised above its floor for
  the rest of the vertical range and for the height rule.
- **Items.** Their paths were logged with the object at its own height
  and raised 1 to 52 px above the floor, in 1/4 px steps near the
  landing limit.
- **Every reference.** Each of the 20 first-level placement references
  that had no kind row was given, in a copy of the image, to one entry
  near the level's start in place of its own reference. Each was then
  tried with the ground action and with a long fall onto it. Only the
  three references of the kinds below released an item.
- **Random inputs.** All the rules were then checked against random
  inputs. Near two hidden objects of the ground-action kind there were
  320 runs (160 000 frames) of standing, walking, running, turning and
  ground-action presses with every direction. Over one of the falling
  kind there were 840 falls (100 800 frames) from random heights and
  offsets, with random steering. See "Evidence" for the counts. On every
  frame there was no mismatch with the rules below.

## The hidden object

A level's placement list (`../formats/placements.md`) places a hidden
object with an entry of one of these kinds ("Kinds" there):

| Kind | Set off by | Item | Item direction `d` |
|---|---|---|---|
| `hidden-bunch` | the ground action ("Struck by the ground action") | a bunch | +1 (right) |
| `hidden-barrel` | a long fall ("Landed on after a long fall") | a barrel | −1 (left) |
| `hidden-metal-barrel` | a long fall ("Landed on after a long fall") | a metal barrel | +1 (right) |

A hidden object does not move. Its **position** (`xo`, `yo`) is its
entry's level x and level y (see "Positions" there). It isn't drawn, it
doesn't stop or hold up the player, and it never hurts. The ground action
doesn't strike the falling kinds, and landing on a `hidden-bunch` does
nothing.

Until it is set off, a hidden object leaves and comes back with the view
like other placed objects (`../formats/placements.md`). Once it is set
off, it doesn't come back for the rest of the visit to the level. Its
item doesn't come back either once it has been removed (see "The
item").

## Offsets

Using whole pixels, let

- **`I`** = the player's `x` rounded down, minus `xo`;
- **`D`** = `yo` − `F`: how many px the player's feet line is above the
  object's position (negative when it is below).

Unlike in `bunches.md`, `I` is not measured in the facing direction: the
ranges below give each facing separately.

## Struck by the ground action

A `hidden-bunch` is **struck** on frame **K** when all of these hold:

1. K is an attack frame of a ground action with **t = 25 or t = 31**.
   The first attack frame, t = 17, never strikes a hidden object.
2. At the end of K, `I` is within the range for the player's facing on
   K (ends included):

   | Player facing | `I` |
   |---|---|
   | right | −53 to 0 |
   | left | 0 to 53 |

3. At the end of K, `D` is from **−52 to 8**: the object's position is
   at most 52 px above the feet line and at most 8 px below it.

The ranges are the same on both attack frames, for direction −1, 0 and
+1, and whichever way the player faces. A player facing right strikes
an object from 53 px in front of it (to its right) up to its own `x`, and
none behind it. A moving action strikes one that is in range on t = 25
or t = 31.

A `hidden-bunch` in range is not struck on an attack frame on which a
walker is knocked out, nor after a walker has been knocked out earlier
in the same action (`ground-action-enemies.md`, "Two things in reach").

K counts as a frame on which something is knocked out, in the sense of
`ground-action-enemies.md` ("The pause"). K + 1 and K + 2 are pause
frames, the action lasts 2 frames longer, and the player's `x` and `vx`
stay as they were at the end of K on the pause frames.

## Landed on after a long fall

For a player in the air, let **`Fmin`** be the smallest feet line the
player has had at the end of any frame since it last stood on a floor.

A `hidden-barrel` or `hidden-metal-barrel` is **landed on** on frame B
when all of these hold at the end of B, after the player has moved on B:

1. the player is in the air: B isn't a landing frame (`landing.md`);
2. the player isn't rolling (a roll in the air passes through the object);
3. `yo` − `Fmin` is at least **160**: at some point since leaving the
   floor, the player's feet line was at least 160 px above the object's
   position;
4. `I` is within the range for the player's facing on B (ends included):

   | Player facing | `I` |
   |---|---|
   | right | −28 to 42 |
   | left | −42 to 28 |

5. `D` is from **−27 to 20**.

`I`'s range doesn't depend on `D`, and neither range depends on how
fast the player falls.

Rule 3 is what keeps an ordinary jump or walk-off from setting the
object off: a player who walks over it, or comes down onto it from less
than 160 px above, passes through it. Only feet lines at the end of
frames count. A player put at rest with its feet line exactly 160 px
above the object and let go is less than 160 px above it by the end of
the first frame, and passes through.

B is a bounce frame: the player bounces as in `bounce.md`, from B. It
moves on B as on any other airborne frame, then `vy` becomes −7.125. It
doesn't move on B + 1 and B + 2. From B + 3 the bounce's gravity rule
applies.

## The item

On the frame **K** a hidden object is set off (K = B for the falling
kinds), the object is gone and its item appears at (`xo`, `yo`). The
item has horizontal speed `d` (the kind's direction, ±1 px per frame)
and `vy` −6.

- On K, K + 1 and K + 2 the item stays at (`xo`, `yo`).
- From K + 3, on each frame that isn't a pause frame, until it lands:
  1. `vy` increases by **0.25**, but never above **6**;
  2. the item moves by `d` horizontally and by `vy` vertically;
  3. if `vy` is positive (moving down) and the item is now less than
     **5 px** above the floor under it, it is put on that floor. This
     frame is the item's **landing frame L**.
- On L + 1 the item moves `d` horizontally once more and stays on the
  floor.
- On L + 2 it becomes, where it stands, a bunch or a barrel ("What the
  item becomes").

On a pause frame from another strike while the item is in the air, it
doesn't move.

The arc doesn't depend on where the player is or which way it faces.
From an object whose position is at the floor's level y, the item lands
exactly on the floor on its 47th move (`vy` 5.75), on frame
L = K + 49. On L + 2 = K + 51 it stands 48 px from `xo` in direction
`d`. From an object 1 px below the floor's level y, it comes within 5 px
of the floor one move earlier and stands 47 px from `xo`. From an object
above the floor, it lands later and further away; once `vy` reaches 6 it
keeps falling 6 px per frame.

An item that leaves the window while in the air is removed and doesn't
come back (seen 4 times in the random runs). When exactly an item is
removed isn't covered.

### What the item becomes

- **A bunch** (`hidden-bunch`). Its position (`xb`, `yb`) in
  `bunches.md` is 1 px left of where the item stands, at the floor's
  level y: `xb` = item's `x` − 1, `yb` = item's level y. From L + 2 it
  is taken, and releases its items, as `bunches.md` says. For an item
  that stands 48 px right of the object, `xb` = `xo` + 47. A player
  walking right took such a bunch on the first frame ending with
  `X − xb` = −15, the end of the standing range.
- **A barrel** (`hidden-barrel`). An object of kind `barrel`
  (`../formats/placements.md`), standing on the floor where the item
  stands. It was picked up, carried and thrown as in `carrying.md`, and
  bounced, rolled and broke as in `thrown-objects.md`.
- **A metal barrel** (`hidden-metal-barrel`). An object that can be
  picked up and carried as in `carrying.md`, standing where the item
  stands. How it moves when thrown and what it does when it meets
  things aren't covered.

A bunch or barrel that hasn't been taken or picked up is removed when it
leaves the window, and doesn't come back.

## Example

Synthetic, from the rules above. A `hidden-bunch` is at (130, 300). The
player stands facing right at `x` 100.5 on the floor at level y 300
(`F` = 300). It presses Down and Action on frame 0 with no direction
held (P = 0, direction 0). On frame 17 (t = 17), `I` = 100 − 130 = −30
and `D` = 0, but t = 17 never strikes. Frame 25 is the next attack
frame.

| Frame | t | Player `x` | Item `x` | Item `y` | Item `vy` | Notes |
|---|---|---|---|---|---|---|
| 25 | 25 | 100.5 | 130 | 300 | −6 | K: struck (`I` = −30, `D` = 0) |
| 26 | 25 | 100.5 | 130 | 300 | −6 | pause |
| 27 | 25 | 100.5 | 130 | 300 | −6 | pause |
| 28 | 26 | 100.5 | 131 | 294.25 | −5.75 | first move |
| 29 | 27 | 100.5 | 132 | 288.75 | −5.5 | |
| 51 | 49 | 100.5 | 154 | 231 | 0 | top of the arc |
| 73 | | 100.5 | 176 | 294.25 | 5.5 | 5.75 px above the floor: not yet |
| 74 | | 100.5 | 177 | 300 | 5.75 | L: 294.25 + 5.75 = 300, on the floor |
| 75 | | 100.5 | 178 | 300 | | L + 1: one more step right |
| 76 | | 100.5 | | | | L + 2: a bunch with `xb` = 177, `yb` = 300 |

The action ends one pause later than without the object: frame 60 is
its last frame (t = 58) and E = 61.

## In a trace

- The frames of a ground action that strikes a hidden object are as in
  `ground-action-enemies.md` ("In a trace"), with the pause.
- A landing on a falling kind is a bounce: B carries the `bounce` event
  and the player's state is `bounce` from B until it lands, as in
  `bounce.md`.
- Hidden objects and items have no rows of their own in the trace
  vocabulary.

## Evidence

- **Ground action, by placement.** All 19 320 placements fit the ranges
  above exactly. Strikes came only on t = 25 and t = 31, with the same
  ranges for direction −1, 0 and +1 and both facings. The vertical
  range was found with the object moved 1 px at a time: struck from
  8 px below the feet line to 52 px above it, not at 9 below or 53
  above, on both attack frames and both facings.
- **Ground action, by inputs.** 320 runs and 160 000 frames near two
  hidden objects of kind `hidden-bunch`. The checks covered 1 020 ground
  actions and 241 strikes. They also covered 482 pause frames and
  48 189 frames of items, 231 of which became bunches. In 7 cases (14
  frames), an item in the air was stopped by a later strike's pause.
  662 ground actions ended on the frame the pause rule gives; the other
  358 left the flat stretch of floor before ending and weren't checked
  for length. Struck objects never came back, and unstruck ones came
  back 200 times after leaving the view. Each strike, each item
  position and each action length matched the rules. The walkers in
  that stretch were removed every frame.
- **Falls, by placement.** The height rule: started at rest 160 px above
  the object, the player passed through. Started from 160.5 px or higher
  above it, the player bounced. The vertical range was found with the
  player starting at heights 1 px apart, and moved onto the object
  partway down for the lower end. The player bounced with `D` from −27
  to 20 and not at −28 or 21. The horizontal range was the same at
  `D` = −27, 13 and 20.
- **Falls, by inputs.** 840 falls (100 800 frames) of the player put
  above a `hidden-metal-barrel`, from 60 to 320 px above it and up to
  90 px to either side. Each started at rest or moving sideways at up to
  2 px per frame, with random Left, Right, Action and no input. In some,
  the player first came down on a floor 177 px above the object to one
  side and left it from there (11 of the bounces). The checks covered
  212 bounces and 16 034 frames of items, 207 of which became metal
  barrels. The bounce frame, the player's `vy` −7.125 on B, the two
  frozen frames and every item frame matched the rules in every fall.
  Airborne rolls were within the ranges on 28 frames and passed through
  the object every time.
- **Items.** With the object raised 1 to 52 px above its floor, the
  item came down to the floor below. It was put on the floor at 4.75 px
  above it, and not at 5 px, which landed one move later.

## Not covered

- Two hidden objects struck on the same attack frame (one pause or two).
- The ground action on slopes or at a ledge, and other things that might
  strike a hidden object (a roll, a thrown object, the other character,
  an animal).
- A player rising, or bouncing again before landing, when it meets a
  falling kind. Coming down onto one while riding an animal.
- Items whose arc meets a wall, a slope, a step or a pit. Only flat
  floor under the arc was measured.
- When exactly items and the objects they become are removed after
  leaving the view.
- Whether the player can take a bunch item, or touch a barrel item,
  while it is in the air.
- The view on K and K + 1 after a strike (for walkers it does nothing,
  `ground-action-enemies.md`).
- What the metal barrel does once thrown.
- The sounds played.
- Hidden objects in other levels, and kinds holding other things.
