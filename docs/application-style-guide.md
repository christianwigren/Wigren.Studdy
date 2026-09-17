# Styleguide: Wigren.Studdy

**Status:** Första visuella riktning
**Målgrupp:** Elever från ungefär 13 år samt lärare och vårdnadshavare
**Produkt:** Webbaserad digital lärare och övningsledare
**Visuell idé:** *Quiet energy*: varm, fokuserad och mänsklig med tillräckligt mycket färg och rörelse för att kännas levande.

## 1. Designmål

Applikationen ska kännas:

- **Lekfull utan att vara barnslig:** energi genom färgblock, små överraskningar, tydliga framsteg och taktila kort, inte genom figurer eller skolclipart.
- **Vuxen utan att vara torr:** mogen typografi, lugna ytor och redaktionell struktur, men med personliga accenter och ett tydligt visuellt svar på elevens handlingar.
- **Trygg utan att vara steril:** fel ska kännas som information och nästa steg, inte som misslyckande.
- **Intelligent utan att vara teknisk:** systemets analys ska presenteras som begriplig vägledning, inte som dashboardspråk eller AI-magi.
- **Koncentrerad utan att vara trång:** en tydlig huvudsak per vy, med möjlighet att fördjupa sig när det behövs.

## 2. Antaganden

Styleguiden utgår från följande:

- Eleven använder applikationen i korta pass på 10–25 minuter.
- Samma produkt ska fungera för Nils, Knut, läraren och en vuxen som hjälper till.
- Innehållet kan vara tungt: matematik, språk, naturvetenskap, historia och längre resonemang.
- Mobil och surfplatta är lika viktiga som dator.
- Svenska är första gränssnittsspråket.
- Tillgänglighet är ett grundkrav. Färg, animation, motorik eller ljud får inte vara enda bärare av information.
- Den visuella identiteten ska fungera utan maskot, könskodning eller ålderskodad illustration.

## 3. Visuellt koncept

### 3.1 Namn på riktningen

**Study Atelier**

Applikationen ska kännas som en välordnad arbetsyta mellan ett bibliotek, en verkstad och en bra anteckningsbok. Den ska inte imitera ett klassrum och inte se ut som ett barnspel.

### 3.2 Formprinciper

- Varm basfärg i stället för kliniskt vitt.
- Djupa, lätt dämpade färger i stället för neon.
- Runda hörn med måtta: 12 px på kort, 8 px på kontroller, aldrig allt som en bubbla.
- Tydliga kanter och tunna linjer som ger struktur.
- Accentformer får vara asymmetriska eller handritade i begränsade doser.
- Innehållskort ska kännas som objekt med vikt, inte som färgade rektanglar staplade utan hierarki.
- Använd skuggor sparsamt. Separation ska primärt komma från ytfärg, linje och typografi.

### 3.3 Lekfullhetens gräns

Använd gärna:

- små markeringar som understrykningar, flikar och marginalnoter,
- progressionslinjer och diskreta konfettipartiklar vid milstolpar,
- lätt varierade accentformer i illustrationer och ämneskort,
- korta, mänskliga mikrotexter,
- animationer som visar orsak och verkan.

Undvik:

- tecknade maskotar som talar för produkten,
- stora emoji-kluster,
- regnbågsfärgade dashboards,
- stjärnor och troféer som primär feedback,
- spelvalutor, loot-lådor och blinkande belöningar,
- överdriven studsande eller skakande feedback,
- färgkodning som liknar lågstadiets läromedel.

## 4. Färgpalett

### 4.1 Grundfärger

