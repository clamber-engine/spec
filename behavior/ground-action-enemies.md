# Ground action meeting enemies

Titles: `title-1`

What happens when the first character's Down + Action **ground action**
(`ground-action.md`) meets an enemy: on which frames of the action and at
which offsets a walker is knocked out, the pause that follows and how it
lengthens the action, what happens to the walker, when the player can be
hurt during the action, the view on those frames, and the thrower. How
the action starts, moves and ends without enemies is in
`ground-action.md`; which contacts hurt a player outside the action is in
`walker-contact.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx`
and `vy` are as in a trace: right and down are positive, in px and px per
frame. This spec uses the names of `ground-action.md`: **P** is the
action's press frame and **E** the first frame after it. **K** is a frame
on which something is knocked out. The offset **I** is as in
`walker-contact.md`: the player's `x` minus the walker's `x`, both rounded
down to whole pixels. The player's **facing** is as in a trace; during
the action it doesn't change and can differ from the action's direction
(`ground-action.md`, "Direction").

Measured with the first character alone, on flat floor, on the two kinds
of walker of `walker-contact.md` and on the thrower (`thrower.md`). See
"Evidence".

## Action frames

Number the frames of the action **t** = 0, 1, 2, … from P, **without
counting pause frames** (below). The action lasts from t = 0 to t = 58,
as in `ground-action.md`; E is the first frame after the frame with
t = 58. With no pause, E = P + 59. Each pause adds 2 frames:
E = P + 59 + 2 × (number of pauses during the action). The speed rule of
`ground-action.md` applies on every frame that has a t, and a Jump press
ends the action as described there.

## Attack frames

The frames with **t = 17, t = 25 and t = 31** are the action's **attack
frames**. They are the same for direction −1, 0 and +1, and whichever
way the player faces. Things are knocked out only on attack frames.

With no pause, the attack frames are P + 17, P + 25 and P + 31. A pause
moves the later ones 2 frames later: after a knock-out on P + 17, they
are P + 27 and P + 33.

## When a walker is knocked out

On an attack frame K, a walker walking at full speed (1 px per frame,
`walker.md`) on the same floor or slope as the player is knocked out on K
when, with both positions **at the end of K** (after both have moved on
K), I is within the knock-out range:

| Walker | Player facing | Walker heading left | Walker heading right |
|---|---|---|---|
| small | right | −62 to 10 | −63 to 9 |
| small | left | −9 to 63 | −10 to 62 |
| large | right | −65 to 13 | −66 to 12 |
| large | left | −12 to 66 | −13 to 65 |

Both ends are included. The ranges facing left are those facing right
mirrored (I becomes −I and the two headings swap). The large walker's
ranges are the small walker's widened by 3 px on each side, as for its
hurt range (`walker-contact.md`). The range reaches far ahead of the
player (where it faces) and only a little behind.

The ranges are the same on all three attack frames and for every
direction, including direction 0: a player who doesn't move knocks out a
walker that walks into the range. A walker knocked out is never also
hurting the player on that frame.

## Being hurt during the action

The hurt rule of `walker-contact.md` applies before, during and after
the action: the player is hurt on frame H when the conditions hold at the
end of H − 1, using the hurt ranges of a player on the ground (standing,
walking or running) in `walker-contact.md`. During the action this
means:

- **Before it starts.** If the conditions hold at the end of P − 1, the
  player is hurt on P and no action starts.
- **On frames that aren't attack frames,** a walker hurts the player as
  usual.
- **On an attack frame,** a walker within the knock-out range at the end
  of the frame is knocked out instead, unless another walker is knocked
  out on that frame or was knocked out earlier in the action ("Two
  things in reach"); it then hurts as on other frames. A walker within
  the hurt range but outside the knock-out range at the end of an attack
  frame hurts the player on the next frame (for example a small walker
  heading left with the player facing right, I from 11 to 24), unless
  that frame is a pause frame (below).
- **After a pause** that starts on K, the player is not hurt on the
  pause frames K + 1 and K + 2. On K + 3 the usual rule applies again:
  the player is hurt on K + 3 when the conditions hold at the end of
  K + 2, which, since nothing moved on the pause frames, are the
  positions at the end of K. A walker that is in the hurt range at the
  end of K and isn't knocked out (for example one beside the walker
  that is) therefore hurts the player on K + 3.

