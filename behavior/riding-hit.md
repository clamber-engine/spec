# Being hit while riding

Titles: `title-1`

What an enemy that reaches the player riding the animal (`riding.md`)
costs: **nothing but the animal.** The rider is knocked off, the animal
runs away, and the player carries on on foot with the same lives and the
same characters. The player is **protected** from the next hit for 128
frames. After that an enemy hurts as it does any player on foot
(`knock-out.md`, `losing-a-character.md`, `swap.md`).

Frames are logic frames at 60 Hz, numbered as in a trace. "Knocked off",
the frame **H** of the knock-off and the frozen frames are as in
`riding-walkers.md`, "The knock-off".

Evidence: on-screen situations observed in the first level with the
first character alone and with both characters (the first leading and the
second leading, the other character on foot behind). In the observed
runs an enemy was touching the rider from a chosen frame on, or walked up
to it, and the run was followed until the player was hurt or for at least
150 frames. A setup method was used to get the enemy and the rider into
those situations. These rules were not checked in ordinary play.

## The hit

- A walker (`walker.md`) that reaches the rider knocks it off, by the
  rules of `riding-walkers.md`. With one character, and with two (whichever
  leads), the number of lives and the characters in the level were the
  same before H and at H + 127, with nothing lost to the knock-off itself.
- A stinger (`stinger.md`) that touches the rider knocks it off on the
  frame after the contact, the same way. The ranges of the contact were
  not measured separately; a stinger that is on the rider at the start
  of a frame knocks it off on the next one.
- The knock-off, the frozen frames and the animal's flight are those of
  `riding-walkers.md`. The animal does not come back: after it leaves
  it runs on in the direction the rider faced, turns at a wall as
  `riding-walkers.md` gives, and is removed when it is far enough off the
  view or falls out of the level. The player cannot get on it again in
  these runs (the measured animal was always out of reach before the player
  had landed on foot).
- The other character, when there is one, is not affected by the
  knock-off: it goes on following the leader as before.

## The protection

From **H** the player cannot be hurt by an enemy for **128 frames**: a
contact on any frame from H to H + 127 does nothing, whether the player is
in the knock-off, falling, landing or standing. The first frame on which
the player can be hurt is **H + 128**: an enemy touching the player at the
end of H + 127 hurts it on H + 128. An enemy that stays on the player all
the time hurts it on H + 128; one that arrives later hurts it on the frame
after it arrives.

Frames held still by bouncing off a walker don't count. If the falling
player bounces off a walker (`bounce.md`) during the protection, the
first frame on which it can be hurt is **H + 130**. This was measured
with one bounce, at several heights above the walker; two bounces were not
measured.

Observed: with one character, a stinger and a walker each touching the
player from frame H on, or from a later frame up to H + 129, and a walker
arriving by itself: the hurt came on H + 128 in every run (no bounce). With
both characters (first and second leading), a walker touching the player
and the fall bouncing off it: the hurt came on H + 130 in every run. The
no-bounce case with two characters and the bounce case with one character
were not observed; the rule for them is expected to be the same but is
not confirmed.

## Worked rows

One character riding (the frame before H in a charge), an enemy touching the rider and staying on the player afterwards:

| Frame | Rider | Lives |
|---|---|---|
| H − 1 | riding (charge) | 5 |
| H | knocked off | 5 |
| H + 1 to H + 15 | frozen | 5 |
| H + 16 to about H + 50 | falling, landing | 5 |
| about H + 51 to H + 127 | on foot, the stinger still on the player | 5 |
| H + 128 | hurt | 5, then 4 ten frames later (`knock-out.md`) |

## Not covered

- The protection length for two characters without a bounce, and for one
  character with a bounce.
- Enemies other than walkers and stingers (thrown balls, flying
  creatures, thrown barrels) reaching a rider.
- The contact range of a stinger against a rider.
- Whether the protection stops other things than hurt (picking things up,
  bunches, other contacts).
- Two or more bounces in the protection, and riding with the walker kinds
  not listed in `riding-walkers.md`.
- Riding a different animal.
