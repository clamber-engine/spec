# Voice vectors

Synthetic vectors for `../../formats/voices.md` (the level). The cases
are made up; none of them come from a game. Written by the research
side's generator from the rules in the prose, and every case also run on
an emulation of the console's sound chip: every level matched.

## Input

`cases.tsv`: one case per row, tab-separated:

| Column | Meaning |
|---|---|
| `case` | name |
| `first_count` | the shared count `c` on the case's first output (output 0) |
| `outputs` | how many outputs the case runs |
| `events` | `;`-separated, each `k what`: handed before output `k` (so they count from output `k` on) |

Events:

- `k set e1 e2 g`: the voice is handed envelope settings `e1`, `e2` and
  gain `g` (decimal).
- `k start`: a start. It takes back a release still handed.
- `k release`: a release. It stays handed until the next start.

Events with the same `k` are handed in the order listed. Before output
0 the voice has never started: level 0, in release, settings 0.

What the cases exercise:

- `slow-attack`, `attack-r3`, `attack-r7`: attacks at rates 1, 3 and 7
  (periods 2048, 1280, 512; the three kinds of `q`), from counts 1, 300
  and 1000.
- `attack-to-2016`: an attack that ends at 2016 and decays from there.
- `fast-attack-decay-sustain`: attack at 1024 a step, decay to a line,
  then sustain at its own rate.
- `decay-line`: a slow decay stopping one step above the line.
- `sustain-fast`: a held note with a fast sustain rate down to 0.
- `release`, `release-mid-attack`: releases from the top and from the
  middle of an attack.
- `fixed-level`, `fixed-level-change`: the fixed level (gain 127 and 37),
  and a change to the envelope in the middle of a note.
- `restart-sounding`, `restart-other-parity`: starts while sounding, with
  the start handed before an odd and an even count.
- `lost-start`: a start at the check right after another start.
- `start-and-release`: a release handed after a start, before its check.
- `change-mid-note`: new sustain settings and a new attack setting
  during a note (the part doesn't change).

## Expected output

`levels.tsv`: tab-separated `case`, `output`, `level`: the level on
output 0 of each case, then a row for every output where the level is
different from the output before. Levels between rows are the same as
the row before.
