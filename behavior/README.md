# Behavior specs

One file per subject (e.g. `player-movement.md`, `entities/cannon.md`).
Use engine-neutral units: pixels, subpixels as fractions, frames at 60 Hz.
State conditions and edge cases explicitly; they are what the clean side
can't infer.

Directly under its heading, every file has a line naming the titles its
values were measured on (`../TITLES.md`):

```
Titles: `title-1`
```

Several ids are separated by commas (`` Titles: `title-1`, `title-2` ``).
A value holds only for the titles listed; where titles differ, the file
says so next to the value.
