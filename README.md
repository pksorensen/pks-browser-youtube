# pks-browser-youtube

Browser-opskrifter til YouTube, kørt **lokalt** af [Agentics browser-tray](https://agentics.dk/products/pks-agent-browser)
på din egen maskine. Videoen optages i din egen browser over din egen internetforbindelse, og intet login forlader maskinen.

**[Åbn opskrifterne på agentics.dk →](https://agentics.dk/browser/recipes/pksorensen/pks-browser-youtube)** Siden læser dette repo
direkte og viser for hver opskrift, hvad trinene gør, og hvilke værter den rører.

## Hvorfor de findes

YouTube afviser hentning fra en server med *"Sign in to confirm you're not a bot"*. Det er
IP-adressen, der bliver afvist, ikke værktøjet. Fra en almindelig hjemmeforbindelse går det
igennem. Trayen kører på den maskine.

## Opskrifter

| Fil | Hvad den gør | Status |
| --- | --- | --- |
| [`recipes/youtube-lyd.json`](recipes/youtube-lyd.json) | Optager lydsporet, til at transskribere selv | **UDKAST: ikke kørt mod YouTube** |
| [`recipes/youtube-video.json`](recipes/youtube-video.json) | Optager hele videoen: lyd og billede som to filer | **UDKAST: ikke kørt mod YouTube** |

Begge tager videoens adresse som parameter og kører uden at du skal være der.

## Sådan virker det

Der er intet hjælpeprogram som yt-dlp. Trayens `media`-trin åbner videoen i en ny fane i
profilen og lader YouTubes egen afspiller spille den **lydløst på 16×**. Undervejs optager
den de bytes, afspilleren giver browserens mediemotor. Det er det originale spor uden
omkodning. YouTubes egen afspiller klarer alt det, der ellers knækker værktøjer udefra.

- **En time tager omkring fire minutter**, hvis forbindelsen kan følge med.
- **Billedkvaliteten** bliver den, afspilleren kan følge med i ved 16×. Det er ofte lav.
  Lyden til en transskription klarer sig fint.
- **Lyd og billede er to filer**, fordi browseren får dem hver for sig. Med ffmpeg fletter
  du dem uden omkodning: `ffmpeg -i X.audio.webm -i X.video.webm -c copy X.mkv`
- Skifter afspilleren kvalitet undervejs, bliver sporet til flere dele (`X.video.1.webm`,
  `X.video.2.webm`), fordi delene ikke kan sættes sammen til én læsbar fil.

Filerne lander i `%USERPROFILE%\.agentics\browser-tray\recipes\<opskrift>.media\`, for
eksempel `youtube-lyd.media\O_zcc6Xl12g.audio.webm`. Titel, varighed og hvor lang tid det
tog står i `<opskrift>.result.json`.

## Kør

**Fra agentics.dk:** åbn [opskriftssiden](https://agentics.dk/browser/recipes/pksorensen/pks-browser-youtube),
indsæt videoens adresse ved opskriften og tryk *Kør nu*. Trayen viser en kvittering med
adressen, før noget kører.

**Fra kommandolinjen:**

```powershell
$tray = "$env:LOCALAPPDATA\Agentics\bin\pks-agent-browser-tray.exe"
& $tray run --name youtube-lyd --param "url=https://www.youtube.com/watch?v=O_zcc6Xl12g" *> run.log; cat run.log
```

Sæt anførselstegn om `url=…`: PowerShell læser `&` i en indsat adresse som en kommando.
Menuen i trayen kan ikke give en adresse med. Brug siden eller kommandolinjen.

Fejler en kørsel, står det optagelsen så i fejlbeskeden: hvor langt den nåede, den fart
afspilleren faktisk kørte, og hvilke spor der kom. Et billede af fanen ligger i
`<opskrift>.fejl.skaerm.png`. Send begge, så kan opskriften rettes.

Trayen er bygget som `WinExe` og har ingen konsol. Uden `*> fil` forsvinder både resultatet
og fejlen.

## Krav

```powershell
irm https://agentics.dk/install/pks-agent-browser-tray.ps1 | iex
```

- Tray-version **0.1.23** eller nyere. Den har `media`-verbet og parametre, springer
  reklamer over og afviser cookies på YouTubes samtykkeside første gang. Med 0.1.22 skal
  samtykkesiden besvares i hånden i profilen, og en reklame spilles færdig.
- Profilen `youtube` følger med opskriften. Første gang spørger kvitteringen, om den må
  oprettes med adgang til youtube.com og youtu.be.

## Få filen et andet sted hen

Er du logget ind på agentics.dk, kan du på opskriftens side vælge en af dine Agent
Share-indbakker under *Send resultatet til*. Når optagelsen er færdig, sender trayen
sporene og resultatet dertil. Trayen skal selv være logget ind (menuen), og den sender kun
til en indbakke du ejer. Vælger du ikke noget, bliver filerne på maskinen.

## Skal jeg logge ind?

Som regel ikke. Login er kun nødvendigt til aldersbegrænsede videoer og videoer kun for
medlemmer. Så log ind i profilen via menuen. Optagelsen bruger profilens login, fordi det er
den samme browser.

**Brug en Google-konto, du kan undvære.** En konto, der bruges til automatiseret
afspilning, kan blive markeret af Google. Hver optagelse står også i kontoens historik.

## Virker ikke på

- DRM-beskyttet indhold, fx købte film. Browseren låser det selv.
- Live-udsendelser, der ikke er slut.

## Brug indholdet ordentligt

Opskrifterne optager til din egen maskine, til dit eget brug: at transskribere, citere og
opsummere. Bliver indholdet delt videre, så krediter skaberen og link til videoen.
