# fmsb

A one-page personal site, rendered entirely in WebGL. The ground, the rules and
every glyph are drawn by the GPU from `src/content/content.json`.

**Nothing readable ships** — not in the HTML, not in the title, not as a string
literal in the bundle. The page is meant to exist without being searchable.
That constraint is why the site is built this way, and `npm run check` is what
enforces it.

```bash
npm install
npm run dev      # watch + serve at http://localhost:5173
npm run build    # production build into dist/
npm run check    # drive the built site in a real browser — the gate before pushing
npm run fonts    # re-subset the webfonts (see below)
npm run lint
```

`check` needs Playwright, which is deliberately not a dependency because it
pulls a browser: `npm i -D playwright && npx playwright install chromium`. Set
`CHROMIUM_PATH` if the machine already has one.

## Editing the content

Everything you can read on the site lives in `src/content/content.json`, and
that file is the only place to change it.

**Every value has an `en` and a `zh` — keep both filled.** The language toggle
is a straight cut between them, so a missing value leaves a hole on screen.
Plain text only: no HTML, no `<br/>`. The layout engine decides line breaks.

The file has three parts:

- **`labels`** — the two words in the language toggle, and the marks.
- **`index`** — the masthead and the foot: name, role, the `statement` set large
  at the top, `context` (the short facts beside the role — two or three words
  each, they share a line), `available`, `contact`, `place`.
- **`blocks`** — the body, in the order it appears:
  - `practice` → **About**, a `prose` array of paragraphs.
  - `writing` → **Writing**, a `pieces` array of `{ type, title, description,
    url }`. Give a piece its `url` and its slot becomes a `Read` button;
    leave it empty and the slot stays outlined.
  - `cv` → **Work**, a `sections` array of `{ section, entries }`, entries being
    `{ year, title, org }`.

Three marks, one meaning each: **`■`** closes the document, **`□`** means a fact
is expected and has not arrived yet, and a year that is still running just ends
on its dash — `2025–`.

An entry marked `"placeholder": true` is a real thing with a fact still missing.
Read its `note`, fill the value in, then delete both keys. `_readme`, `note` and
`placeholder` are stripped at build and never leave the repository.

**After adding a Chinese character that was not already on the site, run
`npm run fonts`.** The CJK faces are subset to exactly the characters this file
uses — 292 of them, which is how several megabytes of Noto becomes about 70kB
each — so a new character is a missing glyph until they are regenerated. English
edits never need it; the Latin faces carry full `latin` + `latin-ext`.

## The rest

The source carries its own reasoning. `src/js/layout.js` is the box model and
holds every visual decision; `scripts/check.mjs` lists what is actually
guaranteed. `CLAUDE.md` has the working rules for changing any of it.

Deploys on Vercel as a static build (`outputDirectory: dist`).
