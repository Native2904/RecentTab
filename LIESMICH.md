# RecentTab

Ein Total-Commander-Dateisystem-Plugin (WFX), das kürzlich geänderte
Dateien als virtuelles Panel anzeigt - ähnlich der "Zuletzt benutzt"-
Ansicht des macOS Finders, aber auf Basis einer echten datumsbasierten
Abfrage über [Everything](https://www.voidtools.com) (voidtools), statt
über einen Hintergrunddienst, ETW-Tracing oder einen Minifilter-Treiber.
Windows speichert grundsätzlich nicht, *welcher Prozess* eine Datei
geändert hat, nur *dass* sie geändert wurde - dieses Plugin arbeitet
bewusst innerhalb dieser Grenze, statt sie mit invasiveren Mitteln zu
umgehen.

Autor: Björn ([Native2904](https://github.com/Native2904))

## Was es tut

Öffne `\\RecentTab\` als Pfad in Total Commander, und du bekommst eine
flache Liste aller Dateien, die geändert wurden, seit du zuletzt auf
Start gedrückt hast - über die Ordner hinweg, die du beobachten lässt
(Standard: Desktop, Dokumente, Downloads, Bilder, Videos, Musik -
aufgelöst über Windows' Known-Folder-API, funktioniert also auch
korrekt, falls z. B. OneDrive einen dieser Ordner umgeleitet hat).

Drei Pseudo-Einträge oben im Panel lassen sich komplett mit der Maus
bedienen, keine Tastenkombination nötig:

| Eintrag | Funktion |
|---|---|
| `! REC` | Aufzeichnung an/aus (Start/Pause). Alt+Enter öffnet den vollständigen Statistik-Dialog. |
| `! Reset history` | Löscht die aufgezeichnete Historie. Die Größe-Spalte zeigt, wie viele Aufzeichnungen das betreffen würde; fragt vorher nach, außer `ConfirmReset=0` ist gesetzt. |
| `! Refresh now` | Maus-Entsprechung zu Strg+R - liest das Panel sofort neu ein, derselbe Mechanismus, den die anderen beiden Einträge nach einer Aktion schon selbst auslösen. |

Echte Dateien in der Liste verweisen direkt auf ihren tatsächlichen
Ort - F3, F4, Kopieren, Verschieben, Löschen usw. wirken auf die echte
Datei, es wird nichts zwischengespeichert oder kopiert.

**RealPath-Spalte:** Da das hier eine flache Liste aus vielen
verschiedenen Ordnern ist, kann Total Commanders übliches
`tc.path`-Feld dir nicht sagen, wo eine Datei tatsächlich liegt - es
gibt ja kein einzelnes "aktuelles Verzeichnis", das man melden könnte,
das ist gerade der Sinn dieser Ansicht. Die eigene `RealPath`-Spalte
zeigt stattdessen den echten, vollständigen Ort jeder Datei - Spalte
bei Bedarf einfach breiter ziehen, falls abgeschnitten.

## Voraussetzungen

- Windows 7 oder neuer, Total Commander 32-Bit oder 64-Bit
- [Everything](https://www.voidtools.com) installiert und laufend (als
  normales Programm oder als Everything-Dienst), mit einem Index, der
  die Laufwerke deiner beobachteten Ordner abdeckt
- `es.exe`, Everythings Kommandozeilen-Client - liegt normalerweise
  schon in Everythings eigenem Installationsordner; falls nicht
  automatisch gefunden, per `EsExePath=` in `RecentTab.ini` angeben
  (siehe unten)

## Installation

- Am einfachsten: Doppelklick auf `RecentTab.zip` direkt in Total
  Commander. TC erkennt die `pluginst.inf` darin und bietet die
  Installation automatisch an - ein einziger Doppelklick, kein Öffnen
  des Archivs oder Entpacken nötig. Voraussetzung: Total Commander 6.5+
  und Konfigurieren → Einstellungen → Packer → "Archive wie
  Verzeichnisse behandeln" aktiviert (Standard). Falls das Archiv im
  selben Panel schon mal zum Reingucken geöffnet wurde (z. B. mit
  Strg+Bild-runter), einmal ein anderes Archiv öffnen/schließen, um den
  Auto-Installer wieder scharfzustellen, dann nochmal auf
  `RecentTab.zip` doppelklicken.
- Geht auch: Archiv vorher entpacken, oder direkt den `release`-Ordner
  aus einem Quellcode-Build nehmen, und dort auf `pluginst.inf`
  doppelklicken - oder manuell über Konfigurieren → Einstellungen →
  Plugins → Dateisystem-Plugins (WFX) → Konfigurieren... →
  Installieren... hinzufügen.
- Manuell: `RecentTab.wfx` (32-Bit) und/oder `RecentTab.wfx64` (64-Bit)
  **zusammen mit `RecentTab.ini`** in denselben Ordner kopieren, dann
  die .wfx-Datei über Konfigurieren... hinzufügen.

## Dateien in diesem Paket - und warum es mehr als eine .wfx gibt

- `RecentTab.wfx` / `RecentTab.wfx64` - das Plugin selbst. Total
  Commander bevorzugt automatisch die 64-Bit-Variante, wenn du 64-Bit-TC
  nutzt und beide im selben Ordner liegen; du brauchst also eigentlich
  nur eine der beiden für dein System, aber beide mitzuliefern erspart
  dir die Überlegung, welche das ist.
- `RecentTab.ini` - **muss im selben Ordner wie die .wfx-Datei liegen**,
  nicht im Konfigurationsordner von Total Commander selbst. Das Plugin
  ermittelt zur Laufzeit seinen eigenen Modul-Ordner und liest
  `RecentTab.ini` von dort - wenn du die Plugin-Datei verschiebst, muss
  die .ini mitwandern. Sie ist mit funktionierenden Standardwerten
  ausgeliefert, du musst also nichts editieren, um loszulegen - sie muss
  nur *vorhanden* sein, da Farbschema, eigene beobachtete Ordner und der
  `es.exe`-Pfad-Override alle daraus gelesen werden.
- `RecentTab_lang.ini` - dieselbe Regel, derselbe Ordner. Enthält die
  Oberflächentexte (standardmäßig Englisch und Deutsch); siehe
  "Sprache" unten.
- `Color_ini_example.ini` - ein durchgearbeitetes Beispiel, wird nicht
  automatisch geladen; siehe "Sprache" unten.
- `RecentTab_state.json` erscheint im selben Ordner, sobald du das
  Plugin zum ersten Mal benutzt - **nicht** Teil dieses Pakets, das
  Plugin legt sie selbst an, um deine Aufzeichnungshistorie (Start-/
  Pause-Zeiten, Lifetime-Statistik) über TC-Neustarts hinweg zu merken.

Der Alt+Enter-Statistik-Dialog nutzt zusätzlich `Msftedit.dll` für seine
formatierte Darstellung (fette Überschriften, Theme-Farben) - das ist
eine Standard-Windows-Komponente, die seit Windows XP auf jeder
Installation vorhanden ist, nichts, was du separat herunterladen
müsstest.

## Konfiguration (RecentTab.ini)

```ini
[Settings]
ConfirmReset=1       ; 0 = Reset sofort ohne "Wirklich zurücksetzen?"-Nachfrage
SortDescending=1     ; 0 = älteste zuerst statt neueste zuerst
Language=eng         ; eng (Standard) / deu / passt zu einer Sektion in RecentTab_lang.ini
DebugLogging=1       ; 0 = RecentTab_debug.log nicht mehr schreiben (echte Kosten pro Aufruf, solange an)
;EsExePath=C:\Everything\es.exe   ; nur nötig, falls es.exe nicht automatisch gefunden wird

[Theme]
Name=gruvbox         ; gruvbox (Standard) / dracula / monokai / everforest / custom
```

Alles oben hat einen funktionierenden Standardwert - editiert werden
muss diese Datei nur für die beiden folgenden Fälle.

### Beobachtete Ordner einschränken

Standardmäßig werden die sechs bekannten Ordner von oben beobachtet.
Für eine eigene Liste einen oder mehrere `[Watched:Name]`-Blöcke
hinzufügen - sobald mindestens einer vorhanden ist, werden die sechs
Standardordner komplett ignoriert (nicht zusammengeführt), außer du
setzt `IncludeDefaultFolders=1` in `[Settings]`:

```ini
[Watched:Projects]
Path=D:\Projects
Exclude=Backup;node_modules

[Watched:Desktop]
Path=%USERPROFILE%\Desktop
```

`Exclude=` nimmt mit Semikolon getrennte Unterpfad-Fragmente, die
innerhalb des `Path=` dieses Blocks übersprungen werden sollen.

**Zu den Standardordnern hinzufügen, statt sie zu ersetzen:** Wenn du
nur einen zusätzlichen Ordner willst (z. B. einen bestimmten
AppData-Unterordner) neben den normalen sechs, setz
`IncludeDefaultFolders=1` in `[Settings]` - deine eigenen Blöcke kommen
dann zu den Standardordnern dazu, statt sie zu ersetzen. Du musst also
nicht alle sechs von Hand nachtragen, nur um einen siebten zu ergänzen:

```ini
[Settings]
IncludeDefaultFolders=1

[Watched:MeineApp]
Path=%APPDATA%\MeineApp\Config
```

Wenn du einen der sechs Standardordner selbst explizit angibst (z. B.
dein eigenes `[Watched:Desktop]` mit eigenem `Exclude=`), wird deine
Version verwendet statt des einfachen Standards - er taucht nicht
doppelt auf.

### Farbschemata

Vier fest eingebaute Presets für den Alt+Enter-Dialog - `gruvbox`
(Standard), `dracula`, `monokai`, `everforest`. Oder `Name=custom`
setzen und eigene Farben angeben:

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

Jede leer gelassene oder entfernte Custom-Farbe fällt einzeln auf den
passenden Gruvbox-Wert zurück, ein unvollständiges Custom-Theme sieht
also nie kaputt aus.

### Sprache

Die Oberflächentexte (Panel-Einträge, Reset-Bestätigung, Alt+Enter-
Dialog) kommen aus `RecentTab_lang.ini`, gelesen mit dem eigenen
UTF-8-sicheren Parser des Plugins statt mit Windows'
`GetPrivateProfileString` - das Unicode-INI-Inhalte nur zuverlässig
erkennt, wenn eine UTF-16LE-BOM am Dateianfang steht, und sonst still
über die ANSI-Codepage des Systems verhackstückelt. Ausgeliefert mit
`eng` (Standard) und `deu`:

```ini
[Settings]
Language=eng   ; passt zu einer Sektionsüberschrift in RecentTab_lang.ini
```

`RecentTab_lang.ini` **muss im selben Ordner** liegen wie die
Plugin-Datei und `RecentTab.ini` - dieselbe Regel wie überall sonst.
Jeder Key, der in einer Sektion fehlt oder leer gelassen wird, fällt
automatisch auf Englisch zurück - eine unvollständige Übersetzung sieht
also nie kaputt aus.

`Color_ini_example.ini` ist ein durchgearbeitetes Beispiel, das zeigt,
wie eine dritte Sprache (`rus`, Kyrillisch) zur Datei hinzugefügt wird -
wird nicht automatisch geladen, sondern ist eine Vorlage, aus der du
eine `[xyz]`-Sektion in `RecentTab_lang.ini` kopieren kannst. Gerade
Kyrillisch deshalb, weil es der eigentliche Beweis ist, dass der
UTF-8-Parser wirklich funktioniert: ein Codepage-Fehler bei
akzentuiertem lateinischem Text kann auf den ersten Blick noch
"ungefähr richtig" aussehen, ein Codepage-Fehler bei Kyrillisch wird
sofort zu komplettem Kauderwelsch - nichts, was man als "wahrscheinlich
in Ordnung" fehldeuten könnte.

## Der Alt+Enter-Dialog

Alt+Enter auf einem der drei Pseudo-Einträge öffnet ein themenfarbiges,
schreibgeschütztes Statistik-Fenster: Aufzeichnungsstatus, Historie,
Lifetime-Nutzung (übersteht Reset), verwendeter `es.exe`-Pfad,
beobachtete Ordner, Config-/State-Dateipfade und monatliche
Panel-Öffnungen. Das Fenster passt seine Größe an den tatsächlichen
Inhalt an (wächst bei langen Pfaden, scrollt bei langen Listen), statt
eine feste Größe zu verwenden.

## Bekannte Einschränkungen

- Noch kein Filter für OneDrive-Cloud-Platzhalterdateien, die nicht
  tatsächlich lokal heruntergeladen wurden (`FILE_ATTRIBUTE_OFFLINE`) -
  geplant.
- Bewusst schreibgeschützt: alle Konfiguration erfolgt durch direktes
  Bearbeiten von `RecentTab.ini`, es gibt keine Einstellungen-Oberfläche
  im Plugin selbst.
- "Zuletzt benutzt" erfasst nur tatsächlich geänderte oder neu
  angelegte Dateien, nicht Dateien, die nur geöffnet/angesehen, aber
  nicht verändert wurden - siehe die Anmerkung zu Windows'
  Aufzeichnungsgrenzen ganz oben in dieser Datei für den Grund.

## Aus dem Quellcode bauen

MinGW-w64 erforderlich (`build_debug.bat` erwartet `C:\mingw64`, mit
optionalem `mingw32`-Unterordner für einen zusätzlichen 32-Bit-Build
neben dem 64-Bit-Build). Siehe `TESTING.md` für das, was nach einem
Build zu prüfen ist.

Nach einem erfolgreichen Build baut `build_debug.bat` automatisch einen
`release`-Ordner neben dem Skript zusammen - mit den kompilierten
.wfx/.wfx64-Dateien zusammen mit `RecentTab.ini`, `RecentTab_lang.ini`,
`Color_ini_example.ini`, `pluginst.inf`, `README.md` und `LIESMICH.md`
- alles, was für Installation oder ein Verteilungs-Zip gebraucht wird,
ohne manuelles Kopieren.

## Lizenz

MIT - passend zu den anderen Total-Commander-Plugins dieses Autors
([XYTags](https://github.com/Native2904), DescriptEdit).
