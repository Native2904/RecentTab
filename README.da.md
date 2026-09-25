# RecentTab

[English](README.md) · [Deutsch](LIESMICH.md) · [Русский](README.ru.md) · [Українська](README.uk.md) · **Dansk**

Et filsystem-plugin (WFX) til Total Commander, der viser, hvad du
senest har arbejdet med: nyligt ændrede og nyoprettede filer fra de
mapper, du interesserer dig for, samlet i ét virtuelt panel.

<img width="1919" height="1042" alt="2026-08-30_141524" src="https://github.com/user-attachments/assets/2fcde74a-3813-488a-ad76-cfe74cb341cb" />


## Hvad det kan

Åbn `\\RecentTab\` i Total Commander, og du får en flad, kronologisk
liste over alle filer, du faktisk har rørt for nylig, samlet fra hele
systemet - via en direkte forbindelse til
[Everything](https://www.voidtools.com) (voidtools), ingen
baggrundstjeneste og ingen langsom manuel scanning. Hver fil i listen
er den ægte fil på dens ægte placering - åbn den, redigér den, kopiér
den, flyt den, slet den direkte fra listen, intet virtuelt eller
mellemlagret. Selve optagelsen er en simpel tænd/sluk-kontakt, som du
styrer med musen direkte i panelet, med sin egen tidstæller. Panelet
har kun to poster øverst - `! REC` til denne kontakt og ved siden af
`! menu` til alt andet (nulstilling, opdatering og søgningen nedenfor)
- så listen over de egentlige filer aldrig skal dele pladsen med en
række knapper.

Kort fortalt:

- **Optagelse** - simpel tænd/sluk-kontakt, overlever genstart, egen
  tidstæller
- **Søgning** - spring til et hvilket som helst tidsrum i fortiden, ikke
  kun "for nylig"
- **Farvetemaer** - fire forvalg plus dit eget, hver lys/mørk, eller
  automatisk efter tidspunkt på dagen
- **Auto-opdatering** - panelet opdaterer sig selv efter en tidsplan,
  intet manuelt `Ctrl+R`
- **Forsvundne filer** - bemærker, når noget, der blev fulgt, forsvinder,
  og kan søge efter, hvor det er blevet af
- **Ekstra kolonner** - ændringstype, relativ tid, kildemappe,
  sessionsnummer, allerede åbnet, låsestatus
- **Lysstyrkeregulering** - finjustér et temas kontrast uden at vælge
  nye farver
- **Medfølgende skrifttyper** - JetBrains Mono og Fira Code følger med,
  ingen separat installation
- **Egne ikoner** - hver panelposts ikon kan udskiftes
- **Nulstil sortering** - ét klik fortryder en utilsigtet sortering ved
  klik på en kolonne
- **Mappetræ** - `! Mappetræ` i `! menu` (eller Alt+Enter på
  `! menu`) viser, hvordan de overvågede
  mapper faktisk forgrener sig, døde stier tydeligt markeret

Listen vokser med pluginet - hvordan hver funktion fungerer i detaljer,
står under "Avanceret konfiguration" nedenfor, de præcise ini-nøgler i
Indstillingsoversigten / Kolonneoversigten til sidst.

Luk fanen, luk Total Commander helt, genstart computeren - det gør
ingen forskel. Din optagelseshistorik ligger i sin egen fil ved siden
af pluginet, ikke i hukommelsen, så næste gang du åbner `\\RecentTab\`,
er alt præcis, hvor du efterlod det - ingen genoptagelse, intet tabt
ved et utilsigtet Alt+F4.

Hvor der kigges, bestemmer du. Som udgangspunkt overvåges de seks
sædvanlige mapper (Skrivebord, Dokumenter, Overførsler, Billeder,
Videoer, Musik), og OneDrive-omdirigeringer følges korrekt, hvis du har
dem. Vil du have noget snævrere eller helt andet - kun dit projektdrev,
for eksempel? Definér din egen hvidliste over mapper og eventuelt en
sortliste over bestemte understier i dem (en `node_modules`-mappe, en
backup-cache, hvad der end roder i resultaterne) - eller behold de seks
standardmapper *og* tilføj dine egne oveni, som du vil.
Pladsholderfiler fra cloud-synkronisering, der endnu ikke er hentet
(OneDrive Filer efter behov og lignende), filtreres automatisk fra, så
de ikke fylder listen med ting, der aldrig rigtig er "brugt" på denne
maskine.

Alt andet er bygget til at holde sig i baggrunden og fortælle dig, hvad
det gør, i stedet for at lade dig gætte. En særlig `RealPath`-kolonne
viser altid en fils sande placering, selvom listen spænder over mange
mapper. Sortering er som standard nyeste først (eller ældste først,
som du vil). Alt+Enter åbner et farvetemasat statistikvindue - fire
indbyggede farvepaletter, størrelsen tilpasser sig indholdet - med et
ur, der tikker direkte i titellinjen, og en letlæselig oversigt over,
hvad der er indlæst lige nu: hvilket tema, hvilket sprog, om
Everything faktisk er forbundet lige nu og hvilken version det kører,
hvor mange forespørgsler der er gået igennem i denne session, hele din
optagelseshistorik, og hvor på disken hver fil, som pluginet bruger,
faktisk ligger. Intet at lede efter, intet gemt bag en
indstillingsdialog - åbn vinduet, og det hele er der.

Under motorhjelmen taler pluginet direkte med Everything via dets egen
IPC-protokol - ingen separat proces for hver forespørgsel, ingen
kommandolinje overhovedet. Det er mærkbart hurtigere end den ældre
tilgang, projektet brugte før, og Alt+Enter-vinduet fortæller dig
klart, om forbindelsen faktisk er oppe. Hele brugerfladen findes i
øvrigt på flere sprog og er nem at oversætte videre, hvis du vil
tilføje dit eget.

## Kendte begrænsninger

- Ingen indstillingsdialog: konfiguration sker ved at redigere
  `RecentTab.ini` direkte (filerne i listen kan selvfølgelig redigeres
  som normalt).
- Omfatter kun filer, der faktisk er ændret eller nyoprettet - ikke
  filer, der kun er åbnet/set uden ændringer. Windows registrerer ikke
  pålideligt, *hvilken proces* der rørte en fil, kun *at* den blev
  ændret, så dette er en hård platformsbegrænsning og ikke noget, en
  fremtidig opdatering kan tilføje.
- En fil, der er ændret inden for det sidste sekund eller to, mens
  optagelsen stadig kører, kan af og til kræve én ekstra opdatering,
  før den dukker op - Everythings eget live-indeks skal lige have et
  øjeblik til at indhente det. Pause (eller lidt længere ventetid) løser
  det altid.

---

## Avanceret konfiguration

Alt nedenfor har en fungerende standardværdi - dette afsnit er for dem,
der vil tilpasse mere, ikke nødvendig læsning for at komme i gang.

```ini
[Settings]
ConfirmReset=1       ; 0 = nulstil straks uden spørgsmålet "Vil du virkelig nulstille?"
SortDescending=1     ; 0 = ældste først i stedet for nyeste først
Language=auto        ; auto (standard) = følg Total Commanders sprog / eller fast: eng / deu / rus / ...
DebugLogging=1       ; 0 = skriv ikke RecentTab_debug.log (koster reel I/O, mens det er slået til)
IncludeDefaultFolders=0  ; 1 = tilføj dine [Watched:...]-blokke til de seks standardmapper i stedet for at erstatte dem
RootButtons=          ; flyt Reset;Refresh;Search;AutoRefresh;SortReset;FolderTree til roden i stedet for kun inde i "! menu"
UseSearch=1            ; 0 = skjul søgningen helt
ShowLiveClock=1        ; 0 = stop uret i Alt+Enter-titellinjen
NoColors=0             ; 1 = almindelige systemfarver overalt i stedet for et tema
FontBrightness=0       ; -3 til +3, finjustér tekstens lysstyrke i det indlæste tema
BackgroundBrightness=0 ; -3 til +3, finjustér baggrundens lysstyrke i det indlæste tema
AutoRefresh=0          ; 1 = panelet genindlæses automatisk uden Ctrl+R
AutoRefreshIntervalSec=600  ; kun relevant ved AutoRefresh=1 - mindst 3 s
;AutoRefreshMaxIdleMin=0     ; 0 = slået fra; ellers springes auto-opdatering over, når systemet har været ubrugt så længe
;QuietHoursStart=22:00       ; ingen auto-opdatering mellem disse to tidspunkter (f.eks. om natten)
;QuietHoursEnd=07:00
;AutoRefreshSkipOnBattery=0  ; 1 = spring auto-opdatering over ved batteridrift
MonoFont=fonts\JetBrainsMono-Regular.ttf   ; skrifttypen i søgevinduets felter - se "Medfølgende skrifttyper" nedenfor
MonoFontName=JetBrains Mono

