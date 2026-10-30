# Contributing

Cuneiform is a personal project, but bug reports and fixes are welcome.

## Issues

- **Bugs:** use the bug report form. The most useful reports attach the Markdown file that shows the problem, what happened and what you expected; a suggested fix is a bonus.
- **Security problems:** report them privately, see [SECURITY.md](SECURITY.md).
- **Feature ideas:** open an issue to discuss it first. Cuneiform is a previewer and stays small on purpose, so not every idea will fit; editing is out of scope.

## How the project is developed

Most of the code is written by coding agents following `AGENTS.md`; its rules apply to human contributions too. In short:

- `./scripts/check.sh` passes: Swift tests, Swift build, app bundle construction, bundle metadata checks and DMG packaging checks.
- Both renderers keep the image and link rules in `ResourcePolicy`. The native renderer is the default; `CUNEIFORM_RENDERER=webview` stays available.
- A change to the default renderer updates the files `AGENTS.md` lists for it together.
- The Swift target and module names stay `SimpleMarkdownPreviewerApp` and `SimpleMarkdownPreviewerCore`.
- Technical documentation, code comments and commit messages are in English. Do not commit `.build/`.

## Pull requests

Describe the change, link the issue if there is one, and list the commands you ran and their results. For changes that may affect startup, say how you measured it (see the Development section of the README).
