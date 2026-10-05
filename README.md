# pdf-extract

A Claude Code skill that extracts a PDF to raw markdown with `pymupdf4llm`. It writes `[base]_raw.md` plus an `images/` folder, and renames captioned figures to `Fig. N. Caption.png`. No formatting is applied.

## Install

```
npx skills add ellebelle99/pdf-extract -g
```

Without Node: copy `skills/pdf-extract/` into `.claude/skills/` (project) or `~/.claude/skills/` (global). Copied skills are not tracked by `npx skills update`.

Start a new Claude Code session afterwards so the skill loads.

## Use

```
/pdf-extract path/to/file.pdf
```

The skill asks for an output folder and a base filename (`Author_YEAR_ShortTitle`), then runs the extraction. `pymupdf4llm` must be installed; the skill explains how to set up a throwaway venv if it is not.

## Optional

If this was useful, a star is appreciated. Watch the repo to see updates.
