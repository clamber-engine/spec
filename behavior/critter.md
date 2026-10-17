# Critters

Titles: `title-1`

A **critter** is a small winged creature that flies about in a level.
It is scenery: nothing in play touches it. Characters, the animal,
walkers and thrown objects pass it as if it weren't there, it is never
taken, it doesn't hurt, and it changes no count. This file covers when
a critter appears and is removed as the view moves, where it appears,
and what it does to play (nothing). How it flies is not covered.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`; `y` grows
downwards.

The first level holds two critters, both of the one kind `critter`: one
near the level's start and one just right of the animal crate.
Measured with the first character leading and alone, using the level's
own critters and copies of them (see "Checks").

## Positions used

- `S` is the critter's entry's level x (`../formats/placements.md`,
  "Positions").
- `X` is the critter's own level x, rounded down to a whole pixel, and
  `y` its level y. They change as it flies.
- `V` is the view's position, rounded down to a whole pixel, as it was
  at the start of the frame, and `U` the view's vertical position (the
  level y of the picture's top row, `view-vertical.md`), rounded down
  the same way.
- The **window** is as in `../formats/placements.md` ("When placed
  objects appear"): a horizontal position `p` is in it when
  −31 ≤ `p` − `V` ≤ 288.

## Appearing

A critter that isn't there appears on the first frame on which its
entry is in the window, as for every class 1 or class 2 entry
(`../formats/placements.md`, including the rule for `V` less than 32).

Unlike other placed objects, a critter doesn't appear at its entry's
position. It appears at the top of the picture: in the 304 appearances
measured, `y` − `U` was from −7 to 2 and `X` − `V` from 14 to 245, so
it was near the top edge and inside the picture's width. Where across
the picture it appears was different from one appearance to the next;
how that place is chosen is not covered. From there it flies down
towards its entry's position.

On the level's first frame, the critter near the level's start is
already there (its entry is in the window). In the runs measured it
stood still for the first 41 frames, 90 px left of its entry and at the
entry's level y, and was then at the top of the picture as after an
appearance. What it does on those 41 frames is not covered.

## Flying

While it is there, a critter flies about, with a small up-and-down
flutter, and at times stays still for a while (up to 360 frames in the
runs). In the runs it was from 87 px left to 25 px right of its entry,
and from 266 px above to 50 px below its entry's level y. Its path, and
where and for how long it stays still, are not covered.

## Removal

A critter that is there is removed on the first frame on which **both**
of these hold:

1. its entry is outside the window (`S` − `V` < −31 or
   `S` − `V` > 288), and
2. the critter itself, after that frame's move, is outside the window:
   `X` − `V` < −31 or `X` − `V` > 288.

So a critter isn't removed while it is in the window, even if its entry
has left it, and isn't removed while its entry is in the window, even
if it has flown out of it. In the runs, critters whose entry was outside
the window were there with `X` − `V` from −31 to 288 (−31 on 33 frames,
288 on 82 frames) and were removed at −32 or less and 289 or more.

A removed critter appears again, as above, the next time its entry is
in the window. This can happen any number of times.

Once, a critter whose entry was outside the window was removed while
flying up past the top of the picture (`y` − `U` went from about −30
to below −31) with `X` inside the window. Removal above or below the
picture is not covered otherwise.

## What a critter does

Nothing. With a critter in the picture, play went exactly as with the
same critter's entry placed 1 000 px higher, out of reach (see
"Checks"): the characters' positions, speeds, actions and facing, the
lives, the collectible count and its display, the figure count, the
letters taken, and every object that isn't the critter's own were the
same on every frame, with one exception below. In particular:

- The player walks, runs, rolls, crouches and jumps through a critter,
  also when it is lowered to the player's height; it is neither a wall
  nor a floor, and it isn't taken.
- Riding the animal through it changes nothing.
- Walkers walk past it, and a thrown barrel flies past it.

The exception: the place of the sparkles shown at a letter
can differ when a critter has flown differently. With one set of
inputs a sparkle at a letter was 2 px left and 13 px lower than in the
other copy. Nothing else differed in those runs.

At times a second object belonging to the second critter appeared
about 4 px left of and 2 px above its entry's position. It was there in
one copy and not, or elsewhere, in the other in 11 of the 270 runs at
the second critter, and changed nothing else either. What it is and
when it appears are not covered.

## Checks

Each "does nothing" check compared two copies of the image, the same but
for the critter's entry's height: in one the critter was at its own
height or lowered to the lead's height, in the other the same entry put
it 1 000 px higher. Both were played with the same inputs, one input per
logic frame, and compared on every frame until play stopped (a
knock-out's fade ends a run). The critter itself was left out of the
comparison.

- **The lead.** The level's second critter at its own height and
  lowered to two heights at which it flies around the lead: 60 runs
  per height of 900 frames of random inputs (walking and running both
  ways, crouching, rolling, jumping, the ground action, standing still,
  breaking the animal crate and riding the animal) after walking
  towards it, 47 513 frames per height, with the lead within 32 px of
  the critter horizontally and from 16 px below to 48 px above it on
  1 916, 3 499 and 5 044 of them, and the animal within 24 px of it on
  about 1 100 frames at each lowered height (within 24 px
  horizontally and 40 px vertically).
- **The level's start.** The first critter from the level's first
  frame, at its own height and lowered: 30 runs per height, 27 146
  frames per height, about 1 200 with the lead near it.
- **Walkers.** The lowered second critter with the small walkers to its
  right: 30 runs, 12 690 frames, 2 700 of them with a walker within
  24 px of the critter.
- **Thrown objects.** The animal crate's entry made a barrel in both
  copies: the lead picks it up, carries it and throws it right through
  the critter, then 900 frames of random inputs; 30 runs at each of two
  heights, 23 487 frames per height.

In all these runs (330 runs, 256 495 frames), the characters, lives and
counts never differed. Other objects differed only as said in "What a
critter does": the sparkle in 3 runs (one set of inputs, at the three
heights of the second critter) and the critter's second object in 11
runs.

- **Appearing and removal.** Random walks, runs and jumps both ways
  around both critters, from four places in the level and from the
  level's first frame, 340 runs: 208 120 frames, 304 appearances and
  443 removals. On every frame the critter was there exactly when the
  rules above say (the critter is gone on the frame of its removal,
  so there `X` was taken as its x on the frame before plus its speed
  then, give or take 1 px: 440 of the 443 removals needed no margin), but for the one removal above the picture.
- **The kind.** The critter's reference given, in a copy of the image,
  to a class 1 entry near the level's start in place of its own placed
  a critter there, which appeared at the top of the picture and flew as
  above.

## Not covered

- How a critter flies: its path, where it stays still and for how long,
  where across the picture it appears, and its first 41 frames at the
  level's start.
- Removal above or below the picture, and critters whose entry is far
  above or below the view.
- The second object that appeared near the second critter (see "What
  a critter does").
- The second character, leading or following.
- Enemies other than small walkers, the thrower's balls, and objects
  other than a thrown barrel meeting a critter.
- How a critter is drawn; critters in other levels.
