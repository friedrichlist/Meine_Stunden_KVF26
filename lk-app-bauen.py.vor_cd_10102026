#!/usr/bin/env python3
"""
Macht aus der Lehrkrafttafel die installierbare Fassung fuer GitHub Pages.

    python3 lk-app-bauen.py "/Pfad/zu/Meine_Stunden_KVF26.html"

Schreibt index.html daneben. Die Quelldatei bleibt unveraendert.
Bricht ab, sobald ein Ansatzpunkt fehlt oder mehrdeutig ist - lieber ein
klarer Fehler als eine halb gepatchte Datei.

Schwester von app-bauen.py der Schueler-App. Die beiden Skripte sind
absichtlich getrennt: die Tafeln aendern sich unabhaengig voneinander, und ein
gemeinsames Skript wuerde bei jeder Aenderung an einer Seite die andere
mitreissen.
"""
import re
import sys
import pathlib

KOPF = '''<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="description" content="Turnusplan KVF 26 für Lehrkräfte – abhaken und Rückmeldung geben. Alle Einträge bleiben auf dem Gerät.">
<meta name="theme-color" content="#F4F6F5" media="(prefers-color-scheme:light)">
<meta name="theme-color" content="#12181A" media="(prefers-color-scheme:dark)">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="apple-mobile-web-app-title" content="Meine Stunden">
<link rel="manifest" href="manifest.webmanifest">
<link rel="icon" href="icon-192.png" sizes="192x192" type="image/png">
<link rel="apple-touch-icon" href="icon-192.png">'''

BOOTSTRAP = '''
<script>
/* PWA + Teams-Einbettung. Greift nicht in die App-Logik ein. */
(function () {
  if ("serviceWorker" in navigator) {
    addEventListener("load", function () {
      navigator.serviceWorker.register("service-worker.js").catch(function () {});
    });
  }

  // Teams-SDK nur laden, wenn die Seite tatsaechlich eingebettet laeuft.
  // Standalone bleibt die App damit vollstaendig offline-faehig.
  if (window.self === window.top) return;

  var s = document.createElement("script");
  s.src = "https://res.cdn.office.net/teams-js/2.32.0/js/microsoft.teams.min.js";
  s.onload = function () {
    if (!window.microsoftTeams || !microsoftTeams.app) return;
    microsoftTeams.app.initialize().then(function () {
      function anwenden(theme) {
        document.documentElement.setAttribute(
          "data-theme", theme === "dark" || theme === "contrast" ? "dark" : "light"
        );
      }
      microsoftTeams.app.getContext().then(function (ctx) {
        anwenden(ctx && ctx.app && ctx.app.theme);
      }).catch(function () {});
      microsoftTeams.app.registerOnThemeChangeHandler(anwenden);
    }).catch(function () {});
  };
  document.head.appendChild(s);
})();
</script>
</body>'''

DATENSCHUTZ_LINK = ('\n    <br><a href="datenschutz.html" class="ds-link">'
                    'Wo Ihre Eintragungen liegen – die lange Fassung</a>\n  </p>')

STIL_ZUSATZ = ('\n.ds-link{color:var(--muted);text-decoration:underline;'
               'text-underline-offset:2px}\n.ds-link:hover{color:var(--ink2)}')


def ersetze_einmal(text, alt, neu, was):
    n = text.count(alt)
    if n != 1:
        sys.exit(f"ABBRUCH: Ansatzpunkt '{was}' {n}x gefunden, erwartet genau 1x.")
    return text.replace(alt, neu, 1)


