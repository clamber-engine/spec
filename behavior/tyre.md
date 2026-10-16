# Tyres

Titles: `title-1`

A tyre is an object the player can stand on that squashes and then
throws the player up. This file says when a tyre catches the player, what
happens while the player stands on it, the throw, and the strong jump a
player can make from a squashed tyre. Frames are logic frames at 60 Hz,
numbered as in a trace. `x` grows to the right; `y` and `vy` are as in a
trace (down is positive, `vy` in px per frame).

Measured with the first character alone, on a tyre standing on flat
ground and on one placed 50 px above it, with random walking, running,
rolling and jumping on, onto, under and past it (about 140 000 frames,
1 150 catches, 470 throws), and with scripted inputs one frame apart for
the limits. Three placement references place a tyre
(`../formats/placements.md`, kind `tyre`); all three behaved the same.

## The tyre

A tyre stays where it is placed. It doesn't fall, even when placed in the
air, and it doesn't move.

Contact is decided from whole-pixel positions, as in `walker-contact.md`:
round each position down (`x` toward the left, the vertical position
toward the floor) and let

- **I** = the player's `x` minus the tyre's `x`;
- **U** = how many pixels the player's position is above the tyre's
  position (0 when the player stands on the floor the tyre was placed
  on; positive when the player is higher).

The tyre has a **top**, **T** pixels above its position. T is 16 while
the tyre is at rest. It is lower on some frames while the tyre is
squashed and while it wobbles after a throw (tables below).

The player **touches** the tyre on a frame when, with the player's
facing at the end of the frame, both hold:

1. I is from **−24 to 22** when facing right, or from **−26 to 20** when
   facing left (both ends included);
2. U is from **T − 67 to T + 2** (both ends included).

A player standing on the top has U = T + 2.

## Order within a frame

On every frame the player first moves by the usual rules (walking,
running, rolling, jumping, falling, landing on the floor). Then the tyre
acts, using the player's position after that move. Everything below
about "after the player's move" means this.

## Being caught

A tyre that isn't squashed and isn't in the 8 frames after a throw is
**ready**. On a frame **C** on which the tyre is ready, the player is
caught if, after the player's move:

1. the player touches the tyre, with T the tyre's top at the end of
   C − 1; and
2. either
   - the player is on the ground and C isn't the take-off frame of a
     jump, or
   - the player is in the air, moving down (`vy` greater than 0), and U
     is at least **T − 13** (the player is less than 16 px below the
     standing height).

A player in the air with `vy` 0 or less (rising, or at the top of an
arc) is never caught.

When caught, the player is moved straight up or down so that the whole
pixels of U are T + 2; the fraction of a pixel is kept. `x` and `vx`
don't change. The player is now on the ground (`ground` 1), on the top.

So a player walking or running into the side of a tyre is lifted onto
its top at once, from any depth at which they touch: a tyre at rest
whose position is up to 51 px above the player's floor lifts a player
who walks under it. A player falling past the side of a tyre is caught
only near its top.

If the player touches a ready tyre on a frame without being caught, the
tyre's wobble (below) doesn't advance on that frame.

## On the squashed tyre

After catching the player on C, the tyre is squashed on frames C + 1 to
C + 14. Its top on these frames:

| Frame | T |
|---|---|
| C + 1 to C + 12 | 12 |
| C + 13 | 16 |
| C + 14 | 12 |

