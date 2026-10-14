# Static Site Hardening

Fertige, kommentierte Konfiguration für **statische Websites** (HTML/CSS/JS ohne Backend) –
für **nginx** und **Apache**, optional hinter **Cloudflare**.

Entstanden beim Betrieb einer Lernplattform; vor allem zwei Stolperfallen werden hier gelöst:

- **`.htaccess` wirkt unter nginx nicht** – die Sicherheits-Header fehlen dann unbemerkt, und die Datei ist
  öffentlich abrufbar.
- **Lange Cache-Zeiten für `sw.js`** (z. B. am CDN-Edge) verhindern, dass Updates bei Besucher:innen ankommen.

## Inhalt

| Datei | Zweck |
|---|---|
| `nginx/site.conf.example` | Server-Block: HTTPS, echte 404, Dotfiles sperren, nur GET/HEAD/OPTIONS, Cache-Regeln, `gzip_static` |
| `nginx/snippets/security-headers.conf` | CSP, HSTS, X-Frame-Options, Referrer-/Permissions-Policy, COOP/CORP |
| `apache/.htaccess` | dasselbe für Apache (`mod_headers`, `mod_rewrite`, `mod_expires`) |
| `CLOUDFLARE.md` | Cache-Regeln, Purge, Header-Dopplungen vermeiden, Prüf-Befehle |

## Verwendung (nginx)

```bash
sudo cp nginx/snippets/security-headers.conf /etc/nginx/snippets/
sudo cp nginx/site.conf.example /etc/nginx/sites-available/example.org
# Domain, root und Zertifikatspfade anpassen
sudo ln -s /etc/nginx/sites-available/example.org /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

Die CSP ist bewusst streng. Externe Quellen (Bilder, eingebettete Videos, APIs) müssen ergänzt werden –
Beispiele stehen als Kommentar in `security-headers.conf`. `'unsafe-inline'` ist für Seiten mit
Inline-Skripten nötig; wer alle Skripte auslagert, sollte es entfernen.

Prüfen: <https://securityheaders.com> bzw. die `curl`-Befehle in `CLOUDFLARE.md`.

## Lizenz

MIT – siehe [`LICENSE`](LICENSE).