| Token | Hex | Användning |
|---|---|---|
| `ink-950` | `#17232B` | Huvudtext, mörka rubriker, primär kontrast |
| `ink-700` | `#465761` | Sekundär text och ikoner |
| `ink-500` | `#74828A` | Hjälptext, metadata, inaktiv information |
| `paper-50` | `#FCFBF7` | Huvudbakgrund |
| `paper-100` | `#F3F0E8` | Sektioner, sidopaneler och vilande ytor |
| `paper-200` | `#E7E3D9` | Subtila gränser och avdelare |
| `white` | `#FFFFFF` | Primära kort och fält när extra kontrast behövs |
| `teal-700` | `#245B61` | Primär färg och huvudhandlingar |
| `teal-800` | `#19464B` | Hover, aktivt läge och mörk primär yta |
| `teal-100` | `#DDEDEC` | Primär färgton, information och lugna markeringar |
| `coral-500` | `#E4775C` | Varm accent, viktiga men icke-farliga markeringar |
| `coral-100` | `#F9E3DC` | Varm bakgrund och uppmuntrande feedback |
| `ochre-500` | `#C79235` | Framsteg, uppmärksamhet och sekundär accent |
| `ochre-100` | `#F6EBCF` | Uppmärksamhetsbakgrund |

### 4.2 Semantiska färger

Semantiska färger ska alltid kombineras med text, ikon eller form.

| Token | Hex | Användning |
|---|---|---|
| `success-700` | `#2D7355` | Korrekt, klart, sparat, godkänt |
| `success-100` | `#DFF0E5` | Bakgrund för positiv status |
| `warning-700` | `#92621C` | Behöver uppmärksamhet eller lärarstöd |
| `warning-100` | `#F8EBCB` | Bakgrund för uppmärksamhet |
| `danger-700` | `#A84646` | Fel, blockerat eller risk |
| `danger-100` | `#F7DEDC` | Bakgrund för felstatus |
| `info-700` | `#376D91` | Neutral vägledning och källinformation |
| `info-100` | `#DCEAF3` | Bakgrund för information |
| `focus-700` | `#245EB5` | Fokusindikator och tangentbordsnavigering |

### 4.3 Profilaccenter

Profilfärger ska hjälpa användaren att se aktiv profil men får inte antyda personlighet, kön eller förmåga.

| Profil | Accent | Ton |
|---|---|---|
| Nils | `#B66A43` | Terrakotta |
| Knut | `#47737B` | Blågrön |

Använd profilaccenten i avatar-ring, aktiv profilflik och små metadataelement. Använd aldrig profiltone som bakgrund för hela applikationen och låt inte profilen kommuniceras enbart genom färg.

### 4.4 Färgregler

- Text på `paper-50`, `paper-100` och vita ytor ska använda `ink-950` eller `ink-700`.
- Primär knapp använder `teal-700` med vit text.
- `coral-500` och `ochre-500` ska normalt inte bära lång text; använd dem som accent eller med mörk text på ljus ton.
- Röd/grön får aldrig vara enda skillnaden mellan fel och rätt.
- Diagram ska använda högst fem kategorifärger samtidigt och komplettera färg med etikett, mönster eller position.
- Undvik stora färgfält med semantisk färg. Status ska ligga nära det som statusen gäller.
- Kontrast ska verifieras mot WCAG 2.2 AA innan komponenten godkänns.

## 5. Typografi

### 5.1 Typsnitt

Primärt gränssnittstypsnitt:

```text
Manrope, ui-sans-serif, system-ui, sans-serif
```

Manrope ger en mjukare och mer personlig form än ett neutralt systemtypsnitt men är tillräckligt vuxet för lärarvyer och tabeller.

För längre reflekterande text, läraranteckningar och källutdrag kan följande användas sparsamt:

```text
Source Serif 4, Georgia, serif
```

Seriftypsnittet ska vara ett innehållsverktyg, inte dekorativ rubrikfont. Blanda inte fler än två familjer i samma vy.

### 5.2 Typografisk skala

| Roll | Storlek | Vikt | Radavstånd |
|---|---:|---:|---:|
| Display | 40 px | 700 | 1.08 |
| H1 | 32 px | 700 | 1.12 |
| H2 | 24 px | 700 | 1.2 |
| H3 | 18 px | 700 | 1.3 |
| Brödtext | 16 px | 400 | 1.55 |
| Kompakt brödtext | 14 px | 400 | 1.45 |
| Etikett | 12 px | 700 | 1.3 |
| Metadata | 12 px | 600 | 1.3 |

På små skärmar ska Display minska till 32 px och H1 till 28 px. Brödtext ska aldrig gå under 16 px i primär elevtext.

