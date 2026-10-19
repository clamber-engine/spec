# The picture game

Titles: `title-1`

The **picture game** is a row of three **places**, each showing pictures
of four animals in turn. The lead stops a place by jumping into it from
below; a stopped place keeps its picture. If all three places end up
with the same animal, a figure of that animal (`figure.md`) drops to the
floor for the lead to take; as soon as two stopped places differ, the
game is lost. Either way the area then ends. The first level's area
behind its second wall exit holds one picture game and nothing else
(`wall-exits.md`, "The area behind the second wall exit"); this file
covers the game there, measured with the lead alone.

Frames are logic frames at 60 Hz, numbered from the area's frame 0
(`wall-exits.md`). Horizontal positions are level x as in `view.md`;
vertical positions are level y as in `view-vertical.md`, growing
downward. `X` is the lead's `x` rounded down and `F` its feet line
(`view-vertical.md`). "Jump" is the neutral input `J` of the scenario
format. `T` is the take-off frame of a jump (`jump-arc.md`).

## The places

A placement-list entry of kind `picture-game`
(`../formats/placements.md`, "Kinds") at (`xe`, `ye`) makes the game.
Its three places, numbered `j` = 1, 2, 3 from left to right, are at

    (xp, yp) = (xe + 64 (j − 2), ye)

so place 2 is at the entry's position and the others 64 px to either
side. In the area behind the second wall exit, `ye` is 81 px above the
floor the lead walks on.

## The animals

There are four **animals**, numbered 1 to 4. Each has its own figure
count (`figure.md`, "The figure count"); animal 2 is the animal of the
first level's own figure.

## The pictures turning

Each place shows one picture or none on each frame. A place that hasn't
been stopped **turns**: on frame `f`, let

    u = f − 3 − j

Place `j` shows no picture if `u` < 0 or `u` mod 19 is 16, 17 or 18;
otherwise it shows animal

    ((j − 1 + ⌊u / 19⌋) mod 4) + 1

So each picture is shown for 16 frames, followed by 3 frames with none,
and the four animals come in order 1, 2, 3, 4, 1, … The places are one
frame and one animal apart: place 1 shows animal 1 from frame 4 to 19,
place 2 animal 2 from frame 5 to 20 and place 3 animal 3 from frame 6
to 21; then the three show nothing on frames 20 to 22, 21 to 23 and 22
to 24, and animals 2, 3 and 4 from frames 23, 24 and 25. The places show
nothing on frame 0.

This doesn't depend on what the lead does, on when the area was
entered, or on the other places being stopped (until the game is
decided, below).

## Stopping a place

A turning place `j` is **stopped** on frame `n` when all of these hold at
the end of frame `n`:

1. it was turning on frame `n` − 1, and the game was not decided before
   `n` (see "Deciding");
2. the lead is in the air in a jump, and `n` ≥ `T` + 6;
3. `F` − `yp` ≤ 57: the lead's feet are at most 57 px below the place
   (with `ye` 81 px above the floor: at least 24 px above the floor);
4. `X` − `xp` is from −21 to 19 when the lead faces right, and from −24
   to 16 when it faces left.

So a lead standing under a place and jumping stops it on `T` + 6,
whether Jump is held or not. A jump that starts away from the place and
comes under it stops it on the first frame from `T` + 6 on that ends
inside these ranges, as long as the lead is still high enough. The
three ranges of `X` − `xp` don't overlap, so a frame stops at most one
place. A stopped place doesn't turn again.

**The picture kept.** From `n` on, the stopped place shows the picture it
showed on frame `n` − 1. If it showed none on `n` − 1, it shows the next
picture its turning would have shown.

For example, a lead standing under place 1 that presses Jump on frame
100 takes off on 101 and stops place 1 on 107. On frame 106 place 1 had
`u` = 102, with 102 mod 19 = 7, so it showed animal ((0 + ⌊102 / 19⌋)
mod 4) + 1 = 2; it keeps animal 2.

## Deciding

Call **`E`** the first frame on which either

