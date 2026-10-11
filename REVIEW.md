# Gatekeeper review checklist

A spec change describes **what happens**, never **how the original
program does it**. Before approving, confirm the change contains none of:

- [ ] Memory addresses, bank/offset locations, ROM offsets, or pointers
- [ ] Routine names, labels, or variable names from any disassembly
- [ ] Step-by-step paraphrases of code ("load X, compare with Y, branch...")
- [ ] Bytes copied from the ROM, including as "examples" (vectors must be synthetic)
- [ ] Assets or transcriptions of creative content (level layouts, graphics, music)
- [ ] Original character names, item names, or other trademarked terms

And that it does contain:

- [ ] Behavior in observable, engine-neutral terms (units, frames, conditions)
- [ ] Format descriptions as structure (fields, encodings, algorithms in prose)
- [ ] Synthetic vectors for any decoder it specifies
