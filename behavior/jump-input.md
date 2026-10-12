# Jump input timing

Titles: `title-1`

When a jump press turns into a jump. Frames are logic frames at 60 Hz,
numbered as in a trace. The **press frame** is the first frame the jump
input is held after a frame where it was not held. The **landing frame**
is the frame carrying the `land` event. Measured on flat ground with the
lead character.

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
