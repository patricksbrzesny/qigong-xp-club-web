# qigong-xp-club-web

Statische Website für **schwarz-auf-weiss.net**: Hinweis zur Einstellung der Schwarz auf Weiss AG und darunter der Qi Gong XP Club (Qi Gong XP Club GbR).

Reines HTML/CSS, kein Build-Schritt, kein JavaScript, keine Cookies, keine externen Schriften oder Skripte. Alle Dateien werden vom eigenen Webspace geladen.

## Aufbau

| Datei | Inhalt |
| --- | --- |
| `index.html` | Hinweis Schwarz auf Weiss AG, danach Qi Gong XP Club |
| `impressum.html` | Impressum Qi Gong XP Club GbR |
| `impressum-ag.html` | Impressum Schwarz auf Weiss AG (gilt bis zur Löschung der AG, danach Datei und Links entfernen) |
| `datenschutz.html` | Datenschutzerklärung (Hosting Hetzner, Server-Logs, E-Mail-Kontakt, keine Cookies) |
| `assets/style.css`, `assets/favicon.svg` | Gestaltung |
| `.htaccess` | Apache-Einstellungen (Sicherheits-Header; HTTPS-Weiterleitung auskommentiert) |
| `ci/deploy.yml` | Deployment-Workflow per SFTP auf Hetzner Webhosting (noch zu aktivieren, siehe unten) |

## Lokale Vorschau

```bash
python3 -m http.server 8000
# dann http://localhost:8000 öffnen
```

## Platzhalter vor dem Go-live füllen

Alle offenen Angaben sind gelb hervorgehoben und beginnen mit `[PLATZHALTER: …]`. Suchen mit:

```bash
grep -rn "PLATZHALTER" --include=*.html .
```

Außerdem enthält der Kontakt-Button in `index.html` den Link `mailto:PLATZHALTER-E-MAIL`, der durch die echte Adresse ersetzt werden muss.

## Deployment (Hetzner Webhosting)

**Workflow aktivieren (einmalig):** Der Workflow liegt als `ci/deploy.yml` im Repo, weil das gh-Token auf der Box keinen `workflow`-Scope hat und GitHub deshalb Dateien unter `.github/workflows/` abgelehnt hat. Aktivieren mit:

```bash
gh auth refresh -h github.com -s workflow   # einmalig im Browser bestätigen
mkdir -p .github/workflows && git mv ci/deploy.yml .github/workflows/deploy.yml
git commit -m "Deploy-Workflow aktivieren" && git push
```

Alternativ im GitHub-Webinterface die Datei `.github/workflows/deploy.yml` mit dem Inhalt von `ci/deploy.yml` anlegen. Ohne Secrets läuft er danach grün durch und überspringt den Upload.

Hetzner Webhosting S bietet SFTP (Port 22) und FTPS (Port 21), aber keinen SSH-Shell-Login. Darum lädt der Workflow mit `lftp mirror` per SFTP hoch statt mit rsync.

1. Webhosting bei Hetzner bestellen und die Domain `schwarz-auf-weiss.net` als extern registrierte Domain anlegen (DNS bleibt bei united-domains). Details: `Golive_schwarz-auf-weiss.net.md` im sawag-Repo.
2. In konsoleH unter *Einstellungen → Logindaten → FTP-Hauptbenutzer* Server, Loginname und Passwort ablesen ([Hetzner Docs: FTP & SFTP](https://docs.hetzner.com/de/managed/administration-on-konsoleh/access-details/ftp-sftp/)).
3. Optional, empfohlen: Schlüssel statt Passwort. `ssh-keygen -t ed25519 -f hetzner_deploy -N ""` erzeugen, den Public Key in konsoleH unter *Öffentliche SFTP-Schlüssel* hochladen (konsoleH wandelt ihn ins RFC4716-Format um; geht nur mit dem FTP-Hauptbenutzer).
4. Im GitHub-Repo unter *Settings → Secrets and variables → Actions* anlegen:
   - `HETZNER_HOST`: z. B. `wwwXXX.your-server.de`
   - `HETZNER_USER`: FTP-Hauptbenutzer
   - `HETZNER_PASSWORD`: FTP-Passwort **oder** `HETZNER_SSH_KEY`: Inhalt der privaten Schlüsseldatei
   - `HETZNER_PATH`: Zielverzeichnis der Domain laut konsoleH, z. B. `public_html`
   - optional `HETZNER_KNOWN_HOSTS`: Ausgabe von `ssh-keyscan -p 22 wwwXXX.your-server.de` (sonst wird der Host-Schlüssel beim ersten Verbinden ungeprüft akzeptiert)
5. Unter *Actions → Deploy auf Hetzner Webhosting → Run workflow* den ersten Lauf manuell starten. Danach deployt jeder Push auf `main` automatisch.
6. Prüfen: In konsoleH die Seite über die Vorschau-Adresse ansehen, erst dann DNS bei united-domains umstellen.
7. Nach der DNS-Umstellung in konsoleH Let's Encrypt für die Domain aktivieren und danach in `.htaccess` die HTTPS-Weiterleitung einkommentieren.

Ohne Secrets wird der Upload übersprungen; der Workflow endet dann grün mit einem Hinweis. Standardmäßig löscht der Upload nichts auf dem Server. Beim manuellen Start lässt sich `delete_remote` aktivieren, dann werden Dateien entfernt, die es im Repo nicht mehr gibt.

## Rechtliches

Impressum und Datenschutzerklärung sind Vorlagen und keine Rechtsberatung. Vor dem Go-live: Platzhalter füllen und den Auftragsverarbeitungsvertrag mit Hetzner in konsoleH abschließen (die Datenschutzerklärung setzt ihn voraus).