### 5.3 Typografiska regler

- Rubriker ska beskriva handling eller mål, inte bara systemstatus.
- Använd meningsform i rubriker: `Träna syror och baser`, inte `SYROR OCH BASER`.
- Fetstil ska markera nyckelord, inte hela meningar.
- Långa källutdrag ska ha generösare radavstånd och tydlig källa.
- Använd tabular numerals i statistik och progression.
- Håll textbredden för längre läsning mellan 55 och 75 tecken.
- Matematiska uttryck och kemiska formler ska använda en konsekvent formelrenderare, inte vanlig brödtext när precision påverkas.

## 6. Layout och rytm

### 6.1 Grid

- Desktop: 12-kolumners grid med maximal innehållsbredd 1280 px.
- Tablet: 8-kolumners grid.
- Mobil: 4-kolumners grid med 16 px sidmarginal.
- Primärt övningsinnehåll ska normalt vara 6–8 kolumner på desktop, med kontext eller progression i sidokolumn.
- Lärarvyer får vara tätare än elevvyer, men ska behålla samma färg- och typografisystem.

### 6.2 Avstånd

Basenhet: 4 px.

| Token | Värde | Användning |
|---|---:|---|
| `space-1` | 4 px | Ikon och text, små interna avstånd |
| `space-2` | 8 px | Kontrollinnehåll och metadata |
| `space-3` | 12 px | Kompakta grupper |
| `space-4` | 16 px | Standardpadding och mobilmarginal |
| `space-5` | 20 px | Fält och kortsektioner |
| `space-6` | 24 px | Kortpadding och gruppavstånd |
| `space-8` | 32 px | Sektioner |
| `space-12` | 48 px | Större layoutavbrott |
| `space-16` | 64 px | Hero eller större sidsektion |

Använd luft för att uttrycka prioritet. En primär övning ska ha mer visuell luft än navigering och sekundär metadata.

### 6.3 Elevvy och lärarvy

Elevvyn ska kännas som en fokuserad arbetsyta:

- en primär uppgift per vy,
- synlig men diskret progressionskontext,
- nästa steg nära feedbacken,
- minimalt med tabeller och systemmetadata.

Lärarvyn ska kännas som en redaktionell kontrollpanel:

- källor, versioner och valideringsfynd ska vara lätta att jämföra,
- täta tabeller får användas,
- risker och osäkerhet ska framhävas tydligt,
- inga färgglada dashboards utan konkret beslut eller åtgärd.

## 7. Komponentstil

### 7.1 Kort

- Bakgrund: `white` eller `paper-50`.
- Border: `paper-200`, 1 px.
- Radie: 12 px.
- Padding: 24 px desktop, 16 px mobil.
- Skugga: endast vid lyft från bakgrunden, exempelvis dialog eller aktiv dragbar yta.
- Primära övningskort kan ha en 4 px accentlinje i `teal-700` eller ämnesaccent.

Kort ska inte staplas i tre eller fler nivåer. Om allt ligger i kort är inget längre primärt.

### 7.2 Knappar

**Primär:** djup teal, vit text, tydlig handling.

**Sekundär:** transparent eller pappersfärgad med teal-border.

**Tertiär:** textknapp för lågprioriterad handling.

**Destruktiv:** använd `danger-700` endast för faktisk destruktivitet, aldrig för ett vanligt felaktigt elevsvar.

Alla knappar ska ha:

- minst 44 × 44 px klickyta,
- synligt hover- och pressed-läge,
- tydligt fokusläge,
- text som beskriver handlingen: `Visa ledtråd`, `Försök igen`, `Godkänn övning`.

Undvik `Fortsätt` när nästa handling kan beskrivas mer konkret.

### 7.3 Formulär och svarsfält

- Fält ska ha etikett före interaktion, inte bara placeholder.
- Svarsfält för elevens text ska kännas som arbetsyta: ljus yta, tydlig kant, generös padding.
- Felmeddelande ska ligga nära fältet och beskriva nästa handling.
- Validera inte aggressivt medan eleven skriver om felet inte behöver upptäckas direkt.
- Visa sparstatus diskret nära innehållet, aldrig som blockerande modal.

