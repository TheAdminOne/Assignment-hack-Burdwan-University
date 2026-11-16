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

`mermaid-config.json` sets `htmlLabels: false`, so the SVGs use plain `<text>`
elements instead of `<foreignObject>`. That keeps them portable across GitHub,
Markdown viewers, and offline editors.

The `-b white` flag gives the diagrams a white background so the dark text stays
readable in GitHub's dark mode.
