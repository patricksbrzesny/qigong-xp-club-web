# Qi Gong XP Club – schwarz-auf-weiss.net

Statische Website (reines HTML/CSS, kein JavaScript, keine Cookies).

## Veröffentlichung

Die Seite wird über **GitHub Pages** aus dem Branch `main` (Wurzelverzeichnis) veröffentlicht. Jeder Push auf `main` geht automatisch live.
Die eigene Domain wird beim Go-live in den Pages-Einstellungen eingetragen (legt die Datei `CNAME` an).

## Bis zum Go-live: noindex

Alle HTML-Seiten (`index.html`, `impressum.html`, `impressum-ag.html`, `datenschutz.html`) enthalten `<meta name="robots" content="noindex">`, damit die Vorschau unter github.io nicht in Suchmaschinen landet. `robots.txt` erlaubt das Crawlen bewusst, sonst sähen Crawler das noindex nicht.

**Zum Go-live entfernen:** die `noindex`-Zeile in allen vier Dateien löschen.

## DNS bei united-domains

| Name | Typ | Wert |
|---|---|---|
| `@` | A | 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 |
| `www` | CNAME | patricksbrzesny.github.io |

MX- und TXT-Einträge (Mail) bleiben unverändert.
