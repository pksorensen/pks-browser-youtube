# pks-browser-youtube

Browser-opskrifter til YouTube, kørt **lokalt** af [Agentics browser-tray](https://agentics.dk/products/pks-agent-browser)
på din egen maskine. Lyden og videoen hentes fra din egen internetforbindelse, og intet login forlader maskinen.

**[Åbn opskrifterne på agentics.dk →](https://agentics.dk/browser/recipes/pksorensen/pks-browser-youtube)** Siden læser dette repo
direkte og viser for hver opskrift, hvad trinene gør, og hvilke værter den rører.

## Hvorfor de findes

YouTube afviser hentning fra en server med *"Sign in to confirm you're not a bot"*. Det sker uanset
værktøj, fordi det er IP-adressen, der bliver afvist. Fra en almindelig hjemmeforbindelse går det
igennem. Trayen kører på den maskine, så den kan hente det, en server ikke kan.

## Opskrifter

| Fil | Hvad den gør | Kræver | Status |
| --- | --- | --- | --- |
| [`recipes/youtube-lyd.json`](recipes/youtube-lyd.json) | Henter lyden som m4a. Den kan transskriberes direkte. | `media`-verbet | **UDKAST: ikke kørt mod YouTube** |
| [`recipes/youtube-video.json`](recipes/youtube-video.json) | Henter videoen som mp4 (typisk 360p) | `media`-verbet | **UDKAST: ikke kørt mod YouTube** |
| [`recipes/youtube-udskrift.json`](recipes/youtube-udskrift.json) | Læser sidens eget "Vis udskrift"-panel: tidskode og tekst | kun profilen `youtube` (se Krav) | **UDKAST: selektorerne er ikke målt** |

Alle tre starter på youtube.com. Du åbner selv videoen i vinduet, og opskriften fortsætter af sig
selv, så snart du står på en videoside (`youtube.com/watch`). Derfor skal **Vis browser** være
slået til i trayens menu, ellers kan du ikke se vinduet. Du har 5 minutter til at finde videoen,
fordi trayen højst venter 10 minutter på hele kørslen, og hentningen skal også nå at blive færdig.

Filen lander i `%USERPROFILE%\.agentics\browser-tray\recipes\<opskrift>.media\`, for eksempel
`youtube-lyd.media\O_zcc6Xl12g.m4a`. Titel, kanal og varighed står i `<opskrift>.result.json`.

## Krav

```powershell
irm https://agentics.dk/install/pks-agent-browser-tray.ps1 | iex
```

- En tray-version med `media`-verbet. Den er endnu **ikke udgivet**.
- En profil der hedder `youtube`. Den følger med som standard fra tray-versionen med `media`.
  På en ældre tray skal du selv tilføje den i `settings.json`, også til `youtube-udskrift`:
  `"profiles": { "youtube": ["youtube.com", "youtu.be"] }` (de andre profiler bevares).
- **Node 22 eller nyere**, hvis trayen kører den slanke motor på din egen Node. yt-dlp kan ikke
  løse YouTubes JS-udfordring med ældre Node. Den fulde motor har sin egen Node 24 med.

yt-dlp følger med motoren, låst til én version og checksum-tjekket. YouTube ændrer sig oftere,
end trayen udkommer. Knækker hentningen, så installér en nyere yt-dlp (`winget install yt-dlp`)
og peg trayen på den i `settings.json`:

```json
{ "ytDlpPath": "C:\\Users\\<dig>\\AppData\\Local\\Microsoft\\WinGet\\Links\\yt-dlp.exe" }
```

## Skal jeg logge ind?

Som regel ikke. Fra en hjemmeforbindelse hentes almindelige videoer uden login. Login er kun
nødvendigt til aldersbegrænsede videoer og videoer kun for medlemmer. Så log ind i profilen via
menuen (*Log ind i profil 'youtube'…*), og sæt `"cookies": true` på `media`-trinnet.

**Brug en Google-konto, du kan undvære.** En konto, der bruges til automatiseret hentning, kan
blive markeret af Google. Cookies sendes kun til YouTubes egne værter. De skrives til en midlertidig
fil, som slettes i samme trin.

## Kør fra kommandolinjen

```powershell
$tray = "$env:LOCALAPPDATA\Agentics\bin\pks-agent-browser-tray.exe"
& $tray recipes
& $tray run --name youtube-lyd *> run.log; cat run.log
```

Trayen er bygget som `WinExe` og har ingen konsol. Uden `*> fil` forsvinder både resultatet og fejlen.

## Brug indholdet ordentligt

Opskrifterne henter til din egen maskine, til dit eget brug: at transskribere, citere og
opsummere. Bliver indholdet delt videre, så krediter skaberen og link til videoen.
