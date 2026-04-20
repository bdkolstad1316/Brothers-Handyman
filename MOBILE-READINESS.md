# Mobile Readiness Report: brothers-handyman-production.up.railway.app

**Overall: B** — Solid mobile foundation with a few tap-target and type-size nits to clean up.

Audited 2026-04-20 against https://brothers-handyman-production.up.railway.app/. Static analysis only; no browser rendering.

## Grades

| Category | Grade | Summary |
|---|---|---|
| Viewport & Layout | A | Viewport correct, 2 media queries, no fixed-width traps. |
| Tap Targets | B | CTAs big, nav hamburger 44px; stacked nav items ~34px. |
| Typography | B | Body inherits 16px default; copyright dips to 12px. |
| Forms | N/A | No forms on site. |
| Performance | A | 30KB total, no images, no render-blocking JS. |
| Navigation & Interaction | B | JS-required mobile menu, no `<noscript>` fallback. |

## Punch List

Ordered by impact — fix top-down.

### 1. Tap Targets — Open mobile menu items are under 44px tall
**Current:** `.nav-links.open a` at line 361 has `padding: 8px 0` + `font-size: 1.1rem` (line-height 1.6) → rendered height ≈ 33–34px. WCAG 2.5.5 wants 44×44.
**Fix:** Bump padding on the open-menu links:
```css
.nav-links.open a {
    font-size: 1.1rem;
    padding: 14px 0;          /* was 8px 0 */
    min-height: 44px;
    display: flex;
    align-items: center;
}
```
**Why it matters:** Fat-fingered taps miss small targets — Kenny's customers are contractors on jobsites swiping with gloves on.

### 2. Typography — Footer copyright dips to 12px
**Current:** Line 618, `font-size: 0.75rem` on the copyright line resolves to ~12px — right at the WCAG floor.
**Fix:** Lift to 0.8rem or leave at 0.75rem but bump color contrast (currently `#666` on `#222` — ~4.1:1, below AA for small text):
```html
<p style="margin-top:12px; font-size:0.8rem; color:#888;">&copy; 2026 ...</p>
```
**Why it matters:** Legal/attribution lines still need to be legible. Aging eyes + sunlight on a phone screen struggle with 12px on dark-gray-on-gray.

### 3. Navigation — Mobile menu requires JS with no fallback
**Current:** `.nav-links` is `display: none` on mobile; only JavaScript flipping `.open` makes it visible. If JS fails or is blocked, the hamburger does nothing and there's no `<noscript>` fallback.
**Fix:** Add a `<noscript>` block that force-shows the menu, or use the native `<details>`/`<summary>` pattern so it works without JS:
```html
<noscript>
  <style>.nav-links { display: flex !important; }</style>
</noscript>
```
**Why it matters:** It's a single-page site, so users can still scroll, but the menu being dead with JS off is a silent failure. Two lines of insurance for free.

### 4. Tap Targets — Verify on a real device
**Current:** Buttons and phone links look generous in CSS (`.btn` padding 14×36, mobile `.hero-cta .btn` padding 16×24 full-width), but static analysis can't confirm rendered size. The hamburger has `min-width/height: 44px` ✓.
**Fix:** Pull up on an iPhone SE and a mid-size Android, tap every link. No code change unless something feels cramped.
**Why it matters:** Report writer's honesty clause — static tools can't see what a thumb sees.

### 5. Typography — Body text in service/why cards is 14–15px
**Current:** `.service-card p` at 0.92rem (~14.7px, line 217) and `.why-item p` at 0.88rem (~14px, line 305).
**Fix:** Nudge to 0.95rem or 1rem on mobile specifically:
```css
@media (max-width: 768px) {
    .service-card p, .why-item p { font-size: 1rem; }
}
```
**Why it matters:** Desktop can get away with 14px; on a phone held at arm's length, 16px reads noticeably easier. Not broken, just a polish.

## What's solid

- **Viewport meta is textbook** — `width=device-width, initial-scale=1.0`, doesn't block zoom. A+ accessibility default.
- **Phone links are everywhere and huge** — `tel:` anchors in hero, contact section, and footer. Click-to-call is the whole funnel for a handyman site and it's nailed.
- **Page weight is absurdly lean** — 30KB total, zero images (SVG icons inline), one Google Fonts request. This loads in a heartbeat on 3G.
- **Mobile-specific CSS is thoughtful** — `100svh` for modern mobile viewport, `scroll-margin-top` so anchor jumps don't hide behind the sticky nav, full-width stacked CTAs in the hero, `transform: none` on hover for touch devices.

## Verification

After applying fixes, re-run: `python mobile-readiness/scripts/audit.py https://brothers-handyman-production.up.railway.app/` or retest at [pagespeed.web.dev](https://pagespeed.web.dev).
