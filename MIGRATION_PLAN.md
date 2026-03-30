# Migration Plan: Google Sites → GitHub Pages + Custom Domain

## Context

**Current setup**
- Personal website hosted on **Google Sites** at `sites.google.com/view/dominiccucic`
- Google Sites embeds three HTML files as iframes, served from GitHub Pages:
  - `https://dom-dc.github.io/gsites-dcucic/home.html`
  - `https://dom-dc.github.io/gsites-dcucic/research.html`
  - `https://dom-dc.github.io/gsites-dcucic/cv.html`
- GitHub repo: `Dom-DC/gsites-dcucic`, branch `gh-pages`
- Local repo path: `/home/dominic/Dropbox/Website/`
- GA4 property already exists: **"Personal-GoogleSite - GA4"** (connected to Google Sites)

**Why migrating**
- Google Sites embeds content as iframes → white space, mobile UX issues, limited control
- The three HTML files are already a complete standalone website
- Traffic is low (~44 sessions/28 days, 68% organic search) → migration risk is minimal
- Unique name ("Dominic Cucic") → Google re-indexes quickly

**Goal**
Point a custom domain (e.g. `dominiccucic.com`) directly to GitHub Pages, drop the iframe
architecture, add GA4 tracking directly to the HTML files, and leave Google Sites live as a
signpost during the transition.

---

## Step 1 — Choose and register a domain

**Do this yourself** (takes ~5 min):

