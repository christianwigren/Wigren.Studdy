# Wigren.Studdy: huvudspecifikation

**Status:** Övergripande produktgrund
**Produkt:** Webbaserad digital lärare och övningsledare
**Första målgrupp:** Elever i grundskolan, med start i årskurs 8
**Profiler i version 1:** Nils och Knut

Det här dokumentet är den lättlästa kartan över hela produkten. Det beskriver varför applikationen finns, hur den ska hjälpa en elev att lära sig och hur de mer detaljerade specifikationerna hänger ihop.

## 1. Vad är Wigren.Studdy?

Wigren.Studdy ska vara en webbaserad digital lärare och övningsledare. Den ska hjälpa en elev att förstå ett mål, försöka själv, få användbar återkoppling, reparera sitt svar och återkomma till kunskapen senare.

Applikationen ska inte bara visa frågor. Den ska bygga en lärloop:

1. Eleven får ett tydligt kunskapsmål.
2. Eleven gör ett eget försök.
3. Systemet analyserar svaret och visar en lagom stor ledtråd eller förklaring.
4. Eleven får göra om, förklara eller använda kunskapen i ett nytt exempel.
5. Systemet sparar vad eleven faktiskt visade.
6. Eleven får återkomma efter ett mellanrum.
7. Läraren kan granska material, svar och automatiska bedömningar.

Den viktigaste framgången är inte flest klick, längst session eller flest poäng. Den är att eleven kan lösa en ny och fördröjd uppgift självständigt.

## 2. Varför finns produkten?

Elever behöver olika mycket repetition, olika typer av stöd och olika lång tid. En lärare behöver samtidigt kunna se:

- vad eleven tränar,
- vilket mål en uppgift hör till,
- vilken typ av fel eleven gör,
- om återkopplingen används,
- om eleven kan överföra kunskapen till ett nytt exempel,
- när en människa behöver granska eller ingripa.

Wigren.Studdy ska göra den loopen möjlig utan att låtsas att automatisk rättning alltid är säker. LLM:er får skapa förslag, tolka språk och hjälpa till med analys, men källor, validering, deterministisk rättning och lärarens omdöme är fortfarande centrala.

## 3. Produktens delar

### 3.1 Elevens webbapp

Eleven väljer profil och möter en fokuserad övningsyta. Den ska stödja bland annat:

- flervalsfrågor,
- kortsvar och fri återkallning,
- problemlösning och förklaringar,
- grafiska uppgifter, matchning och ordning,
- ritning, papper och alternativa tillgängliga svarsvägar,
- worked examples och stegvis borttaget stöd,
- simuleringar och strukturerade samarbetsuppgifter.

Alla centrala kunskapsmål ska kunna tränas med text och tangentbord. Se [interaktionsspecifikationen](digital-teacher-interaction-spec.md).

### 3.2 Lärarens arbetsyta

Läraren ska kunna:

- ladda upp skolplaner, kursplaner, dokument och eget material,
- se vilka kunskapsmål och källor systemet har tolkat,
- granska och redigera genererade övningar,
- godkänna, avvisa eller stoppa innehåll,
- granska elevsvar och automatiska bedömningar,
- se felmönster och behov av extra undervisning,
- följa vilken version av källa, prompt, modell och rubric som använts.

Läraren ska inte behöva lita på en sammanfattande AI-poäng. Underlaget för ett beslut ska gå att öppna.

### 3.3 Övningsmotorn

Övningsmotorn tar lärarens material och skapar validerade kandidater. Den använder elevens tidigare resultat för att avgöra om nästa aktivitet ska vara:

- repetition,
- remediation av en missuppfattning,
- transfer till ett nytt exempel,
- en enklare eller svårare variant,
- en annan interaktionsform,
- eller ingen ny uppgift ännu.

Övningsmotorn beskrivs i [specifikationen för LLM-driven övningsmotor](exercise-engine-spec.md). Forskningsbesluten finns i [forskningsunderlaget för övningsmotorn](exercise-engine-research.md).

### 3.4 Profiler och användare

Version 1 har två separata elevprofiler:

- `nils` / Nils
- `knut` / Knut

Progression, elevsvar, repetitionskö, personliga inställningar och sparade studieprodukter ska hållas separata. Roller, åtkomst, lagring, export och radering hanteras i [specifikationen för profiler och användare](profiles-and-users-spec.md).

### 3.5 Visuellt gränssnitt

Produkten ska ha en balans mellan lekfull och vuxen. Den ska vara varm, uttrycksfull och lätt att använda utan att kännas som ett barnspel eller ett torrt administrationssystem.

Det visuella språket, färgtokens, typografi, komponenter, tonalitet och tillgänglighet finns i [styleguiden](application-style-guide.md).

## 4. Den pedagogiska kedjan

Produktens viktigaste kedja ser ut så här:

```text
Skolplan och lärarmaterial
        ↓
Godkända kunskapsmål och källor
        ↓
Övningskandidat med mental handling och bedömningskriterier
        ↓
Validering och lärargodkännande
        ↓
Elevens försök
        ↓
Rättning, feedback och reparation
        ↓
Fördröjd återkallning och transfer
        ↓
Analys av nästa behov
        ↓
Ny övning, återbesök, lärarstöd eller avslut
```

### 4.1 Från skolplan till mål

Kursplanen är inte en färdig frågelista. Systemet ska först identifiera ett konkret mål:

- vad eleven ska kunna göra,
- vilken mental handling som krävs,
- vilka förkunskaper som behövs,
- vilka fel och missuppfattningar som är vanliga,
- vilken evidens som räknas som ett bra svar,
- hur samma princip kan testas i ett nytt exempel.

För årskurs 8 används materialet under intervallet 7–9 i Lgr22. Det finns normalt inte ett separat nationellt målpaket för just årskurs 8. Se [årskurs 8-resursernas översikt](../resources/knut/8th-grade/README.md).

### 4.2 Från mål till övning

En övning ska inte genereras bara för att fylla en lista. Den ska ha ett tydligt syfte och en svarstyp som passar den mentala handlingen. En flervalsfråga kan vara bra för snabb diagnos, men fri återkallning, förklaring och transfer behövs när självständig användning ska visas.

Varje elevsynlig aktivitet ska därför ha:

- ett versionerat kunskapsmål,
- källhänvisning eller lärarverifiering,
- vald interaktionstyp,
- facit eller rubric,
- feedback och eventuell reparationsuppgift,
- tillgänglig alternativ väg,
- svårighetsnivå och förkunskapskrav.

### 4.3 Från svar till nästa steg

Systemet ska separera tre frågor:

1. **Kan eleven detta?** `masteryNeed`
2. **Är det dags att återkomma?** `spacingNeed`
3. **Kan eleven använda det i en ny situation?** `transferNeed`

Elevens preferens för exempelvis ritning, text eller en viss interaktionsform får hjälpa till att välja mellan pedagogiskt likvärdiga alternativ. Den får inte behandlas som en fast learning style eller användas för att alltid ge den enklaste och roligaste uppgiften.

## 5. Övergripande arkitektur

Applikationen ska byggas som en webbapplikation med tydlig separering mellan klient, API, domänlogik, lagring och modellgateway.

```text
Webbklient
  ├─ profil och elevvy
  ├─ lärarvy
  └─ feedback och sparstatus
        ↓
API och behörighetskontroll
        ↓
Domäntjänster
  ├─ innehåll och kunskapsmål
  ├─ övningsmotor
  ├─ rättning och analys
  ├─ adaptivt urval
  └─ publicering och lärargranskning
        ↓
Lagring och provenance
        ↓
Model gateway
  ├─ lokal behandling på RTX 3090
  ├─ OpenAI
  ├─ Claude
  ├─ Mistral
  └─ Gemini
```

RTX 3090 och externa API-nycklar är tekniska möjligheter, inte en garanti för kvalitet eller hastighet. Modellroutning ska bestämmas med benchmark för kvalitet, säkerhet, kostnad och latency.

## 6. LLM:ns roll

LLM kan användas för:

- att extrahera förslag på kunskapsmål ur lärarmaterial,
- att formulera övningskandidater,
- att skapa rimliga distraktorer utifrån dokumenterade missuppfattningar,
- att generera ledtrådar och elevnära förklaringar,
- att analysera öppna svar mot en rubric,
- att klassificera feltyper och föreslå nästa pedagogiska steg,
- att skapa nya varianter med ändrad yta men samma princip,
- att sammanfatta elevmönster för läraren.

LLM ska inte ensam:

- avgöra ett versionslöst kunskapsmål,
- publicera en övning till elever,
- rätta entydig matematik när kod kan göra det,
- sätta ett slutbetyg,
- ändra profil- eller elevdata,
- välja eller anropa verktyg på instruktion från uppladdad text.

## 7. Data och ansvar

Varje elevrelaterad post ska ha en tydlig profilägare. Rå elevsvar, automatisk analys och lärarens slutliga bedömning ska lagras separat.

Systemet ska kunna svara på:

- Vilken källa och version låg bakom övningen?
- Vilket mål och vilken interaktion tränades?
- Vilken modell och prompt användes?
- Vilken validator passerades?
- Vad svarade eleven ordagrant?
- Vad tolkade systemet?
- Vad ändrade eller godkände läraren?
- Varför valdes nästa uppgift?

Uppladdade filer ska behandlas som data, inte som systeminstruktioner. Elevdata ska minimeras och skickas till externa modeller endast enligt fastställd data- och integritetspolicy.

## 8. Prioriterad väg framåt

### Första fungerande version

