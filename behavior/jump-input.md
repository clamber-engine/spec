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

## Landing lockout

After landing, there is a short time when a jump can't start. Call the
earliest frame a jump can start after landing frame L **E**.

In the original, E is L + 2, L + 3 or L + 4. Which one depends on timing
inside the game that isn't visible to a player and can't be predicted from
inputs, positions, velocities or trace events. Jumps with the same inputs
and airtime differ, and so do identical jumps landing on ground of
different shape.

The spec therefore defines **E = L + 3**, the value seen most often in
measurements. An engine implements exactly this.

Reference comparisons: in the original, a `jump_start` that follows a
`land` without an earlier chance to jump may come one frame earlier or
later than an engine following this spec. Scenarios that measure such a
jump should allow ±1 frame on it, and on any frame count measured from it.

## Pressing before landing (buffered jump)

A press made in the air is remembered for a short time, but only while
the input stays held.

With press frame P before landing, a jump starts on frame E if both hold:

- the jump input is held on every frame from P to E − 1, and
- P ≥ E − 12, i.e. the press came at most 11 frames before frame E − 1.

Otherwise no jump starts on landing. A new press is needed.

In the original this window counts back from the actual earliest frame,
whatever its value, so it holds unchanged with E as defined above.

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

A jump lands on frame 40 (L = 40, so E = 43). Pressing again on frame 31
and holding gives a jump on frame 43. Pressing on frame 30 and holding
gives no jump.

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
