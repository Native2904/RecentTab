# RecentTab

Ein Total-Commander-Dateisystem-Plugin (WFX), das kürzlich geänderte
Dateien als virtuelles Panel anzeigt - ähnlich der "Zuletzt benutzt"-
Ansicht des macOS Finders.

![RecentTab Screenshot](https://raw.githubusercontent.com/Native2904/RecentTab/401f106849b2704410123273c37a39fb2bbb4be2/2026-08-03_153508.png)

## Was es kann

`\\RecentTab\` in Total Commander öffnen zeigt eine flache, chronologische
Liste aller kürzlich geänderten Dateien, systemweit zusammengetragen -
über eine direkte IPC-Verbindung zu [Everything](https://www.voidtools.com)
(voidtools), kein Hintergrunddienst, kein manueller Scan. Jede Datei in
der Liste ist die echte Datei an ihrem echten Ort - öffnen, bearbeiten,
kopieren, verschieben, löschen wirkt direkt auf die echte Datei, nichts
davon ist virtuell oder zwischengespeichert. Die Aufzeichnung selbst ist
ein Ein/Aus-Schalter, mit der Maus im Panel bedienbar, mit eigenem
Laufzeit-Zähler.

Der Zustand liegt in einer eigenen Datei neben dem Plugin, nicht im
Arbeitsspeicher. Tab versehentlich geschlossen, Total Commander beendet,
Neustart des Rechners - macht keinen Unterschied. Beim nächsten Öffnen
von `\\RecentTab\` ist der Verlauf unverändert vorhanden.

Welche Ordner beobachtet werden, ist konfigurierbar. Standardmäßig die
sechs üblichen (Desktop, Dokumente, Downloads, Bilder, Videos, Musik),
inklusive korrekter OneDrive-Umleitungserkennung. Für eine engere oder
komplett andere Auswahl (z. B. nur ein Projektlaufwerk) lässt sich eine
eigene Ordner-Whitelist definieren, mit optionaler Blacklist für
bestimmte Unterpfade darin (`node_modules`, ein Backup-Cache o. Ä.) -
oder die sechs Standardordner behalten und eigene zusätzlich ergänzen.
Cloud-Sync-Platzhalterdateien, die noch nicht wirklich heruntergeladen
wurden (OneDrive Files On-Demand u. Ä.), werden automatisch
herausgefiltert.

Eine eigene `RealPath`-Spalte zeigt immer den echten Ort einer Datei,
auch wenn die Liste selbst über viele Ordner hinweg reicht. Sortierung
standardmäßig neueste zuerst (umstellbar). Alt+Enter öffnet ein
themenfarbiges Statistikfenster - vier eingebaute Farbpaletten, passt
seine Größe an den tatsächlichen Inhalt an - mit einer live
mitlaufenden Uhr im Fensterrahmen und einer vollständigen Übersicht
über den aktuellen Zustand: geladenes Theme, geladene Sprache, ob
Everything gerade tatsächlich verbunden ist und welche Version läuft,
Anzahl der Abfragen in dieser Sitzung, komplette Aufzeichnungshistorie,
und wo jede vom Plugin verwendete Datei tatsächlich liegt. Alles direkt
im Fenster sichtbar, nichts muss über eine separate Einstellungsseite
gesucht werden.

Die Verbindung zu Everything läuft über dessen eigenes IPC-Protokoll,
direkt - kein Subprozess pro Abfrage, keine Kommandozeile involviert.
Spürbar schneller als der frühere Ansatz dieses Projekts. Der
Alt+Enter-Dialog zeigt klar an, ob diese Verbindung gerade steht. Die
Oberfläche selbst liegt in mehr als einer Sprache vor und lässt sich um
eigene Übersetzungen erweitern.

## Bekannte Einschränkungen

- Bewusst schreibgeschützt: Konfiguration erfolgt durch direktes
  Bearbeiten von `RecentTab.ini`, keine Einstellungen-Oberfläche im
  Plugin selbst.
- Erfasst nur tatsächlich geänderte oder neu angelegte Dateien - nicht
  Dateien, die nur geöffnet/angesehen, aber nicht verändert wurden.
  Windows speichert grundsätzlich nicht zuverlässig, *welcher Prozess*
  eine Datei angefasst hat, nur *dass* sie sich geändert hat - eine
  harte Plattformgrenze, kein Punkt für ein zukünftiges Update.
- 

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
innerhalb des `Path=` dieses Blocks übersprungen werden sollen - die
Blacklist pro Ordner. Mit `IncludeDefaultFolders=1` werden eigene
Blöcke zu den sechs Standardordnern hinzugefügt statt sie zu ersetzen
(wird einer der sechs selbst neu definiert, z. B. mit eigenem
`Exclude=`, wird diese Version statt des einfachen Standards
verwendet - kein doppelter Eintrag).

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
schreibgeschütztes Statistikfenster: Aufzeichnungsstatus mit live
mitlaufender Uhr im Fensterrahmen, geladenes Theme und geladene
Sprache, vollständige Aufzeichnungshistorie, Lifetime-Nutzung
(übersteht Reset), ob Everythings IPC-Verbindung gerade steht und
welche Version läuft, beobachtete Ordner (markiert, falls einer auf
der Platte gar nicht existiert), und Config-/State-Dateipfade. Passt
seine Größe an den tatsächlichen Inhalt an statt eine feste Größe zu
verwenden - wächst bei langen Pfaden, scrollt bei langen Listen.

## Voraussetzungen

- Windows 7 oder neuer, Total Commander 32-Bit oder 64-Bit
- [Everything](https://www.voidtools.com) installiert und laufend -
  das Plugin verbindet sich direkt über dessen eigenes IPC-Protokoll,
  kein zusätzlicher Download oder Einrichtungsschritt nötig außer
  Everything selbst im Hintergrund laufen zu lassen

## Dateien in diesem Paket

- `RecentTab.wfx` / `RecentTab.wfx64` - das Plugin (TC wählt automatisch
  die passende, falls beide vorhanden sind)
- `RecentTab.ini`, `RecentTab_lang.ini` - Konfiguration und
  Oberflächentexte; müssen im selben Ordner wie die Plugin-Datei
  liegen, da sie relativ zum eigenen Speicherort gesucht werden, nicht
  in TCs Konfigurationsordner
- `Color_ini_example.ini` - eine Vorlage zum Hinzufügen einer weiteren
  Sprache
- `RecentTab_state.json` - wird beim ersten Gebrauch automatisch
  angelegt, nicht Teil dieses Pakets; merkt sich die
  Aufzeichnungshistorie

## Installation

Doppelklick auf `RecentTab.zip` direkt in Total Commander - erkennt
den Installer darin, bietet die automatische Installation an, ein
Klick. (Voraussetzung: Konfigurieren → Einstellungen → Packer →
"Archive wie Verzeichnisse behandeln" aktiviert, Standardeinstellung.
Falls das Zip im selben Panel schon mal zum Reingucken geöffnet wurde,
einmal ein anderes Archiv öffnen/schließen, dann nochmal
doppelklicken.)

Alternativ: entpacken, oder den `release`-Ordner aus einem
Quellcode-Build nehmen, dort auf `pluginst.inf` doppelklicken - oder
die .wfx manuell über Konfigurieren → Einstellungen → Plugins →
Dateisystem-Plugins (WFX) hinzufügen.

## Aus dem Quellcode bauen

MinGW-w64 erforderlich (`build_debug.bat` erwartet `C:\mingw64`, mit
optionalem `mingw32`-Unterordner für einen zusätzlichen 32-Bit-Build
neben dem 64-Bit-Build). Nach einem erfolgreichen Build werden ein
`release`-Ordner und ein fertiges `RecentTab.zip` automatisch neben dem
Skript zusammengebaut. Siehe `TESTING.md` für das, was danach zu
prüfen ist.

## Lizenz

MIT - passend zu den anderen Total-Commander-Plugins dieses Autors
([XYTags](https://github.com/Native2904), [DescriptEdit](https://github.com/Native2904)).

Autor: Björn ([Native2904](https://github.com/Native2904))
