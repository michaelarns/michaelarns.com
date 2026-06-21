# Deployment & Infrastruktur — michaelarns.com

Referenz zur Hosting- und Deployment-Konfiguration dieses Projekts.
Stand: Juni 2026.

> ⚠️ **Keine Secrets hier ablegen.** Keine API-Tokens, Account-IDs, Passwörter oder
> personenbezogenen Daten in diese Datei aufnehmen.
>
> ℹ️ Werte, die nur im Cloudflare-/GitHub-Dashboard leben (DNS-Records, Custom-Domain-Status,
> Branch-Protection), sind unten als **Soll-Konfiguration** beschrieben und sollten bei
> Unsicherheit im jeweiligen Dashboard verifiziert werden.

---

## Überblick

- **Website:** statische Astro-Seite (Coming-Soon-Landingpage), Build-Output `dist/`.
- **Quellcode:** dieses GitHub-Repo (`michaelarns.com`).
- **Hosting:** Cloudflare Pages (Projekt `michaelarns-com`). Cloudflare baut bei jedem Push
  automatisch und liefert die Seite aus (zugleich CDN + SSL + DNS).
- **Zwei Umgebungen** über Git-Branches (siehe unten).
- **GitHub Pages:** nicht verwendet — nicht anlegen oder aktivieren.

---

## Umgebungen (Stages)

| Stage | Branch | URL | Zweck |
|---|---|---|---|
| **Production** | `main` | https://michaelarns.com · https://www.michaelarns.com | Live-Seite |
| **Dev** | `dev` | https://dev.michaelarns-com.pages.dev | Entwicklung/Preview |
| Lokal | — | http://localhost:4321 (`npm run dev`) | lokale Entwicklung |

- Produktions-Standardhost von Cloudflare Pages: `michaelarns-com.pages.dev`
  (zeigt denselben Stand wie `main`).
- **Regel:** Jeder Branch außer `main` erzeugt automatisch eine Preview unter
  `<branch>.michaelarns-com.pages.dev`.
- **Hinweis:** Cloudflare-Pages-Projektnamen erlauben keinen Punkt; der Domainname
  `michaelarns.com` wird daher als Projekt `michaelarns-com` geführt.
- Eine `staging`-Stufe existiert aktuell **nicht**. Bei Bedarf einen `staging`-Branch
  anlegen — Cloudflare erzeugt dann automatisch `staging.michaelarns-com.pages.dev`.

---

## Arbeitsablauf (Dev → Prod)

`main` ist geschützt — **kein direkter Push**, Aktualisierung nur per Pull Request.

1. Auf `dev` entwickeln → prüfen auf `dev.michaelarns-com.pages.dev`
2. `dev` → `main` per **Pull Request** mergen → geht live auf `michaelarns.com`

```bash
# Beispiel
git checkout dev && git pull && # ...Änderungen committen & pushen...
# dann PR dev -> main auf GitHub erstellen und mergen
```

Jeder Push/Merge löst automatisch das passende Cloudflare-Deployment aus.

---

## Lokale Entwicklung

```bash
npm install        # Abhängigkeiten installieren
npm run dev        # Dev-Server (http://localhost:4321)
npm run build      # Produktions-Build nach ./dist
npm run preview    # Build lokal testen
```

Empfohlene Laufzeit: **Node.js ≥ 22.12.0** (Cloudflare-Standard, identisch zu den
Schwester-Repos). Optional eine `.nvmrc` mit `22.12.0` ergänzen.

---

## Cloudflare Pages — Einstellungen

Pfad: Cloudflare Dashboard → Compute → Workers & Pages → **michaelarns-com**

| Einstellung | Wert |
|---|---|
| Projektname | `michaelarns-com` |
| Framework-Preset | Astro |
| Build command | `npm run build` |
| Build output directory | `dist` |
| Root directory | (leer) |
| Production branch | `main` |
| Preview-Deployments | alle Nicht-Production-Branches |
| Environment-Variable | `NODE_VERSION = 22.12.0` (in **Production UND Preview** gesetzt) |

- **Custom Domains** (Tab „Custom domains"):
  - Production: `michaelarns.com` und `www.michaelarns.com`
  - alle Active, SSL aktiv.
- **Rollback:** Tab „Deployments" → altes Production-Deployment → „Rollback".

---

## Domains & DNS

DNS wird über **Cloudflare** verwaltet (Zone aktiv, Nameserver bei Cloudflare).
Pfad: Dashboard → Domain `michaelarns.com` → DNS → Records.

**Web (auf Cloudflare Pages) — Soll-Konfiguration:**

| Name | Typ | Ziel | Proxy |
|---|---|---|---|
| `michaelarns.com` | CNAME | `michaelarns-com.pages.dev` | Proxied |
| `www` | CNAME | `michaelarns-com.pages.dev` | Proxied |

> **E-Mail & Verifizierungs-Records NICHT ändern** (z. B. MX, SPF/`TXT`, DMARC,
> Site-Verification). Die Domain nutzt extern gehostete E-Mail; bestehende Records vor
> jeder DNS-Änderung im Dashboard prüfen und unverändert lassen — sie sind hier bewusst
> nicht im Detail abgebildet.

---

## GitHub — Einstellungen

- **Repo:** `michaelarns/michaelarns.com`, Default-Branch `main`.
- **Branch Protection auf `main`** (Settings → Branches, Soll-Konfiguration):
  - Require a pull request before merging — **Required approvals: 0**
  - Do not allow bypassing the above settings
  - Force-Pushes blockiert · Löschen von `main` blockiert
  - → kein direkter Commit/Push auf `main`
  - Hinweis: Auf dem **Free-Plan** wird Branch Protection nur bei **öffentlichen** Repos
    erzwungen; für private Repos ist GitHub Pro nötig.
- **GitHub Pages:** deaktiviert / nicht verwendet.
- **GitHub-Actions-Deploy-Workflow:** keiner — überflüssig, da Cloudflare automatisch baut.
  Nicht neu anlegen.

---

## Spickzettel: „Wie ändere ich die Live-Seite?"

1. Branch `dev` auschecken, Änderung machen, pushen.
2. Auf `dev.michaelarns-com.pages.dev` kontrollieren.
3. Pull Request nach `main` erstellen und mergen.
4. Cloudflare deployt automatisch → live auf `michaelarns.com` (1–3 Min).
