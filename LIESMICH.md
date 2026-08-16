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
Laufzeit-Zähler. Das Panel selbst bleibt bei zwei Einträgen oben:
`! REC` für diesen Schalter, direkt daneben `! menu` für alles andere
(Reset, Refresh und die Suche weiter unten) - die eigentliche
Dateiliste muss sich also nie den Platz mit einer Reihe von Buttons
teilen.

Auf einen Blick:

- **Aufzeichnung** - einfacher Ein/Aus-Schalter, übersteht Neustarts,
  eigener Laufzeit-Zähler
- **Suche** - in jeden vergangenen Zeitraum springen, nicht nur
  "kürzlich"
- **Sechs Farb-Themes** - hell/dunkel, oder automatisch nach Uhrzeit
- **Auto-Refresh** - das Panel aktualisiert sich nach Zeitplan selbst,
  kein manuelles `Strg+R` nötig
- **Verlorene Dateien** - merkt, wenn etwas Beobachtetes verschwindet,
  und kann suchen, wohin es gewandert ist
- **Zusatzspalten** - Änderungsart, relative Zeit, Quellordner,
  Session-Nummer, bereits geöffnet, Sperrstatus
- **Helligkeitsregler** - jedes Theme nachjustieren, ohne neue Farben
  zu wählen
- **Mitgelieferte Schriften** - JetBrains Mono und Fira Code liegen
  bei, keine separate Installation nötig
- **Eigene Icons** - jedes Panel-Icon austauschbar
- **Sortierung zurücksetzen** - ein Klick macht eine versehentliche
  Spalten-Sortierung rückgängig

Diese Liste wächst mit dem Plugin mit - wie jede dieser Funktionen im
Detail funktioniert, steht unten bei "Erweiterte Konfiguration", die
genauen ini-Schlüssel in der Einstellungs-Referenz / Spalten-Referenz
weiter unten.

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
- Eine Datei, die innerhalb der letzten ein bis zwei Sekunden geändert
  wurde, während die Aufzeichnung noch aktiv läuft, kann gelegentlich
  einen zusätzlichen Refresh brauchen, bis sie erscheint - Everythings
  eigener Live-Index braucht dafür einen kurzen Moment. Pausieren (oder
  etwas länger warten) löst das immer auf.

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
RootButtons=          ; Reset;Refresh;Search;AutoRefresh an die Wurzel holen, statt nur im "! menu"
UseSearch=1            ; 0 = die Suche komplett ausblenden, kein Zugriffsweg mehr
ShowLiveClock=1        ; 0 = die tickende Uhr im Alt+Enter-Fenstertitel abschalten
NoColors=0             ; 1 = überall reine Systemfarben statt jedem Theme
FontBrightness=0       ; -3 bis +3, Textfarben des geladenen Themes feinjustieren
BackgroundBrightness=0 ; -3 bis +3, Hintergrundfarbe des geladenen Themes feinjustieren
AutoRefresh=0          ; 1 = Panel automatisch neu einlesen, statt Strg+R zu brauchen
AutoRefreshIntervalSec=600  ; nur relevant bei AutoRefresh=1 - Untergrenze von 3s wird erzwungen
;AutoRefreshMaxIdleMin=0     ; 0 = aus; sonst Auto-Refresh aussetzen, wenn das System so lange unbenutzt war
MonoFont=fonts\JetBrainsMono-Regular.ttf   ; Schriftart der Suchfenster-Felder - siehe "Mitgelieferte Schriften" unten
MonoFontName=JetBrains Mono

[Theme]
Name=basic            ; basic (Standard) / gruvbox / everforest / solarized / custom
Mode=dark             ; dark (Standard) / light
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

### Filtern nach Dateityp

`OnlyExtensions=` ist global (gilt für alle beobachteten Ordner
gleichzeitig):

```ini
[Settings]
;OnlyExtensions=docx;pdf;jpg
```