def main():
    if len(sys.argv) < 2:
        sys.exit(__doc__)
    quelle = pathlib.Path(sys.argv[1])
    if not quelle.is_file():
        sys.exit(f"ABBRUCH: {quelle} nicht gefunden.")

    s = quelle.read_text(encoding="utf-8")
    ausgangsgroesse = len(s)

    # Gegenprobe: das ist die Lehrkrafttafel und nicht versehentlich die
    # Schuelertafel - die beiden liegen im selben Ordner nebeneinander.
    if "Meine Stunden · KVF 26" not in s:
        sys.exit("ABBRUCH: das sieht nicht nach der Lehrkrafttafel aus "
                 "(Titel 'Meine Stunden · KVF 26' fehlt).")

    # Die veroeffentlichte Fassung darf keine Nachnamen und keine Kuerzel
    # tragen. tafeln.js prueft das schon; hier steht es ein zweites Mal, weil
    # diese Datei diejenige ist, die tatsaechlich ins offene Repo geht.
    kuerzel = [k for k in ("KG", "LBF", "HEC", "MPP", "HGRA") if f'"{k}"' in s]
    namen = [n for n in ("Kümmling", "Leibnitz", "Heckl", "Peréz", "Peralta", "Häger")
             if n in s]
    if kuerzel or namen:
        sys.exit("ABBRUCH: die Quelle traegt "
                 + " und ".join(filter(None, [
                     f"Kuerzel ({', '.join(kuerzel)})" if kuerzel else "",
                     f"Nachnamen ({', '.join(namen)})" if namen else ""]))
                 + ".\nDiese Datei wird veroeffentlicht. Erst tafeln.js pruefen, dann bauen.")

    # 1) Dunkles Farbschema auch ueber data-theme erreichbar machen (fuer Teams)
    m = re.search(r'@media \(prefers-color-scheme:dark\)\{:root\{(.*?)\}\}', s, re.S)
    if not m:
        sys.exit("ABBRUCH: Block fuer das dunkle Farbschema nicht gefunden.")
    farben = m.group(1)
    s = s.replace(
        m.group(0),
        '@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){' + farben + '}}\n'
        ':root[data-theme="dark"]{' + farben + '}',
        1,
    )

    # 2) Kopfdaten fuer Installation auf dem Startbildschirm
    m = re.search(r'<meta name="viewport"[^>]*>', s)
    if not m:
        sys.exit("ABBRUCH: viewport-Angabe nicht gefunden.")
    s = s.replace(m.group(0), KOPF, 1)

    # 3) Breite: linksbuendig, mehr Platz auf grossen Bildschirmen
    s = ersetze_einmal(
        s,
        '.wrap{max-width:820px;margin:0 auto;padding:26px 18px 70px}',
        '.wrap{max-width:1080px;margin:0;padding:26px 18px 70px}\n'
        '/* Ab Tabletbreite etwas Luft zum linken Fensterrand */\n'
        '@media (min-width:760px){.wrap{padding-left:32px;padding-right:32px}}',
        'Breitenangabe .wrap',
    )

    # 4) Link zur ausfuehrlichen Datenschutzseite in den Fussbereich
    m = re.search(r'(<p class="fuss">.*?)\n(\s*)</p>', s, re.S)
    if m:
        s = s.replace(m.group(0), m.group(1) + DATENSCHUTZ_LINK, 1)
        s = ersetze_einmal(s, '.fuss b{color:var(--ink2)}',
                           '.fuss b{color:var(--ink2)}' + STIL_ZUSATZ,
                           'Stilangabe .fuss b')
    else:
        print("  Hinweis: kein Fussbereich gefunden, Datenschutz-Link ausgelassen.")

    # 5) Registrierung von Offline-Speicher und Teams-Anbindung
    s = ersetze_einmal(s, '</body>', BOOTSTRAP, 'schliessendes body-Tag')

    ziel = pathlib.Path(__file__).parent / 'index.html'
    ziel.write_text(s, encoding='utf-8')
    print(f"  {quelle.name}  ({ausgangsgroesse:,} Bytes)")
    print(f"  -> {ziel}  ({len(s):,} Bytes, +{len(s)-ausgangsgroesse:,})")


if __name__ == '__main__':
    main()