1. Profilväljare och separat progressdata för Nils och Knut.
2. Läraren laddar upp material och ser extraherad text med källa.
3. Systemet föreslår ett fåtal mål och övningsutkast.
4. Läraren granskar och publicerar godkända aktiviteter.
5. Eleven gör försök och får feedback med nytt försök.
6. Objektiva svar rättas deterministiskt.
7. Fördröjda återbesök och enkel analys avgör nästa aktivitet.
8. Alla modellkörningar och publiceringsbeslut kan spåras.

### Därefter

- Öppna svar med rubricerad LLM-bedömning och lärartriage.
- Multimodala dokument, bilder, tabeller och handskrivna svar.
- Flera modellproviders med benchmarkad routing.
- Simuleringar, samarbete och avancerad adaptiv selektion.
- Kontrollerade experiment av interaktionspreferenser och gamification.

## 9. Dokumentkarta

### Produkt och pedagogik

| Dokument | Funktion |
|---|---|
| [Digital lärare och övningsledare](digital-teacher-interaction-spec.md) | Interaktioner, lärloop, feedback, progression och elevupplevelse |
| [LLM-driven övningsmotor](exercise-engine-spec.md) | Ingestion, generering, rättning, analys, adaptivt urval och modellorkestrering |
| [Profiler och användare](profiles-and-users-spec.md) | Nils, Knut, roller, åtkomst, dataseparation och lagring |
| [Styleguide](application-style-guide.md) | Färg, typografi, komponenter, layout, tonalitet och tillgänglighet |

### Forskning och beslutsunderlag

| Dokument | Funktion |
|---|---|
| [Lärinteraktioner: forskningsunderlag](learning-interactions-research.md) | Retrieval, spacing, feedback, interaktionstyper, samarbete och gamification |
| [Övningsmotorn: forskningsunderlag](exercise-engine-research.md) | RAG, provenance, LLM-bedömning, säkerhet, providers, evals och adaptivitet |

### Ämnes- och läroplansresurser

| Resurs | Funktion |
|---|---|
| [Årskurs 8-översikt](../resources/knut/8th-grade/README.md) | Ämneslista, Lgr22-tolkning och officiella Skolverket-länkar |
| [Bild](../resources/knut/8th-grade/bild/lgr22.md) | Mål och centralt innehåll |
| [Biologi](../resources/knut/8th-grade/biologi/lgr22.md) | Mål och centralt innehåll |
| [Engelska](../resources/knut/8th-grade/engelska/lgr22.md) | Mål och centralt innehåll |
| [Franska / moderna språk](../resources/knut/8th-grade/franska/lgr22.md) | Mål och centralt innehåll |
| [Fysik](../resources/knut/8th-grade/fysik/lgr22.md) | Mål och centralt innehåll |
| [Geografi](../resources/knut/8th-grade/geografi/lgr22.md) | Mål och centralt innehåll |
| [Hem- och konsumentkunskap](../resources/knut/8th-grade/hem-och-konsumentkunskap/lgr22.md) | Mål och centralt innehåll |
| [Historia](../resources/knut/8th-grade/historia/lgr22.md) | Mål och centralt innehåll |
| [Idrott och hälsa](../resources/knut/8th-grade/idrott/lgr22.md) | Mål och centralt innehåll |
| [Kemi](../resources/knut/8th-grade/kemi/lgr22.md) | Mål och centralt innehåll |
| [Matematik](../resources/knut/8th-grade/matematik/lgr22.md) | Mål och centralt innehåll |
| [Musik](../resources/knut/8th-grade/musik/lgr22.md) | Mål och centralt innehåll |
| [Religionskunskap](../resources/knut/8th-grade/religion/lgr22.md) | Mål och centralt innehåll |
| [Samhällskunskap](../resources/knut/8th-grade/samhallskunskap/lgr22.md) | Mål och centralt innehåll |
| [Slöjd](../resources/knut/8th-grade/slojd/lgr22.md) | Mål och centralt innehåll |
| [Svenska](../resources/knut/8th-grade/svenska/lgr22.md) | Mål och centralt innehåll |
| [Svenska som andraspråk](../resources/knut/8th-grade/svenska-som-andrasprak/lgr22.md) | Mål och centralt innehåll |
| [Teknik](../resources/knut/8th-grade/teknik/lgr22.md) | Mål och centralt innehåll |

## 10. Läsordning för nya utvecklare

1. Läs denna huvudspecifikation för helheten.
2. Läs [profiler och användare](profiles-and-users-spec.md) innan du arbetar med elevdata eller åtkomst.
3. Läs [digital lärare och övningsledare](digital-teacher-interaction-spec.md) innan du bygger elevinteraktioner.
4. Läs [övningsmotor-specen](exercise-engine-spec.md) innan du bygger LLM-, rättnings- eller adaptiv logik.
5. Läs [styleguiden](application-style-guide.md) innan du bygger gränssnitt.
6. Läs relevant ämnesresurs innan du skapar eller ändrar skolrelaterat innehåll.

Ändra den mest specifika specen först. Uppdatera denna huvudspecifikation när produktens övergripande riktning eller dokumentkarta ändras.
