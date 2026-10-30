# Security

Cuneiform opens Markdown files that often come from somewhere else: downloads, repositories, AI tools. It is not sandboxed, so what matters is that a document can only be read, never act: it must not load files outside its folder, reach the network, run code or open anything but web and mail links.

## Reporting a vulnerability

Please do not open a public issue for a security problem. Report it privately through GitHub: **Security → Report a vulnerability** on this repository ([direct link](https://github.com/QTS-Vibe-Coding/CuneiformMarkdownReader/security/advisories/new)). Include the Markdown file (or the smallest one that shows the problem), what happened, and the version or commit you tested.

You will get an answer within a week. Fixes land on `main`; there are no maintained release branches.

## In scope

- A Markdown file that makes Cuneiform read or show a file outside the document's folder
- A Markdown file that makes Cuneiform load a remote resource or make a network request
- A Markdown file that runs script or HTML in either renderer
- A link in a document that opens something other than an `http`, `https` or `mailto` URL
- A problem in the signed and notarized DMG from the Releases page

## Out of scope

- Anything that needs someone to have already changed `Cuneiform.app` or to run code on the Mac
- Links that open a harmful website in your browser: Cuneiform hands web links to the browser by design
- Builds you made yourself with `scripts/build_app.sh` (they are adhoc signed for local use)
