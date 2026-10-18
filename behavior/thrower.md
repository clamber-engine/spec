# Throwers and their balls

Titles: `title-1`

A **thrower** is an enemy that sits in one place, high up, and throws
**balls** on a fixed timetable: one to the left, one straight up and one
to the right. A ball flies, bounces on floors, drops through them after
a bounce, and is removed once it is outside the picture. This file
covers where a thrower is, when it throws, and how a ball moves until
it is gone. When a ball hurts the player is in `ball-contact.md`, and
when the thrower itself hurts the player or gives a bounce, and what a
bounce does to it, in `thrower-contact.md`. What happens when an object
the player throws meets the thrower is not covered here.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`,
`vx` and `vy` are as in a trace: right and down are positive, speeds in
px per frame. `y` is a level y as in `view-vertical.md`. Speeds are
whole multiples of 1/256 px per frame and are written both ways, as in
`ground-speed.md`. Columns are as in `steep-faces.md`: 1 px wide, and
an object's column is the one containing its `x`. A column can have
several **floors** at different heights (for example ground with
platforms above it, `../formats/level-ground.md`); "higher" means higher
on screen (smaller `y`).

Measured on the first level, which has one thrower, with the lead
character alone, standing at different places near the thrower, walking
under it and past it on both sides, walking away until it was removed
and coming back from both sides, and walking and jumping at random: 203
throws, 236 ball flights and 12 442 ball frames, all checked against
the rules below with no mismatch; 20 times the thrower appeared or was
removed. Separately, about 90 controlled runs in which a ball's
position or speeds were set after a bounce, to find the limits below.

## The thrower

A level places a thrower at a position `P` = (`xt`, `yt`). The thrower
never moves from `P`.

It appears and is removed exactly as a walker whose start is `P`
(`walker-view.md`): it appears on the first frame whose `V` puts `xt` in
the window (−31 ≤ `xt` − `V` ≤ 288), and is removed on the first frame
on which `xt` − `V` ≤ −128 or `xt` − `V` ≥ 385. Measured: appearing at
`xt` − `V` = 287 and 288 with the view moving right, and from −31 to
−29 with it moving left (never at −32); removed at 386 and at −129.
After being removed it appears again by the same rule, and starts its
timetable again.

## When it throws

Let `A` be the frame the thrower appeared. It throws on these frames,
for every whole `n` ≥ 0:

| Frame | Ball |
|---|---|
| `A` + 2 + 173`n` | straight up |
| `A` + 33 + 173`n` | to the right |
| `A` + 144 + 173`n` | to the left |

So after appearing it throws up, then right 31 frames later, then
nothing for 111 frames, then left, up and right 31 frames apart, and so
on every 173 frames. Where the player is or what the player does makes
no difference. The timetable goes on while the thrower is outside the
window but not yet removed, and a throw due on the frame the thrower is
removed still happens (seen once).

The thrower measured was drawn facing left. Whether a thrower facing
the other way throws in a mirrored order isn't covered.

## A thrown ball

A ball thrown on frame `T` starts at (`xt`, `yt` − 8), 8 px above the
thrower's position, with:

| Ball | `vx` | `vy` |
|---|---|---|
| to the left | −3.125 (−800/256) | −7 (−1792/256) |
| straight up | 0 | −7 (−1792/256) |
| to the right | +3.125 (+800/256) | −7 (−1792/256) |

It isn't ignoring floors (below). On `T` itself the ball is **either**

- at its start, with these speeds; it then makes its first move on
  `T` + 1, and isn't removed on `T` even if its start is outside the
  window below; **or**
- already one frame on: it has made one frame of moves ("Each frame")
  from its start on `T`, with that frame's checks, including being
  removed.

Both were seen for every direction (left 36 at the start and 32 one
frame on, up 22 and 63, right 26 and 51), on the same frames of the
timetable. Which of the two happens on a given throw isn't covered. Four
throws made while the thrower was outside the window but not yet
removed (`xt` − `V` from 304 to 308) were never seen at all, which is
what happens when the ball is one frame on and removed on `T`.

## Each frame

Every frame after the ball appears, in this order:

1. The new `vy` is `vy` + **0.4375** (112/256), but never above **8**
   (2048/256).
2. `x` moves by `vx`. Nothing ever changes `vx`.
3. The ball moves vertically, as in "Ignoring floors" if it is ignoring
   floors, or else as in "Bouncing".
4. **Leaving the picture**: unless the ball bounced on this frame or
   stopped ignoring floors on it, it is removed if it is now outside
   the window (below).

## Bouncing

While the ball isn't ignoring floors:

1. Take the new `y` = `y` + new `vy`.
2. Its **floor** `F` is the highest floor in the column of its new `x`
   that is no more than **8 px above** its `y` at the start of the
   frame (before this frame's move): the floor with the smallest level
   y among those with level y ≥ `y` − 8. If there is none, it simply
   moves.
3. It **touches** the floor if the new `vy` is above 0 (moving down)
   and the new `y` is more than `F` − 5 (less than 5 px above the
   floor, or anywhere below it). Otherwise it simply moves to the new
   `y`.
4. When it touches, it **bounces**: its `y` becomes `F` exactly, its
   `vy` becomes **−5** (−1280/256) however fast it was falling, and it
   starts **ignoring floors**, remembering `F` as its **bounce floor**
   `G`.

Measured limits: with the ball moving down onto a floor, a new `y`
4.9375 px above the floor touched and 5 px above didn't; a ball whose
`y` at the start of the frame was 8 px below a floor came back up onto
it, and 8.5 px below didn't. With the start of the frame in one column
that had no floor within reach and the new `x` in a column that had,
it bounced on the new column's floor.

## Ignoring floors

While the ball is ignoring floors, it moves to the new `y` = `y` + new
`vy` and passes through every floor, including the one it bounced on
and lower ones close below it (one 7 px below was passed). It stops
ignoring floors on the frame its new `y` is more than **47 px below**
its bounce floor: `y` > `G` + 47. From the next frame on, floors count
again. Measured: stopped at 47.0625 px below, still ignoring at exactly 47
px and at 46.9375 px below. It is the distance that counts, not the time: with
the ball's speed changed after the bounce, the number of frames until
it stopped ignoring changed and the distance didn't.

So a ball bounces once on each floor it reaches, rising 26.125 px
and falling back through it, and lands next on a floor more than 47 px
lower. A ball that bounces on a ledge drops through it to the ground
below.

## Leaving the picture

Let `V` be the view's horizontal position rounded down (as in
`walker-view.md`), `W` the view's vertical position rounded down (the
level y of the picture's top row, `view-vertical.md`), both **as they
were at the start of the frame**, `X` the ball's new `x` rounded down
and `Y` its new `y` rounded **up**. The ball is in the window while

    −31 ≤ X − V ≤ 288   and   −31 ≤ Y − W ≤ 256

that is, from 31 px left of and above the picture to 32 px right of and
below it (the picture is 256 by 224 px). A ball outside the window at
the end of a frame is removed on that frame, except on a frame on which
it bounced or stopped ignoring floors: then it stays, and is removed on
the next frame if it is still outside. Measured: kept at `X` − `V` =
288 and removed at 289, kept at −31 and removed at −32, kept at
`Y` − `W` = 256 and removed at 257 (with `W` from 187 to 230), kept at
−31 and removed at −32; kept on a frame that ended ignoring at
`Y` − `W` = 257 and on a bounce at `X` − `V` = 294, removed on the
frame after each. With the view moving, the start-of-frame position
fits every removal, and the end-of-frame position doesn't.

A removed ball doesn't come back.

## Balls placed in a level

A level can also place a ball directly. It appears on the first frame
whose `V` puts its placed `x` in the walker window (`walker-view.md`;
measured with the view moving right only), already one frame on: on its appearance frame it has made one frame of moves
from its placed position with its starting speeds. It isn't ignoring
floors, and from then on it moves as above. The one on the first level
started with `vx` −1.171875 (−300/256) and `vy` −3 (−768/256); it was
seen appearing 11 times, always one frame on and always at the same
position. Which speeds a placement gives isn't covered.

A placed ball appears only once. After it is removed it doesn't come
back, however the view moves, until play resumes after a knock-out or
comes back to the level after an exit:

- In the first level, after the ball had been removed, its entry left
  the window and came back into it 23 times, and no ball appeared. 20
  times the view had gone left, with `x` − `V` up to 457; 3 times it had
  gone right, with `x` − `V` down to −416. That is past the margins
  within which a walker stays (`walker-view.md`), on both sides.
- Twice the entry left the window (the view going left) while the ball
  was still there, with `x` − `V` up to 342. The ball wasn't removed
  then: it went on until "Leaving the picture" removed it, and didn't
  come back when the entry came back into the window.
- After a knock-out, play resumed at the restart point
  (`restart-barrel.md`, "Resuming"), left of the ball's entry, and the
  ball appeared again by the rule above when the view came back to it:
  9 times, always on the first frame whose `V` put `x` in the window,
  one frame on and at the same position as before. After 2 other
  resumes the view didn't get that far.
- After a knock-out before the restart point was reached, play resumed
  at the level's start (`restart-barrel.md`, "Resuming"), and the ball
  appeared again when the view came to it, one frame on and at the same
  position as before: 4 times, after the lead was moved next to the
  ball's entry. It did so whether the ball had appeared and been removed
  before the knock-out (2 times), was still there when the knock-out
  came (1), or had never appeared (1); when it had appeared before, it
  appeared again after the same number of frames from the move, and
  went on as before. Without a knock-out, the same moves away and back
  brought no ball.
- After leaving the level by either exit and coming back
  (`exits.md`, "Coming back to the level"), the ball appeared again when
  the view came to it, one frame on and at the same position as before,
  whether or not it had appeared and been removed before leaving.

## While the player is knocked out

When the player is knocked out (`knock-out.md`, hit frame H), the
thrower and every ball go on normally on H, then stop on **H + 1 to
H + 10**: each ball keeps its position and its speeds, no ball is
thrown (the timetable doesn't advance), and none appears or is removed.
How the thrower and the balls are drawn then isn't covered. During the 32-frame
pause between H + 7 and H + 8 nothing moves either (no logic frames
run). The ball that hit the player stops like the others; this differs
from walkers, where the one that knocked the player out walks on
(`walker.md`, "While the player is knocked out").

From **H + 11** everything goes on as if frames H + 1 to H + 10 hadn't
been there:

- A ball that was there on H is, on H + 10 + `n` (`n` ≥ 1), where it
  would have been on H + `n` without the knock-out, with the same
  speeds.
- The thrower's timetable ("When it throws") is 10 frames late: a throw
  that would have come on a frame `T` from H + 1 on comes on `T` + 10,
  in the same direction, and the ball then moves as it would have from
  `T`.

Leaving the picture and appearing still follow the view of each frame,
and the view (which doesn't move from H to H + 9, `view.md`) follows
the player, who moves differently during a knock-out; so a ball may be
removed earlier or later than it would have been, and a throw near the
window's edge may be seen or not ("A thrown ball").

Measured on the first level: 172 knock-outs near the thrower in random
play (in 84 of them the player was out of the thrower's own reach,
`thrower-contact.md`, so a ball hit them), each compared with the same
play from the frame before H with the hit prevented. On H, all 396
objects were as without the hit; on H + 1 to H + 10, 3 980 object
frames showed no change; after that, 220 balls that were there before
H and 467 balls thrown later were followed for 29 986 ball frames, each
exactly 10 frames behind the run without the hit, with no difference.
Each run was compared while the knock-out lasted, until the player in
the run without the hit was hit or bounced, or the thrower was knocked
out or removed in either run (removal starts the timetable again).
Whether a thrown ball is at its start or one frame on, on its throw
frame, differed between the two runs 131 times: the rule above holds
for the frame on which it is thrown, not for which of the two it is
(that is still not covered, "A thrown ball"). 5 throws were seen in one
run only, each with the ball one frame on outside the other run's
window.

A ball placed in the level ("Balls placed in a level") does the same:
it moves on H, keeps its position and speeds on H + 1 to H + 10, isn't
removed then, and from H + 11 is where it would have been 10 frames
earlier without the knock-out. Measured on the first level: 242
knock-outs in random play while its placed ball was there (in 192 of
them it was the only ball, and in all of them the ball nearest the
player just before H), each compared with the same play with the hit
prevented. It was as without the hit on H, unchanged on all 2 420
frames from H + 1 to H + 10, and 10 frames behind the run without the
hit on all 2 893 frames it was followed after that, while both runs
still had it (in 19 runs one of them had removed it by H + 11).

A thrower already knocked out (`thrower-contact.md`, "The knocked-out
thrower") also does the same. It falls as usual on H, keeps its
position and up speed on H + 1 to H + 10, and from H + 11 it is where it
would have been 10 frames earlier without the knock-out, with the same
speeds. It is never removed on H + 1 to H + 10, and it can't be outside
the window then: the view doesn't move from H to H + 9 (`view.md`,
"Frames on which the view stays still"), so on each of H + 1 to H + 10
the removal rule would see the same position and the same view as on
H, when it was kept. From H + 11 the removal
rule there applies again on every frame, with the view of that frame,
so it may be removed earlier or later in its fall than without the
knock-out. Measured on the first level: 354 knock-outs while the
thrower was falling, 3 to 41 frames after it was knocked out by a
bounce. The hit was set up by putting a ball where the player was. Each
knock-out was compared with the same play with the hit prevented. The
thrower was as in that run on H, unchanged on all 3 540 frames from
H + 1 to H + 10, and 10 frames behind it on all 3 384 frames compared
after that. In both runs it was inside the window on every frame it was
there, and outside it by its next position on the frame it was removed
(354 removals in each run, from H + 11 to H + 50 in the knock-out run).
In none of them was it outside the window on H + 1 to H + 10.

It is the same when a roll knocked the thrower out (`thrower-contact.md`,
"Rolling into the thrower"). Measured the same way on the first level:
356 knock-outs while the thrower was falling, 3 to 42 frames after the
player rolled into it from the left. The thrower was as in the run
without the hit on H, unchanged on all 3 560 frames from H + 1 to
H + 10, and 10 frames behind that run on all 9 204 frames compared after
that. Removal followed the rule above in both runs (356 removals in
each, from H + 11 to H + 50 in the knock-out run), and it was never
outside the window on H + 1 to H + 10.

It is the same when the ground action knocked the thrower out
(`ground-action-enemies.md`, "The thrower"), including when the player
is knocked out during that action. Measured the same way on the first
level: 154 knock-outs (143 different ones) while the thrower was
falling, 4 to 40 frames after the player, standing 45 px left of it,
knocked it out with the ground action; the player was still in the
action when hit. The thrower was as in the run without the hit on H,
unchanged on all 1 540 frames from H + 1 to H + 10, and 10 frames
behind that run on all 3 328 frames compared after that. Removal
followed the rule above in both runs (154 removals in each, from H + 11
to H + 47 in the knock-out run), and it was never outside the window on
H + 1 to H + 10.

## While a character is lost

When the player is hit with the other character present
(`losing-a-character.md`, hit frame H), nothing stops: the thrower and
every thrown ball go on from H exactly as they would have without the
hit, on the same frames. Throws come on the timetable's usual frames
("When it throws"), and each ball moves, bounces and leaves the picture
as usual. Leaving the picture and appearing still follow the view of
each frame, which follows the hit character and then the other one, so
a ball may be removed earlier or later than without the hit, and a
throw near the window's edge may be seen or not ("A thrown ball").

Measured on the first level: 180 hits with both characters near the
thrower in random play (in 158 of them the hit character was out of the
thrower's own reach, `thrower-contact.md`, so a ball hit it), each
compared with the same play from the frame before H with the hit
prevented. Each run was compared until the player in the run without
the hit was hit or bounced, either character in the run with the hit
was knocked out or bounced, or the thrower was knocked out or removed
in either run: in 127 runs that was H + 57 or later, in 107 H + 100 or
later. On all 24 274 frames compared no logic frame was paused and the
thrower was the same in both runs; 292 balls that were there before H
and 395 balls thrown later were followed for 26 255 ball frames, each
on the same frame and the same path in both runs, with no difference.
Whether a thrown ball is at its start or one frame on, on its throw
frame, differed between the two runs 54 times, as in "While the player
is knocked out". 8 throws were seen in one run only, each with the ball
one frame on outside the other run's window.

A ball placed in the level ("Balls placed in a level") that is there on
H goes on the same way: on every frame both runs have it, it is at the
same place with the same speeds. It is removed by "Leaving the picture"
with each run's own view at the start of the frame, so it can be
removed one frame earlier or later than without the hit.

Measured on the first level: 242 hits in random play with both
characters near the placed ball while it was there (from 47 to 74
frames after its appearance frame; it was removed after 74), each
compared with the same play from the frame before H with the hit
prevented, until the player in the run without the hit was hit or
bounced, or either character in the run with the hit was knocked out or
bounced (H + 199 or later in every run, so past the ball's removal).
No logic frame was paused on the 52 304 frames compared, and on all
3 379 frames where both runs had the ball it was at the same place with
the same speeds.
In 98 runs it was removed on the same frame in both. In the other 144
the run with the hit removed it one frame earlier, when it went below
the picture: with each run's view at the start of that frame, its `Y` −
`W` was 257 to 263 in the run with the hit, where it was removed, and
249 to 256 in the run without it, where it stayed. It never came back
in either run.

## Example

A thrower is at (1000, 300). In column 1000 there are floors at y 301,
331 and 381. On `T` = `A` + 2 it throws straight up, and the ball is at
its start on `T`. The view keeps the ball in the window. `x` stays 1000.

| Frame | `y` | `vy` | |
|---|---|---|---|
| `T` | 292 | −7 (−1792/256) | at the start |
| `T` + 1 | 285.4375 | −6.5625 | |
| `T` + 15 | 239.5 | −0.4375 | highest point |
| `T` + 16 | 239.5 | 0 | |
| `T` + 31 | 292 | +6.5625 | |
| `T` + 32 | 301 | −5 (−1280/256) | new `y` 299 is less than 5 px above floor 301: bounce, `G` = 301 |
| `T` + 33 | 296.4375 | −4.5625 | ignoring floors |
| `T` + 43 | 274.875 | −0.1875 | highest point after the bounce, 26.125 px above `G` |
| `T` + 54 | 301.6875 | +4.625 | passes floor 301 |
| `T` + 59 | 331.375 | +6.8125 | passes floor 331 |
| `T` + 61 | 346.3125 | +7.6875 | 45.3125 below `G`: still ignoring |
| `T` + 62 | 354.3125 | +8 | 53.3125 below `G`: stops ignoring |
| `T` + 63 | 362.3125 | +8 | |
| `T` + 64 | 370.3125 | +8 | floor 381 in reach, new `y` 10.6875 above it: no touch |
| `T` + 65 | 381 | −5 | new `y` 378.3125 is 2.6875 above floor 381: bounce, `G` = 381 |

Floor 331 never counted: the ball passed it while ignoring floors.

## Not covered

- Thrown objects (`thrown-objects.md`) meeting a ball or the thrower,
  and balls meeting walkers. The thrower touching the player, bouncing
  on it and rolling into it are in `thrower-contact.md`, balls touching
  the player in `ball-contact.md`, and the ground action knocks it out,
  `ground-action-enemies.md`.
- While the player is knocked out: a placed ball whose appearance would
  come on H + 1 to H + 10.
- While a character is lost (`losing-a-character.md`): a placed ball
  that appears after H, and a thrower already knocked out (only thrown
  balls, a placed ball already there on H and a standing thrower were
  measured), and the second character being the one hit.
- A ball moving up into a floor from below while not ignoring floors
  (it was seen to jump several px at the floor). This doesn't happen in
  the first level: balls start above the ledge and only bounce while
  falling.
- Walls and faces in a ball's way (none was met).
- Throwers facing right, other timetables, and throwers in other levels.
- What a placed ball's speeds come from.
- How the thrower and the balls are drawn.
