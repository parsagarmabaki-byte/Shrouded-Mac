# Shrouded — macOS

Ett litet multiplayer-spel. En person är **värd** (kör servern), och alla
andra **ansluter** som spelare. Den här mappen är Mac-versionen.

> **Viktigt först:** den här versionen är byggd för **Apple Silicon-Mac**
> (M1, M2, M3, M4). På en äldre **Intel-Mac** startar programmen inte alls —
> då måste spelet byggas om. Är du osäker: klicka på  > Om den här dattorn
> och titta på raden "Chip". Står det "Apple" är du rätt.

---

## Vad finns i mappen?

```
Shrouded-macOS/
├── client                 ← det här kör alla spelare
├── server                 ← det här kör EN person (värden)
├── Start Client.command   ← dubbelklicka för att starta client
├── Start Server.command   ← dubbelklicka för att starta server
├── assets/                ← bilder, ljud och typsnitt (MÅSTE ligga kvar här)
└── README.md              ← den här filen
```

**Behåll alla filer tillsammans i samma mapp.** Spelet letar efter `assets/`
precis bredvid programmet. Flyttar du ut `client` eller `server` på egen hand,
eller tar bort `assets`, så fungerar det inte.

Du kan flytta eller kopiera **hela mappen** vart du vill (skrivbordet, ett
USB-minne, en annan dator), så länge innehållet hålls ihop.

---

## "Men det finns ju inga SDL- eller bibliotek-filer i mappen?"

Det stämmer, och det är **meningen**. Mac fungerar annorlunda än Windows här.

På Windows ligger spelets bibliotek i samma mapp som programmet. På Mac har
programmet istället den fasta adressen till biblioteken inbyggd i sig, och
hämtar dem från en bestämd plats på datorn (`/opt/homebrew`) — inte från
spelmappen.

Du installerar de biblioteken **en gång** med kommandot i Steg 2 nedan. Efter
det hittar spelet dem automatiskt, trots att de inte syns i mappen. Du behöver
alltså **inte** kopiera in några bibliotekfiler för hand.

---

## Installation (görs en gång)

Allt skrivs in i appen **Terminal**. Du hittar den via Spotlight: tryck
**Cmd + Mellanslag**, skriv `Terminal`, tryck Enter.

### Steg 1 — Installera Homebrew

Homebrew är en "pakethanterare" som hämtar och installerar program åt dig.
Har du redan Homebrew kan du hoppa över det här steget. Annars, klistra in
i Terminal och tryck Enter:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Följ anvisningarna på skärmen (du kan behöva skriva ditt lösenord — det syns
inte när du skriver, det är normalt). Installationen kan be dig köra ett par
extra rader på slutet för att "lägga till Homebrew i PATH" — kopiera och kör
dem i så fall.

### Steg 2 — Installera spelets bibliotek (SDL)

Klistra in i Terminal och tryck Enter:

```bash
brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf
```

Det här hämtar SDL **och alla underliggande bibliotek** automatiskt (bilder,
ljud, typsnitt med mera). Det kan ta någon minut.

### Steg 3 — Tillåt programmen att köra

Eftersom programmen inte är signerade av en känd utvecklare blockerar macOS
(Gatekeeper) dem första gången. Det betyder **inte** att något är fel.

Enklast: öppna mappen i Terminal och kör de här två raderna. Byt först till
mappen genom att skriva `cd ` (med mellanslag efter) och sedan **dra
spelmappen från Finder in i Terminal-fönstret** och trycka Enter. Kör sedan:

```bash
xattr -dr com.apple.quarantine .
chmod +x client server "Start Client.command" "Start Server.command"
```

- Den första raden tar bort "nedladdad från internet"-spärren.
- Den andra ger programmen och startfilerna rätt att köras (ifall det tappades
  bort när mappen packades eller laddades ner).

Alternativ utan Terminal: **högerklicka** på `Start Server.command` →
**Öppna** → bekräfta **Öppna** i dialogen. Gör samma sak för
`Start Client.command`. Det räcker att göra det en gång per fil.

---

## Så här spelar ni

### Värden (en person)

1. Dubbelklicka på **Start Server.command**.
2. Ett Terminal-fönster öppnas och servern startar. Låt det vara öppet så
   länge ni spelar.
3. Ta reda på din **lokala IP-adress** så de andra kan ansluta. Kör i Terminal:
   ```bash
   ipconfig getifaddr en0
   ```
   (Får du inget svar, prova `en1` istället för `en0`.) Det blir något i stil
   med `192.168.x.x`. Skicka den adressen till spelarna.
4. För att spela över internet (inte bara samma nätverk) krävs att port **2000**
   är öppen/vidarebefordrad i routern — annars funkar det bara på samma Wi-Fi.

### Spelarna

1. Dubbelklicka på **Start Client.command**.
2. När programmet frågar, ange **värdens IP-adress** (den ni fick av värden).
3. Spela!

Alla — inklusive värden — använder **Start Client.command** för att själva
vara med i spelet. **Start Server.command** är bara värdens "spelmotor" som
ska köras i bakgrunden.

---

## Felsökning

**"dyld: Library not loaded: /opt/homebrew/opt/sdl2/..."**
SDL-biblioteken saknas. Kör Steg 2 igen:
`brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf`.

**"client kan inte öppnas eftersom utvecklaren inte kan verifieras"**
Gatekeeper blockerar. Gör Steg 3 (Terminal-raderna, eller högerklicka →
Öppna).

**"Start Server.command kan inte köras" / inget händer vid dubbelklick**
Körrättigheterna saknas. Kör `chmod`-raden i Steg 3.

**Programmet startar men det är svart/ingen grafik, eller det kraschar direkt**
`assets`-mappen saknas eller har flyttats. Den måste ligga kvar i samma mapp
som `client` och `server`.

**Programmen startar inte alls / "Bad CPU type in executable"**
Du har troligen en Intel-Mac. Den här versionen är byggd för Apple Silicon och
måste byggas om för Intel.

---

## Sammanfattning

1. Installera Homebrew (Steg 1) — bara om du inte redan har det.
2. `brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf` (Steg 2).
3. Ta bort spärren och ge körrättigheter (Steg 3).
4. Värden kör **Start Server.command**, alla kör **Start Client.command**.

Lycka till och ha kul!
