# Spec: digital lärare och övningsledare

**Status:** Första produktgrund
**Målgrupp:** Elever i grundskolan, med start i årskurs 8
**Syfte:** Definiera vilka interaktioner programmet ska erbjuda och hur de ska användas för att skapa lärande, inte bara aktivitet.
**Forskningsunderlag:** [Forskningsunderlag: interaktioner för digital lärare och övningsledare](learning-interactions-research.md)
**Profil- och användarspecifikation:** [Profiler och användare](profiles-and-users-spec.md)

## 1. Produktbeslut

Programmet ska vara en **övningsledare** som hjälper eleven att:

1. förstå ett kunskapsmål,
2. försöka själv,
3. få en begriplig ledtråd eller förklaring,
4. reparera sitt svar,
5. möta kunskapen igen efter ett mellanrum,
6. använda kunskapen i en ny situation.

Programmet ska inte optimera för flest klick, längst session eller flest poäng. Den primära framgången är att eleven kan lösa en ny, fördröjd uppgift självständigt.

Programmet ska vara en webbaserad applikation som körs i webbläsare på dator, surfplatta och mobil. Krav på användare, profiler, åtkomst och profilspecifik lagring finns i [Profiler och användare](profiles-and-users-spec.md).

## 2. Mål och icke-mål

### Mål

- Göra återkallning, förklaring, tillämpning och reflektion enkla att öva ofta.
- Anpassa stöd och nästa aktivitet efter vad eleven faktiskt visar.
- Ge eleven flera tillgängliga sätt att uttrycka kunskap.
- Ge läraren tillförlitlig evidens om kunskapsmål, feltyper, säkerhet och utveckling.
- Kombinera automatisk återkoppling med lärarens omdöme när uppgiften är komplex.

### Icke-mål

- Programmet ska inte sätta slutbetyg utan lärarens möjlighet att granska och ändra.
- Programmet ska inte anta att en viss UI-form, exempelvis drag-and-drop eller spelpoäng, i sig skapar lärande.
- Programmet ska inte ersätta undervisning, laborationer, samtal eller fysisk aktivitet.
- Programmet ska inte använda AI-genererad text som facit utan versionshantering, källstöd och mänsklig överskrivning.

## 3. Evidensprinciper

Följande principer ska styra designen:

- **Starkast grund:** retrieval practice, distribuerad övning, förklarande feedback och worked examples för nybörjare.
- **Villkorad grund:** interleaving, self-explanation, multimedia, mastery learning, adaptiv tutoring, simuleringar och strukturerat samarbete.
- **Ingen automatisk evidens för UI-formen:** drag-and-drop, grafiska klick, papper kontra tangentbord och gamification måste motiveras av den kognitiva handlingen och testas lokalt.
- Varje aktivitet måste ha ett uttryckligt kunskapsmål och en tänkt mental handling.
- Direkt rätt svar är inte tillräckligt mått. Fördröjd återkallning och transfer ska finnas i produktens mätmodell.

## 4. Interaktionskatalog

### 4.1 Basinteraktioner

| Interaktion | Eleven gör | Använd när | Prioritet |
|---|---|---|---|
| Flervalsfråga | Väljer mellan svar | Snabb diagnos, begrepp och vanliga missuppfattningar | P0 |
| Kortsvar | Skriver ett kort svar | Fakta, begrepp, beräkningar och delsteg | P0 |
| Fri återkallning | Skriver eller talar allt hen minns | Start på område, metakognitiv diagnos och långtidsminne | P0 |
| Tillämpningsproblem | Använder kunskap i nytt exempel | Transfer och självständig problemlösning | P0 |
| Förklara svaret | Motiverar varför ett svar eller steg är rätt | Resonemang, naturvetenskap, matematik och källor | P0 |
| Sant/falskt med motivering | Bedömer påstående och förklarar | Missuppfattningar och begreppskontraster | P0 |
| Sortera eller ordna | Ordnar steg, händelser eller begrepp | Processer, kronologi och procedurer | P1 |
| Matcha | Kopplar ihop två representationer | Begrepp, symboler, definitioner och samband | P1 |
| Drag-and-drop | Flyttar objekt till rätt plats | Endast när den spatiala relationen är själva målet | P1 |
| Grafiskt klick | Markerar punkt, område eller del | Kartor, grafer, celler, krafter och diagram | P1 |
| Rita eller konstruera | Ritar, markerar eller bygger en modell | Geometri, kartor, grafer och tekniska modeller | P1 |
| Papper eller handskrivning | Löser fysiskt och lämnar in foto/scan eller resultat | När handens eller pennans ämneshandling är relevant | P1 |

