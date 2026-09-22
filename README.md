# The Higher Rock Foundation — Website Handoff

Source for the current version of the Higher Rock Foundation site, recovered from the live
deployment at https://luminous-toffee-e04928.netlify.app/

Prepared for handoff to the developer finishing the build.

---

## 1. What this is

A **static site**. No framework, no build step, no bundler, no npm install.
Markup lives in `index.html`, all styling in `style.css`, and the page carries one small
inline `<script>` at the bottom for the sticky nav, the mobile menu, the scroll reveals
and the rotating photo track.

That makes it about as portable as a site gets: open it, edit it, upload it.

```
higher-rock-site/
├── index.html                      markup + a small inline script (~38 KB)
├── style.css                       all styling (~28 KB)
├── README.md                       this file
└── assets/
    ├── logo-brown.png              wordmark, dark — used in the header
    ├── logo-white.png              wordmark, light — used in the footer
    ├── mark-blue.png               logo mark — favicon + watermark in the Giving section
    ├── mark-white.png              logo mark, light — watermark in the pullquote band
    ├── partners/
    │   ├── asone_brown.png
    │   ├── careforchildren_brown.png
    │   ├── era92_brown.png
    │   └── justicerising_brown.png
    └── images/                     photography (see §3)
        ├── hero.jpg                the banner
        ├── strip/                  carousel slides
        ├── regions/                Where We Work cards
        ├── field/                  From the Field stories
        └── instagram/              Instagram grid
```

### Tuning the photo track

The rotating image band is driven by three CSS variables at the top of `style.css`:

```css
--photo-panel: 74vh;                      /* height of the pinned panel — lower = less space */
--photo-card:  clamp(300px,33vw,500px);   /* card width — smaller = shorter row = less scrolling */
--photo-gap:   clamp(24px,3.4vw,54px);    /* space between cards */
```

The section's total height is computed from these on load and on resize, so there are no
pixel values to keep in sync. Card angles are `TILT` and `SWING` in the script at the
bottom of `index.html`.

Note: `body` uses `overflow-x: clip`, **not** `hidden`. `hidden` makes the body a scroll
container, which silently breaks the `position: sticky` pin on that section.

---

## 3. Photography — where images go

The layout follows the section rhythm of austinstone.org: full-bleed banner, photo carousel,
alternating split bands, image-first card grids, multi-column footer.

Every photo slot is a `<figure class="ph">` holding an `<img>`. **Drop a file at the path
below and it appears** — no markup change needed. Until a file exists, `onerror` removes the
broken image and a labelled placeholder shows the path and the recommended size, so an
unfinished page never shows a broken-image icon.

| Path | Size | Used by |
|---|---|---|
| `assets/images/hero.jpg` | 2560×1430 | Hero banner (full-bleed) |
| `assets/images/strip/strip-1…5.jpg` | 2000×1200 | Photo carousel, 5 slides |
| `assets/images/heart.jpg` | 1200×1500 | Our Heart — portrait, left of the copy |
| `assets/images/regions/uganda.jpg` | 1200×900 | Where We Work card |
| `assets/images/regions/madagascar.jpg` | 1200×900 | Where We Work card |
| `assets/images/regions/drcongo.jpg` | 1200×900 | Where We Work card |
| `assets/images/regions/unitedstates.jpg` | 1200×900 | Where We Work card |
| `assets/images/field/featured.jpg` | 1600×1000 | From the Field — featured story |
| `assets/images/field/story-2.jpg` | 1200×675 | From the Field — second story |
| `assets/images/field/story-3.jpg` | 1200×675 | From the Field — third story |
| `assets/images/cta-band.jpg` | 2400×1000 | Full-bleed CTA band background |
| `assets/images/instagram/ig-1…6.jpg` | 1080×1080 | Instagram grid |
| `assets/images/contact.jpg` | 1200×800 | Contact section |

**Current banner.** `assets/images/hero.jpg` is a 2560px web version of
`assets/images/field/family-showcasing-their-home.jpg` (the 8192px original is kept
untouched). To change the banner, replace `assets/images/hero.jpg` — nothing else moves.

Keep hero and carousel files under ~400 KB each; resize oversized originals before
uploading rather than letting the browser scale a 30-megapixel file.

---

## 4. Typefaces

The layout matches austinstone.org, which sets **Proxima Nova** (sans, used for body *and*
headings) with **Freight Text Pro** as a sparing serif accent. Both are licensed fonts, so
they are named first in the stack and fall back to the closest free Google equivalents:

```css
--sans: 'Proxima Nova', 'Figtree', system-ui, -apple-system, sans-serif;
--serif:'Freight Text Pro', 'Source Serif 4', Georgia, serif;
```

If the foundation buys an Adobe Fonts / Typekit licence for Proxima Nova and Freight Text
Pro, add the kit `<link>` in the `<head>` and both take over automatically — no other edit.

The serif is deliberately used only for accents (the Psalm verse, the pullquote, the Giving
lead), the same way Austin Stone uses Freight Text Pro.