## The pause

When anything is knocked out on K (a walker, the thrower, or a hidden
object struck as in `hidden-objects.md`), **K + 1** and **K + 2** are **pause
frames**:

- The player's `x` and `vx` stay as they were at the end of K. The
  player stays in the action, and t doesn't advance.
- The knocked-out walker doesn't move, and neither do other walkers.
- On K + 3, t is one more than on K, and the speed rule of
  `ground-action.md` continues from the `vx` of K.

### Two things in reach

One action knocks out **at most one walker**:

- **Two walkers in range on the same attack frame.** Only one of them is
  knocked out, and there is one pause. Which one doesn't depend on where
  they are: for a given pair of walkers it was always the same one,
  whatever their offsets, including when the other one was nearer to the
  player. Which of two walkers it is isn't specified.
- **After a walker has been knocked out,** the rest of that action's
  attack frames knock out nothing and strike no hidden object, even
  when a walker or a hidden object is in range on them, and there is no
  further pause. Another walker is not kept from hurting the player: it
  hurts as on frames that aren't attack frames ("Being hurt during the
  action"), on K + 3 if it was in the hurt range at the end of K.
- **A new action** starts afresh: a walker that comes into range on an
  attack frame of the next action is knocked out as usual.

Striking a hidden object (`hidden-objects.md`) doesn't use up the
action in this way:

- **A walker and a hidden object in range on the same attack frame**
  (t = 25 or t = 31): the walker is knocked out and the hidden object
  isn't struck. There is one pause.
- **A hidden object struck first** doesn't stop a walker: a walker in
  range on a later attack frame is knocked out as usual. The two make
  two pauses (E = P + 63).

For example, with the player facing right and direction 0, two small
walkers heading left that are at I = −30 and I = −55 at the end of
t = 17 make one pause: one of them is knocked out on t = 17 and E is
P + 61. The other one is not knocked out on t = 25 or t = 31; it hurts
the player when it reaches the hurt range.

## The view

On K and on K + 1, the view does nothing, horizontally (`view.md`) and
vertically (`view-vertical.md`): the target, the position and the
easing state keep their values from the frame before, as on the frame of
a roll defeat and the frame after it. The view continues from K + 2.

## The walker

From K, a knocked-out walker moves as a walker bounced on (`walker.md`,
"Being bounced on") with B = K, except that it also doesn't move on any
later pause frame:

- on K it moves horizontally by its `vx` as usual but not vertically,
  and its `vy` becomes −6;
- on K + 1 and K + 2 it doesn't move;
- from K + 3, on each frame that isn't a pause frame, its `vy` increases
  by 7/16, up to 8, and it moves by its `vy` and by the `vx` it had on K.

It doesn't land: it falls through the floor and is removed when it
leaves the window, as in `walker-view.md`, "Defeated walkers falling
out", with the first falling frame K + 3. It is there on K, K + 1 and
K + 2 wherever it is. From K + 3 it is removed on the first frame on
which, after that frame's move, it is outside the window. On flat floor
with the view standing still, a small walker is there for the last time
on K + 41, 95 px below the floor it walked on.

From K on, a knocked-out walker doesn't touch the player. It never hurts
the player, the player never bounces on it, and rolling into it does
nothing, on any frame and in any pose, even while it overlaps the player.

## The thrower

The thrower (`thrower.md`) is knocked out too. With the player standing
on the thrower's ledge facing it, with direction 0, and I (the player's
`x` minus the thrower's, rounded down) from −51 (the left end of the
ledge) to −37, the thrower was knocked out on the action's first attack
frame (t = 17) every time, with the same pause. Closer than that the
thrower hurt the player before the action started.

From K the thrower moves like a knocked-out walker but with `vy` −5 and
`vx` 0: it doesn't move on K, K + 1 and K + 2, then from K + 3 its `vy`
increases by 7/16 per frame and it moves by it.
What it does after that (no contact, its removal) is in
`thrower-contact.md`, "The knocked-out thrower".