Flervalsfrågor och matchning ska inte användas som ren dekoration. Distraktorer ska representera vanliga eller rimliga fel. Grafiska aktiviteter ska följas av en kort motivering eller transferfråga när själva klicket inte visar förståelsen.

### 4.2 Förklarande interaktioner

| Interaktion | Eleven gör | Produktkrav |
|---|---|---|
| Worked example | Studerar en fullständig lösning | Visa relationen mellan steg och princip, inte bara facit |
| Fading | Fyller successivt i fler steg själv | Ta bort stöd efter visad kompetens |
| Self-explanation | Svarar på varför/hur-frågor | Använd få och ämnesspecifika prompts |
| Sokratisk följdfråga | Förutsäger, väljer strategi eller hittar fel | Nästa fråga ska bygga på elevens föregående svar |
| Prediktion före visning | Förutspår utfall innan video, modell eller simulering | Lås prediktionen före observation och be om jämförelse efteråt |
| Jämförelse | Jämför två lösningar, texter, källor eller modeller | Gör skillnaden och den relevanta principen synlig |

### 4.3 Feedback

Feedback ska vara en interaktionsloop, inte en etikett.

1. Bekräfta vad uppgiften testade.
2. Visa om svaret innehåller ett relevant steg eller en missuppfattning.
3. Ge minsta hjälpsamma ledtråd.
4. Låt eleven försöka igen.
5. Visa förklaring eller komplett modell först när det behövs.
6. Följ med en analog eller något svårare uppgift.

Feedbacknivåer:

- `mål`: påminn om vad som ska lösas.
- `ledtråd`: peka på nästa steg eller relevant begrepp.
- `kontrast`: visa varför en vanlig alternativ tanke inte fungerar.
- `förklaring`: koppla lösningen till regel, modell eller källa.
- `modell`: visa ett komplett exempel.
- `reparation`: kräver nytt svar, korrigering eller förklaring.

Timing ska kunna variera efter uppgift och förkunskap. Enkel faktaträning kan få snabb korrigering. Komplex problemlösning ska kunna få en ledtråd före full förklaring så att eleven fortfarande behöver tänka.

### 4.4 Återbesök och variation

- Nya kunskapsmål ska få flera återbesök över dagar och veckor.
- Nästa intervall ska påverkas av korrekthet, återkallningstid, säkerhet och fördröjt resultat.
- När grunderna är etablerade ska problemtyper blandas så att eleven måste välja metod.
- Blandningen ska vara kontrasterande och begriplig, inte helt slumpmässig.
- Samma mål ska ibland testas med annan svarstyp och nytt exempel.

### 4.5 Multimedia, simulering och laboration

Multimedia ska användas när en bild, animation, ljudfil eller modell visar en relation som text ensam inte visar. Den ska vara segmenterad, signalerad och följas av prediktion, återkallning eller förklaring.

Simuleringar ska följa modellen:

1. formulera hypotes,
2. ändra en relevant variabel,
3. observera eller samla data,
4. jämföra med hypotes,
5. förklara och överföra till en icke-simulerad uppgift.

Riktiga laborationer, material och klassrumsaktiviteter ska kunna länkas till samma kunskapsmål. Simuleringen är inte en ersättning för praktisk undervisning när praktisk undervisning är möjlig och säker.

### 4.6 Samarbete och peer interaction

Samarbete ska vara strukturerat:

- varje elev svarar individuellt före diskussion,
- roller och samtalsregler är tydliga,
- gruppen måste använda evidens och bemöta ett motargument,
- varje elev lämnar en individuell förklaring efteråt,
- individuell transfer mäts efter samarbetet.

Ostrukturerat grupparbete och frivillig tävling ska inte vara standard.

### 4.7 Gamification

Gamification får bära en god lärloop men får inte vara lärandemålet. Starta utan ranking och med privat progression.

Tillåtna belöningar:

- genomförda återbesök,
- förbättrat svar,
- bra förklaring,
- uthållighet efter fel,
- hjälpsamt samarbete,
- uppnådd fördröjd återkallning.

Undvik som standard:

- streaks som kan skapa stress,
- offentlig ranking,
- poäng för hastighet,
- belöning för enkla uppgifter,
- belöningar som försvinner när eleven behöver stöd.

Varje spelifierad funktion ska kunna stängas av och ska utvärderas mot samma funktion utan spelifiering.

## 5. Lärloop

En rekommenderad session:

1. **Startdiagnos:** en eller två återkallningsfrågor från tidigare mål.
2. **Mål:** visa vad eleven ska kunna göra, inte bara vilket kapitel som öppnas.
3. **Exempel eller kort instruktion:** använd endast det stöd som behövs.
4. **Försök:** välj svarstyp efter mental handling.
5. **Feedback:** ledtråd, förklaring och reparation.
6. **Variation:** nytt exempel eller blandad problemtyp.
7. **Reflektion:** säkerhet, strategi eller vad som ändrades.
8. **Planerat återbesök:** lägg målet i elevens repetitionskö.

En session ska kunna avslutas efter ett meningsfullt försök. Programmet ska inte kräva långa sessioner för att registrera lärande.

## 6. Domänmodell

### `LearningObjective`

- `id`
- `subject`
- `gradeBand`
- `description`
- `prerequisiteObjectiveIds`
- `evidenceTypes`
- `masteryPolicy`

### `Activity`

- `id`
- `objectiveIds`
- `type`
- `prompt`
- `stimulus`
- `expectedMentalAction`
- `answerSchema`
- `distractors`
- `hintLevels`
- `feedbackPolicy`
- `accessibilityAlternatives`
- `sourceVersion`

### `Attempt`

- `activityId`
- `profileId` (se [Profiler och användare](profiles-and-users-spec.md))
- `answer`
- `startedAt`, `submittedAt`
- `answerTimeMs`
- `confidence`
- `automaticAssessment`
- `feedbackShown`
- `repairAttemptId`
- `teacherAssessment`

### `EvidenceArtifact`

- `profileId` (se [Profiler och användare](profiles-and-users-spec.md))

En sparad produkt som kan granskas av eleven eller läraren: text, ljud, foto, handritad bild, beräkning, simuleringens data eller gruppdiskussionens individuella slutsats.

### `ReviewItem`

- `profileId` (se [Profiler och användare](profiles-and-users-spec.md))
- `objectiveId`
- `nextReviewAt`
- `interval`
- `successHistory`
- `failureTypes`
- `lastDelayedResult`
- `studentConfidence`

Systemet ska separera **rå evidens**, **automatisk tolkning** och **lärarens bedömning**. En automatisk bedömning får aldrig skriva över råsvaret.

## 7. Prioriterad leverans

### P0: första användbara version

- Webbläsarbaserad, responsiv elevvy.
- Kunskapsmål och aktivitetsbank.
- Flervalsfråga, kortsvar, fri återkallning och tillämpningsproblem.
- Försök, ledtråd, förklaring, reparation och nytt försök.
- Repetitionskö med distribuerade återbesök.
- Enkel blandning av uppgiftstyper.
- Automatisk rättning för objektiva svar.
- Lärarens granskning av öppna svar och möjlighet att ändra bedömning.
- Elevens säkerhetsmarkering.
- Tillgänglig text- och tangentbordsbaserad väg för alla kärnaktiviteter.
- Loggning av försök, feltyp, feedback och fördröjt resultat.
- Automatisk sparstatus och hantering av tillfälligt avbrott i nätverket.