Akzeptiert `exe`, `*.exe` oder `.exe` je Eintrag - wie auch immer man's
natürlich eintippt. Sofern überhaupt gesetzt, hat es absoluten
Vorrang - nur Dateien, die darauf passen, werden gezeigt, jedes
`ExcludeExtensions=` (global oder pro Ordner) wird währenddessen
komplett ignoriert.

`ExcludeExtensions=` selbst liegt dagegen pro beobachtetem Ordner -
eine einzige globale Ausschlussliste könnte zwei unterschiedlich
genutzte Ordner nicht auseinanderhalten:

```ini
[Watched:Projects]
Path=D:\Projects
ExcludeExtensions=exe;dll
```

### Farbschemata

Vier eingebaute Preset-Familien - `basic` (Standard), `gruvbox`,
`everforest`, `solarized` - jede in Hell- und Dunkelvariante über
`Mode=`:

```ini
[Theme]
Name=basic
Mode=dark             ; dark (Standard) / light
```

(Dracula und Monokai stehen hier nicht mehr als Presets zur
Verfügung - keins von beiden hat eine offizielle helle Variante, um
es mit den anderen dreien zu paaren; für einen Dracula/Monokai-Look
dafür `custom` nutzen.) Oder `Name=custom` setzen und eigene Farben
angeben:

```ini
[Theme]
Name=custom
Background=#2b2a27
Foreground=#ede0ce
Heading=#d68f41
Green=#39b81f
Accent2=#00a8c6
Yellow=#ebb626
Accent4=#d63131
Muted=#7a7267
```

Jede leer gelassene Farbe fällt auf den passenden Basic-Wert zurück,
ein unvollständiges Custom-Theme sieht also nie kaputt aus.

Statt einem festen `Mode=` kann Hell/Dunkel auch der Uhrzeit folgen:

```ini
[Theme]
TimeBasedMode=0        ; 1 = Mode= oben ignorieren, stattdessen nach Uhrzeit entscheiden
LightStartHour=6
DarkStartHour=18
```

Die Standardwerte bedeuten hell von 6:00 bis 17:59, dunkel den Rest
der Zeit. Wird bei jedem Fensteröffnen frisch geprüft, schaltet also
live um, kein Neustart nötig.

`NoColors=1` in `[Settings]` hebelt jedes Theme oben komplett aus -
reine Systemfarben stattdessen, überall (Alt+Enter-Dialog, Suchfenster,
und Menu.exe, sobald es existiert). Jede Rolle, die über Farbe
Bedeutung trägt, sagt das auch im Text selbst (Warnungen buchstabieren
"WARNUNG:" aus) - nichts wird dadurch unlesbar, nur optisch weniger
auffällig.

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

### Zusatzspalten

Sechs optionale Spalten, jede standardmäßig aus - nur die aktivieren,
die man wirklich will, dann über TCs eigenes Shift+F1 "Configure
custom columns" zum Panel hinzufügen:

```ini
[Settings]
;ShowChangeType=0
;ShowRelativeTime=0
;ShowSourceFolder=0
;ShowSession=0
;ShowOpened=0
;ShowLocked=0
```

![SourceFolder, RelativeTime und Session-Spalten im Panel](screenshots/extra-columns.png)

**`ShowChangeType`** - das Plugin entscheidet intern ja schon, ob die
*Änderung* oder die *Erstellung* einer Datei den Ausschlag gegeben hat
(siehe die Anmerkung zu `dm:`/`dc:` weiter oben - deckt den
Archiv-Fall ab, wo eine alte Datei mit unverändertem Änderungsdatum
neu ausgepackt wird, aber *hier* trotzdem neu ist). Diese Spalte macht
diese schon vorhandene Entscheidung nur sichtbar ("Geändert" / "Neu"),
statt sie rein intern zu halten.

**`ShowRelativeTime`** - ein reines Datum braucht einen Moment zum
Erfassen. "vor 5 Minuten" nicht.

