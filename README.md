# echodraft-landing

Beta-tester landing page for Echoes. Single static HTML file
implemented from the **Claude Design** handoff (`Echoes Landing.html` —
"living draft" hero direction). No framework, no build step, no JS
dependencies. Inline `<style>`; a small `<script>` runs the typing→draft
hero animation on load.

```
echodraft-landing/
├── index.html       ← the entire page (HTML + inline CSS + tiny JS)
├── favicon.svg      ← brand mark, served as the tab icon
├── vercel.json      ← security headers + clean URLs
├── .gitignore
└── README.md        ← you are here
```

## Page sections (in scroll order)

1. **Nav** — sticky, blurred, brand mark + wordmark, "Private beta · Android" chip.
2. **Hero — Living Draft** — pulsing mark, "Listening" eyebrow, hesitant spoken
   line types itself in italic, then resolves into a real two-line draft
   with brass italic emphasis on *smaller arrows*. CTA + scroll hint fade
   up at the end. `prefers-reduced-motion` shows the resolved state instantly.
3. **01 · What this is** — 2-col with brass italic drop-cap lede.
4. **02 · How it works** — three hairline-grid steps + Capture→Draft phone sequence.
5. **Pull quote** — "Begin where you'd *begin a letter*."
6. **03 · Install on Android** — trust rows + the **install card** (signed-build
   header, numbered steps, SHA-256 footer). Engineered specifically to defuse
   sideload anxiety.
7. **04 · What it costs** — Free / $5 monthly (featured) / $40 annual tiers
   with a "without selling what you said" closing note.
8. **05 · This is a beta** — honesty + "If something breaks, send" card
   with `hello@tryechoes.app` mailto.
9. **Footer** — mark, wordmark, contact/privacy/terms.

---

## 1. First deploy (~10 minutes, one-time)

### A. Push this folder to GitHub

```bash
cd "/Users/AB/Design Project👨‍💻/echodraft-landing"
git init
git add .
git commit -m "Initial landing page"
```

Then create a new **public** repo on GitHub called `echodraft-landing`
(no README, no .gitignore — leave it empty so the first push doesn't
conflict), and:

```bash
git remote add origin git@github.com:YOUR_USERNAME/echodraft-landing.git
git branch -M main
git push -u origin main
```

### B. Import to Vercel

