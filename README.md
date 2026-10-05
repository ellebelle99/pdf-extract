# pdf-extract

A Claude Code skill that extracts a PDF to raw markdown.

Papers become plain text that Claude can read and quote.

Claude writes `[base]_raw.md` and an `images/` folder, and renames figures that have a caption.


## Why this skill

It is one command, and it only extracts.

No formatting is applied, so the text is exactly what the PDF holds.

Two-column layouts and figures come through, which plain text converters tend to break.


## Install

    npx skills add ellebelle99/pdf-extract -g

The `-g` installs it for every project.

Installing this way lets `npx skills update` keep the skill current for you.

No Node? Copy `skills/pdf-extract/` into `.claude/skills/`.
Copied skills are not tracked, so `npx skills update` will not update them.


## Requirements

- Python 3.
- `pymupdf4llm`, installed with `pip install pymupdf4llm`. It brings `pymupdf` along.

The skill does not install `pymupdf4llm` for you.

If it is missing, the skill tells Claude to build a throwaway virtual environment and run the extraction from there, so nothing is added to your system Python.


## Use

    /pdf-extract path/to/file.pdf

Claude asks for an output folder and a base filename, for example `Teece_2007_Explicating_Dynamic_Capabilities`.

It then writes `[base]_raw.md` and the images, and reports the output path, the character count and the renamed figures.


## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

A sample of the output layout is in [examples/output-layout.md](examples/output-layout.md).



If you like the skill, a star is appreciated.

Watching the repo is an easy way to keep an eye on changes.


## License

MIT, see [LICENSE](LICENSE).