### 7.4 Progression

Progression ska visa riktning, inte rangordning.

Använd:

- horisontella eller vertikala banor med små milstolpar,
- tydliga kunskapsmål,
- diskreta markörer för `påbörjad`, `behöver återbesök`, `stabil`, `lärargranskning`,
- text som förklarar varför nästa uppgift valdes.

Undvik:

- procent som ser exakt ut när bedömningen är osäker,
- ledartavlor,
- nivåer som antyder personlig intelligens,
- stora låsta kartor som gör kunskap till ett spel istället för en aktivitet.

### 7.5 Feedback

Feedback ska ha en tydlig tonal och visuell ordning:

1. **Mål:** neutral teal-ton, påminner om vad uppgiften tränar.
2. **Ledtråd:** blågrön eller ockra, pekar mot nästa steg.
3. **Korrekt:** grön ton plus text och ikon.
4. **Behöver justeras:** varm ockra eller korall, utan skammande språk.
5. **Lärargranskning:** lila får användas som sällsynt neutral signal för mänsklig bedömning, annars info-ton.

Feedbackytan ska animeras med en kort fade eller höjdjustering, inte skaka hela sidan eller spela ljud som standard.

### 7.6 Dialoger och modaler

Använd modal endast när användaren måste fatta ett beslut eller bekräfta en risk. Förklaringar och feedback ska normalt expandera i flödet.

Profilbyte, radering, publicering och läraröverskrivning kräver tydlig bekräftelse. En modal ska visa konsekvens, inte bara `Är du säker?`.

## 8. Ikoner och illustrationer

- Ikoner ska vara linjära, enkla och ha samma strokevikt.
- Rekommenderad ikonstorlek: 20 px i kontroller, 24 px i navigering, 32 px i tomtillstånd.
- Ikonen kompletterar text och ersätter den inte i kärnflöden.
- Illustrationer ska vara geometriska, redaktionella och ämnesnära: diagram, verktyg, former, pappersfragment och abstrakta processer.
- Använd inte ansikten och figurer som standardiserade elevavatarer.
- Illustrationer får använda coral, ochre och teal men ska alltid fungera i monokrom eller med reducerad färg.

## 9. Ämnesidentitet

Ämnen kan ha en accentfärg, men hela applikationen ska fortfarande kännas som samma produkt.

| Ämnesgrupp | Accent |
|---|---|
| Språk och litteratur | `#8B607D` dämpad plommon |
| Matematik och teknik | `#3E7180` blågrön |
| Naturvetenskap | `#4E806B` salvia |
| Historia och samhälle | `#B07845` brons |
| Bild och musik | `#B76759` tegelkorall |
| Idrott och hälsa | `#66804B` olivgrön |

Ämnesfärg ska användas i små ytor: etikett, ikon, linje eller diagramserie. Den får inte ersätta ämnesnamnet.

## 10. Rörelse och ljud

### Rörelseprinciper

- Rörelse ska förklara förändring, återkoppling eller fokus.
- Standardduration: 120–220 ms för små övergångar, 240–400 ms för paneler och feedback.
- Undvik oändliga animationer.
- Respektera `prefers-reduced-motion` och erbjud samma information utan rörelse.
- Vid rätt svar får en liten markering eller progressionsförändring ske; hela skärmen ska inte fira.

### Ljud

- Ljud är avstängt som standard.
- Ljud får användas för uttal, språkövningar och explicit elevvald feedback.
- Ljud får inte vara enda signal för korrekthet, fel eller viktiga systemmeddelanden.
- Ljuddesign ska vara kort, varm och diskret, aldrig arkadlik.

## 11. Tonalitet i gränssnittet

### Språk

Skriv direkt, lugnt och respektfullt. Använd `du`, konkreta verb och korta meningar.

Bra:

- `Du har rätt princip. Kontrollera enheten i sista steget.`
- `Försök igen med en ledtråd.`
- `Det här målet behöver ett återbesök senare.`
- `Läraren behöver titta på det här svaret.`

Undvik:

- `Fel!`
- `Fantastiskt!!!`
- `Du är ett geni!`
- `AI:n tror att...`
- `Du måste klara detta för att gå vidare.`