**`ShowSourceFolder`** - nur nützlich, sobald mehr als ein
`[Watched:...]`-Block existiert. Ohne sie müsste man den vollen Pfad
selbst lesen, um herauszufinden, welche Regel gegriffen hat.

**`ShowSession`** - aus welcher Aufzeichnungssitzung (Pause/Resume-
Zyklus) eine Datei stammt - praktisch, um "alles von heute Vormittag"
von "alles von eben" zu unterscheiden, ohne selbst mit Zeitstempeln zu
rechnen.

**`ShowOpened`** - markiert Dateien, die schon mal über dieses Panel
geöffnet wurden, mit einem "x" - praktisch beim Durchsehen mehrerer
Treffer, um die noch nicht angeschaute zu finden. Wichtige Grenze
dabei: Das weiß nur über Dateien Bescheid, die *über RecentTab selbst*
geöffnet wurden - ob danach im externen Programm wirklich etwas
bearbeitet wurde, sehen wir nicht, das passiert komplett außerhalb der
Sichtbarkeit des Plugins (und selbst Windows verfolgt das nicht
zuverlässig). Getrennt gesteuert über `OpenedTracking=session`
(vergisst bei TC-Neustart, Standard) oder `permanent` (übersteht
Neustarts, in einer eigenen kleinen Datei `RecentTab_opened.txt`).

**`ShowLocked`** - "Access", "Locked" oder "Not found" - hält gerade
ein anderes Programm die Datei geöffnet? Läuft auf einem
Hintergrund-Thread, das Panel selbst wartet nie darauf - die Spalte
bleibt kurz leer, bis der Check fertig ist, meist noch vor dem
nächsten Neuzeichnen. Der Check öffnet dabei tatsächlich jede
gelistete Datei zum Testen, was ihre Zugriffszeit verändert und
Antivirus oder Cloud-Sync aufwecken kann - gut zu wissen, bevor man's
einschaltet. Lokale Laufwerke werden immer geprüft; Netzlaufwerke,
Wechseldatenträger und optische Laufwerke sind standardmäßig aus
(jeweils ein eigener Roundtrip oder ein Laufwerk, das erst hochfahren
müsste) und einzeln über `AllowDriveNetwork=`, `AllowDriveRemovable=`,
`AllowDriveCDRom=` wieder einschaltbar. Cloud-Platzhalterdateien
(OneDrive Files On-Demand und Ähnliches) werden komplett übersprungen
- nie angefasst, sodass Durchblättern nie einen Download auslöst.

### Eigene Icons

