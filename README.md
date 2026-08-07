# portfolio.atma.marketing — deploy notes

Static site. No build step, no framework, no bundler — plain HTML with inline
styles and two Google Font families. Upload the contents of this folder to the
web root and it works.

## Files

    index.html          the portfolio page
    chapter-two.html    "The Marketing You Never Saw" — sample chapter
    og-image.png        1200×630 social share card
    favicon.svg         primary icon (chocolate square, cream chevron)
    favicon-32.png      PNG fallback
    apple-touch-icon.png
    site.webmanifest
    robots.txt
    sitemap.xml
    img/                all page imagery
    files/              chapter two as a PDF (not linked; kept for reference)

## Deploying

Any static host. Drop the folder in and point the domain at it:

- **Netlify / Vercel / Cloudflare Pages** — drag the folder into the dashboard,
  or connect a repo with this as the publish directory. No build command.
- **S3 + CloudFront** — sync the folder, set `index.html` as the index document.
- **Apache / nginx** — copy to the document root.

Serve over HTTPS. Absolute paths are used for icons and the manifest
(`/favicon.svg`), so the site must live at the domain root, not a subfolder.
If you do put it in a subfolder, make those paths relative.

## Before it goes live

1. **Confirm the canonical domain.** Every `<link rel="canonical">`, `og:url`,
   and the sitemap use `https://portfolio.atma.marketing`. If the real host
   differs, find-and-replace that string in `index.html`, `chapter-two.html`,
   `sitemap.xml`, and `robots.txt`.
2. **Submit the sitemap** in Google Search Console once DNS resolves.
3. **Test the share card** with the LinkedIn Post Inspector and Twitter Card
   Validator. Both cache aggressively — validate before you share the link
   anywhere, because a bad first fetch sticks around.

## SEO and metadata already in place

- Unique `<title>` and meta description per page.
- Canonical URLs; `robots` set to `index, follow` with `max-image-preview:large`.
- Full Open Graph and Twitter card tags, both pointing at `og-image.png`
  (1200×630, dimensions declared so scrapers don't have to fetch to measure).
- JSON-LD: `ProfilePage` → `Person` on the portfolio, `Article` (part of the
  `Book`) on the chapter.
- `sitemap.xml` with lastmod dates, `robots.txt` pointing to it.
- Icons and web manifest for browser tabs, iOS home screen, and PWA installs.

## Performance and accessibility notes

- Images below the first screen are `loading="lazy" decoding="async"`.
- Fonts preconnect to `fonts.googleapis.com` and `fonts.gstatic.com` and load
  with `display=swap`, so text paints before the webfonts arrive.
- External links carry `target="_blank" rel="noopener"`.
- A print stylesheet prints link URLs after their text.
- Responsive rules collapse every multi-column grid to one column and step the
  display type down under 900px. They use `!important` because the layout is
  inline-styled — that is deliberate, not a smell.

### Images are already compressed

Every photograph and screenshot has been resized to roughly twice its display
size and re-encoded — the image folder went from 23.1 MB to 9.4 MB. Logos and
the Vice & Variance mark stayed PNG because they need transparency; everything
else is JPEG at quality 86, which is visually indistinguishable at these sizes.

**One file is still heavy: `img/gb-dashboard.gif`, at 7.5 MB — about 79% of all
remaining image weight.** Animated GIF is a wasteful format, and it can't be
made smaller without re-encoding the animation itself. Two ways to fix it:

1. Convert it to MP4 or WebM (roughly 200–400 KB for the same clip) and swap the
   `<img>` for a muted, autoplaying, looping `<video>`. Best result.
2. Replace it with a single still frame as a JPEG (~60 KB). Simplest.

It is `loading="lazy"`, so it does not block first paint either way.
