# Memory — Sell Out Campaign

Session note: updated 2026-08-10. Keep this current — overwrite stale sections.

## Project overview
- **Landing page** for Divine Bakare's training: *"How Smart Nigerians Are Making Their First ₦50k–₦200k Selling What They Already Know... Without Learning Another Skill."*
- **Location:** `C:\Users\NexusPC\Projects\Sell Out Campaign`
- **Git repo:** yes — remote `origin` = https://github.com/thedivinebakare/selloutreg.git, branch `main`, git identity Divine Bakare <officialdivinebakare@gmail.com>.
- **Single-file site:** `index.html`, all styles inline, Tailwind via CDN, mobile-first.
- **Live:** https://selloutreg.vercel.app (Vercel, project `selloutreg`, linked via `.vercel/project.json`). Vercel CLI is now logged in on this machine.

## Files in project root
- `index.html` — **V3 (LIVE, APPROVED 2026-08-09)** + post-launch fixes below.
- `v3.html` — **FROZEN SNAPSHOT of V3**. Do NOT edit. Restore point if edits go wrong.
- `v25.html` — rejected card-stack hero version. `v2.html` — earlier version.
- `serve.mjs` — local server on port 3000 (`node serve.mjs`).
- `screenshot.mjs` — desktop (1280x800) puppeteer screenshot → `temporary screenshots/screenshot-N[-label].png`.
- `brand_assets/` — real brand assets; **`My Image (1).jpg` = Divine's portrait** (About section + hero invite card).
- `reference/` — structural blueprint images (re-render in brand style, don't copy look).
- `CLAUDE.md` — frontend rules: invoke `frontend-design` skill first each session; never screenshot `file://`; always serve localhost; deployment workflow.

## Design system (V3)
- Palette: ink `#0B1526`, royal deep `#16357F`, royal `#2654B6`, royal light `#3A6FE0`, pearl `#FBFCF8`, accent orange `#FF6B35`. CSS vars in `:root`.
- Type: **Bricolage Grotesque** display + **Archivo** body + **IBM Plex Mono** labels.
- Signature: `₦50k–₦200k` blue-gradient (`gradient-text`) with self-drawing orange underline (`money-sweep`, `.money-sweep::after` sweep animation).
- Theme: ink hero frame → pearl body → ink footer (ONE switch). Sections: hero+marquee (ink) → problem (hairline rows) → "What if…" → Inside (01/02/03 Offer/Position/Sell) → Who It's For → About (portrait) → Details → Register (6-step form) → Confirmed → footer + sticky countdown bar.

## Registration form → WhatsApp
- Multi-step form; on submit builds `https://wa.me/2348055791348?text=...` including name + phone, shows `#confirmed`. `DIVINE_WA` constant in submit handler.

## 2026-08-10 session — hero polish + go-to-top (LIVE)
Committed `fca8e49`, pushed, deployed, verified HTTP 200:
1. **Headline → solid 4 lines at ALL widths.** Replaced `<br>` + spans with four `block` spans: `Sell what you` / `already know.` / `Make your first` / `₦50k–₦200k.` (money-sweep span also `block`). Was: orphaned "know." at ~430px, collapsed to 2 lines on tablets.
2. **Pre-headline pill → controlled 2-line on mobile**, 1 line from `sm`. `items-start` + `mt-[6px]` dot aligned to first line; text = `Free private live training<br>Sat 29 Aug · 8:00 PM WAT` (mobile) / one line (sm+ via `hidden sm:inline` + `sm:hidden`). Removed `flex-wrap`; `py-2` mobile, `py-1.5` sm.
3. **Go-to-top button** (`#to-top`): fixed bottom-right (`right: clamp(1.25rem,4vw,2rem); bottom: 5rem; z-index:70`), 3rem ink circle, royal ring `rgba(58,111,224,.38)`, layered royal-tinted shadow, pearl stroke arrow. `.show` toggled by scroll > 700px; click → smooth scroll top (reduced-motion aware). Sits above sticky bar (z-60) — no overlap.

## 2026-08-10 session 2 — mobile headline size + pill one-liner (LIVE)
Committed `30b4feb`, pushed, deployed, verified HTTP 200:
1. **Mobile headline bigger:** `text-[2.9rem]` (46.4px) at ≥360px, `max-[359px]:text-[2.55rem]` fallback. Kept 4 solid lines.
2. **Orange underline fix:** money-sweep span is `block w-fit` → the sweep underline shrink-wraps exactly under `₦50k–₦200k.` (was spanning full column width). Underline box == text box at all widths (verified 258/291/337/401px).
3. **Preheadline → one line on mobile:** direct flex items (dot + `<span class="hidden sm:inline">` desktop full text + `<span class="sm:hidden">` = "Free Live Training · Sat 29 Aug"). Compact pill (292px at 375, 276px at 320 via `max-[349px]:px-2.5 max-[349px]:gap-1.5`). Desktop keeps full "Free private live training · Sat 29 Aug · 8:00 PM WAT". Time dropped on mobile pill (still on invite card/countdown).
- **Gotcha:** `elementHandle.screenshot()` (used by `mobile-shot.mjs`) HANGS on this Chrome; use `page.screenshot({clip})` instead (`mobile-shot2.mjs` works).

## Verification (2026-08-10)
- Headline = 4 lines at 320/375/414/430/640/768/1024/1280. No horizontal overflow (marquee excluded). No console errors.
- Go-to-top: hidden at top, visible after scroll, click scrolls to 0 and hides, no overlap with sticky bar.
- Live page checked: HTTP 200, `#to-top` + block headline present.
- **Known pre-existing quirk (NOT fixed, out of scope):** sticky countdown bar at 320px — the "Reserve My Seat" button clips past the right edge (bar content ~358px vs 320px). Doesn't cause horizontal scroll. Would need px/gap/cell shrink on very small screens.

## ENVIRONMENT / TOOLING
- **Dev server:** `node serve.mjs` → http://localhost:3000. Start before screenshots/QA.
- **Screenshots:** `node screenshot.mjs http://localhost:3000 [label]` (desktop). Mobile helper: `C:\Users\NexusPC\AppData\Local\Temp\puppeteer-test\mobile-shot.mjs` (env `VW` width, captures hero section, 2x DPR). This model (big-pickle) CANNOT read images — verify via DOM geometry instead.
- **Puppeteer:** installed at `C:/Users/NexusPC/AppData/Local/Temp/puppeteer-test/`; Chrome `C:/Program Files/Google/Chrome/Application/chrome.exe`; cache `C:/Users/NexusPC/.cache/puppeteer/`. Always unique timestamped profile dir; **kill stale chrome**: `Get-CimInstance Win32_Process -Filter "Name='chrome.exe'" | ? { $_.CommandLine -match 'profile-' } | % { Stop-Process -Id $_.ProcessId -Force }`.
- **QA scripts** in puppeteer-test: `verify.mjs` (h1 lines + pill + overflow + toTop at 8 widths), `verify-top.mjs` (sticky bar + toTop click), `diag-hero.mjs`/`diag-lines.mjs` (line breakdowns). Run with `workdir` there.
- **PowerShell flakiness:** node scripts hang sometimes from `Temp\opencode`; run from `puppeteer-test` with workdir. `cmd /c ...` also worked.

## DEPLOY WORKFLOW (per CLAUDE.md — "make it live")
1. `git add index.html && git commit -m "..."` → `git push origin main`
2. `vercel --prod` (project is linked; CLI is logged in now — `vercel login` already done 2026-08-10).
3. Verify: `Invoke-WebRequest https://selloutreg.vercel.app` → expect 200; check new markup present. Tell user the live link.
- **Never deploy without explicit instruction.** Don't touch `v3.html`.
