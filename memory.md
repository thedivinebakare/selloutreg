# Memory — Sell Out Campaign

Session note: written 2026-08-08 to resume from where we stopped. Keep this updated.

## Project overview
- **Landing page** for Divine Bakare's training: *"How Smart Nigerians Are Making Their First ₦50k–₦200k Selling What They Already Know... Without Learning Another Skill."*
- **Location:** `C:\Users\NexusPC\Projects\Sell Out Campaign`
- **Not a git repo** (as of last session). No commits, no remote.
- **Single-file site:** everything in `index.html`, all styles inline, Tailwind via CDN (`https://cdn.tailwindcss.com`), mobile-first.

## Files in project root
- `index.html` — **V3 (CURRENT LIVE, USER-APPROVED 2026-08-09)**
- `v3.html` — **FROZEN SNAPSHOT of V3** — the exact version the user approved ("store this so new changes don't affect it"). If future edits go wrong, restore `index.html` from `v3.html`. Do NOT edit v3.html.
- `v25.html` — backup of the previous (rejected) card-stack hero version
- `v2.html` — earlier approved V2
- `serve.mjs` — local static server on port 3000 (`node serve.mjs`)
- `screenshot.mjs` — puppeteer screenshot script (1280x800), saves to `temporary screenshots/screenshot-N[-label].png`
- `brand_assets/` — real brand assets; **`My Image (1).jpg` is Divine's portrait** (used in About section)
- `reference/` — reference images (structural blueprint only, re-render in brand style)
- `CLAUDE.md` — frontend rules (invoke `frontend-design` skill first each session; never screenshot `file://`; always serve on localhost)

## V1 vs V2 vs V3
- **V3 is LIVE and APPROVED (2026-08-09).** Full redesign: dark ink hero frame → pearl body → ink footer (ONE theme switch). Type: **Bricolage Grotesque** display + **Archivo** body + **IBM Plex Mono** labels. Signature: `₦50k–₦200k` blue-gradient with self-drawing orange underline (`money-sweep`). Sections: hero+marquee (on ink) → problem (hairline rows) → "What if…" statement → Inside (sticky col + numbered 01/02/03 Offer/Position/Sell) → Who It's For (checklist + dark card) → About (real portrait) → Details → Register (6-step form) → Confirmed → ink footer + sticky countdown bar.
- **Key lesson:** the flashy rotating card-stack hero (v25) was REJECTED. User's brand = minimalist/premium/strategic (see `brand_assets/visual-identity.md`). Keep heroes calm and typography-driven.
- **Restore:** `v3.html` (approved) → `index.html`. `v25.html` = rejected card-stack. `v2.html` = earlier version.
- **V1** (earliest): no backup; survives only in `temporary screenshots/screenshot-{2,3,4}*.png`.


## V2 design system
- **Brand palette:** royal navy `#16357F`, `#2654B6`, `#3A6FE0`, deep ink `#0B1526`, accent orange `#FF6B35` (derive from these, never default Tailwind indigo/blue).
- **Typography:** display = **Fraunces** serif (headings, `font-display`), body = system sans. Tight tracking `-0.03em` on big headings, `line-height 1.7` body.
- **Header/footer wordmark:** "The Divine Bakare" (Fraunces) — text only, no image in nav.
- **Sections (`id` order):** `top` (hero), `pattern`, `whatif`, `discover`, `foryou`, `about`, `details`, `reserve`, `register`, `confirmed` (form + success panel).
- **Signature interactions:** marquee strip (CSS `marquee` anim, 2 duplicated groups, seamless), count-up stats (2, 4, 90, 100%), hero h1 shine (`hshine` ::after anim), tilt card (`#tilt-card`), reveal-on-scroll (`[data-reveal]`, `.in` class via IntersectionObserver), layered color-tinted shadows (`.shadow-royal`), SVG grain texture, gradient overlays + `mix-blend-multiply` on images.

## Registration form → WhatsApp
- Multi-step form (name / WhatsApp / email → who → selling → sell what → challenge → investment → goal). On submit builds a WhatsApp deep link to **`2348055791348`** (`https://wa.me/2348055791348?text=...`) including name **and phone**, shows `#confirmed` panel.
- `DIVINE_WA` constant in the submit handler at `index.html:1125`.

## V2 QA results — ALL PASSED (2026-08-08)
- No horizontal overflow at 320 / 375 / 390 / 1280 px
- No page errors / console errors
- Count-up animates to 2, 4, 90, 100% when stats strip scrolls into view
- Marquee, tilt card, h1 shine, reveals all working
- Form flow works end-to-end; WA link correct with name + phone
- About portrait (`brand_assets/My Image (1).jpg`) loads (2560px natural width)
- Desktop viewport 1280x800 shows 2 reveals at load; mobile reveals on scroll (intended)

