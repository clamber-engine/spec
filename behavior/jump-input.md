# Jump input timing

Titles: `title-1`, `title-2`

When a jump press turns into a jump. Frames are logic frames at 60 Hz,
numbered as in a trace. The **press frame** is the first frame the jump
input is held after a frame where it was not held. The **landing frame**
is the frame carrying the `land` event. Measured on flat ground with the
lead character.

The sections below up to "In `title-2`" give the `title-1` values.
`title-2` is different throughout; "In `title-2`" gives its complete
rules.

## On the ground

A press on frame P while grounded starts a jump on frame P + 1
(`jump_start` on P + 1). This includes every frame of a roll, standing or
moving: rolling never delays or blocks a jump.

What counts is being grounded at the end of frame P − 1. So where the
ground simply ends, a press on the first airborne frame of a walk-off
still starts a jump; at a ledge with a steep face it doesn't if the
player was carried down the face just before leaving it
(`walk-off.md`, "Jumping near the edge" and "Ledges without a face").

## Landing lockout

After landing, there is a short time when a jump can't start. Call the
earliest frame a jump can start after landing frame L **E**. For a
landing after a jump, E = **M + 2**, where M is the landing's **switch
frame** below. M is L or later (up to L + 6 in the measurements below),
so E is L + 2 or later. Jumps started from a roll are an exception (see
the end of this section).

### The switch frame M

Let T be the frame carrying the jump's `jump_start` (`jump-arc.md`).

Let **A**, the **approach frame**, be the first frame from **T + 20** on
at whose end there is floor within reach below the player. In level y
(`view-vertical.md`), with the feet line F (the player's level y rounded
up to a whole px) and the floor `Y` of the column containing `x`: the
floor is within reach when it is at or below the feet and at most
**71 px** below them (0 ≤ `Y` − F ≤ 71), and its 32 px band is at most 2
more than the band of F, as in `view-vertical.md` ("The ground used"). A
player on the floor always has floor within reach, so A is never later
than the first frame from T + 20 on which the player is on the floor. A
doesn't depend on whether the player is still rising.

From A on there are seven **stage frames**:

- if A = T + 20: A, A + 2, A + 4, A + 6, A + 8, A + 11 and A + 15;
- if A is later than T + 20: A, A + 1, A + 3, A + 5, A + 7, A + 10 and
  A + 14.

Then:

- If L < A (the player landed before the approach frame): M = A + 4.
- Otherwise let **n** be the number of stage frames that are L or
  earlier (1 to 7):

  | n | M |
  |---|---|
  | 7 | L |
  | 6 | L + 1 |
  | 5 or 4 | L + 2 |
  | 3 or 2 | L + 3 |
  | 1 | L + 4 |

So a high jump that comes down onto lower or level ground usually has
M = L or L + 1, and a jump that lands on higher ground soon after its
top has a later M.

Examples: standing jumps on flat ground (the take-off floor is the
landing floor), Jump pressed on T − 1:

| Jump held | A | L | n | M | E |
|---|---|---|---|---|---|
| 1 frame | T + 20 | T + 31 | 6 | L + 1 | L + 3 |
| 10 frames | T + 20 | T + 36 | 7 | L | L + 2 |
| 14 frames | T + 24 | T + 38 | 7 | L | L + 2 |
| 20 frames | T + 29 | T + 41 | 6 | L + 1 | L + 3 |

With Jump held 14 frames, the feet are 73 px above the floor at the end
of T + 23 and 71 px at the end of T + 24, so A = T + 24 and the second
list of stage frames applies: L = A + 14 is the seventh. With Jump held
1 frame, the player is within reach already at T + 20, and L = A + 11 is
the sixth stage frame of the first list.

The same switch frame decides when walking, running or standing starts
after a landing (`ground-speed.md`, "After a landing").

Measured on `title-1` with the lead character: 826 landings after
standing, walking and running jumps (Jump held 1 to 30 frames, Action on
or off, some after walking back first): 730 on flat floor and 96 on
slopes, on floors from 82 px below to 80 px above the take-off floor,
including sites with steep faces (landings on face columns, or with A
ending on one, left out). L ranged from 2 frames before A to 23 frames after it. M matched
on all 826, and E (Jump pressed on L − 1 and held) on all 819 where it
was measured. 238 of these landings were recorded after the rule was
fixed. The window of "Pressing before landing" was checked to count
back from E with E from L + 2 to L + 8.

### Landings this doesn't cover

