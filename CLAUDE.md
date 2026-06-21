# michaelarns.com — Projektkontext

Persönliche Präsenz von Michael Arns. Aktuell eine schlanke **Coming-Soon-/Landingpage**
(„Hier entsteht eine neue Präsenz.") — bewusst minimal gehalten und ausbaufähig.

## Stack
- **Astro 6**, statischer Build nach `dist/`. **Kein** Tailwind, **kein** UI-Framework.
- Eine einzige Seite (`src/pages/index.astro`) auf Basis eines `Layout.astro`
  (`src/layouts/`). Styling über **scoped CSS** in der Seite und **globales CSS**
  (`<style is:global>`) im Layout.
- Dunkles Design mit Gold-Akzent über CSS-Custom-Properties
  (`--bg`, `--fg`, `--muted`, `--accent: #c9a96a`). System-Schriftstack (kein Web-Font).
- Dezente Animationen (`rise`, `spin`), per `prefers-reduced-motion` deaktivierbar.
- Assets in `public/` (`favicon.svg`). `site` ist in `astro.config.mjs` auf
  `https://michaelarns.com` gesetzt.

## Deployment — WICHTIG
- **Produktion läuft über Cloudflare Pages, NICHT GitHub Pages.** Cloudflare baut `main`
  automatisch über seine eigene GitHub-Integration. Build-/Deploy-Status ist **nur im
  Cloudflare-Dashboard** sichtbar — **nicht** über GitHub Actions / `gh run list`.
- Es gibt **keinen** GitHub-Actions-Deploy-Workflow. **Nicht** neu anlegen oder aktivieren.
- Vollständige Infra-Doku: **`DEPLOYMENT.md`** (Umgebungen, Domains, Cloudflare-Settings).
  Bei Deploy-/Infra-Fragen IMMER zuerst dort nachsehen.

## Branch-Flow
`dev → main`, jeweils mit eigener Cloudflare-Preview unter
`<branch>.michaelarns-com.pages.dev`. **`main` ist protected** → Aktualisierung nur per
**Pull Request** (kein direkter Push). Merge nach `main` = Live-Deploy auf michaelarns.com.
(Bei Bedarf später um eine `staging`-Stufe erweiterbar — siehe `DEPLOYMENT.md`.)

## Verifikation bei UI-Änderungen
Lokal `npm run dev` / `npm run preview`; gegen **1440 / 768 / 390 px** prüfen
(die vorhandenen `clamp()`-Größen skalieren bereits responsiv). Accessibility erhalten
(`lang="de"`, sinnvolle Meta-/OG-Tags, `prefers-reduced-motion`, `aria-hidden` für rein
dekorative Elemente). Empfohlene Laufzeit: **Node.js ≥ 22.12.0** (Cloudflare-Standard).
