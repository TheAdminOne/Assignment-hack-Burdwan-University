# Mermaid sources

Each `answerN.mmd` is the editable source for the rendered diagram at
`../images/answerN.svg`, which is what the answer pages display.

## Regenerate all images

```bash
npm i -g @mermaid-js/mermaid-cli
cd "1st Sem/Algorithm & Flowchart Assignment/diagrams"

for n in $(seq 1 14); do
  mmdc -i "answer$n.mmd" -o "../images/answer$n.svg" -b white -c mermaid-config.json
done
```

`mermaid-config.json` sets:

- `htmlLabels: false` — the SVGs use plain `<text>` elements instead of
  `<foreignObject>`, so they stay portable across GitHub, Markdown viewers, and
  offline editors.
- `useMaxWidth: false` — the SVGs get explicit `width`/`height` attributes.
  Without this, browsers treat them as 300×150 and GitHub shows them very small.

The `-b white` flag gives the diagrams a white background so the dark text stays
readable in GitHub's dark mode.