### P1: fördjupad övningsledare

- Worked examples, self-explanation och fading.
- Grafiska klick, matchning, ordning, ritning och foto/scan av papper.
- Text-till-tal, tal-till-text, alt-text och penna som alternativ.
- Diagnostik av förkunskaper och kunskapsmålens beroenden.
- Interleaving med ämnesdidaktiska regler.
- Multimedia med prediktion och segmenterad visning.
- Simuleringar med hypotes, data och förklaring.
- Läraröversikt med elevens faktiska evidens, inte bara procent rätt.

### P2: piloter och experiment

- Peer instruction och strukturerade gruppuppgifter.
- Adaptiva masterytrösklar.
- Automatisk bedömning av längre resonemang.
- Dialogisk tutoring med språkmodell.
- Gamification, privat progression och valfri spelifiering.
- Ranking eller tävlingslägen, endast om separata tester visar nytta utan negativa effekter.

## 8. Krav på AI och automatisk bedömning

- AI ska skilja mellan säker kunskap, gissning och osäker automatisk tolkning.
- AI ska inte ge full lösning innan eleven har gjort ett seriöst försök, utom när eleven uttryckligen väljer studie-/exempelläge.
- AI-feedback ska hänvisa till kunskapsmål, svarsevidens och nästa handling.
- AI ska kunna säga `osäker` och skicka uppgiften till lärare.
- Facit, bedömningsrubrik och promptversion ska sparas.
- Lärare ska kunna se originalfråga, elevsvar, automatisk motivering och ändra beslut.
- Eleven ska kunna be om en enklare förklaring, annan representation eller tillgänglig svarsväg.

## 9. Tillgänglighet och likvärdighet

- Alla kärnkunskapsmål ska kunna tränas utan drag-and-drop, färgkodning, ljud eller handskrift.
- Grafiska och handritade svar ska ha textbaserat alternativ.
- Talsyntes, textning, tangentbordsstöd, tillräcklig kontrast och förstoring ska stödjas.
- Stavning och motorik ska inte sänka ämnesbedömningen när de inte är målet.
- Språkstöd ska kunna läggas till utan att rätt svar avslöjas.
- Designen ska fungera på mobil, surfplatta och dator samt vid låg bandbredd.

## 10. Utvärdering

### Primära utfall

- Fördröjd återkallning efter minst en vecka.
- Transfer till ny uppgift och ny svarstyp.
- Kvalitet på förklaringar och problemlösningsstrategier.
- Individuell prestation efter samarbete.

### Sekundära utfall

- Direkt korrekthet.
- Feltyp och antal försök till reparation.
- Om feedbacken öppnades och användes.
- Återbesök enligt plan.
- Svarstid, avhopp och upplevd belastning.
- Skillnader mellan förkunskapsnivåer och tillgänglighetsbehov.

### Experimentregler

- Jämför samma kunskapsmål och innehåll, inte bara två olika lektioner.
- Använd direkt test och fördröjt test.
- Testa nya exempel, inte bara samma fråga igen.
- Förregistrera primärt utfall innan A/B-test.
- Mät spelifiering separat från lärande.
- Låt lärare granska ett urval av öppna svar för att kontrollera automatisk bedömning.

## 11. Öppna produktbeslut

- Vilken innehållsmodell ska beskriva kunskapsmål och förkunskapsrelationer?
- Ska första versionen stödja enbart svenska eller även engelska och andra språk?
- Vilka öppna svar får automatisk återkoppling i första versionen?
- Hur ska elevens samtycke, lagringstid och lärarens åtkomst hanteras?
- Ska foto av papper analyseras direkt, eller endast sparas som lärarunderlag?
- Vilka ämnen ska användas i den första piloten?
- Vilka masteryregler är transparenta nog för elev och lärare?

## 12. Referens

Se [forskningsunderlaget](learning-interactions-research.md) för evidensnivåer, mekanismer, risker och full källista. Centrala referenser är practice testing, spacing, feedback, worked examples, self-explanation, multimedia, intelligent tutoring och gamification.
