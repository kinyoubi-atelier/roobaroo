# Roobaroo file audit — 2026-04-11

A stock-take of the Roobaroo design system before any further edits. What I looked at, what I could not look at, and what I found.

---

## 1. What I could audit

Only two files live in the Cowork workspace folder (`~/Desktop/KINYOUBI ATELIER & CO./WEB Designing/roobaroo package/Roobaroo/`):

| File | Lines | Size |
|---|---|---|
| `index.html` | 744 | ~1.05 MB (base64 images inlined) |
| `menu.html` | 204 | ~20 KB |

The four other files from last session — `roobaroo-standee-v2.html`, `roobaroo-banner.html`, `roobaroo-banner-portrait.html`, `roobaroo-status.html` — are **not** in this folder. They may still live in your `~/Desktop/roobaroo/` git repo, or they may only have existed in the previous Claude sandbox (which is gone). If you want those audited, drop them into this folder and I can do a second pass.

I also fetched the live site at `https://roobaroo.vercel.app/` to compare against the local copy.

---

## 2. Big headline: the three "pending" pushes all landed

The workspace `index.html` is **byte-identical** to what is currently served at `roobaroo.vercel.app`:

```
22decf764664eda8c0ad2ee3053b27ad  live-index.html
22decf764664eda8c0ad2ee3053b27ad  index.html  (workspace)
```

So whatever happened last session with the "nothing to commit, working tree clean" loop eventually resolved. All three of the previous edits are live right now:

- **Hotel eyebrow removal** — no "— Hotel —" in the hero, confirmed live
- **Seijaku strip (site only)** — zero `Seijaku` or `静` occurrences in `index.html` or in the live HTML. Hero rail says `"the art of quiet"` in all three languages. Footer tag reads `"quiet · garden · fire"`.
- **Domain swap** — every user-facing reference points at `roobaroo.vercel.app`. No `roobaroojeypore.com` left anywhere in the site file.

The one thing that is still missing on Vercel is `menu.html`:

```
HTTP/2 200   roobaroo.vercel.app/         ← index is live
HTTP/2 404   roobaroo.vercel.app/menu     ← menu is NOT on Vercel
```

So the footnote at `index.html:457` that points to `roobaroo.vercel.app/menu` currently lands on a 404. **This is the one item from the "three pending" that genuinely didn't make it.** Fixing it is just a matter of copying `menu.html` into your git repo and pushing — the file itself is in good shape (see §4).

---

## 3. `index.html` — findings

### Clean (verified)

- **Design tokens intact.** Full palette matches the locked set: `--ink #0b0f0c`, `--moss #2f3a1f`, `--moss-lift #55642f`, `--parchment #f1e7d0`, `--saffron #e8873a`, `--saffron-hi #f2a55a`. Extended tokens (`--ink-2/3`, `--parchment-2`, `--ghost`, `--hairline`) are consistent with themselves.
- **Fonts loaded correctly.** Fraunces (with SOFT/WONK axes), Cormorant Garamond, Italianno, Noto Serif Devanagari, Noto Serif Oriya, Shippori Mincho — all in one Google Fonts import.
- **Trilingual i18n is tight.** 11 `data-i18n` keys in the HTML, 11 keys in each of `en` / `hi` / `or` dictionaries — no orphans, no missing translations. All three language buttons (`EN`, `हिं`, `ଓ`) exist and wire up to `setLang()`. Odia font override for `[lang="or"]` selectors is present.
- **Contact info correct.** Phones `+91 81424 23488` (primary/WhatsApp) and `+91 63701 95759`, Instagram `@roobaroojeypore`, all present and linked (`tel:`, `https://wa.me/…`, `https://instagram.com/…`). Find Us says "Jeypore · Koraput · Odisha · India" — city-level, as intended.
- **Kinyoubi partner block** is present at line 543 with the QR image at line 540.
- **Vercel frosted sticker** at line 735 links to `https://vercel.com` with `aria-label="Hosted on Vercel"`.
- **Web Audio ambient pad** — four sine oscillators tuned to C3/E3/G3/B3 (warm C-major jazz voicing), LFO vibrato, 3-second fade-in, mute button wired up. 100% generative, zero external audio, zero copyright exposure.
- **No debug leftovers** — no `console.log`, `TODO`, `FIXME`, or `XXX` comments anywhere in the file.
- **External domains** are only what you'd expect: `fonts.googleapis.com`, `fonts.gstatic.com`, `instagram.com`, `wa.me`, `vercel.com`, and self-references to `roobaroo.vercel.app`.