**Fixes made during verification:**
- Nav: removed invalid `h-15` utility → `min-h-16` (`index.html` header nav)
- What-If panel: added `relative` so its glow anchors inside the panel
- About halo: `-inset-6` → `-inset-3 lg:-inset-6` to stop 4px mobile overflow
- WA message: re-added phone line ("My WhatsApp number is ...")

## ENVIRONMENT / TOOLING
- **Dev server:** run `node serve.mjs` → `http://localhost:3000` (was running, pid 11684; verify with `Invoke-WebRequest http://localhost:3000`)
- **Screenshot:** `node screenshot.mjs http://localhost:3000 [label]` → PNGs in `temporary screenshots/`. Read PNGs with the Read tool for visual diffing.
- **Puppeteer:** installed at `C:/Users/NexusPC/AppData/Local/Temp/puppeteer-test/`; Chrome cache `C:/Users/NexusPC/.cache/puppeteer/`
- **QA scripts** (working copies live in `C:/Users/NexusPC/AppData/Local/Temp/puppeteer-test/`): `v2qa4.js` (full verify: overflow/errors/marquee/tilt/counts/reveals/form/WA), `diag.js` (overflow offenders + counts + WA), `diag2.js` (unclipped overflow), `diag3.js` (multi-width overflow + WA phone). Outputs written to `C:/Users/NexusPC/AppData/Local/Temp/opencode/*-out.txt`.
- **Working launch pattern:** `puppeteer.launch({ headless:true, executablePath:'C:/Program Files/Google/Chrome/Application/chrome.exe', userDataDir: <UNIQUE timestamp+random dir> })`. **Never reuse a fixed profile dir.**

## KNOWN GOTCHAS (important)
1. **`screenshot.mjs` profile lock:** it uses a FIXED `userDataDir` (`~/.cache/puppeteer/profile`) → a stale headless Chrome locks it → launch hangs/fails with "The browser is already running for ... Use a different userDataDir". **Recommended fix (not yet applied):** timestamped profile per run. Last session's screenshot step never completed because of this.
2. **PowerShell flakiness:** commands intermittently hang with "(no output)" + timeout, especially node scripts run from `...\Temp\opencode\`. **Workaround that works:** write the script directly into `...\Temp\puppeteer-test\` and run with `workdir` there (node finds its local modules). `cmd /c ...` also worked when PowerShell hung.
3. **Killing stale headless Chrome:** `wmic process where "name='chrome.exe'" get ProcessId,CommandLine /format:csv | Select-String puppet` → extract PID → `taskkill /PID <pid> /F /T`.
4. Node on this machine: `node v24.18.0`.

## IN PROGRESS / RESUME HERE TOMORROW — GitHub + Vercel deploy
User request: **"push to GitHub and then Vercel"** (V2 live). Approved plan:
1. Install **GitHub CLI** (`winget install --id GitHub.cli`) → `gh auth login` (browser). ⚠️ **STATUS: install FAILED / not verified** — the winget command returned empty output and no `gh.exe` was found at `C:\Program Files\GitHub CLI\gh.exe`. Retry needed (maybe `winget upgrade --all` or download from https://github.com/cli/cli/releases).
2. Install **Vercel CLI** (`npm i -g vercel`) → `vercel login` (browser) → `vercel --prod`. ⚠️ **STATUS: not installed** (`vercel.cmd` not found).
3. **Git identity NOT provided yet** — user chose "I'll type it" but hasn't given name/email. Ask for them before committing (git has no global user.name/email configured).
4. Then: `git init` → add `.gitignore` (e.g., `temporary screenshots/`) → commit → `gh repo create sell-out-campaign --public --source=. --push` → `vercel --prod`.

Auth decisions from user: **browser login for both gh and Vercel** (no PAT). Git Credential Manager is bundled with Git for Windows (`git-credential-manager.exe` present), so HTTPS push can use browser OAuth.

## NEXT SESSION CHEATSHEET
- Start server: `node serve.mjs` (in project root, background) before any screenshot.
- Preview V2: `http://localhost:3000`
- Verify after edits: run `v2qa4.js` from `...\puppeteer-test\` and read `...\Temp\opencode\v2-qa-out.txt`.
- Visual pass: `node screenshot.mjs http://localhost:3000 v2-check` then Read the PNG.
- Resume deploy: retry gh + vercel install, ask user for git name/email, then init/push/deploy.