## 2. Running it

```bash
# just open it
open index.html

# or serve it locally if you prefer a real server
python3 -m http.server 8000     # then visit http://localhost:8000
```

Deploy = upload the folder. Netlify (drag-and-drop the folder), Cloudflare Pages, GitHub
Pages, or any shared host will serve it as-is. No environment variables, no secrets.

## 3. Design system already in place

Defined as CSS custom properties at the top of `index.html` under `:root` — change them
there and the whole site follows.

| Token | Value | Use |
|---|---|---|
| `--ink` | `#2b211a` | body text, deep warm brown-black |
| `--brown` | `#4e3c31` | brand brown, taken from the wordmark |
| `--brown-soft` | `#6a564a` | secondary brown |
| `--cream` | `#f7f3ea` | page background, warm paper |
| `--cream-2` | `#efe9dc` | alternating section background |
| `--sand` | `#e4dac8` | panels, image placeholders |
| `--blue` | `#a4bbd4` | brand dusty blue, from the logo mark |
| `--blue-deep` | `#6f88a8` | blue text on light backgrounds |

Type: **Fraunces** (serif, headings and pull quotes) and **Hanken Grotesk** (sans, body and
eyebrows), both loaded from Google Fonts. A subtle SVG noise grain is layered over the page
via `body::before`.

Motion: a `.reveal` fade-and-rise on hero elements, with a
`prefers-reduced-motion` opt-out already handled.

## 4. Page structure

One page, anchor navigation, in this order:

1. **Hero** (`#top`) — mission statement, Psalm 61:2
2. **Our Heart** (`#heart`) — who the foundation is, plus a pull quote
3. **Where We Work** (`#work`) — four region cards: Uganda, Madagascar, DR Congo, United States
4. **From the Field** (`#field`) — three story cards
5. **Partners We Walk With** (`#partners`) — six partner cards
6. **Follow Along** (`#instagram`) — six-tile Instagram grid
7. **A Note on Giving** — states that unsolicited proposals are not accepted
8. **Get in Touch** (`#contact`) — email address and a contact form
9. **Footer** — wordmark, Romans 11:36, Dana Point CA, copyright

Responsive down to mobile; grids collapse via media queries near the bottom of the style block.

## 5. What is NOT finished

This is the punch list. Everything below is deliberate placeholder work that needs real
content or real wiring before launch.

**Blocking — must be fixed before the site goes live**

- **The contact form does not send anything.** `<form class="form" onsubmit="...">` only
  swaps in a thank-you message. It needs a real endpoint — Netlify Forms (`data-netlify="true"`
  plus a hidden `form-name` field) is the two-minute version; Formspree or a mail handler
  otherwise. Confirm with the client where submissions should land.
- **The Instagram link is a dead link.** It points at `https://instagram.com/` rather than the
  foundation's account. Body copy references `@higherrockfoundation` — confirm the real handle.
- **The Instagram grid is fake.** Six CSS-gradient tiles labelled "Instagram post". Either pull
  the real feed (Instagram Basic Display API, or a widget such as Behold/EmbedSocial) or replace
  the tiles with six hand-picked photos linking to the account.
- **Confirm `hello@higherrockfoundation.org` is a live, monitored mailbox.**

**Photography — every image slot on the page is currently a coloured gradient**

- Four **region cards** (`.region .img`) — one photo each for Uganda, Madagascar, DR Congo, US
- Three **story cards** (`.story .img`) — one photo each
- A **hero background** would lift the page considerably if the client has a usable image

**Content to confirm with the client**

- The three "From the Field" stories read as example copy (Kampala cohort, Congo well,
  Madagascar church roof). Confirm each is accurate, or replace with real updates.
- Two partner cards render the organization name as text because no logo file exists:
  **Jesus Family Kingdom** (Madagascar) and **Acres of Life** (United States). Acres of Life
  also still says "Partner profile coming soon."
- Care for Children is listed under "Asia" while every other card names a country.

**Polish**

- No Open Graph or Twitter card tags, so link previews will be bare. Needs `og:title`,
  `og:description`, `og:image`.
- Favicon is `assets/mark-blue.png` only — no `.ico` or Apple touch icon.
- No custom 404 page (the host serves its default).
- No analytics.
- The Netlify auto-injected `hosting-provider` / `netlify-deploy` meta tags near the top of
  `<head>` can be deleted; they are not part of the design.
- Domain still needs pointing to `higherrockfoundation.org`.

## 6. If the decision is to rebuild on Wix instead

This came up in the June 22 thread — the client already has a Wix platform. Worth knowing
before that call:

- The page is deliberately quiet and typographic. Fraunces, the cream paper background and
  the grain are doing most of the work. A generic Wix template will not look like this without
  real effort on type and spacing.
- Wix does give the client self-service editing, which this static file does not.
- Either way, section 5 is the same list of missing content. The photos, the real stories,
  the Instagram handle and the form destination are needed regardless of platform.
- Middle path: keep this file and add a small CMS for "From the Field" only, since that is
  the one section that changes.
