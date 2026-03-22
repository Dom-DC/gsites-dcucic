# Personal Website — Dominic Cucic

Empirical financial economist at Danmarks Nationalbank. This repo hosts the HTML for my Google Sites website.

## How it works

- GitHub Pages serves from the **`gh-pages`** branch at `https://dom-dc.github.io/gsites-dcucic/`
- Each HTML file is embedded as an iframe in the corresponding **Google Sites** tab
- Edits take effect after pushing to `gh-pages` (may take ~1 min for GitHub Pages cache)
- Make sure you are on the `gh-pages` branch when editing and pushing

## File → Google Sites mapping

| File | Google Sites tab | Live URL |
|------|-----------------|----------|
| `home.html` | Home | `https://dom-dc.github.io/gsites-dcucic/home.html` |
| `research.html` | Research | `https://dom-dc.github.io/gsites-dcucic/research.html` |
| `cv.html` | Curriculum Vitae | `https://dom-dc.github.io/gsites-dcucic/cv.html` |

## Editing workflow

1. Edit the HTML file(s)
2. Commit and push: `git add -A && git commit -m "description" && git push`
3. The Google Sites iframe auto-updates

## Style notes

- Primary accent color: `#1a0dab`
- Font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`
- Responsive breakpoint at 768px
- Assets (images, PDFs) are also hosted in this repo and referenced via GitHub Pages URLs