[Theme]
Name=basic            ; basic (standard) / gruvbox / everforest / solarized / custom
Mode=dark             ; dark (standard) / light
```

### Vælg hvilke mapper der overvåges (hvidliste), og hvad der springes over i dem (sortliste)

Tilføj én eller flere `[Watched:Name]`-blokke for at definere din egen
liste - så snart der er mindst én, ignoreres de seks standardmapper
helt, medmindre `IncludeDefaultFolders=1` er sat:

```ini
[Watched:Projects]
Path=D:\Projects
Exclude=Backup;node_modules

[Watched:Desktop]
Path=%USERPROFILE%\Desktop
```

`Exclude=` tager semikolonseparerede understi-fragmenter, der skal
springes over inden for blokkens `Path=` - din sortliste pr. mappe. Med
`IncludeDefaultFolders=1` tilføjes dine egne blokke til de seks
standardmapper i stedet for at erstatte dem (definér selv en af de
seks, f.eks. med dit eget `Exclude=`, så bruges din version i stedet
for standarden - den dukker ikke op to gange).

### Filtrering efter filtype

`OnlyExtensions=` er global (gælder for alle overvågede mapper på én
gang):

```ini
[Settings]
;OnlyExtensions=docx;pdf;jpg
```

Accepterer `exe`, `*.exe` eller `.exe` for hver post - som du nu
naturligt skriver det. Er den overhovedet sat, har den absolut
forrang - kun filer, der matcher en af dem, vises, og alle
`ExcludeExtensions=` (globale eller pr. mappe) tilsidesættes helt, så
længe den er aktiv.

`ExcludeExtensions=` angives i stedet pr. overvåget mappe - én fælles
global udelukkelsesliste kunne ikke skelne mellem to mapper med
forskelligt formål:

```ini
[Watched:Projects]
Path=D:\Projects
ExcludeExtensions=exe;dll
```

### Farvetemaer

Fire indbyggede familier - `basic` (standard), `gruvbox`,
`everforest`, `solarized` - hver i en mørk og en lys variant via
`Mode=`:

```ini
[Theme]
Name=basic
Mode=dark             ; dark (standard) / light
```

(Dracula og Monokai tilbydes ikke som forvalg her - ingen af dem har en
officiel lys pendant som de andre tre; brug `custom` nedenfor for et
Dracula/Monokai-agtigt udseende.) Eller sæt `Name=custom` og angiv dine
egne farver:

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

En farve, der efterlades tom, falder tilbage til den tilsvarende
basic-værdi, så et delvist udfyldt eget tema aldrig ser ødelagt ud.

I stedet for et fast `Mode=` kan lys/mørk også følge uret:

```ini
[Theme]
TimeBasedMode=0        ; 1 = ignorér Mode= ovenfor, afgør efter klokkeslæt i stedet
LightStartHour=6
DarkStartHour=18
```

Standardværdierne betyder lys fra 6:00 til 17:59, mørk resten af
tiden. Tjekkes på ny, hver gang et vindue åbnes, så det skifter
løbende - ingen genstart nødvendig.

`NoColors=1` i `[Settings]` tilsidesætter ethvert tema ovenfor helt -
almindelige systemfarver i stedet, overalt (Alt+Enter-dialogen,
søgevinduet, infovinduet for forsvundne filer, mappetræet). Hver rolle,
der bærer betydning via farve, siger det også i sin egen tekst
(advarsler skriver "WARNING:" ud), så intet bliver ulæseligt uden
farver - bare mindre iøjnefaldende ved første øjekast.

### Sprog

Som standard taler RecentTab simpelthen Total Commanders sprog: det,
der er valgt under Konfiguration → Indstillinger → Sprog
(`WCMD_DAN.LNG` → dansk, `WCMD_DEU.LNG` → tysk, TC's indbyggede
engelsk → engelsk). `Language=deu` (eller et andet sektionsnavn) i
`[Settings]` låser sproget uafhængigt af TC. Sproget læses én gang pr.
TC-session - genstart TC efter at have skiftet sprog i TC.

Brugerfladens tekster kommer fra `RecentTab.lng` - én fil, én
`[eng]`/`[dan]` osv.-sektion pr. sprog, den sædvanlige konvention
for TC-plugins - og læses med pluginets egen UTF-8-sikre parser i
stedet for Windows' `GetPrivateProfileString` (som ødelægger ikke-latinske
skriftsystemer via systemets ANSI-tegntabel, medmindre filen har en
UTF-16LE-BOM). Leveres med `eng`, `deu`, `rus`, `ukr` og `dan`; en
nøgle, der mangler eller er tom, falder automatisk tilbage til engelsk.
(Før 1.5.1 hed filen `RecentTab_lang.ini`
- den læses stadig, hvis der ikke findes en `RecentTab.lng`.)

Tilføj et sprog: kopiér `[eng]`-sektionen, omdøb den til TC's
trebogstavskode (delen efter sidste `_` i navnet på TC's egen sprogfil,
f.eks. `WCMD_DAN.LNG` → `[dan]`), og oversæt værdierne. Pladsholdere
som `%ls`/`%d`/`%lld` skal forblive de samme og i samme rækkefølge - en
linje, hvor de afviger, vises på engelsk i stedet for at risikere et
nedbrud. Nøgler, der ender på `_one`/`_many`, er ental-/flertalsformer
(på dansk: 1 minut / 5 minutter).

En bivirkning, man bør kende: `! ...`-posterne i panelet oversættes
også. En TC-knap eller genvejstast, der henviser til en af dem ved navn
(f.eks. `\\RecentTab\! REC`), passer kun på det sprog - lås
`Language=`, hvis du bruger sådan en genvej.

### Ekstra kolonner

Seks valgfrie kolonner, hver slået fra som standard - slå kun dem til,
du faktisk vil have, og tilføj dem derefter til panelet via Total
Commanders egen Shift+F1 "Brugerdefinerede kolonner":

```ini
[Settings]
;ShowChangeType=0
;ShowRelativeTime=0
;ShowSourceFolder=0
;ShowSession=0
;ShowOpened=0
;ShowLocked=0
```

![Kolonnerne SourceFolder, RelativeTime og Session i panelet](screenshots/extra-columns.png)

**`ShowChangeType`** - pluginet afgør allerede internt, om det var en
fils *ændring* eller dens *oprettelse*, der gjorde, at den skulle vises
(håndterer arkivtilfældet, hvor en gammel fil udpakkes med sin
oprindelige ændringsdato, men er helt ny *her*). Denne kolonne gør bare
den eksisterende afgørelse synlig ("Ændret" / "Ny") i stedet for at
holde den rent intern.

**`ShowRelativeTime`** - en almindelig dato tager et øjeblik at aflæse.
"For 5 minutter siden" gør ikke.

**`ShowSourceFolder`** - kun nyttig, når du har mere end én
`[Watched:...]`-blok. Uden den må du selv læse hele stien for at finde
ud af, hvilken regel der fangede en fil.

**`ShowSession`** - hvilken optagelsessession (pause/genoptag-cyklus)
en fil stammer fra - praktisk til at skelne "alt fra i morges" fra "alt
fra lige nu" uden selv at regne på tidsstempler.

**`ShowOpened`** - markerer filer, du allerede har åbnet via dette panel,
med et "x" - praktisk, når du gennemgår flere træf efter den, du endnu
ikke har kigget på. Vigtig begrænsning: den kender kun til filer, der
er åbnet *netop via RecentTab* - den kan ikke fortælle, om filen
bagefter faktisk blev redigeret i det program, der åbnede den, for det
sker helt uden for pluginets synsfelt (og Windows sporer heller ikke
det pålideligt). Styres separat via `OpenedTracking=session` (glemmes
ved genstart af TC, standard) eller `permanent` (huskes på tværs af
genstarter i sin egen lille fil `RecentTab_opened.txt`).

**`ShowLocked`** - "Adgang", "Låst" eller "Ikke fundet" - holder et
andet program filen åben lige nu? Tjekkes i en baggrundstråd, så
panelet aldrig venter på det - kolonnen forbliver bare tom et øjeblik,
til tjekket er færdigt, som regel længe før næste gentegning. Tjekket
åbner faktisk hver fil i listen for at teste den, hvilket ændrer filens
tidspunkt for seneste adgang og kan vække antivirus eller
cloud-synkronisering - godt at vide, før du slår det til. Lokale drev
tjekkes altid; netværksdrev, flytbare medier og optiske drev er slået
fra som standard (hver især en ekstra rundtur eller et drev, der måske
først skal spinne op) og slås til enkeltvis via `AllowDriveNetwork=`,
`AllowDriveRemovable=`, `AllowDriveCDRom=`. Pladsholderfiler fra skyen
(OneDrive Filer efter behov og lignende) springes helt over - de røres
aldrig, så gennemsyn udløser aldrig en download.

### Egne ikoner

Alle elleve panelposter leveres allerede med deres eget passende ikon som
aktiv standard (se `icons\` og `[Icons]`-sektionen i
`RecentTab_example.ini`) - samme familie med orange ring og blåt symbol
hele vejen igennem, hvor hver form afspejler, hvad posten gør. Hver
enkelt kan tilsidesættes i `[Icons]` med din egen `.ico`/`.exe`/`.dll`
- en relativ sti som `icons\age.ico` regnes ud fra ini-filens egen
mappe:

```ini
[Icons]
AgeIcon=icons\age.ico
```

Se Indstillingsoversigt → Stier for den komplette liste over alle elleve
nøgler.

### Søgning - find hurtigt noget i din egen historik

Den normale liste viser alt optaget, men at rulle langt tilbage bliver
hurtigt trættende. **Søgning** er en hurtig, målrettet søgning **i din
egen optagelseshistorik** - ikke i hele Everythings indeks, kun i det,
du faktisk har optaget, i dine konfigurerede mapper. Intet
vinduesskift, ingen omvej via et andet program - resultaterne lander
direkte i `\\RecentTab\`, ligesom den normale liste.

![Søgevinduet](screenshots/search-window.png)

Findes under `! menu` → `! Search`. Et lille vindue åbnes med to
felter, **FRA** og **TIL** (i den engelske brugerflade FROM og TO) - indtastningen er mere tilgivende, end den
ser ud:

- Et **kolon** gør din indtastning til et klokkeslæt: `17:00`
- Et **punktum, en bindestreg eller en skråstreg** gør den til en dato:
  `04.08.2026` (dagen først - den 4. august, ikke april)
- Begge dele (adskilt af mellemrum) giver et præcist tidspunkt:
  `04.08.2026 17:00`
- Ingen dato angivet? Så antages **i dag**.
- Intet klokkeslæt angivet? FRA betyder dagens start, TIL dagens
  slutning - et TIL med kun dato dækker hele dagen.
- Lad TIL være tom for "**indtil nu**".

`! Back to recent files` fører dig tilbage til den normale visning.
`UseSearch=0` i `[Settings]` skjuler søgningen helt, hvis du hellere vil
være fri for den.

### Alt+Enter-dialogen

Åbner et farvetemasat, skrivebeskyttet statistikvindue fra `! REC`,
`! menu` eller en af posterne i den: optagelsesstatus med et tikkende ur
i titellinjen (`ShowLiveClock=0` slår det fra), hvilket tema og sprog
der er indlæst, hele din optagelseshistorik, samlet brug (overlever
nulstilling), om Everythings IPC-forbindelse er oppe lige nu og hvilken
version den kører, overvågede mapper (markeret, hvis en ikke findes på
disken), enhver `[Icons]`-tilsidesættelse, der peger på en fil, som
ikke kunne indlæses, samt stier til konfigurations- og tilstandsfiler.
Størrelsen tilpasser sig det faktiske indhold i stedet for en fast
størrelse - vokser ved lange stier, ruller ved lange lister.

### Auto-opdatering - panelet opdaterer sig selv, hvis du vil

Slået fra som standard. Slå det til, og panelet genindlæser sig selv
efter en timer, uden `Ctrl+R`:

```ini
[Settings]
AutoRefresh=0
AutoRefreshIntervalSec=600
```

Kan også skiftes under kørsel via `! menu` → `! Auto-Refresh: ...` -
Enter på den skifter Fra → 1 → 5 → 10 → 30 minutter → Fra, uden at
redigere ini eller genstarte. Menukontakten gælder kun den aktuelle
session; ini-værdien er blot det, den starter med, når TC indlæser
pluginet.

Et par ting tjekkes bevidst, før den slår til, så der ikke sker noget
overraskende i baggrunden:

- Slår kun til, mens det aktive panel faktisk viser RecentTab - genindlæser
  aldrig et panel, du ikke kigger på
- `AutoRefreshMaxIdleMin=` - springer over, når systemet har været
  ubrugt så længe (mus/tastatur inaktivt, ikke kun TC)
- `QuietHoursStart=`/`QuietHoursEnd=` - et tidsvindue helt uden
  auto-opdatering, f.eks. om natten
- `AutoRefreshSkipOnBattery=1` - springer over ved batteridrift

Alt+Enter-dialogen viser, hvad der faktisk skete ved sidste tik - ikke
bare "det kørte", men om selve dataforespørgslen lykkedes, fandt
ændringer, intet nyt fandt eller slog fejl. Kort sagt: en simpel
tik-tæller ville vise, at noget blev *forsøgt*, ikke om det faktisk
virkede - derfor bruges der bevidst ikke en.

### Lysstyrkeregulering - justér et tema uden at vælge nye farver

```ini
[Settings]
FontBrightness=0        ; -3 til +3
BackgroundBrightness=0  ; -3 til +3
```

Syv faste trin hver, ikke et frit tal - `FontBrightness` forskyder alle
tekstfarver, `BackgroundBrightness` kun baggrunden (et smallere
område, da det er den største flade og mere følsom over for tab af
kontrast). Hvis en bestemt kombination af de to ville give for lidt
kontrast mellem tekst og baggrund, ignoreres begge i stilhed, og der
vises en advarsel i Alt+Enter-dialogen i stedet for et knap læseligt
resultat. Faste, forhåndstjekkede trin er bedre end et frit tal her af
samme grund, som temaforvalgene selv er nøje udvalgt og ikke
vilkårlige.

### Medfølgende skrifttyper

Søgevinduets FROM/TO-felter bruger en fastbreddeskrift, der kun
indlæses privat til denne proces - ingen systemdækkende installation,
ingen administratorrettigheder, ser ens ud uanset hvad der er
installeret på maskinen. To skrifttyper følger med i `fonts\`-mappen
(begge under SIL Open Font License, frit videredistribuerbare):
JetBrains Mono (standard) og Fira Code.

```ini
[Settings]
MonoFont=fonts\JetBrainsMono-Regular.ttf
MonoFontName=JetBrains Mono
;MonoFont=fonts\FiraCode-Regular.ttf
;MonoFontName=Fira Code
```

Enhver anden kompatibel fastbredde-`.ttf` virker også - begge linjer
skal passe til den fil, du peger på: `MonoFont=` er filstien,
`MonoFontName=` er skrifttypens interne familienavn, som ikke altid er
det samme som filnavnet. Et forkert `MonoFontName=` giver ingen fejl
eller nedbrud - Windows erstatter bare i stilhed med en anden
skrifttype, og du opdager det kun på udseendet. For at finde det
rigtige navn på en skrifttype, du er usikker på: højreklik på den i
Stifinder → Eksempel (eller dobbeltklik bare) - eksempelvinduets titel
viser det rigtige familienavn.

For at bruge Cascadia Code i stedet (allerede installeret med Windows,
intet at hente): lad `MonoFont=` være tom, og sæt
`MonoFontName=Cascadia Code`.

### Forsvundne filer - hvad forsvandt, og hvornår

Slået fra som standard. Slå det til, og RecentTab begynder at bemærke,
når noget, det fulgte, forsvinder - sletning, flytning uden for en
overvåget mappe og omdøbning ser ens ud herfra, så det påstår aldrig at
vide, hvad der skete, kun at noget skete:

```ini
[Settings]
TrackLostFiles=0
LostFilesTracking=session
```

Vises som `! Lost` inde i `! menu`, med antallet synligt i TC's egen
størrelseskolonne. Hver post husker det, der senest var kendt om den -
navn, størrelse, datoer, hvilken overvåget mappe, hvilken
optagelsessession - og åbner du en, vises et lille infovindue med det
hele plus en knap "Søg igen", der spørger Everything efter det præcise
filnavn i hele systemet uden mappebegrænsning, hvis filen blot er
flyttet i stedet for forsvundet. Det er bevidst ikke en øjeblikkelig
detektor - den ved kun, at noget mangler mellem én opdatering og den
næste, aldrig det præcise øjeblik. Ryddes sammen med
optagelseshistorikken ved nulstilling, ikke separat. Se
`notes/lost-files/` for den fulde begrundelse, herunder hvorfor et par
mere ambitiøse tilgange (en live-overvåger i baggrunden, læsning af
papirkurven) blev overvejet og lagt til side.

### Mappetræ - se hvordan de overvågede mapper faktisk forgrener sig

`! menu` → `! Mappetræ` (eller Alt+Enter direkte på `! menu`) åbner et
lille selvstændigt vindue (samme visuelle
stil som søgevinduet - tynde kanter, eget tema, uafhængigt af TC's eget
udseende), der viser hver overvåget mappe som et træ - sammen med de
mapper, de optagne filer faktisk ligger i, da de som regel ligger
flere niveauer dybere end selve den overvågede mappe.

Mapper med fælles overordnet mappe grupperes automatisk under den, også
hvis den overordnede mappe ikke selv overvåges - kun en visuel
gruppering, intet ekstra spores. En kæde af mapper, der hver har
præcis én undermappe - ingen reel forgrening undervejs - foldes sammen
til én linje i stedet for fem-seks indrykkede niveauer uden ekstra
information; ligger der stadig filer direkte i en af de mellemliggende
mapper, forbliver antallet synligt ved siden af navnet og skjules ikke
af sammenfoldningen.

En overvåget mappe, hvis sti ikke længere findes på disken, får et
særskilt, bevidst iøjnefaldende ikon (en vejbom, ikke det samme spøgelse,
der bruges til enkelte forsvundne filer - en hel manglende konfigureret
mappe er en anden situation end én forsvundet fil).

Vinduet begrænses til omtrent højden af et standard Lister-vindue (TC
giver ikke plugins mulighed for at spørge om den faktiske størrelse, så
det er en tilnærmelse, ikke en måling) - alt højere rulles med
musehjulet. Se `notes/folder-tree/` for den fulde begrundelse, herunder
hvorfor netop GDI+ var nødvendigt her.

## Indstillingsoversigt

Alle eksisterende indstillinger samlet ét sted - grupperet på samme
måde som `RecentTab_example.ini` selv (funktionskontakter, farver,
stier), så listen og filen altid stemmer overens. Afsnittene ovenfor
forklarer *hvorfor*; her er *hvad*, bevidst kortfattet. Nye
indstillinger får én linje her, intet andet omskrives.

**Funktionskontakter**

| Indstilling | Hvad den gør |
|---|---|
| `ConfirmReset` | Spørg "vil du virkelig nulstille?" før historikken ryddes |
| `SortDescending` | Nyeste først (1) eller ældste først (0) |
| `DebugLogging` | Skriv `RecentTab_debug.log` - slå fra, før en build deles |
| `Language` | `auto` (standard) følger TC's sprog; eller et fast sektionsnavn i `RecentTab.lng` |
| `IncludeDefaultFolders` | Tilføj dine egne `[Watched:...]`-blokke til de seks standardmapper i stedet for at erstatte dem |
| `RootButtons` | Hvilke hjælpeposter der ligger i roden i stedet for inde i `! menu` |
| `UseSearch` | 0 skjuler `! Search` helt |
| `ShowLiveClock` | Tikkende ur i Alt+Enter-titellinjen |
| `OnlyExtensions` | Global tilladt-liste over filtyper - tilsidesætter alle udelukkelser, mens den er sat |
| `ShowChangeType` / `ShowRelativeTime` / `ShowSourceFolder` / `ShowSession` / `ShowOpened` / `ShowLocked` | De seks ekstra kolonner - se Kolonneoversigt nedenfor |
| `OpenedTracking` | `session`- eller `permanent`-hukommelse for `ShowOpened` |
| `AllowDriveNetwork` / `AllowDriveRemovable` / `AllowDriveCDRom` | Lad `ShowLocked` også tjekke disse drevtyper |
| `NoColors` | Almindelige systemfarver overalt, intet tema |
| `AutoRefresh` | Panelet genindlæser sig selv automatisk |
| `AutoRefreshIntervalSec` | Hvor ofte - mindst 3 s |
| `AutoRefreshMaxIdleMin` | Spring auto-opdatering over, når systemet har været inaktivt så længe |
| `QuietHoursStart` / `QuietHoursEnd` | Ingen auto-opdatering mellem disse to tidspunkter (f.eks. om natten) |
| `AutoRefreshSkipOnBattery` | Spring auto-opdatering over ved batteridrift |
| `FontBrightness` / `BackgroundBrightness` | Finjustér det indlæste tema, -3 til +3 |
| `MonoFont` / `MonoFontName` | Hvilken medfølgende (eller egen) skrifttype søgevinduet bruger |
| `TrackLostFiles` | Bemærk, når en fulgt fil forsvinder |
| `LostFilesTracking` | `session`- eller `permanent`-hukommelse for listen over forsvundne |
| `LostSearchStrict` | Kræv nøjagtigt match af størrelse og dato, før "Søg igen" bekræfter et fund |

**Farver** (`[Theme]`)

| Indstilling | Hvad den gør |
|---|---|
| `Name` | `basic` / `gruvbox` / `everforest` / `solarized` / `custom` |
| `Mode` | `dark` eller `light` |
| `TimeBasedMode` | Skift Mode automatisk efter uret i stedet |
| `LightStartHour` / `DarkStartHour` | Skiftetidspunkterne, hvis TimeBasedMode=1 |
| `Background` / `Foreground` / `Heading` / `Green` / `Accent2` / `Yellow` / `Accent4` / `Muted` | De otte farveroller, bruges kun ved `Name=custom` |

**Stier** (maskinspecifikke - ikke beregnet til at blive delt/kopieret som de er)

| Indstilling | Hvad den gør |
|---|---|
| `Path` (inde i `[Watched:Name]`) | Selve mappen |
| `Exclude` | Understier, der springes over i den mappe |
| `ExcludeExtensions` | Filtyper, der springes over i den mappe |
| `RecIcon` / `ResetIcon` / `RefreshIcon` / `MenuIcon` / `AgeIcon` / `BackIcon` / `AutoRefreshIcon` / `SortResetIcon` / `FolderTreeIcon` / `LostIcon` / `FallbackIcon` / `DeadPathIcon` | Ikon-tilsidesættelser pr. post, alle valgfrie |

## Kolonneoversigt

Alle seks ligger i `RecentTab.ini`, slået fra som standard, og tilføjes
derefter til panelet via TC's egen Shift+F1 "Brugerdefinerede
kolonner".

| Kolonne | Indstilling | Virker også inde i `! Lost` |
|---|---|---|
| Ændret / Ny | `ShowChangeType=1` | Ja |
| Relativ tid | `ShowRelativeTime=1` | Ja |
| Kildemappe | `ShowSourceFolder=1` | Ja |
| Sessionsnummer | `ShowSession=1` | Ja |
| Allerede åbnet | `ShowOpened=1` | Ja |
| Låsestatus | `ShowLocked=1` | Nej - filen vides allerede at være væk, et låsetjek ville altid kun sige "Ikke fundet" |

## Krav

- Windows 7 eller nyere, 32- eller 64-bit Total Commander
- [Everything](https://www.voidtools.com) installeret og kørende -
  pluginet forbinder direkte til det via dets egen IPC-protokol, intet
  ekstra at hente eller sætte op ud over at have Everything kørende i
  baggrunden

## Filer i denne pakke

- `RecentTab.wfx` / `RecentTab.wfx64` - selve pluginet (TC vælger
  automatisk den rigtige, hvis begge findes)
- `RecentTab_example.ini` - en referencekopi af alle tilgængelige
  indstillinger, med vilje leveret under dette navn (ikke
  `RecentTab.ini`): på den måde overskriver udpakning af en opdatering
  aldrig din egen konfiguration. Kopiér den, omdøb kopien til
  `RecentTab.ini`, og redigér kopien - pluginet leder efter netop det
  filnavn og fungerer fint med fornuftige standardværdier, også før
  filen overhovedet findes. Efter en opdatering: sammenlign denne fil
  med din egen `RecentTab.ini` for at finde nyheder.
- `RecentTab.lng` - brugerfladens tekster på alle medfølgende sprog;
  **skal blive i samme mappe som plugin-filen**, da den findes ud fra
  pluginets egen placering, ikke TC's konfigurationsmappe
- `icons\age.ico`, `icons\menu.ico` - standardikonerne for `! Search`
  og `! menu`, angivet via `[Icons]` - kan når som helst udskiftes med
  dine egne, se Egne ikoner ovenfor
- `RecentTab_state.json` - oprettes automatisk ved første brug, ikke en
  del af pakken; husker din optagelseshistorik
- `RecentTab_opened.txt` - oprettes kun automatisk ved `ShowOpened=1` og
  `OpenedTracking=permanent`; husker, hvilke filer du allerede har åbnet
  via panelet

## Installation

Dobbeltklik på `RecentTab.zip` inde i Total Commander - den genkender
installationsprogrammet indeni og tilbyder at installere automatisk,
med ét klik. (Kræver at Konfiguration → Indstillinger → Pakkere →
"Behandl arkiver som mapper" er slået til, hvilket er standard. Hvis
zip-filen allerede er åbnet i samme panel for at se indholdet, så
åbn/luk et andet arkiv én gang først, og dobbeltklik derefter igen.)

Alternativt: pak den ud, eller brug `release`-mappen fra en build fra
kildekode, og dobbeltklik på `pluginst.inf` - eller tilføj .wfx-filen
manuelt via Konfiguration → Indstillinger → Plugins →
Filsystem-plugins (WFX).

Kun første gang: kopiér `RecentTab_example.ini`, og omdøb kopien til
`RecentTab.ini` i samme mappe. Alt fungerer med almindelige
standardværdier også uden det trin - der er bare ikke noget at redigere
endnu, hvis du vil ændre noget.

## Byg fra kildekode

Kræver MinGW-w64 (`build_debug.bat` forventer `C:\mingw64`, med en
valgfri `mingw32`-undermappe til en 32-bit build ved siden af 64-bit).
Efter en vellykket build samles en `release`-mappe og en klar-til-brug
`RecentTab.zip` automatisk ved siden af scriptet. Se `TESTING.md` for,
hvad der bør tjekkes bagefter.

## Licens

MIT - ligesom forfatterens andre Total Commander-plugins
([XYTags](https://github.com/Native2904), [DescriptEdit](https://github.com/Native2904)).

Forfatter: Björn ([Native2904](https://github.com/Native2904))