For a landing after a jump that started from a roll, after a fall that
didn't start with a jump (walking or rolling off a ledge), or after a
bounce, the spec defines **E = L + 3** unless another file says
otherwise (after a jump from a roll, M = L + 1). The mode after these
landings is in `ground-speed.md` ("After a landing", "After a fall or a
bounce"). In the original E varies on these landings
too. Reference comparisons on them should allow ±1 frame on the
`jump_start` and on frame counts measured from it.

## Pressing before landing (buffered jump)

A press made in the air is remembered for a short time, but only while
the input stays held.

With press frame P before landing, a jump starts on frame E if both hold:

- the jump input is held on every frame from P to E − 1, and
- P ≥ E − 12, i.e. the press came at most 11 frames before frame E − 1.

Otherwise no jump starts on landing. A new press is needed.

The window counts back from E, whatever M is.

| Case | Result |
|---|---|
| Pressed 1 to 9 frames before L, held through E − 1 | Jumps on E |
| Pressed exactly at E − 12, held through E − 1 | Jumps on E |
| Pressed at E − 13 or earlier, held through landing | No jump |
| Pressed in the air, released before E − 1 | No jump |
| Held continuously since the jump that took off | No jump |

A press on or after the landing frame (P ≥ L) starts a jump on
max(P + 1, E).

## Worked example

A jump lands on frame 40 with M = L + 1 (L = 40, so E = 43). Pressing
again on frame 31 and holding gives a jump on frame 43. Pressing on
frame 30 and holding gives no jump.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. A jump in `title-2`
starts on a grounded frame **G** and takes off two frames later: the
`jump_start` frame T is **G + 2** (`jump-arc.md`, "In `title-2`").
Nothing about the jump is visible on G and G + 1; the player stays on the
ground and horizontal speed follows the ground rules.

### When a jump starts (`title-2`)

A jump starts on frame G if all of these hold:

- the player is grounded on G, and G is not the landing frame (frames of
  a roll and of a crouch count as grounded; see below for crouches);
- the jump input is held on G;
- the press it belongs to (the first frame of the current unbroken hold,
  P) was made on **G − 15 or later**;
- that press hasn't already started a jump.

For a press made on the ground, this means a jump starts on P itself,
T = P + 2, even if the input is held for only one frame. This includes
every frame of a roll, standing or moving.

### Landing (`title-2`)

There is no variable lockout as in `title-1`. With landing frame L, the
earliest frame a jump can start is **L + 1** (so the earliest take-off is
L + 3). This was the same for every one of 180 landings measured (heights
from every hold length 1 to 30, standing and walking).

A press made in the air starts a jump on L + 1 if it is still held on
L + 1 and was made on L − 14 or later:

| Case | Result |
|---|---|
| Pressed L − 14 to L, held through L + 1 | Jump starts on L + 1 (T = L + 3) |
| Pressed L − 15 or earlier, held through L + 1 | No jump |
| Pressed in the air (or on L), released on or before L | No jump |
| Held continuously since the jump that took off | No jump |
| Pressed on L + 1 or later | Jump starts on the press frame |

The gravity rule in `jump-arc.md` counts held frames from G, which here
is L + 1.

### Crouching (`title-2`)

Crouching is described in `ground-speed.md` ("In `title-2`"). A jump can
start on any crouched frame, including the frames of getting down and the
11 frames of getting up. But if a jump starts on a frame G where the
crouch continues (exactly one of Up and Down held), and the crouch ends
on G + 1 or G + 2 (neither or both of Up and Down held), the jump doesn't
take off. Instead the crouch ends on that frame as usual, and:

- if the jump input is held on that frame, a jump starts over with that
  frame as the new G (take-off two frames later);
- otherwise there is no jump, and a new press is needed.

A jump started on the frame a crouch ends (the jump pressed together with
releasing Down) takes off normally.

| Inputs from G (crouched, Down held before) | Result |
|---|---|
| Down + jump, then Down, Down | Take-off on G + 2 |
| Down + jump, then Up, Up | Take-off on G + 2 |
| Down + jump, then nothing | No jump; getting up starts on G + 1 |
| Down + jump, then Down + jump, then nothing | No jump; getting up starts on G + 2 |
| Down + jump, then jump held | Jump starts over on G + 1, take-off on G + 3 |
| Jump alone (Down released on G) | Take-off on G + 2 |

### Not covered (`title-2`)

Presses made just before landing on ground of a different height, and
the second character. Jumps on slopes start as on flat floor (`slopes.md`,
"In `title-2`"). Jumping after walking off a ledge,
and on landing after such a fall (one frame later than after a jump), is
in `walk-off.md` ("In `title-2`").