1. Go to [namecheap.com](https://namecheap.com) (recommended: transparent pricing, free WhoisGuard)
2. Search for your preferred domain. Suggestions in order of preference:
   - `dominiccucic.com`
   - `dcucic.com`
   - `dominiccucic.eu` (if you prefer European TLD)
3. Purchase (~$12–15/year for `.com`). Use the default nameservers (Namecheap DNS).
4. Note down the domain you purchased — you'll need it in every step below.

**Tell the Claude instance:** "I registered `YOUR-DOMAIN.com`"

---

## Step 2 — Configure GitHub Pages to use the custom domain

GitHub Pages natively supports custom domains with free HTTPS.

### 2a. Add a CNAME file to the repo

In `/home/dominic/Dropbox/Website/`, create a file named exactly `CNAME` (no extension)
containing just the domain, e.g.:

```
dominiccucic.com
```

Commit and push:
```bash
cd /home/dominic/Dropbox/Website
echo "YOUR-DOMAIN.com" > CNAME
git add CNAME
git commit -m "Add custom domain CNAME"
git push
```

### 2b. Configure GitHub repository settings

1. Go to `https://github.com/Dom-DC/gsites-dcucic/settings/pages`
2. Under **Custom domain**, enter your domain and click Save
3. Check **Enforce HTTPS** (may take a few minutes to activate after DNS is set up)

---

## Step 3 — Point the domain DNS to GitHub Pages

Log in to Namecheap → Domain List → Manage → **Advanced DNS**.

Add these records:

| Type | Host | Value | TTL |
|------|------|-------|-----|
| A | @ | 185.199.108.153 | Automatic |
| A | @ | 185.199.109.153 | Automatic |
| A | @ | 185.199.110.153 | Automatic |
| A | @ | 185.199.111.153 | Automatic |
| CNAME | www | dom-dc.github.io | Automatic |

Delete any pre-existing A records or CNAME records for `@` or `www` that conflict.

DNS propagation takes **10 minutes to 48 hours** (usually under 1 hour with Namecheap).

**To verify propagation:** run `dig YOUR-DOMAIN.com` or use `https://dnschecker.org`

---

## Step 4 — Update internal links in all HTML files

Once the domain is live, update all self-referencing GitHub Pages URLs in the HTML files.

Files to edit: `home.html`, `research.html`, `cv.html`

### 4a. Mobile nav bar links (all three files)

Each file has a `<nav class="mobile-nav">` block. Update the hrefs from:
```
https://dom-dc.github.io/gsites-dcucic/home.html
https://dom-dc.github.io/gsites-dcucic/research.html
https://dom-dc.github.io/gsites-dcucic/cv.html
```
To:
```
https://YOUR-DOMAIN.com/
https://YOUR-DOMAIN.com/research.html
https://YOUR-DOMAIN.com/cv.html
```

### 4b. Asset links (images, PDFs)

Search for remaining `dom-dc.github.io/gsites-dcucic/` references:
```bash
grep -r "dom-dc.github.io" /home/dominic/Dropbox/Website/*.html
```
Update any that point to PDFs or images (e.g. `DC_foto.jpg`, `CV_DC.pdf`, paper PDFs) to use
the new domain. PDFs and images are also served from the same GitHub Pages repo, so they'll
be available at `https://YOUR-DOMAIN.com/CV_DC.pdf` etc.

### 4c. Create an index.html redirect

GitHub Pages serves `index.html` at the root. Create a simple redirect so
`YOUR-DOMAIN.com` (root URL) goes to `home.html`:

```bash
cat > /home/dominic/Dropbox/Website/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta http-equiv="refresh" content="0; url=home.html">
  <link rel="canonical" href="home.html">
</head>
<body></body>
</html>
EOF
```

---

## Step 5 — Add Google Analytics 4 tracking to all HTML files

This preserves your existing analytics data and gives better tracking (page-level, not just site-level).

### 5a. Find your GA4 Measurement ID

1. Go to [analytics.google.com](https://analytics.google.com)
2. Select **Personal-GoogleSite - GA4**
3. Click **Admin** (bottom left gear icon)
4. Under **Data collection and modification** → **Data Streams**
5. Click on your web stream → copy the **Measurement ID** (format: `G-XXXXXXXXXX`)

### 5b. Add the tracking snippet to each HTML file

Add the following two tags to the `<head>` section of `home.html`, `research.html`, and `cv.html`.
Replace `G-XXXXXXXXXX` with your actual Measurement ID:

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

Place it just before `</head>` in each file.

---

## Step 6 — Add SEO meta tags to all HTML files

With a standalone site, proper meta tags help Google index the new domain quickly.

Add the following to the `<head>` of each file (customize per page):

**home.html:**
```html
<meta name="description" content="Dominic Cucic — Senior Research Economist at Danmarks Nationalbank. Research on banking, deposit markets, nonbank lending, and monetary policy.">
<meta name="author" content="Dominic Cucic">
<link rel="canonical" href="https://YOUR-DOMAIN.com/">
```

**research.html:**
```html
<meta name="description" content="Research papers by Dominic Cucic on nonbank lending, deposit insurance, bail-ins, and monetary policy transmission.">
<meta name="author" content="Dominic Cucic">
<link rel="canonical" href="https://YOUR-DOMAIN.com/research.html">
```

**cv.html:**
```html
<meta name="description" content="Curriculum Vitae — Dominic Cucic, Senior Research Economist, Danmarks Nationalbank.">
<meta name="author" content="Dominic Cucic">
<link rel="canonical" href="https://YOUR-DOMAIN.com/cv.html">
```

---

## Step 7 — Leave Google Sites live as a signpost

Do NOT delete Google Sites. Leave it live so the old URL still works for people who
bookmarked it or find it in search during the transition period.

Edit the Google Sites home page to add a brief notice above or below the iframe:

> "This site has moved to [YOUR-DOMAIN.com](https://YOUR-DOMAIN.com). Please update your bookmarks."

Do this via the **Google Sites editor** (sites.google.com → pencil/edit icon).
You can add a Text block at the top of the page.

---

## Step 8 — Update external references

Update the URL wherever you have listed your website:

- [ ] **LaTeX CV** — `/home/dominic/Dropbox/Documents/Latex CV/CV_DC.tex`
  - Find the personal website URL and update it
  - Recompile: `cd "/home/dominic/Dropbox/Documents/Latex CV" && pdflatex CV_DC.tex`
  - Copy PDF: `cp CV_DC.pdf /home/dominic/Dropbox/Website/CV_DC.pdf`

- [ ] **Google Scholar profile** — scholar.google.com → edit profile → homepage URL

- [ ] **Danmarks Nationalbank staff page** — if you have a page there with a website link

- [ ] **SSRN / IDEAS / RePEc profiles** — update homepage URL if listed

- [ ] **Email signature** — update if your website URL is listed

- [ ] **cv.html** — the web CV has a "Personal Website" link that needs updating

---

## Step 9 — Register new domain in Google Search Console

This tells Google to crawl the new domain and accelerates indexing.

1. Go to [search.google.com/search-console](https://search.google.com/search-console)
2. Add new property → enter `YOUR-DOMAIN.com`
3. Verify ownership via the HTML file method (download a verification file and add it to the repo)
   or via DNS TXT record (add a TXT record in Namecheap DNS)
4. Submit a sitemap (see Step 10)

---

## Step 10 — Create a sitemap

A sitemap helps Google find and index all pages quickly. Create
`/home/dominic/Dropbox/Website/sitemap.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://YOUR-DOMAIN.com/</loc>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://YOUR-DOMAIN.com/home.html</loc>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://YOUR-DOMAIN.com/research.html</loc>
    <priority>0.9</priority>
  </url>
  <url>
    <loc>https://YOUR-DOMAIN.com/cv.html</loc>
    <priority>0.8</priority>
  </url>
</urlset>
```

Then in Google Search Console → Sitemaps → submit `https://YOUR-DOMAIN.com/sitemap.xml`.

---

## Step 11 — Final commit and deploy

```bash
cd /home/dominic/Dropbox/Website
git add -A
git commit -m "Migrate to custom domain: update links, add GA4, SEO meta tags, sitemap"
git push
```

---

## Verification checklist

After completing all steps, verify:

- [ ] `https://YOUR-DOMAIN.com` loads `home.html` correctly
- [ ] `https://www.YOUR-DOMAIN.com` also works (www redirect)
- [ ] HTTPS padlock is shown (no mixed content warnings)
- [ ] Mobile nav bar links work (navigate within same tab)
- [ ] `https://YOUR-DOMAIN.com/CV_DC.pdf` opens the CV PDF
- [ ] GA4 real-time report shows a visit when you open the site
- [ ] Google Sites old URL still loads (with the redirect notice)
- [ ] Search Console shows the domain as verified

---

## Timeline expectations

| Task | Time |
|------|------|
| Domain registration | 5 min |
| GitHub + DNS setup | 15 min |
| DNS propagation | 10 min – 48 hours |
| HTML file updates | 30 min (with Claude) |
| Google re-indexing | 2–6 weeks |
| Full traffic on new domain | 4–8 weeks |

---

## Key file locations (for the Claude instance)

| File | Path |
|------|------|
| Home page | `/home/dominic/Dropbox/Website/home.html` |
| Research page | `/home/dominic/Dropbox/Website/research.html` |
| CV page | `/home/dominic/Dropbox/Website/cv.html` |
| LaTeX CV | `/home/dominic/Dropbox/Documents/Latex CV/CV_DC.tex` |
| GitHub repo | `https://github.com/Dom-DC/gsites-dcucic` (branch: `gh-pages`) |
| Google Sites | `https://sites.google.com/view/dominiccucic` |
| GA4 property | "Personal-GoogleSite - GA4" at analytics.google.com |
