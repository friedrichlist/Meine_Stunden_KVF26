# Meine Stunden · KVF 26

Der Turnusplan des Bildungsgangs für die **Lehrkräfte**: die eigenen Stunden abhaken und
in wenigen Klicks zurückmelden, ob Zeit und Material getragen haben. Als Web-App, die sich
auf dem Handy wie eine App verhält und auch als Tab in Microsoft Teams läuft.

Schwester der Schüler-App `Mein 1. AJ · KVF 26`. Beide entstehen aus derselben Quelle —
dem Turnusplan — und sehen deshalb gleich aus.

Alle Einträge bleiben im Browser des jeweiligen Geräts. Es gibt kein Backend, keine
Anmeldung, keine Datenübertragung. Weitergegeben wird nur, was die Lehrkraft selbst als
Datei ausgibt.

---

## Was liegt hier

| Datei | Zweck |
|---|---|
| `index.html` | Die App, erzeugt aus `Meine_Stunden_KVF26.html` durch `lk-app-bauen.py`. Nicht von Hand bearbeiten. |
| `lk-app-bauen.py` | Baut aus der Lehrkrafttafel diese installierbare Fassung. |
| `manifest.webmanifest` | Macht die Seite installierbar (Name, Icon, Startbildschirm). |
| `service-worker.js` | Offline-Cache. Lädt die App beim zweiten Besuch auch ohne Netz. |
| `datenschutz.html` | Datenschutzhinweis, wird vom Teams-Manifest und aus dem Fußbereich verlinkt. |
| `icon-192.png`, `icon-512.png` | App-Icons: ein Haken, damit die App auf dem Startbildschirm nicht mit der Schüler-App verwechselt wird. |
| `teams/manifest.json` | Teams-App-Definition. |

## Vor der ersten Veröffentlichung zu klären

Die App liegt in einem **offenen** Repo. Sie trägt deshalb weder Nachnamen noch Kürzel —
nur die Vornamen, die ohnehin im Turnusplan stehen. `tafeln.js` und `lk-app-bauen.py`
prüfen das beide und brechen ab, wenn doch etwas durchrutscht.

Vor dem Hochladen anzupassen:

- in `teams/manifest.json`: `contentUrl`, `websiteUrl` und `validDomains` auf die
  tatsächliche Adresse des Repos
- `developer.name`, `privacyUrl` und `termsOfUseUrl` prüfen

## Installieren

**iPhone** — Safari öffnen (nicht Chrome), Link aufrufen, Teilen-Symbol → „Zum
Home-Bildschirm".

**Android** — Chrome öffnen, Link aufrufen, Menü (⋮) → „App installieren".

**Teams** — muss einmalig von der IT freigegeben werden.

Paket für das Teams Admin Center bauen:

```bash
cd teams && cp ../icon-color.png ../icon-outline.png . && zip -j ../meine-stunden-kvf26-teams.zip manifest.json icon-color.png icon-outline.png && rm icon-color.png icon-outline.png
```

## So läuft es in der Blockwoche

1. Oben den eigenen **Vornamen** wählen — danach zeigt die App die eigenen Fächer.
   Über „ganzer Plan" ist der Rest jederzeit erreichbar; die eigenen Fächer bleiben
   dabei hervorgehoben.
2. Während der Woche die Karten antippen: **Stand** setzen (gehalten, teilweise,
   verschoben, entfallen), zu **Zeit** und **Material** je einen Wert, bei Bedarf einen
   Vermerk. Das ist in etwa zwanzig Sekunden je Baustein erledigt.
3. Am Ende der Woche den **Rückblick** je Blockwoche ausfüllen — was liegengeblieben ist,
   wie der Takt war.
4. **„Rückmeldung ausgeben"** drückt eine Datei
   `KVF_Rueckmeldung_<Vorname>_<Datum>.json` in die Downloads. Diese Datei an Christian.
   Sie ist zugleich die einzige Sicherung.

## Die Rückmeldungen zusammenlegen

Im Ordner `1. AJ` liegt `rueckmeldungen.py`:

```bash
python3 rueckmeldungen.py KVF_Rueckmeldung_*.json
```

Daraus entstehen `KVF_Rueckmeldungen_gesamt_<Datum>.json` (maschinenlesbar) und
`…​.md` (lesbarer Bericht, beginnend mit dem Abschnitt „Was Nacharbeit braucht").

Rückmeldungen zweier Lehrkräfte zum selben Baustein werden **beide** geführt — zwei
Einschätzungen zu einer Stunde sind zwei Befunde, keine Dublette. Nur wenn dieselbe Person
denselben Posten mehrfach ausgegeben hat, gilt ihr jüngster Eintrag.

## Wichtig zu wissen

**Getrennte Speicher.** Die App auf dem Startbildschirm und die App im Teams-Tab benutzen
jeweils einen eigenen Speicher. Am besten einen Weg festlegen und dabei bleiben — oder die
Ausgabedatei zum Übertragen nutzen.

**Der Turnusplan ändert sich.** Die Einträge hängen an den Kennungen der Bausteine und
Leistungen (`LF2-05`, `lf5_W9_KA`), nicht an ihrer Reihenfolge. Eine neue Variante darf
umsortieren, ohne dass etwas verlorengeht. Fällt eine Kennung ganz weg, erscheint ihr
Eintrag in einem eigenen Kasten als **verwaist** und steht weiter im Bericht — er wird
nicht still gelöscht.

**Keine Namen von Lernenden** in die Vermerkfelder. Die App bewertet das Material, nicht
Personen.

## Neue Version einspielen

Die Tafel wird im Bauablauf des Turnusplans gesetzt (`tafeln.js` aus
`lehrkraft.tpl.html` und `bau_daten.json`). Aus der fertigen Tafel entsteht `index.html`:

```bash
python3 lk-app-bauen.py "/Pfad/zu/Meine_Stunden_KVF26.html"
```

Findet das Skript einen Ansatzpunkt nicht oder mehrfach, bricht es ab und sagt welchen —
dann hat sich die Vorlage an dieser Stelle geändert und das Skript muss nachgezogen werden.