### Known cruft (matches the brief — left intentionally)

- **Dead `.hero-eyebrow` CSS class at line 57.** The HTML consumer was removed during the hotel-eyebrow strip, but the class definition stayed. Harmless (~200 bytes). Leave it if you might reuse it; delete it if you want the file cleaner.
- **Meta description still starts with "Hotel Roobaroo Restaurant"** at line 7. The brief said this was intentional because Google and WhatsApp link previews use it. Your call whether to soften it to just "Roobaroo Restaurant — Jeypore, Odisha" for the editorial read.
- **Kinyoubi partner QR** at line 540 is base64-encoded — I cannot decode it from here, so I could not verify what URL it points to. Per the brief it's `instagram.com/kinyoubi.atelier` as a placeholder. Flag it to regenerate when you have the real Kinyoubi Atelier URL.

### Gaps worth considering

- **No Open Graph / Twitter card meta tags.** When someone pastes `roobaroo.vercel.app` into WhatsApp, Instagram DMs, or iMessage, the link preview currently falls back to just `<title>` + `<meta description>` — no custom image, no explicit card. For a restaurant whose primary distribution will be social sharing, this is worth adding. ~15 lines of `<meta property="og:*">` + one `og:image` (can point to the pergola base64 or a hosted version) would do it.
- **No `<link rel="canonical">`.** Low priority, but a `canonical` pointing at `https://roobaroo.vercel.app/` future-proofs you when/if `roobaroojeypore.com` comes online.
- **No `favicon.ico` link.** Browser tabs will show a blank icon. A simple lantern-O favicon would carry the wordmark into the tab.

---

## 4. `menu.html` — findings

### Clean

- **Palette and fonts** match `index.html` for everything it uses. It only imports the fonts it actually needs (drops Shippori Mincho, which is correct — no Kinyoubi block on the menu page). No broken `var(--…)` references.
- **Section structure** — eight sections, exactly as expected:
  - Beginnings (4 dishes)
  - From the Tandoor (5)
  - Indian Mains (6)
  - Chinese (5)
  - Biryani & Rice (4)
  - Breads (4)
  - Mocktails & Milkshakes (5)
  - Desserts (2)
  - **Total: 35 dishes, 43 price tokens** (8 dishes have a split price like `₹260 / ₹460`)
- **Dotted-leader format** is used consistently (`.dish` → `.body` → `.leader` → `.price`).
- **Nav "back to garden"** links are in place at the top and bottom, both pointing to `/` and `/#reserve`. No stale `roobaroo-site-v2.html` references.
- **Footer reads** "© Roobaroo Restaurant · Jeypore · Koraput · Odisha · all prices in ₹ inclusive of taxes · subject to availability" — clean copy.
- **No Open Graph tags** (same gap as index, same recommendation).

### Seijaku — STILL PRESENT here

**Line 99:**
```html
<div class="rail"><span class="line"></span><span class="dot"></span>
<span>Seijaku · the art of quiet</span>
<span class="dot"></span><span class="line"></span></div>
```

The Seijaku strip you did last session was site-only (per the brief). The menu page was added after that sweep and still carries the full `Seijaku · the art of quiet` rail. If you want the menu page to match the site's new "just `the art of quiet`" tone, this is a one-line edit — remove the words `Seijaku · ` from line 99.

### Content gaps

- **Placeholder prices.** All 43 ₹ values are plausible-range fillers I invented last session. You still need to drop in the real menu. Until then, `menu.html` should probably stay off the live deployment — or carry a visible "— preview, prices provisional —" banner to protect the brand.
- **No contact channels on the menu page.** If a guest scrolls the menu on their phone and wants to book, there is no `tel:`, no `wa.me/`, no IG handle anywhere on the page. Worth adding a quiet "reserve" strip at the bottom — one line with the WhatsApp deep link and `@roobaroojeypore`.
- **English-only copy.** The site is committed to trilingual; the menu is not. If Odia/Hindi names for sections (even just the 8 section labels, not every dish) matter to you, flag it. My default read: the menu being English-only is defensible because dish names are already a mix of languages in practice, but the section headers could carry Hindi/Odia subheads without cluttering the page.

