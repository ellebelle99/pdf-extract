---
name: pdf-extract
description: Extract a PDF to raw markdown using pymupdf4llm — no formatting, just extraction. Auto-renames captioned figures.
allowed-tools: Bash
argument-hint: "[path/to/file.pdf]"
---

# /pdf-extract

Extract a PDF to raw markdown using pymupdf4llm. Saves as `[base]_raw.md` with images alongside. No formatting — run `/pdf-format` on the output next.

> **Never use markitdown** — it produces character-spaced, table-artefact-heavy output on academic PDFs (confirmed on Ahuja & Lampert 2001). pymupdf4llm only.

## If pymupdf4llm is not installed

The command below fails with `ModuleNotFoundError: No module named 'pymupdf4llm'` when it is missing from the system `python3`. **Build a scratchpad venv first and call its interpreter by absolute path** — don't install into system Python:

```bash
python3 -m venv <scratchpad>/venv && <scratchpad>/venv/bin/pip install -q pymupdf4llm
```

Then run every snippet below with `<scratchpad>/venv/bin/python` (macOS/Linux) or `<scratchpad>\venv\Scripts\python.exe` (Windows) instead of `python`. Use `import pymupdf`; `import fitz` prints a deprecation warning. `pymupdf` comes along as a dependency, which is what renders pages to PNG for the visual check.

## Before starting, ask

1. Output folder and base filename? (e.g. `Papers/Dynamic_Capabilities` / `Teece_2007_Explicating_Dynamic_Capabilities`)
   Follow the `Author_YEAR_ShortTitle` naming convention — no spaces.

## Extraction (with auto figure-renaming)

Same pymupdf4llm call as before, plus a post-process pass: any extracted image whose markdown embed is followed within a few lines by a `**Fig. N.** Caption text` line gets renamed to `Fig. N. Caption text.png`, and that one image link is rewritten to match. Images with no such caption nearby (journal mastheads, logos, decorative page furniture) are left with their raw pymupdf4llm names — untouched, on purpose.

```python
python -c "
import pymupdf4llm, os, re
out_dir = '[OUTPUT_FOLDER]'
base = '[BASE_NAME]'
img_dir = os.path.join(out_dir, 'images')
os.makedirs(img_dir, exist_ok=True)
md = pymupdf4llm.to_markdown(
    '[FILENAME.pdf]',
    write_images=True,
    image_path=img_dir,
    image_format='png'
)

# Auto-rename captioned figures: an image line followed (within a few lines,
# allowing blank lines / page-break furniture in between) by a bold
# '**Fig. N.** Caption' line gets renamed to 'Fig. N. Caption.png' and its
# markdown link rewritten. Uncaptioned images (logos, mastheads) are skipped.
invalid_chars = r'<>:\"/\\\\|?*'
lines = md.split('\n')
img_re = re.compile(r'!\[\]\((?P<path>[^)]+\.png)\)')
cap_re = re.compile(r'\*\*Fig\.\s*(?P<num>\d+)\.\*\*\s*(?P<caption>.+)')
renamed = []
for i, line in enumerate(lines):
    m = img_re.search(line)
    if not m or not os.path.exists(m.group('path')):
        continue
    old_rel = m.group('path')
    for j in range(i + 1, min(i + 6, len(lines))):
        cm = cap_re.search(lines[j])
        if not cm:
            continue
        caption = cm.group('caption').strip().rstrip('.') + '.'
        new_name = re.sub(f'[{invalid_chars}]', '', f\"Fig. {cm.group('num')}. {caption}\").rstrip('. ') + '.png'
        new_abs = os.path.join(os.path.dirname(old_rel), new_name)
        if os.path.exists(new_abs) and new_abs != old_rel:
            break
        os.rename(old_rel, new_abs)
        lines[i] = line.replace(old_rel, new_abs)
        renamed.append((old_rel, new_abs))
        break

md = '\n'.join(lines)
open(os.path.join(out_dir, base + '_raw.md'), 'w', encoding='utf-8').write(md)
print('Done, length:', len(md))
print('Renamed figures:', len(renamed))
for old, new in renamed:
    print(' -', old, '->', new)
"
```

Best-effort: catches the common one-caption-a-few-lines-below-its-image pattern. Multi-line captions, captions not immediately following their image, or figures spanning multiple images may not be caught — check the printed rename list and finish any stragglers by hand the same way (rename the file, fix its one markdown link).

Output: `[out_dir]/[base]_raw.md` + images in `[out_dir]/images/[auto-subfolder]/`.

Report the output path, character count, and the renamed-figures list when done.
