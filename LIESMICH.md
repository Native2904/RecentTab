# RecentTab

Ein Total-Commander-Dateisystem-Plugin (WFX), das kürzlich geänderte
Dateien als virtuelles Panel anzeigt - ähnlich der "Zuletzt benutzt"-
Ansicht des macOS Finders.

![RecentTab Screenshot](https://raw.githubusercontent.com/Native2904/RecentTab/401f106849b2704410123273c37a39fb2bbb4be2/2026-08-03_153508.png)

## Was es kann

Öffne `\\RecentTab\` in Total Commander und bekomme eine flache,
chronologische Liste aller Dateien, die du tatsächlich kürzlich
angefasst hast, zusammengetragen über dein ganzes System hinweg -
angetrieben von einer echten [Everything](https://www.voidtools.com)-
Datumsbereichsabfrage, keinem Hintergrunddienst und keinem langsamen
manuellen Scan. Jede Datei in der Liste ist die echte Datei an ihrem
echten Ort - öffnen, bearbeiten, kopieren, verschieben, löschen, direkt
aus der Liste heraus, nichts davon ist virtuell oder zwischengelagert.
Die Aufzeichnung selbst ist ein simpler Ein/Aus-Schalter, den du mit
der Maus direkt im Panel steuerst, mit eigenem Live-Laufzeit- und
Verlaufszähler.

Wo hingeschaut wird, entscheidest komplett du. Von Haus aus werden die
sechs üblichen Verdächtigen beobachtet (Desktop, Dokumente, Downloads,
Bilder, Videos, Musik), inklusive korrekter Erkennung von
OneDrive-Umleitungen, falls vorhanden. Willst du stattdessen etwas
Engeres oder komplett anderes - nur dein Projekt-Laufwerk zum
Beispiel? Definier deine eigene Ordner-Whitelist, und blackliste
optional bestimmte Unterpfade innerhalb davon (ein `node_modules`-
Ordner, ein Backup-Cache, was auch immer deine Ergebnisse zumüllt) -
oder behalte die sechs Standardordner *und* ergänze deine eigenen
obendrauf, ganz wie du willst. Cloud-Sync-Platzhalterdateien, die noch
gar nicht wirklich heruntergeladen wurden (OneDrive Files On-Demand
und Ähnliches), werden automatisch rausgefiltert, damit sie die Liste
nicht mit Dingen zumüllen, die auf diesem Rechner nie wirklich
"benutzt" wurden.

Alles andere ist darauf ausgelegt, dir nicht im Weg zu stehen: eine
eigene `RealPath`-Spalte zeigt immer den echten Ort einer Datei, auch
wenn die Liste selbst über viele Ordner hinweg reicht, die Sortierung
ist standardmäßig neueste-zuerst (oder älteste-zuerst, deine
Entscheidung), und ein themenfarbiges Alt+Enter-Statistikfenster - vier
eingebaute Farbpaletten, passt sich in der Größe an das an, was es
tatsächlich zeigen muss - gibt dir das komplette Bild (Verlauf,
Lifetime-Statistik, beobachtete Ordner, Konfigurationspfade), ohne das
Panel zu verlassen. Die gesamte Oberfläche ist in mehr als einer
Sprache verfügbar und leicht um eigene Übersetzungen zu erweitern.

## Voraussetzungen

- Windows 7 oder neuer, Total Commander 32-Bit oder 64-Bit
- [Everything](https://www.voidtools.com) installiert und laufend, mit
  einem Index, der die Laufwerke deiner beobachteten Ordner abdeckt
- `es.exe`, Everythings Kommandozeilen-Client (liegt normalerweise
  schon in Everythings eigenem Installationsordner)

## Installation

Doppelklick auf `RecentTab.zip` direkt in Total Commander - es erkennt
den Installer darin und bietet die automatische Installation an, ein
einziger Klick. (Braucht Konfigurieren → Einstellungen → Packer →
"Archive wie Verzeichnisse behandeln" aktiviert, Standardeinstellung.
Falls das Zip im selben Panel schon mal zum Reingucken geöffnet wurde,
einmal ein anderes Archiv öffnen/schließen, dann nochmal
doppelklicken.)

Geht auch: entpacken, oder den `release`-Ordner aus einem
Quellcode-Build nehmen, und dort auf `pluginst.inf` doppelklicken -
oder die .wfx manuell über Konfigurieren → Einstellungen → Plugins →
Dateisystem-Plugins (WFX) hinzufügen.

## Dateien in diesem Paket

- `RecentTab.wfx` / `RecentTab.wfx64` - das Plugin (TC wählt automatisch
  die passende, falls beide vorhanden sind)
- `RecentTab.ini`, `RecentTab_lang.ini` - Konfiguration und
  Oberflächentexte; **müssen im selben Ordner wie die Plugin-Datei
  liegen**, da sie relativ zum eigenen Speicherort gesucht werden,
  nicht in TCs Konfigurationsordner
- `Color_ini_example.ini` - eine Vorlage zum Hinzufügen einer weiteren
  Sprache
- `RecentTab_state.json` - wird beim ersten Gebrauch automatisch
  angelegt, nicht Teil dieses Pakets; merkt sich deine
  Aufzeichnungshistorie

## Bekannte Einschränkungen

- Bewusst schreibgeschützt: Konfiguration erfolgt durch direktes
  Bearbeiten von `RecentTab.ini`, keine Einstellungen-Oberfläche im
  Plugin selbst.
- Erfasst nur tatsächlich geänderte oder neu angelegte Dateien - nicht
  Dateien, die nur geöffnet/angesehen, aber nicht verändert wurden.
  Windows selbst speichert grundsätzlich nicht zuverlässig, *welcher
  Prozess* eine Datei angefasst hat, nur *dass* sie sich geändert hat -
  das ist also eine harte Plattformgrenze, kein Punkt, den ein
  zukünftiges Update noch nachrüsten könnte.

---

## Fortgeschrittene Konfiguration

Alles unten hat einen funktionierenden Standardwert - dieser Abschnitt
ist für alle, die weiter anpassen wollen, kein Pflichtlesestoff zum
Loslegen.

```ini
[Settings]
ConfirmReset=1       ; 0 = Reset sofort ohne "Wirklich zurücksetzen?"-Nachfrage
SortDescending=1     ; 0 = älteste zuerst statt neueste zuerst
Language=eng         ; eng (Standard) / deu / passt zu einer Sektion in RecentTab_lang.ini
DebugLogging=1       ; 0 = RecentTab_debug.log nicht mehr schreiben (echte Kosten pro Aufruf, solange an)
IncludeDefaultFolders=0  ; 1 = eigene [Watched:...]-Blöcke zu den sechs Standardordnern hinzufügen statt sie zu ersetzen
;EsExePath=C:\Everything\es.exe   ; nur nötig, falls es.exe nicht automatisch gefunden wird

[Theme]
Name=gruvbox         ; gruvbox (Standard) / dracula / monokai / everforest / custom
```

### Beobachtete Ordner wählen (Whitelist) und was darin übersprungen wird (Blacklist)

Einen oder mehrere `[Watched:Name]`-Blöcke hinzufügen, um eine eigene
Liste zu definieren - sobald mindestens einer vorhanden ist, werden die
sechs Standardordner komplett ignoriert, außer `IncludeDefaultFolders=1`
ist gesetzt:

```ini
[Watched:Projects]
Path=D:\Projects
Exclude=Backup;node_modules

[Watched:Desktop]
Path=%USERPROFILE%\Desktop
```

`Exclude=` nimmt mit Semikolon getrennte Unterpfad-Fragmente, die
innerhalb des `Path=` dieses Blocks übersprungen werden sollen - deine
Blacklist pro Ordner. Mit `IncludeDefaultFolders=1` werden eigene
Blöcke zu den sechs Standardordnern hinzugefügt statt sie zu ersetzen
(definierst du einen der sechs selbst neu, z. B. mit eigenem
`Exclude=`, wird deine Version statt des einfachen Standards
verwendet - er taucht nicht doppelt auf).

### Farbschemata

Vier eingebaute Presets - `gruvbox` (Standard), `dracula`, `monokai`,
`everforest`. Oder `Name=custom` setzen und eigene Farben angeben:

```ini
[Theme]
Name=custom
Background=#282828
Foreground=#ebdbb2
Heading=#d3869b
Green=#b8bb26
Accent2=#8ec07c
Yellow=#fabd2f
Accent4=#fe8019
Muted=#928374
```

Jede leer gelassene Farbe fällt auf den passenden Gruvbox-Wert zurück,
ein unvollständiges Custom-Theme sieht also nie kaputt aus.

### Sprache

Oberflächentexte kommen aus `RecentTab_lang.ini` (dieselbe Ordnerregel
wie überall sonst), gelesen mit dem eigenen UTF-8-sicheren Parser des
Plugins statt mit Windows' `GetPrivateProfileString` (das
nicht-lateinische Schriften über die ANSI-Codepage des Systems
verhackstückelt, außer die Datei hat eine UTF-16LE-BOM). Ausgeliefert
mit `eng` und `deu`; jeder fehlende oder leere Key fällt automatisch
auf Englisch zurück.

`Color_ini_example.ini` zeigt eine dritte Sprache (`rus`, Kyrillisch),
die der Datei hinzugefügt wurde - eine Vorlage zum Herauskopieren einer
`[xyz]`-Sektion, wird nicht automatisch geladen.

### Der Alt+Enter-Dialog

Öffnet von jedem der drei Pseudo-Einträge aus ein themenfarbiges,
schreibgeschütztes Statistikfenster: Aufzeichnungsstatus, Verlauf,
Lifetime-Nutzung (übersteht Reset), beobachtete Ordner,
Konfigurations-/Zustandspfade, monatliche Panel-Öffnungen. Passt seine
Größe an den tatsächlichen Inhalt an statt eine feste Größe zu
verwenden - wächst bei langen Pfaden, scrollt bei langen Listen.

## Aus dem Quellcode bauen

MinGW-w64 erforderlich (`build_debug.bat` erwartet `C:\mingw64`, mit
optionalem `mingw32`-Unterordner für einen zusätzlichen 32-Bit-Build
neben dem 64-Bit-Build). Nach einem erfolgreichen Build werden ein
`release`-Ordner und ein fertiges `RecentTab.zip` automatisch neben dem
Skript zusammengebaut. Siehe `TESTING.md` für das, was danach zu
prüfen ist.

## Lizenz

MIT - passend zu den anderen Total-Commander-Plugins dieses Autors
([XYTags](https://github.com/Native2904), DescriptEdit).

Autor: Björn ([Native2904](https://github.com/Native2904))
