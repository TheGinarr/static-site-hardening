# Cloudflare vor einer statischen Seite – Checkliste

1. **Cache-Regel für `/sw.js`**: *Caching → Cache Rules* → URI-Pfad gleich `/sw.js` → **Bypass cache**.
   Sonst liegt der Service Worker bis zu einem Jahr im Edge-Cache und Nutzer bekommen keine Updates.
2. **HTML nicht am Edge cachen** (Standard bei Cloudflare) bzw. Origin-`Cache-Control` respektieren lassen.
3. **Nach jedem Deploy**: *Caching → Configuration → Purge Everything* (oder gezielt die geänderten URLs).
4. **Security-Header**: werden vom Origin (nginx/Apache) gesetzt. Alternativ *Rules → Transform Rules →
   Modify Response Header*. Nicht doppelt setzen – sonst zwei widersprüchliche CSP-Header.
5. **SSL/TLS-Modus**: *Full (strict)*, wenn der Origin ein gültiges Zertifikat hat.
6. **Bot Fight Mode / JS Detections** lädt `/cdn-cgi/challenge-platform/…` – mit `script-src 'self'` erlaubt;
   in der Datenschutzerklärung erwähnen.
7. **E-Mail-Obfuscation** (*Scrape Shield*) ersetzt Adressen im HTML durch ein Skript – unkritisch,
   braucht aber `script-src 'self'`.
8. Kontrolle:
   ```bash
   curl -sI https://example.org/            | grep -iE 'content-security|x-frame|strict-transport'
   curl -s  -o /dev/null -w '%{http_code}\n' https://example.org/gibtsnicht   # 404 erwartet
   curl -s  -o /dev/null -w '%{http_code}\n' https://example.org/.htaccess    # 404 erwartet
   curl -sI https://example.org/sw.js        | grep -iE 'cache-control|cf-cache-status'
   ```
