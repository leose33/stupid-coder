# stupid-coder

A Claude skill that writes (or rewrites) Python so people who *know* Python but haven't *applied* it much can read and maintain it.

**Target reader:** finance/economics students and graduates who took a Python-for-finance course (NumPy, pandas, OOP, standard library) and used scikit-learn in a thesis or small project.

**What it does**
- Descriptive names, one step per line, small functions
- A short header comment before every function and class
- Inline comments on practical patterns, pitfalls (look-ahead bias, leakage, index alignment) and unusual arguments, not on basics
- Calibrates to your own code when it's available
- Optional Mermaid chart of how the functions call each other

## Install
- **Claude.ai / desktop app:** upload `SKILL.md` under Settings → Skills (or upload the zipped folder).
- **Claude Code:** copy the folder to `~/.claude/skills/stupid-coder/`.

Then call it with `/stupid-coder` or "use Stupid Coder on this".

## Adapt it to your level
Edit the **Appendix: assumed knowledge** section at the end of `SKILL.md`: move topics into or out of "Not assumed".

## Credits
Designed by Leon Schmitz-Elvenich; drafted and refined together with Claude (Anthropic).