1. Go to https://vercel.com/new
2. Pick **Import Git Repository**, select `echodraft-landing`.
3. Framework preset: **Other** (it's plain HTML — no framework needed).
4. Leave every other setting default. Click **Deploy**.
5. ~30 seconds later you'll get a live URL like
   `https://echodraft-landing-abc123.vercel.app/`.
6. In the Vercel project → **Settings → Domains**, click **Edit**
   on the auto-assigned URL and shorten it to something memorable
   like `tryechoes.app`.

That's it — the page is live. Every `git push` to `main` redeploys
automatically.

---

## 2. Cutting a beta release (every time you ship a new APK)

The hero download button currently scrolls to the Install section as
a fallback. To make it actually download the APK:

### A. Build the APK

```bash
cd "/Users/AB/Design Project👨‍💻/Echoes"
eas build -p android --profile commercial
```

Wait for the build to finish, then **download the APK** from the EAS
build URL to your laptop.

### B. Create a GitHub Release

You need a public-readable home for the APK. Two options:

**Option 1 — Same repo as the app code** (if your Echoes app code
lives in a public GitHub repo). Release files inherit the repo's
visibility — public repo = public download URL.

**Option 2 — Separate releases repo.** If your app code is private,
create a new public repo just for releases: e.g.
`echodraft-releases`. Push an empty README, then attach APKs to
releases there.

In either case, in the chosen repo:

1. GitHub → **Releases → Draft a new release**.
2. **Tag:** `v0.2.0` (match the `version` in `app.config.ts`).
3. **Title:** `Echoes v0.2.0 — beta`.
4. **Notes:** what changed since the last release (one bullet list).
5. **Attach the APK:** drag the file from your downloads into the
   "Attach binaries" box. Rename it to `echodraft-v0.2.0.apk` so it
   matches the filename shown in the install card.
6. Tick **Set as a pre-release** (it's a beta — keeps the green "latest"
   badge off).
7. Click **Publish release**.
8. After publishing, **right-click the .apk asset** under the release
   and **Copy link address**. URL looks like:

   ```
   https://github.com/USER/REPO/releases/download/v0.2.0/echodraft-v0.2.0.apk
   ```

### C. Update the landing page

Three places in `index.html` to touch (search for the strings):

| Find | Change to |
|---|---|
| `href="#install" id="download-cta"` | `href="YOUR_GITHUB_APK_URL" id="download-cta"` |
| `<b>v0.2.0</b> · ~25 MB · Android 8+` | bump the version + size to match your release |
| `<span class="fn">echodraft-v0.2.0.apk</span>` (install card header) | bump the filename to match your release |

There's a clearly-marked HTML comment right above the hero CTA pointing
to this README — won't get lost between releases.

Then push:

```bash
git add index.html
git commit -m "Release v0.2.0"
git push
```

Vercel redeploys in ~30s. Beta testers now see the new build.

### D. Want a stable "latest" URL?

GitHub also exposes `…/releases/latest/download/echodraft.apk` which
auto-points at whatever is tagged latest (non-prerelease). If you stop
marking releases as pre-release, you can hardcode that URL in the
landing page once and never edit it again on subsequent releases.

---

## 3. When you buy `tryechoes.app` (or another domain)

1. Buy the domain anywhere (Namecheap, Cloudflare Registrar, Porkbun…).
2. In Vercel project → **Settings → Domains**, click **Add** and enter
   `tryechoes.app`.
3. Vercel shows you the DNS records to add:
   - For root domain (`tryechoes.app`): an **A record** → `76.76.21.21`
   - For `www.tryechoes.app`: a **CNAME** → `cname.vercel-dns.com`
4. Add those records at your registrar. Propagation: minutes to hours.
5. Vercel auto-provisions a Let's Encrypt SSL certificate once DNS resolves.

Update any external references (Stripe success URLs aren't relevant
since they go to `echodraft://`, not the web; but if you later add a
"Sign up on web" path, you'll point it at this domain).

---

## 4. Pre-launch checklist (before charging real money)

These need to exist before flipping Stripe to live mode:

- [ ] **Privacy policy** at `/privacy` — explain what data we collect
      (email, audio sent to Groq for transcription, prompts sent to
      Anthropic for drafting), retention, third-party processors.
- [ ] **Terms of service** at `/terms` — usage rules, subscription
      terms, refund policy.
- [ ] **Support email** confirmed: `hello@tryechoes.app` (currently
      live in the page footer + beta card).
- [ ] **Privacy/Terms links** un-stubbed in the footer of `index.html`
      (they're `href="#"` placeholders right now).
- [ ] **Reproducible-build claim.** The install card says "Signed,
      reproducible build." Confirm this is true — if EAS Build is your
      signer, the keystore lives on Expo's servers, which is *signed*
      but not *reproducible* in the strict sense. Soften the copy to
      "Signed build" if needed.
- [ ] **SHA-256 footer claim.** Says "SHA-256 published with every
      release." Make sure you actually publish the checksum in each
      GitHub Release's notes (`sha256sum echodraft.apk`).

Templates for v1: https://www.termsfeed.com (free generators, edit by
hand to match your actual processors — Anthropic, Groq, Supabase, Stripe).

---

## 5. Editing the page

Everything is inline in `index.html`. Sections are marked with HTML
comments (`<!-- ═══════════ HERO ═══════════ -->`). Common edits:

- **Hero headline** — the resolved draft text lives in `#living-draft`
  (two `<p>` tags). The hesitant typed line is in the `ops` array
  inside the script at the bottom of the file.
- **Install steps** — search for `class="istep"` (four of them).
- **Pricing values** — search for `class="tier"` (three of them).
  The featured one has class `tier feature` for the brass border.
- **Contact email** — search for `mailto:hello@tryechoes.app` (two refs).
- **Brand colors** — `:root` block at the top of the `<style>` defines
  every token. Don't hardcode hex values in markup.

The page is desktop-first. Two breakpoints: `≤980px` (tablet — hero
stage collapses, steps & tiers stack) and `≤600px` (mobile — hero
typography shrinks, sequence stacks vertically, CTAs go full-width).
All three breakpoints already designed and tested in the source.

### Brand intent (don't "fix" these)

Per the designer's spec — preserve these even when iterating:

- **Brass italic once per headline.** Never two. This is the signature.
- **Install card is load-bearing trust.** The verified badge, mono
  filename, checksum line — they exist to defuse sideload anxiety.
  Don't remove them while "tidying."
- **Calm > dense.** Generous band padding and the quiet pull-quote
  band are deliberate. The brand is "between Apple product pages and
  Linear's blog," not a busy SaaS marketing page.
- **Mark as wayfinding.** The brand mark recurs in nav, hero, step 3,
  install card, and footer on purpose. The lead-bar pulses only on
  idle chrome (nav + hero).
- **No emoji, no exclamation marks, period stops, concrete verbs.**
  Match the voice when writing new copy.

### Adding screenshots later

The `phone` component is already designed (296px frame, 7px bezel,
40px radius). To add a real screenshot, replace `.screen` contents with
an `<img>` at the screen's aspect ratio — see the Capture and Draft
phone bodies inside the **How it works** section for the structure.