Alle zehn Panel-Einträge liefern bereits ein eigenes, passendes Icon
als aktiven Standard mit (siehe `icons\` und den `[Icons]`-Abschnitt
von `RecentTab_example.ini`) - dieselbe Orange-Ring/Blaues-Symbol-
Familie durchweg, jede Form spiegelt, was ihr Eintrag tut. Jedes davon
ist eine einfache Überschreibung in `[Icons]`, zeigt stattdessen
lieber auf die eigene `.ico`/`.exe`/`.dll` - ein relativer Pfad wie
`icons\age.ico` wird relativ zum Ordner dieser ini aufgelöst:

```ini
[Icons]
AgeIcon=icons\age.ico
```

Die vollständige Liste aller zehn Schlüssel steht in der
Einstellungs-Referenz → Pfade.

### Suche — schnell in der eigenen Historie finden

Die normale Liste zeigt alles Aufgezeichnete, aber lang zurückscrollen
ist irgendwann mühsam. **Die Suche** ist genau dafür da: eine schnelle,
gezielte Suche **innerhalb deiner eigenen Aufzeichnungshistorie** -
nicht Everythings ganzer Index, nur das, was du selbst schon
aufgenommen hast, in deinen konfigurierten Ordnern. Kein Fenster
wechseln, kein Umweg über eine andere Anwendung, das Ergebnis landet
genauso im `\\RecentTab\`-Panel wie die normale Liste auch.

![Das Suchfenster](screenshots/search-window.png)

Zu finden unter `! menu` → `! Search`. Es öffnet sich ein kleines Fenster
mit zwei Feldern, **FROM** und **TO** - probier ruhig ein bisschen
herum, die Eingabe ist toleranter, als sie aussieht:

- Ein **Doppelpunkt** macht aus deiner Eingabe eine Uhrzeit: `17:00`
- Ein **Punkt, Bindestrich oder Schrägstrich** macht daraus ein Datum:
  `04.08.2026`
- Beides zusammen (mit Leerzeichen getrennt) ergibt einen genauen
  Zeitpunkt: `04.08.2026 17:00`
- Datum vergessen? Wird automatisch als **heute** angenommen.
- Uhrzeit vergessen? Bei FROM wird der Tagesanfang angenommen, bei TO
  das Tagesende - ein reines Datum in TO deckt also den ganzen Tag ab.
- TO einfach leer lassen, wenn's **bis jetzt** gehen soll.

Mit `! Back to recent files` geht's zurück zur normalen Ansicht. Über
`UseSearch=0` in `[Settings]` lässt sich die Suche bei Bedarf auch
komplett ausblenden.

### Der Alt+Enter-Dialog

Öffnet von `! REC`, `! menu` oder jedem Eintrag darin ein
themenfarbiges, schreibgeschütztes Statistikfenster: Aufzeichnungsstatus
mit live mitlaufender Uhr im Fensterrahmen (`ShowLiveClock=0` schaltet
die ab), geladenes Theme und geladene Sprache, vollständige
Aufzeichnungshistorie, Lifetime-Nutzung (übersteht Reset), ob
Everythings IPC-Verbindung gerade steht und welche Version läuft,
beobachtete Ordner (markiert, falls einer auf der Platte gar nicht
existiert), jede `[Icons]`-Einstellung, die auf eine nicht ladbare
Datei zeigt, und Config-/State-Dateipfade. Passt seine Größe an den
tatsächlichen Inhalt an statt eine feste Größe zu verwenden - wächst
bei langen Pfaden, scrollt bei langen Listen.

### Auto-Refresh - das Panel aktualisiert sich selbst, wenn gewünscht

Standardmäßig aus. Einschalten, und das Panel liest sich selbst nach
einem Zeitplan neu ein, ohne `Strg+R`:

```ini
[Settings]
AutoRefresh=0
AutoRefreshIntervalSec=600
```

Auch zur Laufzeit umschaltbar über `! menu` → `! Auto-Refresh: ...` -
Enter darauf schaltet zyklisch Aus → 1 → 5 → 10 → 30 Minuten → Aus,
keine ini-Änderung oder Neustart nötig. Dieser Menü-Schalter wirkt nur
für die laufende Sitzung; der ini-Wert ist nur der Startwert beim
Laden des Plugins.

Ein paar Dinge werden bewusst vor jedem automatischen Refresh geprüft,
damit im Hintergrund nichts Überraschendes passiert:

- Feuert nur, solange das aktive Panel tatsächlich RecentTab zeigt -
  aktualisiert nie ein Panel, das man gerade gar nicht anschaut
- `AutoRefreshMaxIdleMin=` - setzt aus, sobald das System so lange
  unbenutzt war (Maus/Tastatur-Ruhe, nicht nur TC selbst)

Der Alt+Enter-Dialog zeigt, was beim letzten Tick tatsächlich passiert
ist - nicht nur "es lief", sondern ob die Datenabfrage selbst
erfolgreich war, Änderungen fand, nichts Neues fand, oder komplett
fehlschlug. Die Kurzfassung der Überlegung dahinter: Ein einfacher
Tick-Zähler würde nur zeigen, dass etwas *versucht* wurde, nicht ob es
tatsächlich geklappt hat - deshalb bewusst keiner.

### Helligkeitsregler - ein Theme nachjustieren, ohne neue Farben zu wählen

```ini
[Settings]
FontBrightness=0        ; -3 bis +3
BackgroundBrightness=0  ; -3 bis +3
```

Sieben feste Stufen pro Regler, kein freier Wert - `FontBrightness`
verschiebt alle Textfarben, `BackgroundBrightness` nur den Hintergrund
(enger bemessen, da er die größte Fläche einnimmt und empfindlicher
auf Kontrastverlust reagiert). Würde eine bestimmte Kombination aus
beiden zu wenig Kontrast zwischen Text und Hintergrund übrig lassen,
werden beide stillschweigend ignoriert und eine Warnung erscheint im
Alt+Enter-Dialog, statt eines kaum lesbaren Ergebnisses. Feste,
vorab geprüfte Stufen statt eines freien Werts, aus demselben Grund,
warum die Theme-Presets selbst kuratiert sind statt beliebig.

### Mitgelieferte Schriften

Die FROM/TO-Felder im Suchfenster nutzen eine Monospace-Schrift, die
privat nur für diesen Prozess geladen wird - keine systemweite
Installation, keine Administrator-Rechte nötig, sieht überall gleich
aus, unabhängig davon, was auf der jeweiligen Maschine installiert
ist. Zwei Schriften liegen im `fonts\`-Ordner bei (beide SIL Open Font
License, frei weitergebbar): JetBrains Mono (Standard) und Fira Code.

```ini
[Settings]
MonoFont=fonts\JetBrainsMono-Regular.ttf
MonoFontName=JetBrains Mono
;MonoFont=fonts\FiraCode-Regular.ttf
;MonoFontName=Fira Code
```

Jede andere kompatible Monospace-`.ttf` funktioniert genauso - beide
Zeilen müssen zur jeweiligen Datei passen: `MonoFont=` ist der
Dateipfad, `MonoFontName=` ist der interne Schriftfamilien-Name der
Schrift selbst, der nicht immer mit dem Dateinamen übereinstimmt. Ein
falscher `MonoFontName=` erzeugt keinen Fehler und keinen Absturz -
Windows tauscht einfach still eine Ersatzschrift ein, man merkt es nur
an der Optik. Um den richtigen Namen zu einer unbekannten Schriftdatei
zu finden: Rechtsklick im Explorer → Vorschau (oder einfach
Doppelklick) - der Titel des Vorschaufensters zeigt den echten
Familiennamen.

Für Cascadia Code stattdessen (schon mit Windows installiert, nichts
herunterzuladen): `MonoFont=` leer lassen und
`MonoFontName=Cascadia Code` setzen.

### Verlorene Dateien - was verschwunden ist, und wann

Standardmäßig aus. Einschalten, und RecentTab merkt sich, wenn etwas
Beobachtetes verschwindet - gelöscht, außerhalb eines beobachteten
Ordners verschoben, oder umbenannt sehen von hier aus identisch aus,
deshalb wird nie behauptet, welcher Fall genau zutrifft, nur dass
etwas passiert ist:

```ini
[Settings]
TrackLostFiles=0
LostFilesTracking=session
```

Erscheint als `! Lost` innerhalb von `! menu`, mit der Anzahl in TCs
eigener Größen-Spalte sichtbar. Jeder Eintrag merkt sich, was zuletzt
bekannt war - Name, Größe, Daten, welcher beobachtete Ordner, welche
Aufzeichnungssitzung - und beim Öffnen erscheint ein kleines
Info-Fenster mit allem davon, plus ein "Erneut suchen"-Button, der
Everything systemweit nach dem exakten Dateinamen fragt, ohne
Ordner-Einschränkung, falls sie nur verschoben statt verschwunden ist.
Bewusst kein Live-/Sofort-Erkenner - weiß nur, dass zwischen zwei
Aktualisierungen etwas fehlt, nie den genauen Moment. Wird zusammen
mit dem Aufzeichnungsverlauf beim Zurücksetzen gelöscht, nicht separat.
Siehe `notes/lost-files/` für die ausführlichere Begründung, inklusive
warum zwei ambitioniertere Ansätze (ein Live-Hintergrundbeobachter, den
Papierkorb auslesen) erwogen und wieder verworfen wurden.

## Einstellungs-Referenz

Jede existierende Einstellung an einem Ort - gruppiert genauso wie
`RecentTab_example.ini` selbst (Funktionsschalter, Farben, Pfade),
damit diese Liste und die Datei immer zueinander passen. Die
Erzählabschnitte oben erklären das *Warum*; das hier ist das *Was*,
bewusst knapp gehalten. Neue Einstellungen bekommen eine Zeile hier
dazu, sonst wird nichts umgeschrieben.

**Funktionsschalter**

| Einstellung | Was sie tut |
|---|---|
| `ConfirmReset` | Vor dem Zurücksetzen "wirklich?" nachfragen |
| `SortDescending` | Neueste zuerst (1) oder älteste zuerst (0) |
| `DebugLogging` | `RecentTab_debug.log` schreiben - vor dem Weitergeben aus |
| `Language` | Oberflächensprache, passt zu einer Sektion in `RecentTab_lang.ini` |
| `IncludeDefaultFolders` | Eigene `[Watched:...]`-Blöcke zu den sechs Standardordnern hinzufügen statt ersetzen |
| `RootButtons` | Welche Utility-Einträge an der Wurzel stehen statt in `! menu` |
| `UseSearch` | 0 blendet `! Suche` komplett aus |
| `ShowLiveClock` | Tickende Uhr im Alt+Enter-Fenstertitel |
| `OnlyExtensions` | Globale Erlaubnisliste für Endungen - hebelt jeden Ausschluss aus, solange gesetzt |
| `ShowChangeType` / `ShowRelativeTime` / `ShowSourceFolder` / `ShowSession` / `ShowOpened` / `ShowLocked` | Die sechs Zusatzspalten - siehe Spalten-Referenz unten |
| `OpenedTracking` | `session`- oder `permanent`-Gedächtnis für `ShowOpened` |
| `AllowDriveNetwork` / `AllowDriveRemovable` / `AllowDriveCDRom` | Lässt `ShowLocked` auch diese Laufwerkstypen prüfen |
| `NoColors` | Überall reine Systemfarben, kein Theme |
| `AutoRefresh` | Panel liest sich automatisch neu ein |
| `AutoRefreshIntervalSec` | Wie oft - Untergrenze 3s erzwungen |
| `AutoRefreshMaxIdleMin` | Auto-Refresh aussetzen, sobald das System so lange untätig war |
| `FontBrightness` / `BackgroundBrightness` | Geladenes Theme nachjustieren, -3 bis +3 |
| `MonoFont` / `MonoFontName` | Welche mitgelieferte (oder eigene) Schrift das Suchfenster nutzt |
| `TrackLostFiles` | Bemerken, wenn eine beobachtete Datei verschwindet |
| `LostFilesTracking` | `session`- oder `permanent`-Gedächtnis für die Lost-Liste |
| `LostSearchStrict` | Exakte Größe/Datum-Übereinstimmung verlangen, damit "Erneut suchen" einen Fund bestätigt |

**Farben** (`[Theme]`)

| Einstellung | Was sie tut |
|---|---|
| `Name` | `basic` / `gruvbox` / `everforest` / `solarized` / `custom` |
| `Mode` | `dark` oder `light` |
| `TimeBasedMode` | Mode stattdessen automatisch nach Uhrzeit umschalten |
| `LightStartHour` / `DarkStartHour` | Die Umschaltzeiten, falls TimeBasedMode=1 |
| `Background` / `Foreground` / `Heading` / `Green` / `Accent2` / `Yellow` / `Accent4` / `Muted` | Die acht Farbrollen, nur bei `Name=custom` genutzt |

**Pfade** (maschinenspezifisch - nicht zum unveränderten Teilen/Kopieren gedacht)

| Einstellung | Was sie tut |
|---|---|
| `Path` (innerhalb `[Watched:Name]`) | Der Ordner selbst |
| `Exclude` | Unterpfade, die in diesem Ordner übersprungen werden |
| `ExcludeExtensions` | Endungen, die in diesem Ordner übersprungen werden |
| `RecIcon` / `ResetIcon` / `RefreshIcon` / `MenuIcon` / `AgeIcon` / `BackIcon` / `AutoRefreshIcon` / `SortResetIcon` / `LostIcon` / `FallbackIcon` | Icon-Überschreibungen pro Eintrag, alle optional |

## Spalten-Referenz

Alle sechs liegen in `RecentTab.ini`, standardmäßig aus, danach über
TCs eigenes Shift+F1 "Configure custom columns" zum Panel hinzugefügt.

| Spalte | Einstellung | Funktioniert auch in `! Lost` |
|---|---|---|
| Geändert / Neu | `ShowChangeType=1` | Ja |
| Relative Zeit | `ShowRelativeTime=1` | Ja |
| Quellordner | `ShowSourceFolder=1` | Ja |
| Session-Nummer | `ShowSession=1` | Ja |
| Bereits geöffnet | `ShowOpened=1` | Ja |
| Sperrstatus | `ShowLocked=1` | Nein - die Datei ist schon bekanntermaßen weg, ein Sperr-Check würde immer nur "Nicht gefunden" liefern |

## Voraussetzungen

- Windows 7 oder neuer, Total Commander 32-Bit oder 64-Bit
- [Everything](https://www.voidtools.com) installiert und laufend -
  das Plugin verbindet sich direkt über dessen eigenes IPC-Protokoll,
  kein zusätzlicher Download oder Einrichtungsschritt nötig außer
  Everything selbst im Hintergrund laufen zu lassen

## Dateien in diesem Paket

- `RecentTab.wfx` / `RecentTab.wfx64` - das Plugin (TC wählt automatisch
  die passende, falls beide vorhanden sind)
- `RecentTab_example.ini` - eine Referenzkopie aller verfügbaren
  Einstellungen, bewusst unter diesem Namen ausgeliefert (nicht
  `RecentTab.ini`): Ein Update entpacken überschreibt so nie die eigene
  Konfiguration. Diese Datei kopieren, die Kopie in `RecentTab.ini`
  umbenennen, und diese Kopie bearbeiten - das Plugin sucht genau
  diesen Dateinamen, funktioniert aber auch mit sinnvollen
  Standardwerten, selbst bevor diese Datei überhaupt existiert. Nach
  einem Update: diese Datei gegen die eigene `RecentTab.ini`
  vergleichen, um Neues zu finden.
- `RecentTab_lang.ini` - Oberflächentexte; muss im selben Ordner wie
  die Plugin-Datei liegen, da relativ zum eigenen Speicherort gesucht,
  nicht in TCs Konfigurationsordner
- `Color_ini_example.ini` - eine Vorlage zum Hinzufügen einer weiteren
  Sprache
- `icons\age.ico`, `icons\menu.ico` - die Standard-Icons für `! Search`
  und `! menu`, über `[Icons]` eingebunden - jederzeit gegen eigene
  austauschbar, siehe Eigene Icons oben
- `RecentTab_state.json` - wird beim ersten Gebrauch automatisch
  angelegt, nicht Teil dieses Pakets; merkt sich die
  Aufzeichnungshistorie
- `RecentTab_opened.txt` - wird nur bei `ShowOpened=1` und
  `OpenedTracking=permanent` automatisch angelegt; merkt sich, welche
  Dateien schon über das Panel geöffnet wurden

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

Nur beim ersten Mal nötig: `RecentTab_example.ini` kopieren, die Kopie
in `RecentTab.ini` umbenennen, im selben Ordner. Alles funktioniert
auch ohne diesen Schritt mit reinen Standardwerten - dann gibt's nur
noch nichts zum Bearbeiten, falls man was ändern möchte.

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
