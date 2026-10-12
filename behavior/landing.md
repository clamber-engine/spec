# Landing

When a falling player lands on the floor, and where they end up. Frames
are logic frames at 60 Hz, numbered as in a trace. Heights are measured
upward from the floor surface, in pixels. Measured on flat ground with the
lead character. Slopes and moving platforms aren't covered yet.

## Rule

Each airborne frame, first apply the frame's vertical motion as usual: the
new `vy` for the frame, then the position moved by that `vy`. Then the
player lands **on this frame** if both hold:

- the player is moving down on this frame (`vy` > 0 after the frame's
  speed change), and
- the new position is **less than 5 px** above the floor, or at or below
  it.

On a landing frame the player is placed exactly on the floor (`y` equal to
the floor's `y`), `vy` is 0, `ground` is 1, and the row carries `land`. The
movement that would have been left over below the floor is discarded, and
the player doesn't rise back.

Otherwise the player stays airborne at the new position.

## Consequences

- A landing usually comes a frame **earlier** than plain motion would
  reach the floor: on the last airborne frame a falling player is always
  at least 5 px above the floor. So on the landing row `y` can change by
  up to just under the frame's `vy` plus 5 px.
- Exactly 5 px above the floor after moving is still airborne; anything
  less, down to a fraction of a pixel, lands.
- The rule doesn't depend on horizontal speed (standing, walking or
  running jumps) or on whether jump is held. Falls that didn't start with
  a jump (walking off a ledge) haven't been measured yet.
- A player moving up, or with `vy` exactly 0 on the frame, never lands,
  even when closer than 5 px to the floor.

## Examples

| Before the frame: height | Frame's `vy` | Height after moving | Result |
|---|---|---|---|
| 9.84375 | 8.125 | 1.71875 | Lands: `y` on the floor, `vy` 0 |
| 11.25 | 6.40625 | 4.84375 | Lands |
| 10.4375 | 5.4375 | 5 | Airborne at 5 px |
| 12.0625 | 6.8125 | 5.25 | Airborne at 5.25 px; lands next frame |
| 1 | −0.0625 (rising) | 1.0625 | Airborne |
