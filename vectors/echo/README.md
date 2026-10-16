# Echo vectors

Synthetic vectors for `../../formats/echo.md`. The samples, memory
entries and cases are made up; none of them come from a game. Written by
the research side's generator with the weights of `voice-output.md`
("Blending"), and every case also run on an emulation of the console's
sound chip, which uses its own weights: its outputs differ from these by
at most 188 (in `mix-limits`; at most 98 in the other cases), and with
the console's weights in place of the formula every output and the echo
memory after the last output matched. Values are only handed where it
makes no difference on which of the next three outputs they count
(`voice-output.md`, "When handed values count").

## Input

`cases.tsv`: one case per row, tab-separated. The columns are those of
`../voice-output/cases.tsv` with one more:

| Column | Meaning |
|---|---|
| `case` | name |
| `first_count` | the shared count `c` (`voices.md`) on the case's first output (output 0) |
| `delay` | the echo delay `d` |
| `outputs` | how many outputs the case runs |
| `samples` | as in `../voice-output/` (empty when the case has no voice) |
| `events` | as in `../voice-output/`, with the echo events below |

Echo events (numbers decimal; bytes as in `echo.md`):

- `k echo vol l r`: echo volume left `l`, right `r`.
- `k echo feedback b`: the feedback.
- `k echo filter c0 c1 c2 c3 c4 c5 c6 c7`: the 8 filter values.
- `k echo writing on` / `k echo writing off`.
- `k vN echo on` / `k vN echo off`: echo on or off for voice `N`.
- `0 echo entry i l r`: entry `i` of the echo memory holds `l`, `r`
  before output 0.

Before output 0: everything as in `../voice-output/`; the echo memory
all 0 except the entries given, the position 0, the history all 0, echo
volume, feedback and filter values 0, echo writing off, echo off for
every voice.

What the cases exercise:

- `unit-delay`: the example in `echo.md`.
- `feedback`: a one-shot voice with echo on and a looping voice with
  echo off; feedback 90, so the echo comes round again and again.
- `delay-zero`: echo delay 0: one entry, read and rewritten on every
  output, with all eight filter values.
- `writing-off`: entries set before output 0 with echo writing off:
  heard on every pass, unchanged; echo writing on from output 700.
- `filter-limits`: full-scale entries and filter values all 127: the
  sum of the first seven parts wraps and the sum is limited.
- `last-part`: `c7` −128 on −16 384 gives 32 768, which wraps to
  −32 768; echo volume −128 on −32 768 wraps likewise.
- `mix-limits`: four loud voices with echo on: limited echo input,
  limited written values, outputs limited with the echo part.
- `changes`: settings handed while nothing sounds: echo writing off (a
  note with no echo), then on with another echo volume, feedback and
  filter.
- `delay-4`: echo delay 4 (2 048 outputs), as in `title-1`, with a
  made-up filter of the shape `title-1` songs use.

## Expected output

`outputs.tsv`: tab-separated `case`, `output`, `left`, `right`, one row
for every output of every case.
