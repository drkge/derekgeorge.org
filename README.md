# derekgeorge.org

Personal site for Derek George — Shopify developer, part-time Apple developer.

Hand-written static HTML with a single stylesheet. No build step, no
dependencies, no framework. Edit a file, commit, done.

## Structure

```
index.html              Home page
404.html                Not-found page, served by Pages for any missing path
style.css               The whole design system
favicon.svg             Tab icon — the terminal prompt from the design system
apple-touch-icon.png    180×180 raster of the same mark, for iOS home screens
robots.txt              Crawler policy; points at the sitemap
sitemap.xml             Every public URL — hand-maintained, see Adding a page
CNAME                   Custom domain for GitHub Pages
```

## Adding a page

**`sitemap.xml` is hand-maintained and nothing will warn you if it drifts.**
There is no build step to catch it, no test, and no error at deploy — a page
left out is simply invisible to the sitemap, silently and indefinitely. This is
the step that gets forgotten, so do it as part of adding the page rather than
afterwards.

1. Create `<dir>/index.html`, so it gets a clean `/<dir>/` URL. Copy the
   `<head>` block from `index.html` and change `canonical`, `og:title`,
   `og:description` and `og:url`.
2. **Add it to `sitemap.xml`:**

   ```xml
   <url>
     <loc>https://derekgeorge.org/newpage/</loc>
     <lastmod>2026-10-22</lastmod>
     <priority>0.5</priority>
   </url>
   ```

   Trailing slash, no `index.html`. `lastmod` is the date you commit, in
   `YYYY-MM-DD`. Home is `1.0`, other pages `0.5`. Update `lastmod` when you
   materially change a page — a date that never moves teaches crawlers to stop
   checking.
3. Check nothing drifted, in both directions — a page missing from the sitemap
   and a sitemap entry with no page are both silent failures:

   ```bash
   python3 - <<'EOF'
   import re, pathlib
   BASE = "https://derekgeorge.org/"
   disk = set()
   for p in pathlib.Path(".").rglob("index.html"):
       if ".git" in p.parts: continue
       d = p.parent.as_posix()
       disk.add(BASE if d == "." else f"{BASE}{d}/")
   listed = set(re.findall(r"<loc>([^<]+)</loc>", pathlib.Path("sitemap.xml").read_text()))
   for u in sorted(disk - listed): print("MISSING FROM SITEMAP:", u)
   for u in sorted(listed - disk): print("IN SITEMAP, NO FILE: ", u)
   if disk == listed: print("sitemap matches the pages on disk")
   EOF
   ```

**Do not list `404.html`** — it is `noindex` by design and does not belong in a
sitemap.

You do not need to resubmit to Search Console. The sitemap is registered there
already, and Google refetches it periodically on its own.

## Running locally

```bash
python3 -m http.server 4173
```

Then open <http://localhost:4173>. Python's server matches GitHub Pages closely
enough for this site — it serves `index.html` for directory requests and
redirects to the trailing slash the same way.

## Design system

Everything is driven by custom properties at the top of `style.css` — a blush
and deep-plum palette (`--bg`, `--ink`, `--muted`, `--accent`, `--accent-soft`,
`--rule`) plus `--font-sans` and `--font-mono`. Change a colour there and it
changes everywhere.

The look is quietly terminal-flavoured. Sections follow one pattern:

```html
<section id="thing">
  <span class="eyebrow" aria-hidden="true">// slug-in-mono</span>
  <h2>Heading</h2>
  <hr />
  <p>Body copy.</p>
</section>
```

The eyebrow is decorative, so it carries `aria-hidden="true"` — without it a
screen reader announces "slash slash slug in mono" before every heading.

`.prompt` renders a mono line prefixed with `$ ` and is used once per page, at
the top. `.eyebrow` is the mono `// slug` label above each heading. Body text is
`--muted`; headings step up to `--ink`. `footer .note` sets a footer line in
sans rather than the footer's default mono, as the copyright line does.

## Deployment

GitHub Pages, from `main` at the repository root.

All internal links are relative, so the site renders correctly both at a domain
root and under a project subpath (`drkge.github.io/derekgeorge.org/`). Keep it
that way — no leading slashes on internal `href`s — and the site stays portable
between the two.

`404.html` is the one deliberate exception and uses root-absolute paths. Pages
serves it for a missing path at *any* depth, so a relative `href` there would
resolve against `/a/b/c/` and 404 in turn.

## Head boilerplate

Every page carries a canonical URL, Open Graph tags, `theme-color`, and links to
both icons. Copy the block from `index.html` when adding a page and change the
four values that differ: `canonical`, `og:title`, `og:description`, `og:url`.

There is no `og:image` — a good one needs type set in a real design tool, and a
bad one is worse than none, since a share card falls back to title and
description cleanly on its own.

Keep meta descriptions between 120 and 160 characters; below that search results
look thin, above it they get truncated.

The homepage additionally carries JSON-LD (`Person` + `WebSite`).