On each of these frames, after the player's move, if the player touches
the tyre (with that frame's T) and isn't rising (on the ground and not
taking off, or in the air with `vy` greater than 0), the player is put
back on the top: the whole pixels of U become T + 2, the fraction is
kept. There is no 16 px limit here: any touch puts the player back.

So a player who stays on the tyre has U = 14 on C + 1 to C + 12, 18 on
C + 13 and 14 on C + 14, with `ground` 1 and `vy` 0. The player can
walk, run, roll and turn on the top; walking, stopping and turning were
compared frame by frame with the floor and change `x` and `vx` exactly
as there (`ground-speed.md`).

### Walking off the top

Call **F** the first frame on which a player who was put on the top on
F − 1 isn't put back (they no longer touch the tyre after their move).

- On F the player ends **8 px lower** than on F − 1. `ground` is still 1
  and `vy` 0 on F.
- F + 1 is the leave frame of `walk-off.md` (`ground` 0), except that
  the player moves down **0.44140625 px** (113/256) on it instead of
  3.4375. `vy` is 0.00390625 at its end, as in `walk-off.md`.
- From F + 2 the player falls as from L + 1 in `walk-off.md`.

A Jump press on F gives a tyre jump (below). A press on F + 1 starts an
ordinary jump with take-off frame F + 2 (`jump-arc.md`): the player
moves down 0.28515625 px (73/256) on F + 1 and again on F + 2, where `vy`
is −7. Measured on both tyres, walking off both sides.

## The throw

Frame **L** = C + 15 is the throw frame. After the player's move on L,
the tyre throws the player if

- I is within the range of rule 1 under "The tyre", and
- U is from **−54 to 15** (both ends included).

Whether the player is rising doesn't matter here. A player who stood on
the top through C + 14 moves 8 px down on L (as on F above) and is
thrown from U = 6.

When thrown:

- On **L**: `vy` becomes **−6**. The tyre doesn't move the player: `x`
  and `y` are where the player's move left them, and horizontal motion
  on L follows the ground rules. `ground` is 1.
- On **L + 1** nothing changes: `x`, `y`, `vx`, `vy` and facing stay as
  at the end of L, and all input is ignored. `ground` is 1.
- From **L + 2** the player is in the air (`ground` 0). On each frame,
  `vy` increases by **0.4375**, but never above 8, then the player moves
  down by `vy`. Holding Jump makes no difference. Horizontal motion
  follows the air rules (`air-control.md`).

On frame L + n (n ≥ 2, before landing or being caught), relative to the
end of L:

- `vy` = −6 + 0.4375 × (n − 1)
- `y` = −6 × (n − 1) + 0.4375 × (n − 1) × n / 2

The top of the arc is on L + 14, 38.1875 px above the end of L (so 30.1875
px above the standing height before the throw). With no input, a player
thrown from a tyre at rest on flat ground falls back onto it and is
caught again on L + 25; the cycle repeats every 40 frames.

After a throw, the tyre doesn't catch anyone on frames L + 1 to L + 8.
It is ready again from L + 9.

If the player doesn't satisfy the two conditions on L (for example they
jumped or walked away), there is no throw and the tyre is ready from
L + 1.

## The wobble

From frame L on, whether or not it threw anyone, the tyre wobbles. With
**m** = the number of wobble frames counted since L (m = 0 on L), its top
at the end of the frame is:

| m | T |
|---|---|
| 0 to 7 | 12 |
| 8, 9 | 16 |
| 10 to 15 | 12 |
| 16, 17 | 16 |
| 18 to 23 | 12 |
| 24, 25 | 16 |
| 26, 27 | 12 |
| 28, 29 | 16 |
| 30, 31 | 12 |
| 32 and later | 16 (at rest) |

m goes up by 1 on every frame after L, except on frames on which the
tyre is ready and the player touches it without being caught: on those,
m and T stay as they were. Catching the player starts a new squash, as
on C above, at any point of the wobble.

Remember that a catch on frame C uses T at the end of C − 1. For
example, a player thrown from a tyre at rest falls back on L + 25 and is
caught with T = 16 (m = 24 at the end of L + 24), so the whole pixels of
U become 18.

## The tyre jump

A Jump press on a frame **P** (the first frame Jump is held after a frame
without it) gives a **tyre jump** on P if the player was put on the top
of a tyre (caught or put back) on P − 1, with the exceptions in "When a
tyre jump can start".

On P, `vy` becomes −10; then gravity is added and the player moves as on
any airborne frame. So at the end of P, `vy` is **−9.6875** and the
player is 9.6875 px higher than at the end of P − 1. `ground` is 1 on P
and 0 from P + 1. Horizontal motion isn't changed by the jump.

On each frame from P on, until the player lands or is caught:

1. `vy` increases by the frame's gravity, but never above **8**;
2. the player moves down by `vy`.

The gravity is **0.3125** (5/16) if Jump is held on every frame from P up
to and including this frame, and **0.4375** (7/16) otherwise. As in
`jump-arc.md`, the lighter gravity doesn't stop at the top of the arc,
and pressing again in the air doesn't bring it back.

