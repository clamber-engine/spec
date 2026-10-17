# Signs

Titles: `title-1`

A **sign** is a board on a post placed in a level. It is scenery: it
stands where it is placed and nothing in play touches it. Characters,
walkers and thrown objects pass it as if it weren't there, it is never
taken, it doesn't hurt, and it changes no count. This file covers where
a sign is, when it appears and is removed as the view moves, and what
it does to play (nothing).

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`.

The first level holds two signs, near its end, with different pictures
(one shows an arrow, the other a word). Both are of the one kind `sign`;
the picture belongs to the reference, and how a sign is drawn is not
covered. Measured with the first character leading and alone, using the
level's own signs and, in a copy of the image, copies of each of the
two placed near the level's start (see "Checks").

## A sign's position

A level's placement list places each sign with an entry of kind `sign`
(`../formats/placements.md`, "Kinds"). A sign's position is its entry's
position: level x `x` and level y `Yb` − `h`
(`../formats/placements.md`, "Positions"). A sign never moves: in every
frame of every run in "Checks" it was at its entry's position.

## Appearing and being removed

`V` is the view's position, rounded down to a whole pixel, as it was at
the start of the frame, and the **window** is as in
`../formats/placements.md` ("When placed objects appear"):
−31 ≤ `x` − `V` ≤ 288.

- **Appearing.** A sign that isn't there appears on the first frame on
  which its entry is in the window, as for every class 1 or class 2
  entry (`../formats/placements.md`). On the level's first frame, the
  signs already in the window are there.
- **Removal.** A sign that is there is removed on the first frame on
  which `x` − `V` ≤ −128 or `x` − `V` ≥ 385. These are the distances at
  which a walker that is away from its start is removed
  (`walker-view.md`); a sign is always at its start.
- **Coming back.** A removed sign appears again, as above, the next
  time its entry is in the window. This can happen any number of times.

So while `x` − `V` is from −127 to −32 or from 289 to 384, a sign is
there if it was there on the frame before and not there if it wasn't.

Measured edges: a sign was there at `x` − `V` = −127 and 384 and removed
at −128 and 385; it appeared at 288 coming in from the right (not at 289
or 290) and at −31 coming in from the left (not at −32 or −33).

## What a sign does

Nothing. With a sign in the picture, play went exactly as with the same
sign placed out of reach (see "Checks"): the characters' positions,
speeds, actions and facing, the lives, the collectible count and its
display, the figure count, the letters taken, and every other object
were the same on every frame. In particular:

- The player walks, runs, rolls, crouches and jumps through a sign,
  and lands where it stands as if it weren't there. It is neither a
  wall nor a floor, and it isn't taken.
- Walkers walk past a sign without turning.
- A thrown object flies past a sign without breaking, bouncing or
  slowing.

In the pictures checked (the lead standing at each of the two signs),
the sign was drawn over the lead where they overlapped.

## Checks

Each check compared two copies of the image, the same but for one
entry's height: in one the sign stood on the floor, in the other the
same entry put it 1 000 px higher, out of reach. Both were played with
the same inputs, one input per logic frame, and compared on every frame
until play stopped (a knock-out's fade ends a run).

- **The lead.** A copy of each sign placed on the floor near the level's
  start: 60 runs per sign of 900 frames of random inputs (walking and
  running both ways, crouching, rolling, jumping, the ground action,
  standing still) after walking towards the sign, 54 459 frames per
  sign, 10 923 of them with the lead within 48 px of the sign
  horizontally and 80 px vertically. Differences: 0.
- **Walkers.** A copy of each sign placed on the floor on which a small
  walker patrols: 31 runs per sign, 22 888 frames per sign, about
  3 800 of them with a walker within 24 px of the sign. Differences: 0.
- **Thrown objects.** A copy of each sign placed on the floor, with a
  barrel placed before it in both copies: the lead picks up the barrel
  and throws it right through the sign, then 900 frames of random
  inputs; 30 runs per sign, 27 253 frames per sign. The barrel passed the
  sign in every run. Differences: 0.
- **Appearing and removal.** The two copies near the level's start and
  the level's own signs, walking, running and jumping away from and
  back to them in both directions, 41 runs: 10 920 frames, with 21
  appearances (14 from the right, 7 from the left) and 20 removals (11
  on the right, 9 on the left). On every frame the sign was there
  exactly when the rules above say.

## Not covered

- The second character, leading or following.
- Signs far above or below the view. The copy placed 1 000 px above
  its floor was missing on some frames on which its entry was in the
  window.
- Enemies other than small walkers, the thrower's balls, the animal and
  riding, and objects other than a thrown barrel meeting a sign.
- How a sign is drawn, beyond what "What a sign does" says; signs in
  other levels.