## Example

Synthetic, from the rules above. The player stands facing right at `x`
100 and presses Down and Action on frame 0 with no direction held
(P = 0, direction 0). A small walker heads left at full speed on the same
floor and is at `x` 180.25 at the end of frame −1. "Walker `y`" is
relative to its floor (up is negative).

| Frame | t | Player `x` | Walker `x` | Walker `vy` | Walker `y` | State | Notes |
|---|---|---|---|---|---|---|---|
| 0 | 0 | 100 | 179.25 | 0 | 0 | `ground_action` | P |
| 16 | 16 | 100 | 163.25 | 0 | 0 | `ground_action` | I = −63, not an attack frame |
| 17 | 17 | 100 | 162.25 | −6 | 0 | `ground_action` | attack frame, I = −62: K |
| 18 | 17 | 100 | 162.25 | −6 | 0 | `ground_action` | pause |
| 19 | 17 | 100 | 162.25 | −6 | 0 | `ground_action` | pause |
| 20 | 18 | 100 | 161.25 | −5.5625 | −5.5625 | `ground_action` | K + 3 |
| 21 | 19 | 100 | 160.25 | −5.125 | −10.6875 | `ground_action` | |
| 27 | 25 | 100 | 154.25 | −2.5 | −32.25 | `ground_action` | attack frame |
| 33 | 31 | 100 | 148.25 | 0.125 | −38.0625 | `ground_action` | attack frame |
| 60 | 58 | 100 | | | | `ground_action` | last frame of the action |
| 61 | | 100 | | | | `idle` | E = P + 61 |

Without the walker, the action would have ended with E on frame 59.

## In a trace

- Pause frames have state `ground_action` and repeat `x` and `vx` of K.
- The action has 59 frames of state `ground_action` plus 2 for each
  pause; E maps as in `ground-action.md`.
- No event in the trace vocabulary marks a knock-out.

## Evidence

- **Placement.** The player was put at chosen offsets from a walker, one
  pixel apart, on every frame from P − 1 to P + 66 (small walker) or
  P + 41 (large walker), for each facing and each direction (−1, 0, +1),
  with the walker heading left and right: 180 336 placements on the small
  walker (4 212 knock-outs, 34 860 hurts) and 65 092 on the large walker
  (4 226 knock-outs, 24 540 hurts), all with the same attack frames and
  ranges in every combination, and no gaps. The large walker was
  measured on a flat stretch 68 px long: facing left with the walker
  heading left, offsets above 59 couldn't be placed, and the far end of
  66 in the table follows the 3 px widening that every other end shows.
- **By inputs alone.** 399 runs (133 937 frames) at three places (the
  small walker heading left and heading right, the large walker heading
  left on a slope and a flat stretch), with the press on many frames,
  direction −1, 0 and +1, facing either way, and a second action after
  the first: 397 actions, 156 knock-outs (118 on t = 17, 24 on t = 25,
  14 on t = 31), 410 pause frames, 186 hurts and 6 283 frames of
  knocked-out walkers. On every frame, the knock-out frames, the pause,
  the action's length, `x` and `vx` during the action, the hurts and the
  walkers' motion matched the rules above, with 0 mismatches. In all 156
  knock-outs the view did nothing on K and K + 1 and moved again on
  K + 2.
- **Hurts after a pause.** In the placements below, a walker that had
  moved on K by itself and was in the hurt range at the end of K hurt
  the player on K + 3: 23 048 placements with another walker knocked out
  on K, and 714 with a hidden object struck on K; none hurt on K + 1 or
  K + 2. A walker moved into place by placement hurts no earlier than 2
  frames after the frame it was moved on, pause or not (32 hurts in 36
  placements with no pause, all 2 frames after; 355 placements at the
  end of K to K + 4 of a walker knock-out's pause, earliest hurts K + 4,
  K + 4, K + 4, K + 5, K + 6). So the earlier placements in the hurt
  range at the end of K, K + 1 or K + 2 of pauses from a hidden object
  (243 placements), which never hurt the player before K + 4, didn't
  show the hurt on K + 3.
