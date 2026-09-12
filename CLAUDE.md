# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Single-page static website for Edelweiss Quartet (Paris jazz quartet), built on the
[Brushed](https://themes.alessioatzeni.com/html/brushed/) HTML template (jQuery 1.9.1 /
Bootstrap 2.3.1 era, ~2012-2013). No build step, no package manager, no framework — just
`index.html` plus vendored JS/CSS in `js/` and `css/`.

Hosted on GitHub Pages with a custom domain (see `CNAME`); every push to `main` deploys
automatically. No CI, no tests.

## Running locally

```bash
python3 -m http.server 8000
# or
npx serve .
```

Then open `http://localhost:8000/index.html`. There is no build/lint/test command —
verify changes by loading the page in a browser (or headless Chrome via CDP) and
checking the console for errors.

## Architecture

Everything lives in one `index.html`, split into full-width `<div>` sections that double
as scroll-spy anchors: `#home-slider`, `#music`, `#video`, `#gigs`, `#contact`. The nav
bar (`#menu-nav`) and the click/scroll behavior between these sections is the core piece
of interactive logic — see below.

### Nav scrolling (`js/plugins.js` + `js/main.js`)

`BRUSHED.menu()` (in `main.js`) wires `#menu-nav` to the bundled `onePageNav` jQuery
plugin (in `plugins.js`), which does two things: highlights the current section on
scroll, and smooth-scrolls to a section on click. The click handler used to call the
vendored `jquery.scrollTo` plugin, but that plugin's ~2012 webkit-detection code
animates `document.body.scrollTop`, which modern Chrome ignores (it scrolls
`document.documentElement`) — clicks would update the highlighted nav item but never
actually move the page. It's been replaced with a plain `$('body, html').animate({scrollTop: ...})`
call, matching the pattern already used by `BRUSHED.goSection()`, `BRUSHED.goUp()`, and
`BRUSHED.scrollToTop()` elsewhere in `main.js`. If nav scrolling ever seems "stuck"
again, this is the first place to look — and be suspicious of any other direct
`$.scrollTo(...)` call.

### Contact form (`#contact-form` in `index.html`, `BRUSHED.contactForm()` in `main.js`)

Submits to Formspree (`https://formspree.io/f/mnpqykon`) via a plain form `action`/`method`
(works with JS disabled) plus a jQuery AJAX handler for a no-reload UX. Includes a
`_gotcha` honeypot field and native HTML5 validation (`required`, `type="email"`).
Formspree's dashboard has a domain restriction set to the production domain — test
submissions from `localhost` will land in Formspree's spam folder, not the inbox, which
is expected and not a bug in this codebase.

### Gigs / concerts (`#gigs` section)

Two Bootstrap tabs, `#tab_next` ("À venir") and `#tab_past` ("Passés"), each containing
its own `<table>`. Both tables use an explicit `<colgroup>` with fixed percentage widths
(`table-layout:fixed`) so their columns line up with each other — without it, each table
auto-sizes its columns independently based on its own row content, and short "upcoming
show" rows don't align with long "past shows" rows. Keep both tables' colgroups in sync
when editing either one.

## Future work: dependency modernization

The vendored libraries are all long past EOL (jQuery 1.9.1, Bootstrap 2.3.1, FancyBox
2.1.4, Isotope, Supersized 3.2.7, Modernizr 2.5.3). This is a deliberately deferred
chantier, not an oversight:

- Real-world risk today is low — this is a static site with no user-generated content
  rendered back into the DOM (the contact form posts to Formspree and doesn't reflect
  data), so the known CVEs in old jQuery/Bootstrap (XSS in `.html()`/tooltip/affix) have
  no practical attack surface here.
- Upgrading is not a drop-in version bump: `jquery.scrollTo` (in `plugins.js`), Isotope's
  filter/masonry layout, and FancyBox all depend on jQuery 1.x APIs (`.delegate()`,
  `.bind()`/`.unbind()`) that jQuery 3.x removed, and Bootstrap 2 markup/classes
  (`span2`, `span4`, etc.) aren't compatible with later Bootstrap versions. Doing this
  properly means a real regression pass across the slider, lightbox, tab filtering, and
  nav scroll — not something to attempt as a quick edit.
