# Portfolio Site — Setup & Deployment Guide

This document explains everything that was done to build, fix, and publish the portfolio site, step by step, so you have a complete record to refer back to.

---

## 1. Quick Reference

| Item | Value |
|---|---|
| Live site | https://abhishekkumar1709.github.io/abhishek-k-portfolio/ |
| GitHub repository | https://github.com/AbhishekKumar1709/abhishek-k-portfolio |
| Default branch | `main` |
| Hosting | GitHub Pages (free, auto-deploys on every push to `main`) |
| Google Search Console | Verified via HTML meta tag |
| Sitemap | https://abhishekkumar1709.github.io/abhishek-k-portfolio/sitemap.xml (submitted to Google) |

---

## 2. What the Site Contains

The portfolio (`index.html`) is a single self-contained HTML file with inline CSS and JavaScript. It includes:

- Hero section with name, role, and a photo
- Skills, experience, and projects sections
- An Education section with a **Certifications** block showing 3 certificates
- Light/dark theme toggle
- Contact section

Supporting files:
- `certs/*.pdf` — the original certificate PDF files
- `certs/*.png` — a rendered image of each certificate's first page (used for the inline preview popup)
- `robots.txt` — tells search engines which pages they're allowed to crawl, and points them to the sitemap
- `sitemap.xml` — lists the site's pages so Google can discover and index them faster

---

## 3. Certificate Preview Feature — What Was Fixed

**Original problem:** Clicking "View certificate" either did nothing, or opened a blank/broken preview.

**Root causes found, in order:**
1. The certificate links were plain `<a href="certs/....pdf" target="_blank">` — this opens the PDF in a *new browser tab* instead of showing it on the same page.
2. When the site was tested inside a Claude Artifact preview (a sandboxed iframe), opening new tabs is blocked by the sandbox for security reasons, so nothing happened at all.
3. After switching to an in-page popup (modal) that loaded the PDF in a nested `<iframe>`, the sandboxed environment *also* blocked loading a PDF that way — showing a broken-file icon.

**Final fix:**
- Each certificate's first page was rendered to a PNG image (using a local script with `pdfjs-dist` + `@napi-rs/canvas`).
- Clicking a certificate card now opens an on-page popup (modal) that displays this PNG image directly — images always work reliably, unlike embedded PDFs in a sandboxed frame.
- The popup also has an **"Open in new tab"** link (points to the real PDF) and a close (×) button.
- This works both in the real, publicly hosted site and inside any sandboxed preview.

---

## 4. Deployment — Step by Step

### Step 1: Create the GitHub repository
- Went to `github.com/new`
- Repository name: `abhishek-k-portfolio`
- Visibility: **Public** (required for free GitHub Pages)
- No README, no `.gitignore`, no license added (to avoid conflicts with existing local code)

### Step 2: Connect the local project to GitHub
```
git remote add origin https://github.com/AbhishekKumar1709/abhishek-k-portfolio.git
git branch -M main
git push -u origin main
```

### Step 3: Enable GitHub Pages
- Repo → **Settings → Pages**
- Source: **Deploy from a branch**
- Branch: **main**, folder: **/ (root)**
- Saved — GitHub automatically builds and publishes the site on every push to `main`

Result: site went live at `https://abhishekkumar1709.github.io/abhishek-k-portfolio/`

### Step 4: Fix SEO metadata for the real URL
Because the site lives at a **subpath** (`/abhishek-k-portfolio/`) rather than the domain root, these were updated to point at the correct full URL:
- `<link rel="canonical">` and `<meta property="og:url">` in `index.html`
- The `<loc>` in `sitemap.xml`
- The `Sitemap:` line in `robots.txt`

### Step 5: Verify ownership in Google Search Console
- Search Console → Add property → **URL prefix** → entered the live site URL
- Verification method: **HTML tag**
- Google gave a meta tag:
  `<meta name="google-site-verification" content="..." />`
- This tag was added to the `<head>` of `index.html`, committed, and pushed
- Waited for GitHub Pages to rebuild, then clicked **Verify** in Search Console → ownership confirmed

### Step 6: Submit the sitemap
- Search Console → **Sitemaps** (left sidebar)
- Entered `sitemap.xml` in the submission box → **Submit**
- Result: "Sitemap submitted successfully"

---

## 5. How to Update the Site in the Future

Any time you want to change something on the site:

1. Edit `index.html` (or add/replace files in `certs/`) locally.
2. Run these commands in the project folder:
   ```
   git add -A
   git commit -m "describe your change here"
   git push origin main
   ```
3. GitHub Pages automatically rebuilds — changes usually go live within **1–2 minutes**.
4. No need to touch GitHub Pages settings or Search Console again for normal content updates.

If you ever add a **new page** (not just edit the existing one), remember to add its URL to `sitemap.xml` too, so Google finds it.

---

## 6. Getting Found on Google

Being live and having a sitemap submitted does **not** mean the site appears in Google search results immediately.

- Google typically takes **anywhere from a few days to 1–2 weeks** to crawl and index a new site, even with a submitted sitemap.
- To check indexing status any time, search on Google:
  ```
  site:abhishekkumar1709.github.io/abhishek-k-portfolio
  ```
  Once a result shows up here, the site is indexed.
- You can also check **Search Console → Pages** (left sidebar) to see whether Google has crawled and indexed the page, and if not, why.
- Ranking well for specific searches (like your name) takes longer and improves over time as the site gets more visibility/links — this is normal for any new site.