- a place is stopped that leaves two stopped places showing different
  animals (**lost**), or
- the third place is stopped, all three showing the same animal
  (**won**).

So the game is lost as soon as two stopped places differ, even with one
place still turning. A game that is never decided goes on as long as
the lead stays in the area: nothing else ends it.

From `E` on, no place can be stopped. A place still turning on `E` shows
on `E` what its turning gives, and from `E` + 1 on the picture it showed
on `E`, or, if it showed none on `E`, the next picture its turning would
have shown.

## A lost game

- **The places go.** On `E` + 32 and after, no place is shown.
- **The lead stops.** Call **`Q`** the first frame from `E` + 34 on that
  ends with the lead on the ground. From `Q` on, the lead stays where it
  is on `Q`, whatever is pressed or held. If the lead is
  standing, walking or running on the ground on `E` + 33, `Q` is `E` +
  34; if it is in the air in a jump, `Q` is the frame it lands.
- **Leaving.** `Q` + 368 is the last frame in the area. The brightness
  of the whole picture is as in `exits.md` ("Leaving the area") with its
  `F` = `Q` + 338: 15 up to `Q` + 338, `15 − j` on `Q` + 337 + 2`j` and `Q`
  + 338 + 2`j`, 0 on `Q` + 367 and `Q` + 368. On the next logic frame,
  play is back in the first level (`wall-exits.md`, "Coming back from
  the second wall exit's area").

The number of lives and the figure counts don't change.

## A won game

Let `a` be the animal the three places show.

1. **Waiting.** From `E` to `E` + 67 the three places stay where they
   are, showing animal `a`.
2. **Sliding together.** On `E` + `k`, for `k` from 68 to 83, place 1 is
   at level x `xe` − 64 + 4 (`k` − 67) and place 3 at `xe` + 64 − 4 (`k`
   − 67): they move 4 px a frame toward place 2 and are all at `xe` on
   `E` + 83 and `E` + 84.
3. **The drop.** From `E` + 85 the three are replaced by one picture of
   animal `a` at level x `xe`, which rises, falls to the floor and
   bounces twice (below). On the frame it comes to rest it becomes a
   **figure** of animal `a` resting on the floor at level x `xe`: on `E`
   + 151, or `E` + 150 for animal 2.
4. **The figure** stays there until the lead takes it: it was still
   there 1 421 frames or more after `E` in every run where it wasn't
   taken. Taking it adds one to animal `a`'s figure count and to no
   other (`figure.md`); the number of lives doesn't change. It can also
   be taken while it is still dropping. When the lead takes it is not
   covered (see "Not covered").
5. **The lead stops** on a frame `Q` after the figure is taken: in the
   runs, `Q` was from 31 to 83 frames after it. From `Q` on, the lead
   stays where it is, whatever is pressed or held.
6. **Leaving.** `Q` + 342 is the last frame in the area, with the
   brightness of a lost game's leaving but with `F` = `Q` + 312, and
   play then goes back to the first level as for a lost game.

### The drop

Heights here are px above the figure's resting point, a point on the
floor at level x `xe`. On `E` + 85 + `k` the picture is at height `s` +
`r`(`k`), with the start `s` and the steps `r` below, until the first
frame on which that would be 4 or less: on that frame it is at 0 (it
lands), and the first bounce starts.

| Animal | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| `s` | 97 | 94 | 95 | 99 |

`r`(0) to `r`(33) are 0, 4, 8, 11, 14, 16, 18, 20, 21, 21, 22, 21, 21,
20, 18, 16, 14, 11, 7, 4, −1, −5, −10, −16, −22, −28, −35, −42, −50,
−58, −66, −74, −82, −90, and `r`(34) is −98. So it lands on `E` + 119,
or `E` + 118 for animal 2.

On the frames after it lands, its heights are

    4, 8, 12, 15, 18, 20, 22, 24, 25, 25, 26, 25, 25, 24, 22, 20, 18, 15, 11, 8, 0

(the first bounce, landing on the 21st of these frames), then

    2, 4, 6, 7, 8, 8, 8, 8, 7, 5, 0

(the second bounce). The frame of the last 0 is the frame it comes to
rest and becomes the figure.

For example, for animal 1: 97 on `E` + 85, 119 on `E` + 95 (`r`(10)),
7 on `E` + 118, 0 on `E` + 119, 26 on `E` + 130, 0 on `E` + 140, 8 on
`E` + 145 to `E` + 148, and resting from `E` + 151.

## Checks

The game was played from the area's frame 0 in 3 201 runs, the lead
alone, entering the area 0 to 60 frames after the wall exit opened so
that the area began at different times:

- 2 000 runs with random play: the lead walked to a random point up to
  40 px either side of a place, chosen in random order, and jumped with
  Jump held for a random 3 to 40 frames after a random wait, then moved
  on; after the game, random input of every kind or none.
- 1 200 runs in which each jump was pressed on a frame chosen from the
  turning so as to keep a chosen animal: all three the same in most runs,
  one different in a quarter of them; up to 14 px either side of the
  place.
- 1 run with nothing held for 7 200 frames.

Every frame was judged against the rules above:

- **Turning:** on every frame a place was turning, up to `E` (5 773 715
  frames judged for a stop), its picture was as "The pictures turning"
  gives.
- **Stopping:** every frame on which a place could be stopped was judged
  by the four conditions: 7 217 stops, each on exactly the frame they
  give, and no stop on any other frame. 6 707 of the stops came on `T` +
  6, the rest later. In the air at `T` + 6 or later, just outside the
  ranges, there was no stop on 7 485 frames (`X` − `xp` of −22 or 20
  facing right, −25 or 17 facing left), nor on 34 frames inside them
  with the feet 58 px or more below the place. In the ranges on `T` to
  `T` + 5 (47 366 frames) no place was stopped.
- **The picture kept:** every one of the 7 217 stops, 587 of them on a
  frame after one with no picture.
- **Deciding and the places after it:** 1 433 games lost on the second
  stop, 358 on the third, 975 won, 435 never decided (still going when
  the run ended); every place still turning on `E` showed the picture
  given above on each of 44 423 frames after it.
- **Lost games:** the places were gone from `E` + 32 in all 1 791;
  `Q` was the first frame from `E` + 34 ending on the ground in all 1 297
  whose lead only stood, walked, ran or was in the air in a jump from `E`
  + 33 on; the lead stayed where it was from `Q`; `Q` + 368 was the last
  frame in the area, the brightness as given on every frame from `Q`,
  and play then came back to the first level, in all 1 789 lost games
  whose run reached it.
- **Won games:** in the 900 won games of the second kind, the slide and
  the drop were as given above on every frame up to the one the figure
  was taken on, the same in every game with the same animal. Of all 975
  won games, the figure was taken in 649: each time that animal's count
  went up by one and no other count or the lives changed, and `Q` + 342
  was the last frame, with the brightness as given. In the other 326 the
  figure was still on the floor when the run ended, 1 421 frames or more
  after `E`.
- **The entry decides where.** In a copy of the image with the game's
  entry moved 24 px left and 16 px up, the places were at the moved
  position, 64 px apart, in all 40 runs; the stops were as above with
  the moved `xp` and `yp` (97 stops), a place being stopped with `F` −
  `yp` up to 57 (the feet then at least 40 px above the floor).

## Not covered

- The second character, two characters in the area, and riding.
- When the lead takes the dropped figure, whether resting or dropping.
  The ranges of `figure.md` gave the frame it was taken in 22 of 58
  runs judged; in 23 it was taken one or two frames after the first
  frame they give, and in 13 on a frame they don't give at all.
- When the lead stops after a won game (`Q`), and after a lost game when
  it is rolling, doing the Down + Action ground action
  (`ground-action.md`) or taking off from `E` + 33 on (`Q` then came 1
  to 54 frames later than the first frame on the ground).
- Being stopped by other things than the lead's jumps (thrown objects,
  rolls into the air, bounces).
- How the places, the pictures and the figure are drawn, and the
  sounds.
- Picture games in other levels.