---

## 5. Tokens — cross-file consistency

| Token | `index.html` | `menu.html` |
|---|---|---|
| `--ink` | `#0b0f0c` | `#0b0f0c` ✓ |
| `--ink-2` / `--ink-3` | `#111814` / `#161e18` | same ✓ |
| `--moss` | `#2f3a1f` | **not defined** (not used either — OK) |
| `--moss-lift` | `#55642f` | same ✓ |
| `--parchment` | `#f1e7d0` | same ✓ |
| `--parchment-2` | `#e6d9b8` | same ✓ |
| `--saffron` / `--saffron-hi` | `#e8873a` / `#f2a55a` | same ✓ |
| `--ghost` / `--ghost-2` | same rgba | same ✓ |
| `--hairline` / `--hairline-2` | same rgba | same ✓ |
| `--display` | `"Fraunces","Cormorant Garamond",serif` | `"Fraunces",serif` (different fallback, cosmetic) |
| `--jp` (Shippori Mincho) | present | **omitted** (correct — no Kinyoubi block) |
| `--odia` | present | **omitted** (correct — no Odia script in menu) |

No broken token references — `menu.html` only uses tokens it defines. The palette itself is 100% consistent across both files.

---

## 6. What I could NOT audit (and what to do about it)

The four files not in the workspace folder:

- `roobaroo-standee-v2.html` (foyer standee)
- `roobaroo-banner.html` (landscape 16:7)
- `roobaroo-banner-portrait.html` (portrait 7:16)
- `roobaroo-status.html` (9:16 Instagram/WhatsApp story)

Per the brief, all four still carry Seijaku / 静 branding — you mentioned "remove from all files" as a possible sweep, not yet done. Until I can see them, I cannot confirm anything about their current state (tokens, contact info, address lines, Kinyoubi QR).

**Two ways to fix this**, your pick:

1. **Drop them into this workspace folder.** Easiest — copy the four files from `~/Desktop/roobaroo/` (or wherever your canonical copies live) into `~/Desktop/KINYOUBI ATELIER & CO./WEB Designing/roobaroo package/Roobaroo/`. Next chat I'll audit them the same way.
2. **Tell me the GitHub repo is private and add me (or make it public temporarily).** The public `ankitsahu` account only shows `ankitsahu/ankitsahu` and `ankitsahu/deepdetect` — no `roobaroo` repo visible. If your Roobaroo repo is private or lives under a different account, I don't have visibility. Public for a minute would let me pull everything fresh.

---

## 7. Recommended next moves, in order

1. **Push `menu.html` to Vercel.** Single highest-impact fix — the footnote in your live site already advertises `/menu` and users hitting it get a 404. The file is clean enough to deploy; you can patch placeholder prices later. **Before you push**, do the tiny Seijaku edit on line 99 so the menu page matches the site's tone.
2. **Sweep the remaining four files** for Seijaku/静, but only after you decide the direction. Options: (a) keep Seijaku on print-only pieces as a subtle "design language" flag, or (b) remove everywhere for a unified "the art of quiet" voice. I'd lean (b) — consistency across touchpoints is the whole point of the design system.
3. **Open Graph meta** on `index.html` and `menu.html` — ten-minute edit, large payoff for the first time someone shares the link on WhatsApp.
4. **Real menu prices** — when Hotel Roobaroo sends them, I can do a single-pass replacement.
5. **`roobaroojeypore.com` DNS cutover** whenever you're ready. Unrelated to the file system, just a Vercel dashboard task.
6. **Kinyoubi Atelier real URL** — regenerate the partner QR in index.html (and in the standee file) once you have it.

---

## 8. Ground truth / deployment state as of this audit

- `index.html` — local matches live on Vercel (MD5 identical). ✅
- `menu.html` — local exists in workspace, **not deployed** (Vercel returns 404 on `/menu`). ❌
- Standee / both banners / status file — **not in workspace folder**, state unknown. Flag.
- `roobaroojeypore.com` — not resolving at Vercel (was not checked; per brief, DNS not yet cut over).

---

*Audit run 2026-04-11 by Claude in Cowork mode. Covers only the two files present in the workspace folder and the live `roobaroo.vercel.app` HTML.*