Let **K** be the number of frames Jump is held without a break,
starting with P (K ≥ 1). The height of the top of the arc above the
player's position at the end of P − 1:

| K | Height (px) |
|---|---|
| 1 | 112.125 |
| 2 | 114.875 |
| 3 | 117.5 |
| 4 | 120 |
| 5 | 122.5 |
| 8 | 129.3125 |
| 10 | 133.4375 |
| 12 | 137.1875 |
| 15 | 142.125 |
| 20 | 148.625 |
| 25 | 152.8125 |
| 30 | 154.875 |
| 31 or more | 155 |

A tyre jump takes the player off the top, so on the throw frame L the
player no longer touches the tyre and isn't thrown; a press on L itself
gives a tyre jump instead of the throw.

### When a tyre jump can start

- **Arriving on the ground.** When the player was caught while on the
  ground (walking, running, rolling onto the tyre), a press on any frame
  from C + 1 on, while the player is still put on the top, gives a tyre
  jump on the press frame. A press on C itself is an ordinary press made
  on the ground (`jump-input.md`, including its landing lockout if the
  player has just landed on the floor): it starts an ordinary jump on
  C + 1, and on C + 1 the player first moves 8 px down (as on F above),
  so the jump starts 8 px below the top.
- **Arriving from the air.** When the player was caught in the air,
  a tyre jump can't start before **E = C + 2**. A press made from C − 9
  to E, with Jump held on every frame from the press to E, gives a tyre
  jump on E (if the player was on the top on E − 1). A press after E
  gives a tyre jump on the press frame. Holding Jump since before C − 9
  (for example since the jump that brought the player there) never
  gives a tyre jump.

In the original, the earliest frame after arriving from the air is
sometimes later than C + 2, depending on timing inside the game that
isn't visible to a player, as for the landing lockout in
`jump-input.md`. Of 225 presses measured after arrivals from the air,
188 started on the frame given above, 27 one to three frames later with
Jump still held, and 10 not at all although Jump was held for a few
frames. The spec defines E = C + 2; an engine implements exactly this.

## Frozen frames

On the two frozen frames after the player bounces off an enemy
(`bounce.md`) the tyre doesn't change at all: its top, its squash or
wobble count and the frames counted after a throw stay as they were.

## Example

A tyre at rest on flat ground. The player walks right at 2 px per frame
and is caught on frame C, then releases Right. U is given here with its
fraction (the height above the tyre's position, not rounded).

| Frame | I | U | `vy` | `ground` | Notes |
|---|---|---|---|---|---|
| C − 1 | −26 | 0 | 0 | 1 | not touching (I < −24) |
| C | −24 | 18 | 0 | 1 | caught: U = T + 2 with T = 16 |
| C + 1 | −23 | 14 | 0 | 1 | squashed, T = 12 |
| C + 12 | −13 | 14 | 0 | 1 | |
| C + 13 | −13 | 18 | 0 | 1 | T = 16 |
| C + 14 | −12 | 14 | 0 | 1 | |
| C + 15 = L | −12 | 6 | −6 | 1 | moved 8 px down, thrown |
| L + 1 | −12 | 6 | −6 | 1 | nothing changes |
| L + 2 | −12 | 11.5625 | −5.5625 | 0 | |
| L + 14 | −10 | 44.1875 | −0.3125 | 0 | top of the arc |
| L + 24 | −10 | 23.25 | 4.0625 | 0 | |
| L + 25 | −10 | 18.75 | 0 | 1 | caught again (T = 16), fraction kept |
| L + 26 | −10 | 14.75 | 0 | 1 | squashed |

## Not covered

- The second character, and two characters on one tyre.
- A fourth reference that places an upside-down tyre (no kind row).
- Other objects (walkers, thrown objects) touching a tyre.
- Jump presses during a roll on the top: 60 of 62 measured gave a tyre
  jump as above; in 2 the player didn't take off.
- Jump presses made in the air before an arrival on the ground (the
  player landing on the floor next to the tyre and being lifted on the
  same frame), and presses on a frame on which a player who had walked
  off is put back on the top.
- Trace states and events for the frames on and thrown from a tyre.
- The other titles.
