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

## Adding a presentation

When the user says something like "Add [conference] for [paper]", update all 4 files below:

### 1. `home.html` — Recent & Upcoming Presentations box
- In the `update-category` div with heading "Recent & Upcoming Presentations", add/update an `update-item`:
  ```html
  <div class="update-item">
      <span class="update-date">Month YYYY:</span> Conference Name
  </div>
  ```
- Remove entries that are no longer recent (e.g., past by 6+ months)

### 2. `research.html` — Selected presentations for the paper
- Find the paper's `<div class="presentations">` block
- Append the conference to the comma-separated list after `<strong>Selected presentations:</strong>`

### 3. `cv.html` — Presentations section
- Find `<h2 class="section-title">Presentations (2024-2026)</h2>`
- Add to the correct year's `<div class="cv-item-details">` (comma-separated list)
- If the year doesn't exist yet, add a new `cv-item` block

### 4. LaTeX CV — `/home/dcucic/Dropbox/Documents/Latex CV/CV_DC.tex`
- Find `\section*{Conference and Seminar Presentations}`
- Add to the correct year's `\item \textbf{YEAR:}` line

### After all edits: compile and deploy
```bash
cd "/home/dcucic/Dropbox/Documents/Latex CV" && pdflatex CV_DC.tex
cp "/home/dcucic/Dropbox/Documents/Latex CV/CV_DC.pdf" /home/dcucic/Dropbox/Website/CV_DC.pdf
cd /home/dcucic/Dropbox/Website && git add -A && git commit -m "Add [conference] presentation" && git push
```

## Style notes

- Primary accent color: `#1a0dab`
- Font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`
- Responsive breakpoint at 768px
- Assets (images, PDFs) are also hosted in this repo and referenced via GitHub Pages URLs
