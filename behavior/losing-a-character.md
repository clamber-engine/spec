# Losing a character (hit with two characters)

Titles: `title-1`

What happens when the player takes damage while the other character is
with them: the character under control (the **hit character**) is
thrown up, lands and runs out of the level, and the **other character**
takes its place and becomes the one under control. Being hit with no
other character is in `knock-out.md`. Which contacts with a walker cause
damage rather than a bounce is in `walker-contact.md` (measured with one
character), and when a thrower's ball does in `ball-contact.md`
(the thrower itself, measured with one character, in
`thrower-contact.md`).

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y` and
`vy` are as in a trace: down is positive, speeds in px per frame. Speeds
and positions are whole multiples of 1/256 px.

The **first character** is the one under control when a game begins;
the **second character** is the one that joins later (how it joins, and
how the other character follows, is in `following.md`). Measured with the
first character as the hit character and the second following it, hit by ground enemies from the
side: walking or running into them, standing facing toward and away from
an enemy that walks into the player from the right or from the left,
while turning, on the frame of a jump press, on the take-off frame,
rising in a jump with the jump input held or already released, and
falling, by inputs alone (90 runs). Mostly on flat ground.

## The hit frame

Call **H** the first frame on which the player is hurt (the trace row
carrying the `hurt` event). Call the hit character's position on H the
**hit spot**.

On H the hit character:

- doesn't move: `x` and `y` are those of H − 1, whatever the speed was
  before, on the ground or in the air;
- gets `vy` **−8** and no horizontal speed;
- faces **right**, whichever way it faced before and whichever side the
  enemy came from.

From H on, all input is ignored until T (below). Nothing pauses and
nothing freezes: there is no pause like the one in `knock-out.md`, and
enemies keep moving on every frame (the thrower and its balls:
`thrower.md`, "While a character is lost").

## The hit character

From **H + 1**, each frame until it lands:

1. `vy` increases by **0.4375** (7/16), but never above **8**. If on
   H − 1 a jump was in progress with the jump input held on every frame
   since its press frame (the press frame included), so that the lighter
   gravity of `jump-arc.md` applied, the increase is **0.28125** (9/32)
   on every frame of this flight instead, whatever the input does after
   H.
2. It moves vertically by the new `vy`. It doesn't move horizontally.
3. The landing rule in `landing.md` applies.

Hit on flat ground with the usual increase, it rises 69.1875 px (highest
on H + 18) and lands back on the floor on **H + 35**. Hit higher up it
lands later.

On the frame after it lands it doesn't move. From the next frame it runs
**right** at **4 px per frame**, following the ground (1 to 3 px up or down
a frame on slopes, seen in the same runs as the drop), until it is out
of view, and then it is gone. It plays no further part.

The run keeps its running state the whole way, and what it meets on the
way:

- **A drop.** Where the floor ends (a 64 px drop was measured, in 95 runs
  with the hit on different frames) it leaves the floor, falls with the
  usual gravity of `jump-arc.md` while still moving right at 4 px per
  frame, lands on the lower floor and runs on. Nothing about the run
  changes.
- **A steep rise.** A rise of 48 px over about 52 px of `x` on the first
  level (much steeper than the slopes of `slopes.md`: about 12 px of height
  per 13 px of `x` on average) was run up in 7 runs with the hit on
  different frames. A setup method was used to reach each hit. The run
  didn't slow, stop or change state. It kept 4 px per frame and its
  height followed the ground, the same in every run for the same `x`:
  up about 0.5 to 5.2 px a frame along the rise, then about 10.4 px on the
  frame onto the top. So the 1 to 3 px a frame above is what gentle slopes
  give, not a limit. Not checked in ordinary play with a hit there.
- **A wall.** Observed at one wall, on one route of the first level, where
  a walking character moving right stops: the run stopped at the same `x`
  as the walking character on that route, with the fraction of `x` kept, and
  did not turn, climb, jump or play another state. Its horizontal speed stayed
  at 4. It was still there after 290 frames with the other character playing
  on, and until the game ended. It is one run (the hit happened once on that
  route), so other walls and steps are not covered.
- **A stop near the right end of the first level.** On the lower floor at
  the far right end of the level, a run that began on the upper floor (it ran
  off a ledge and fell about 200 px while still moving right at 4) stopped
  for good. Over 7 hits on consecutive frames the stop moved by at most
  about 4 px between runs: two values alternated one frame after another,
  2 px apart, and the first start, which landed lower, stopped about 2 px
  beyond them. After the stop the horizontal speed stayed at 4 while `x` did
  not change, for 100 more frames, with the other character walking back.
  The stop was well short of the 289 cut-off (about 164 right of the view's
  left edge). A walking character on the upper floor goes past this place
  and leaves the level, so the stop belongs to the lower floor. Whether it
  is a wall or the level's edge was not separated: a walking character
  on the lower floor stopped earlier, on a raised floor, so it does not
  compare. A setup method was used to reach each hit. Not checked in
  ordinary play with a hit there.
- **Out of view.** It is gone on the first frame where its `x` rounded down
  is 289 or more to the right of the view's left edge. Over 40 runs its last
  moving frames were 289.6 to 292.9 right of the view and the frame before
  was never past 288.9. The cut-off within the gap between 288.9 and 289.6
  was not pinned down; the rounded-down rule fits all runs. What a run
  stopped at a wall does when the view moves left, or leaves it behind, was
  not observed (in the wall run the stopped lead stayed less than 289 from
  the view). How far the run goes therefore depends on where the view is,
  which follows the other character.

## The other character

From **H to H + 36** the other character doesn't move, even if it was
moving on H − 1.

From **H + 37**, each frame, it moves toward the hit spot. The floor
doesn't hold it: when the hit spot is in the air it rises off the floor
to it, and it comes down through the air to a hit spot below it. The target
is the hit spot with the fraction of `x` dropped, and `y` rounded to a
whole pixel toward the floor if it has a fraction. Each axis moves on
its own:

1. **gap** = target − the character's own coordinate rounded the same
   way (fraction of `x` dropped, `y` rounded toward the floor).
2. If |gap| ÷ 16 is 3 or more, the move is gap ÷ 16 (exact; a multiple
   of 1/16). Otherwise the move is 3 toward the target, or the gap
   itself if that is smaller than 3.
3. The coordinate changes by the move. Its fraction is kept.

When both rounded coordinates equal the target, the run is over. The
next frame is **T**.

On **T** the character is placed half a pixel right of the target `x`
and half a pixel above the target `y`, and starts a hop: it then moves
up by **9.5625** on T + 1, **9.125** on T + 2 and **7.9140625** on
T + 3. After that, each frame it moves up 0.4375 (7/16) less than the
frame before, so the move becomes downward, never more than **8** down.
The landing rule in `landing.md` applies. With no Left or Right held,
and on flat ground, the hop rises 94.7421875 px (highest on T + 21) and
lands on **T + 41** where it started.

### Control returns

- From **T + 1**, Left and Right steer the hop and change facing, as
  they do in the air for that character. Before T + 1 they do nothing.
- Jump presses before the hop lands start nothing by themselves. From
  the hop's landing, jumping follows `jump-input.md` exactly as after any
  landing (the landing lockout, and a press held through it starts a
  jump on E). Measured with presses from 5 frames before the landing to
  4 frames after it.
- From the hop's landing the other character is the character under
  control, in every respect.

### Example (made-up numbers)

The hit character is hit standing on flat ground at `x` = 200.25. The
other character stands on the same floor at `x` = 138. From H + 37 its
`x` is 141.875 (gap 62, move 3.875), 145.5625 (gap 59), 149 (gap 55),
152.1875 (gap 51), 155.1875 (gap 48, move 3), then 3 more each frame
until 200.1875 on H + 56, when the rounded `x` is 200. T is H + 57: `x`
becomes 200.5 and `y` half a pixel above the floor. Its `y` doesn't
change before T, since its gap there is 0.

Vertically, a character 78 px above the target (`y` = target − 78)
moves down by 4.875, then 4.5625 (gap 73), and so on; its last moves
are 3 (from 4.6875 above to 1.6875 above), then 1 (gap 1, to 0.6875
above, which rounds toward the floor to the target).

## The second character as the hit character (`title-1`)

When the second character is under control and is hit, with the first
character following, everything above holds with the roles swapped, with
these differences. Measured with small, medium and large ground walkers
coming from the right and from the left, with the second character
standing, walking, running, turning, jumping into the walker and at
every moment of a jump, in 386 runs. A setup method was used to reach
these situations: in each, the walker was seen standing close beside the
second character, on its left or its right. All runs are arranged
situations; none was reached by inputs alone, so the rules are measured
only for hits like these. The other character's timings and hop above are
not valid here.

The hit character (the second character):

- On H it is placed, thrown up and turned right exactly as above:
  `vy` −8, no horizontal speed, facing right.
- Falling, `vy` increases by 0.4375 per frame, never above 8. If on
  H − 1 a jump was in progress with the jump input held on every frame
  since its press, the increase is **0.3515625** (90/256) instead
  (the lighter gravity in `second-character.md`).
- On flat ground it lands on H + 35, as above, and runs out right at 4
  px per frame from H + 37.

The other character (the first character):

- It is **not** still on H. It makes the move it would have made without
  the hit (`following.md`), and is still from H + 1 to H + 37.
- From **H + 38** it moves toward the hit spot by the same rule as above,
  one frame later than the second character does when it is the other
  character; T is therefore one frame later than the same distance would
  give above.
- The hop starts as above, then moves up by 9.5625 on T + 1, 9.125 on
  T + 2 and **6.5625** on T + 3, and each frame after that 0.4375 less
  than the frame before, never more than 8 down. With no Left or Right
  held, and on flat ground, it rises **71.6875** px (highest on T + 17,
  the same height on T + 18) and lands on **T + 35** where it started.
- Control returns as above: Left and Right steer the hop from T + 1 and
  change facing, as they do in the air for the first character. Jump
  presses follow `jump-input.md` after the hop's landing; presses from 10
  frames before to 7 frames after the landing behave exactly as after any
  landing of the first character.

In a trace the rows follow the same split as above (the hit character
from H to H + 35, the other from H + 36), except that state `swap` of the
first character starts on H + 37. On H + 36 the first character is still
as it has been since the hit: not moving (`vx` 0, `vy` 0, `ground` 1), in
an ordinary ground state, not `swap`, and with no events.

### Example (made-up numbers)

The second character, standing on flat ground at `x` = 200.25, is hit on
frame 100 (H). The first character was walking at 2 px per frame and is at
`x` = 130 on frame 99: it is at 132 on frame 100 and stays there until
frame 137 (H + 37). On frame 138 it moves by 4.25 (gap 68). The second
character lands on frame 135 and runs out from frame 137.

## Hits during a roll or a bounce (`title-1`)

**A roll.** A hit on any frame of a roll (`rolling-into-walkers.md`), of
the stop that ends it, or of the running a roll carries on into, changes
nothing above: the hit character is thrown up, turned right and flies and
lands as in "The hit character", whatever its speed was (no roll goes on),
and the other character waits, runs and hops as in "The other character".
When the second character is the one rolling, "The second character as
the hit character" applies (the first character is the other
character). Measured with a setup method used to reach each situation, a
stinger touching the rolling character: 74 runs with the first character, one hit on each of 37
consecutive frames from the first frame of a roll through its stop (the
hop checked in full) and through the running a roll carries on into
(flight and run only, since the held direction steers the hop), and 39
runs with the second character, one hit on each of 39 consecutive frames
of a roll and its stop. The hit-character rows, the other character's
rows up to the hop, and the hop, matched the rules of those two
sections in all of them. All of these situations were arranged; no hit
during a roll was reached by inputs alone, so the rule is not checked in
ordinary play. Which contacts hurt around a roll is in
`rolling-into-walkers.md`; other enemies were not tried.

**A bounce.** For a hit on a frame of a bounce (B is the bounce frame,
`bounce.md`), the hit character flies up from where it was hit, in the
air, as above: `x` stays, `vy` is −8 on H, and it lands on the first
frame the landing rule of `landing.md` gives, on the floor under the hit
spot. That floor can be well below the hit spot, so the flight is
longer than the 35 frames of a hit on flat ground, and it ends on
the frame the landing rule gives. The only difference is the gravity: the
increase is **0.28125** (9/32) instead of 0.4375 if jump is held on every
frame from B + 3 up to and including H − 1 (so always when H is B + 3),
the same hold as the bounce's own lighter gravity; if jump is released
on any frame in between, or isn't held on B + 3, it is 0.4375. The other
character waits, runs through the air to the hit spot and hops as in "The
other character" (first character leading); the hop lands on the floor
under the hit spot.

Measured with the first character leading and the second following, a
setup method used to reach each situation, a stinger touching the
bouncing character: 39 hits on
consecutive frames from B + 3 on, 9 to 98 px above the floor under the
hit spot. The hit character's rows and the other character's run
matched in all of them, and the hop matched in the 20 that were not cut
short by a ceiling above (the other 19 were not compared). The gravity
rule was measured in 29 more runs, with jump held from different frames
to different frames around B + 3 and H − 1, and hits from B + 3 on. With
the stinger touching the character from different frames, a hit came on
B + 3 only if it was already touching on B itself, and on B + 7 or
later otherwise (no hit came on B + 4 to B + 6). This was seen in
arranged situations only and is not checked in ordinary play; which
frames of a bounce a stinger, or any other enemy, can hurt on was not
examined further. No hit during a bounce was reached by inputs alone
either.

## Hits during a swap (`title-1`)

A swap is in `swap.md`: **Q** is the press frame and **E** the frame on
which control returns. Measured with the first character as the player
(the second character takes over) and with the second character as the
player (the first character takes over).

- **Nobody is hurt from Q to E − 1.** A ground walker moves on Q as it would
  without the swap, stands still on every frame from Q + 1 to E − 1 (to
  Q + 78 when the first character is the player, to Q + 71 when the
  second is) and moves again on E. Whatever is touching either character
  on those frames does nothing. (E itself has one exception, below.)
- **The first frame on which the character that takes over can be hurt
  is E + 1.** A walker or stinger touching it at the end of any frame
  from Q + 1 to E − 1, or at the end of E, hurts it on E + 1 (the hurt
  frame H, as in "The hit frame"). A walker that is not touching yet at
  the end of E hurts it on the frame after it first touches, as without a
  swap.
- **An exception, first character as the player.** When a walker is
  standing next to where the first character ends up (it is the other
  character from E), the character that takes over can be hurt on **E**
  itself, though it stands about 60 px away: the walker, heading left,
  with its `x` minus the first character's `x` at the end of E − 1
  between **−13.35 and 29.65** (hurt at −13.35 and 29.65, not at −14.35
  or 30.65) and standing at the first character's height. From E + 1 a
  walker touching the other character does nothing. With the second
  character as the player there is no such hurt on E.
- **After the hit** the character that took over is the hit character,
  and everything above holds: after a swap with the first character as
  the player it is the second character ("The second character as the
  hit character"), the first character being the other character; after
  a swap with the second as the player it is the first character, as in
  "The hit character" and "The other character".

Measured with a setup method: walkers stood beside a character at a
range of distances, touching it at different frames around E, and a
stinger touched a character on each of the first 90 frames after the
press (180 runs, 90 for each character taking over). The
frames of the first hurt matched the rules above in all of them. With the
first character as the player, 45 hits (3 placement frames, 15 distances)
had the hit character's rows, the other character's run and the hop
match "The second character as the hit character"; with the second as
the player, 100 walker hits matched "The hit character" and "The other
character" (81 distances, −30 to 50 px, at one placement frame, and 19
distances, 12 to 30 px, at another; the hop is compared until it meets
different ground). The
exception on E was seen in two arrangements, 60 and 62 px apart, and why
it only happens with the first character as the player was not found.
All hits were arranged; none was reached by inputs alone, so hits during
a swap in ordinary play weren't examined. Other enemies
than a walker and a stinger weren't tried.

## Hits while carrying an object (`title-1`)

**The first character carrying a barrel** (`carrying.md`; held above its
head). The barrel is let go on H and flies as in `knock-out.md`, "An
object in the hands": on H it stays where it was held on H − 1, with `vx`
**1** in the direction the character faced on H − 1 (left if it faced
left, even though the hit turns it right) and `vy` **−4**; then, each
frame, `vy` grows by 0.4375 (never above 8) and it moves by `vx` and the
new `vy`; it touches nothing and is removed under the same rules. Only
the start differs: with a second character there is no pause, so the
barrel moves from **H + 1**, not H + 11. A throw that began on H − 1 or on
H never happens, as in `knock-out.md`; a throw that began on H − 2 or
earlier is an ordinary thrown object (`thrown-objects.md`). Everything
else about the hit is as above, the held direction steering the hop as
usual.

**The second character carrying a barrel** (`carrying.md`, "The second
character"; held low, beside it). The barrel is **gone on H − 1**, the
frame before the hurt frame, and doesn't fly off. The hit goes on as in
"The second character as the hit character". A stinger that touched only
the barrel, on its far side from the character, removed the barrel and
did not hurt the character; the range of positions where that happens
wasn't measured.

Measured with a setup method, a stinger touching the character, the
character standing and walking, facing both ways, with the hit on frames
spread over 40 frames of the carry: 88 hits of the first character with
the barrel in the hands (3 255 frames of the barrel compared with the
rules above, 0 differences, also its removal) and 109 hits of the second
character, in every one the barrel gone on H − 1. In a stinger touching
the character from the side away from the barrel, about 30 px from it,
the character was hurt and the barrel gone just the same. All hits were
arranged; none was reached by inputs alone. Only the barrel and only a
stinger as the cause were tried; walkers hurting a carrying
character with both there, a holder, and a swap press while carrying
aren't covered.

## In a trace

The trace follows the hit character from H to H + 35, and the other
character from H + 36. `x` and `y` stay relative to the scenario's start
position, so they jump on H + 36 to wherever the other character is.

Rows H to H + 35 (the hit character):

- State `hurt`. Events: `hurt` on H, and `turn` on H too if the hit
  character faced left on H − 1. Facing right throughout.
- `vx` 0. `vy` is the vertical speed applied on that frame (−8 on H).
- `ground` on H as on H − 1, then 0 while in the air. If it lands by
  H + 35: the landing row has `y` on the floor, `vy` 0, `ground` 1 and
  event `land`, and later rows up to H + 35 have `vy` 0, `ground` 1 and
  no events.

Rows from H + 36 (the other character):

- State `swap` from H + 36 until the row before the hop lands. `ground`
  0 on all of these rows, including the run along a floor.
- `vx` and `vy` are the moves made on that frame (0 and 0 on H + 36).
  On T, `vy` is −10 and `vx` 0 (the speed the hop is given there, like
  the speed shown on a bounce frame in `bounce.md`), though the
  placement on T is only half a pixel. On T + 1, T + 2 and T + 3, `vy`
  is −9.5625, −9.125 and −7.9140625.
- Facing is the other character's own. No `turn` on H + 36; `turn` on
  later rows whenever facing changes.
- The hop's landing row: `y` on the floor, `vy` 0, `ground` 1, event
  `land`, and a ground state as usual (`idle`, `walk`, `run`).

## Not covered

- The second character's own movement values are in
  `second-character.md` (walking, running, crouching, jumps, air
  control). Everything else in the other behavior files was measured
  with the first character.
- The first character joining as the other character (the first
  character taking over after the second is hit is covered above, only
  for hits from the side by ground walkers, on flat ground, in arranged
  situations; hits reached in ordinary play by inputs alone were not
  checked).
  The second character knocked out with no one left is in
  `knock-out.md`, "The second character (`title-1`)".
- A hit when the other character is already at the hit spot, out of
  view, or facing away from the hit spot when its run begins.
- Ground or walls between the other character and the hit spot.
- Protection from further damage after the take-over is in
  `protection.md`.
- What the hit character's run does at a bottomless pit and at the
  level's right edge before it leaves view (the stop above is not
  separated into wall or edge); what a run stopped at a wall
  does once the view moves away from it; other rises and steep faces;
  walls other than the one measured (a wall in view while the other character stands ahead of
  the run was not arranged); the second character as the hit
  character.
- Hits during a roll or a bounce other than those above (rolls with
  other enemies; bounces with the second character; hits during a roll or a
  bounce reached in ordinary play; hits where the hop
  meets a ceiling), hits during a swap by other enemies than walkers
  and stingers, hits while carrying other things than a barrel or by
  other things than a stinger, a stinger touching only a barrel (positions
other than those above), a swap press while carrying, and hits during a
swap or while carrying reached in ordinary play.