- **Two things in reach.** Two small walkers heading left on a flat
  stretch, the player facing right: both walkers put at every pair of
  whole-pixel offsets from −70 to 16 at the end of t = 16, t = 24 and
  t = 30 with direction 0 (three pairs of walkers, both ways round), and
  at the end of t = 16 with direction +1 and −1 (offsets −50 to 30 for
  −1); one walker put at −90 to −62 (in range only later) with the other
  at −70 to 16; and the second walker in range in the next action:
  59 892 placements, each compared with the same walker alone (1 392
  single-walker placements); 57 939 were checked (the others had a hurt
  before any knock-out). In 36 178 both walkers were in range on the
  same frame; in 3 584 one of them came into range on a later attack
  frame of the action after the other was knocked out, and in 2 263 in
  the next action. With a hidden object (`hidden-bunch`) at the level's
  start: the player at
  52 places from 0 to 66 px left of it, the walker at offsets −90 to 16,
  placed at the end of t = 16, t = 24 and t = 30 (16 692 placements,
  16 314 checked; 7 854 with both in range on the same frame, 3 723 with
  the walker first, 612 with the hidden object first). Every checked
  placement matched the rules of "Two things in reach", the knock-out
  frames, the pause frames and E, with 0 mismatches.
- **Knocked-out walkers.** A small walker knocked out after the press
  on 8 different frames, and a large walker after 12. From every frame
  K + 1 to K + 41, with 3 input sequences (walking, running, rolling,
  jumping, nothing held), the knocked-out walker was put on the player
  before every frame so that it ended each frame on the player's
  position: 2 460 runs and 51 660 frames, the walker there on 51 633 of
  them (within 1 px across and 8 px up or down on 42 161), with the
  player on the ground on 25 433 frames (rolling on 28) and in the air
  on 26 200. On every frame the player's state, position and speeds were
  the same as with the walker left where it was. As a control, a walking walker was
  put on the player in the same way from the same frames, with the
  same inputs, in runs without the action. It hurt the player on the
  next frame in 1 980 runs, and it was rolled into on the first frame
  in 420. The other 60 runs had only one frame.
- **Removal.** 56 knock-outs by inputs alone (32 small, 24 large walkers;
  2 198 falling frames), all removed at the bottom edge on the frame the
  rule gives. A small walker on flat floor was there for the last
  time on K + 41 in all 32. Controlled: the walker put at chosen places
  at the end of K to K + 3, K + 10, K + 20 and K + 30, with `X` − `V`
  (as in `walker-view.md`) from −200 to 300 and in 1 px steps across the
  side edges, and `y` in 1/16 px steps across the bottom edge, small and
  large: 855 frames, 0 mismatches. Put outside the window at the end of
  K, K + 1 or K + 2, it was still there on K + 2 and was removed on K + 3.
- **The thrower:** 28 placements on its ledge, one pixel apart (15
  knock-outs, 13 hurts before the action).

## Not covered

- Which of two walkers in range on the same attack frame is knocked out.
  Two things in reach with the player facing left, with the large or
  medium walker, with walkers heading right, and with the thrower; two
  hidden objects in reach of one action (`hidden-objects.md`).
- Walkers that are turning, speeding up, stopped or frozen, walkers on a
  different floor from the player, and the action on slopes or at a
  ledge (`ground-action.md`).
- Inputs on the pause frames (a Jump press, for example).
- Knocked-out walkers on later pause frames (another knock-out during
  their fall): whether they can be removed then. None happened in the
  runs measured. Knocked-out walkers and the second character.
- The thrower: other offsets, the player facing away, whether it can be
  knocked out from below its ledge, what happens to its throws, and
  balls (`thrower.md`).
- Other kinds of enemies, and objects such as carried or thrown objects.
- Other things the action strikes. Hidden objects that release a bunch
  are in `hidden-objects.md`.
- How a pause affects the view's shake (`view-vertical.md`).
- The other character, when present, during the pause.