### Feedbackton

Feedback ska separera personen från svaret. Beskriv handling, evidens och nästa steg. Använd inte språk som antyder att resultatet är elevens identitet eller intelligens.

## 12. Tillgänglighet

- WCAG 2.2 AA är miniminivå för kontrast, fokus, tangentbord och formulär.
- All funktion ska vara möjlig med tangentbord.
- Fokusindikator ska vara minst 2 px och använda `focus-700` med tydlig kontrast.
- Färg får aldrig vara enda bärare av rätt/fel, profil, progressionsstatus eller ämne.
- Alla grafiska övningar ska ha alternativ med text, tangentbord eller strukturerade objekt.
- Text ska kunna förstoras utan att innehåll eller funktion går förlorad.
- Fel, feedback och sparstatus ska exponeras semantiskt för hjälpmedel.
- Respektera reducerad rörelse, hög kontrast och användarens textstorlek.
- Testa på mobil, tangentbord, skärmläsare och med färgseendedefekter.

## 13. Teman

### Ljust tema: standard

Ljust tema använder `paper-50` som huvudbakgrund, `white` för primära ytor och `ink-950` för text. Det är applikationens varma, redaktionella grund.

### Mörkt tema: senare men förberett

Mörkt tema ska inte vara en enkel inversion. Det ska behålla låg färgmättnad och använda:

| Token | Hex |
|---|---|
| `dark-bg` | `#121A1E` |
| `dark-surface` | `#1B272C` |
| `dark-raised` | `#24343A` |
| `dark-text` | `#F4F1E8` |
| `dark-muted` | `#A7B3B3` |
| `dark-primary` | `#7EC3BF` |
| `dark-accent` | `#F09A7D` |

Mörkt tema ska testas separat för kontrast, diagram och feedbackstatus. Bygg inte ett mörkt tema genom att blanda in ren svart eller neonfärger.

## 14. Design tokens som implementation

Tokens ska vara semantiska och kunna bytas per tema. Komponenter ska använda semantiska tokens, inte hexkoder direkt.

Exempel:

```css
:root {
  --color-bg: #fcfbf7;
  --color-surface: #ffffff;
  --color-text: #17232b;
  --color-text-muted: #74828a;
  --color-border: #e7e3d9;
  --color-primary: #245b61;
  --color-primary-hover: #19464b;
  --color-accent: #e4775c;
  --color-focus: #245eb5;
  --radius-control: 8px;
  --radius-card: 12px;
  --shadow-raised: 0 8px 24px rgb(23 35 43 / 10%);
}
```

Komponenter ska aldrig använda `#E4775C` direkt. De ska använda `--color-accent` eller en mer specifik semantisk token.

## 15. Kvalitetsgrindar

En ny vy eller komponent är inte klar innan den klarar:

- tydlig primär handling inom tre sekunder,
- fungerande mobilvy utan horisontell scroll,
- tangentbordsnavigering och synligt fokus,
- kontrasttest i ljust tema,
- rätt/fel-information utan färgberoende,
- reducerad rörelse,
- tomtillstånd, laddning, fel, sparning och offline/återförsök,
- elev- och lärartext som är konkret och respektfull,
- visuell konsekvens med denna palette, typografi och radiesystem.

## 16. Exempel på önskad känsla

En elev öppnar en övning och ser en varm, nästan pappersfärgad arbetsyta. En djup teal-rubrik anger målet. Frågan ligger i ett ljust kort med gott om luft. När eleven svarar fel blir inte hela kortet rött; en smal korallmarkering och texten `Pröva en annan väg` öppnar en konkret ledtråd. När eleven lyckas flyttar sig en liten progressionsmarkör framåt och visar vad som faktiskt blev stabilt.

En lärare öppnar samma innehåll och får en tätare granskningsvy med källutdrag, versionsetiketter och tydliga riskmarkeringar. Den känns som ett redaktionellt arbetsverktyg, inte som elevvyn med fler färger.

Det är balansen som ska bevaras: **varm nog för att bjuda in, stadig nog för att tas på allvar.**
