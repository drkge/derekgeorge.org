# derekgeorge.org

Personal site for Derek George — Shopify developer, part-time Apple developer.

Hand-written static HTML with a single stylesheet. No build step, no
dependencies, no framework. Edit a file, commit, done.

## Structure

```
index.html              Home page
style.css               The whole design system
policy/babee/index.html Privacy policy for Babee (iOS)
```

Privacy policies live at `policy/<app>/index.html` so each one gets a clean
`/policy/<app>/` URL. Add a directory per app.

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
  <span class="eyebrow">// slug-in-mono</span>
  <h2>Heading</h2>
  <hr />
  <p>Body copy.</p>
</section>
```

`.prompt` renders a mono line prefixed with `$ ` and is used once per page, at
the top. `.eyebrow` is the mono `// slug` label above each heading. Body text is
`--muted`; `strong` and headings step up to `--ink`.

Long-form pages (the policies) add a few extras: `.doc-head` for the title
block, `.updated` for the mono datestamp, `.callout` for the summary panel, and
`footer .note` for a disclaimer in sans rather than the footer's default mono.

## Deployment

GitHub Pages, from `main` at the repository root.

All internal links are relative, so the site renders correctly both at a domain
root and under a project subpath (`drkge.github.io/derekgeorge.org/`). Keep it
that way — no leading slashes on internal `href`s — and the site stays portable
between the two.
