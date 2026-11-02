# Happy Painting by Sam: source

Concept site for Happy Painting by Sam (painters in Windsor & Essex County, ON).
The live files are in [`/happy-painting-by-sam`](../../happy-painting-by-sam). This folder holds what
generates and checks them.

- Preview: https://zandstrading1-hash.github.io/zs-mockups/happy-painting-by-sam/
- Intended domain: `happypaintingbysam.ca` (Cloudflare, see below)

## Layout

```
happy-painting-by-sam/            <- the deployed site (static, no build step needed to serve it)
  index.html services.html work.html colour-lab.html about.html contact.html   <- generated
  style.css  script.js           <- edit directly (shared by all pages)
  img/*.webp                     <- Sam's project photos, 1200px max
  _headers                       <- Cloudflare Pages: X-Robots-Tag noindex

source/happy-painting-by-sam/     <- this folder
  build_site.py                  <- writes the six .html pages
  parts/*.html                   <- shared fragments (head, header, footer, sections)
  check_pages.js                 <- smoke test for all pages, desktop + phone
  package.json                   <- npm run build / check
```

## Editing

- **Styles or behaviour:** edit `happy-painting-by-sam/style.css` or `script.js`. Nothing to rebuild.
- **Page content, nav, footer, CTA band:** edit `parts/*.html` and/or `build_site.py`, then run

  ```
  python3 source/happy-painting-by-sam/build_site.py
  ```

  This overwrites the six HTML pages. If you hand-edit a page instead, the next build will undo it,
  so pick one way and stick to it. `build_site.py` holds the page-specific copy (service details,
  About text, FAQ, page titles and descriptions); `parts/` holds sections reused across pages.
- `script.js` only runs the modules a page has (`if` the element exists), so a section can move
  between pages without JS changes.

## Checking

```
python3 -m http.server 8765        # from the repo root, leave it running

cd source/happy-painting-by-sam
npm install && npx playwright install chromium   # once
npm run check                      # or: npm run check:shots  (saves to screenshots/, gitignored)
```

It loads all six pages at 1440×900 and 390×844, fails on console errors, horizontal overflow or a
broken form prefill, and exits non-zero if anything fails.

## Before it goes live for Sam

- **Search engines:** every page has `<meta name="robots" content="noindex, nofollow">` (in
  `parts/head.html`) and `_headers` sends `X-Robots-Tag: noindex`. Remove both once Sam signs off.
- **Estimate form:** it opens the visitor's email app with a prefilled message to
  happypaintingbysam@gmail.com (`mailto:`). There's no backend. A real launch should post to a form
  service or a Cloudflare Worker so requests arrive without relying on the visitor's mail app.
- **Copy:** service details, About and FAQ only restate what's on Sam's existing site (free
  estimates, most rooms in a day, sharp lines, floors and furniture protected, full clean-up, the 8
  towns). There are no invented reviews or stats. A photo of Sam and his own story would make About
  much stronger.
- **Domain:** it's registered under Z&S Trading. Point it at the site once Sam has agreed, then
  transfer it to him or include it in the price.

## Deploying to happypaintingbysam.ca (Cloudflare Pages)

1. **Workers & Pages → Create application → Pages → Connect to Git**, then pick
   `zandstrading1-hash/zs-mockups`.
2. Set: framework preset **None**, build command *(blank)*, build output directory
   **`happy-painting-by-sam`**, production branch **`main`**. Click Save and Deploy.
3. **Custom domains → Set up a domain**: add `happypaintingbysam.ca`, then
   `www.happypaintingbysam.ca`. Remove any old A or CNAME records on the domain first.

Every push to `main` redeploys automatically. Only the `happy-painting-by-sam/` folder is
published there, so this `source/` folder and the other mockups are not.
