# Spec: LLM-driven övningsmotor

**Status:** Första produktgrund
**Målgrupp:** Grundskola, initialt årskurs 8
**Relaterade dokument:**

- [Digital lärare och övningsledare](digital-teacher-interaction-spec.md)
- [Profiler och användare](profiles-and-users-spec.md)
- [Forskningsunderlag för övningsmotorn](exercise-engine-research.md)

## 1. Syfte och avgränsning

Övningsmotorn är programmets innehålls- och analysdel. Den ska kunna:

1. ta emot lärarens filer, skolplaner och annan godkänd information,
2. omvandla materialet till versionerade kunskapsmål och övningskandidater,
3. använda elevens tidigare resultat för att välja nivå, uppgiftstyp och nästa steg,
4. använda elevens interaktionspreferenser som tillgänglighets- och urvalssignal,
5. rätta uppgifter och analysera råsvar, feltyper och säkerhet,
6. avgöra om ytterligare övning behövs och skapa eller välja rätt nästa övning,
7. skicka osäkra resultat till läraren i stället för att låsa elevens progression.

Motorn ska utnyttja LLM:er till största delen för språkförståelse, källtolkning, generering, förklaring, klassificering och analys. Deterministisk kod och ämnesspecifika lösare ska fortfarande användas där de är säkrare, exempelvis numerisk rättning, enheter, algebra och svarsalternativ.

## 2. Produktbeslut

- En LLM-genererad artefakt är alltid ett utkast tills den passerat validering och, som standard, lärargodkännande.
- Råkälla, elevsvar, automatisk tolkning, lärarbedömning och publiceringsbeslut lagras som separata objekt.
- LLM:er får föreslå, förklara och prioritera, men får inte själva publicera material, ändra elevdata eller ändra behörigheter.
- Direkt rätt svar är inte tillräckligt. Motorn ska väga in fördröjd återkallning, transfer, feltyp och om feedbacken reparerades.
- `preference_fit` och upplevd rolighet får påverka val mellan pedagogiskt likvärdiga kandidater, men får inte ersätta `mastery_need`, spacing eller transfer.
- Öppna svar bedöms som beslutsstöd med confidence och möjlighet att avstå. De ska inte automatiskt sätta slutbetyg.
- Alla elevsynliga beslut ska kunna förklaras med kunskapsmål, evidens och en versionerad `reasonCode`.

## 3. Icke-mål

- Att låta en modell läsa lärarens fil och direkt publicera elevmaterial.
- Att använda en LLM som enda facit för matematik, kemi, programmering eller andra uppgifter där kod kan verifiera svaret.
- Att optimera uppgiftsval mot klick, sessionstid, streaks eller omedelbar rättprocent.
- Att modellera eleven som en fast "visuell", "auditiv" eller annan learning style.
- Att skicka råa elevsvar eller identifierande elevdata till externa API:er som standard.
- Att ha en enda provider-specifik implementation som gör OpenAI, Claude, Mistral eller Gemini till ett hårt beroende.

## 4. Arkitektur

### 4.1 Komponenter

1. **Ingestion service:** tar emot filer, parser/OCR:ar och skapar versionerade källor.
2. **Knowledge modeler:** extraherar kunskapsmål, förkunskaper, begrepp, missuppfattningar och bedömningskriterier.
3. **Retrieval service:** hämtar godkända källfragment med metadata och provenance.
4. **Generation orchestrator:** väljer prompt, verktyg, modellroute och skapar kandidater.
5. **Validation pipeline:** kontrollerar schema, källstöd, svarbarhet, pedagogik, säkerhet och duplicering.
6. **Teacher review:** låter läraren granska, redigera, godkänna, avvisa och publicera.
7. **Assessment service:** rättar elevsvar deterministiskt eller med rubricerad LLM-analys.
8. **Learner model:** sammanställer resultat per kunskapsmål, feltyp, nivå, svarstyp och fördröjning.
9. **Selection service:** avgör om mer övning behövs och väljer eller beställer nästa aktivitet.
10. **Model gateway:** en provider-neutral adapter för lokal modell och externa API:er.
11. **Evaluation/observability:** sparar traces, golden sets, modellversioner, kostnad, latency och regressionresultat.

### 4.2 Dataflöde

```text
Lärarfil
  -> karantän, hash, åtkomstkontroll, parser/OCR
  -> strukturbevarade chunks med provenance
  -> kunskapsmål och förkunskapsgraf
  -> övningskandidater från LLM
  -> deterministisk + semantisk + säkerhetsmässig validering
  -> lärargranskning och immutable content version
  -> elevförsök
  -> rättning, felanalys och learner model
  -> selection decision
  -> befintlig övning eller ny kandidat
```

## 5. Inläsning av lärarens material

### 5.1 Tillåtna källor

V1 ska stödja:

- PDF och textdokument,
- presentationsfiler,
- bilder eller skannade arbetsblad med OCR,
- lärarens fria instruktioner,
- länkar till godkända webbkällor,
- skolplan och kursplan som versionerade referenskällor.

Originalfilen ska sparas separat från extraherad text. En OCR- eller parsergissning får inte tyst ersätta oläsligt innehåll.

### 5.2 Ingestion pipeline

1. Tilldela `documentId`, `documentVersion`, tenant, skapare, filhash, MIME-typ, storlek, språk och åtkomstpolicy.
2. Lägg filen i karantän och skanna den innan parser, OCR eller LLM får läsa den.
3. Extrahera text och struktur: sidor, rubriker, listor, tabeller, figurer, fotnoter och OCR-confidence.
4. Segmentera först vid dokumentstruktur, därefter vid stycke/mening och sist efter tokenbegränsning.
5. Skapa chunks med `page`, `headingPath`, `charStart`, `charEnd`, `contentHash`, språk, ämne, årskurs och approval-status.
6. Indexera med hybrid search: semantisk sökning för betydelse och lexical search för namn, formler och exakta begrepp.
7. Filtrera alltid på version, ämne, språk, årskurs, åtkomst och lärargodkännande före eller under retrieval.
8. Spara retrievalresultat och valda källor i en trace.

### 5.3 Provenance

Varje påstående som LLM:n använder för ett elevmaterial ska kunna spåras till:

```text
SourceRef {
  documentId,
  documentVersion,
  chunkId,
  locator: { page?, headingPath?, charStart?, charEnd? },
  contentHash,
  retrievedRank,
  retrievalScore,
  approvedBy?,
  approvedAt?
}
```

En citation utan ett faktiskt och stödjande källfragment är ogiltig. Om källunderlaget inte räcker ska generatorn avstå eller be om mer material, inte fylla i med omarkerad allmän modellkunskap.

### 5.4 Prompt injection i källmaterial

All uppladdad text, inklusive instruktioner som ser ut som systemmeddelanden, ska behandlas som data. Källmaterial får inte:

- välja modell, verktyg, mottagare eller filväg,
- anropa funktioner,
- ändra publiceringsstatus eller elevdata,
- begära hemligheter eller API-nycklar,
- skriva över systemets policy.

Parser, OCR och verktyg ska köras med sandbox, timeout, fil- och nätverksbegränsning. Modellen ska inte ha direkta API-nycklar eller verktyg som kan ändra produktionsdata. Injection-tester ska ingå i regressionstestningen.

## 6. Kunskapsmodell

LLM:n ska inte ensam bestämma vad eleven ska lära sig. Den får föreslå en modell som måste knytas till skolans styrdokument eller lärarens källa.

### `LearningObjective`

- `id`
- `version`
- `subject`
- `gradeBand`
- `description`
- `targetBehavior`
- `mentalAction`
- `prerequisiteObjectiveIds`
- `invariants`
- `surfaceFeatures`
- `acceptableEvidence`
- `commonMisconceptions`
- `assessmentCriteria`
- `transferCases`
- `sourceRefs[]`
- `approvalStatus`

### Målprofil

Varje mål måste uttrycka:

- vad eleven ska kunna göra,
- vad som får variera mellan uppgifter,
- vad som måste vara konstant,
- vilka svar och resonemang som räknas som evidens,
- vilka förkunskaper som krävs,
- hur en ny transferuppgift kan se ut.

Ett mål är inte godkänt för generering om det bara består av ett kapitelnamn eller ett löst ämnesord.

## 7. Generering av övningar

### 7.1 Kandidatgenerering

Generatorn får följande kontext:

- godkänt kunskapsmål och dess version,
- relevanta källchunks och provenance,
- elevnivå som intervall, inte onödiga personuppgifter,
- tidigare feltyper och vad eleven redan har försökt,
- önskad mental handling,
- tillåtna interaktionsformer,
- rubric och bedömningskriterier,
- säkerhets- och språkpolicy.

Generatorn ska returnera strukturerad data enligt ett versionslåst schema, inte fri Markdown som enda gränssnitt.

### 7.2 `ActivityDraft`

```text
ActivityDraft {
  id,
  contentVersion,
  status: draft|validated|approved|published|retired,
  objectiveIds[],
  subject,
  gradeBand,
  language,
  prompt,
  stimulus?,
  interactionType,
  expectedMentalAction,
  difficulty,
  prerequisiteObjectiveIds[],
  answerSchema,
  referenceAnswer,
  scoringRules,
  rubric?,
  distractors[],
  hintLevels[],
  feedbackPolicy,
  transferVariants[],
  accessibilityAlternatives[],
  sourceRefs[],
  generatedBy,
  validationReport,
  approvalHistory[]
}
```

`additionalProperties=false` ska användas där kontraktet kräver strikt schema. Enum, språk, längder, identifierare och obligatoriska källor ska valideras i kod efter modellsvaret.

### 7.3 Generatorns begränsningar

- En övning ska ha exakt ett primärt mål i V1.
- Fråga, facit, feedback och rubric ska använda samma källversion.
- Single-choice får inte ha flera korrekta alternativ.
- Distraktorer ska kopplas till verkliga missuppfattningar eller metodförväxlingar.
- Feedback får inte avslöja hela svaret före ett seriöst försök.
- Varje mål ska kunna få minst en ny variant med ändrad yta men bevarad princip.
- Varje kärnövning ska ha en text- och tangentbordsbaserad alternativ väg.
- Oklart, motsägelsefullt eller otillräckligt underlag ska leda till `needs_teacher_review`.

## 8. Valideringspipeline

Validera i följande ordning, från billig och deterministisk kontroll till dyr modellanalys:

1. **Schema:** giltig struktur, enum, längd, datatyper och obligatoriska fält.
2. **Mål:** godkänd målversion, rätt ämne/årskurs, förkunskaper och mental handling.
3. **Svarbarhet:** solver, facitkontroll, enhet/tolerans, symbolisk algebra eller referenslösning där möjligt.
4. **Distraktorer:** dokumenterad missuppfattning, inga positions- eller längdledtrådar.
5. **Grounding:** källversion, citation och stöd för fråga, facit och feedback.
6. **Pedagogik:** nivå, kognitiv belastning, transfer, feedback och svarstyp.
7. **Säkerhet:** injection, olämpligt innehåll, diskriminerande exempel, personuppgifter och promptläckage.
8. **Kvalitet:** dubblettkontroll, läsbarhet, alternativ väg och konsekvens mellan facit/rubric/feedback.
9. **Mänsklig granskning:** läraren ser den faktiska artefakten, källutdrag och validatorfynd.

En misslyckad validering får inte repareras genom att samma LLM får sista ordet utan ny validering. Varje ändring skapar en ny `contentVersion`.

## 9. Lärargodkännande och publicering

Nygenererade mål, övningar, facit, rubric, feedback och källtolkningar börjar i `draft`.

Läraren ska kunna:

- se övningen i elevläge,
- se relevanta källutdrag och sid-/avsnittshänvisning,
- se elevnivå, mental handling och avsedd svårighet,
- se facit, distraktorer, missuppfattning och feedbacknivåer,
- godkänna, redigera, avvisa eller begära ny version,
- markera en alternativ korrekt lösning,
- stoppa en publicerad version,
- se vilka elever som har fått en felaktig version och skapa reparationsåtgärd.

Publicering skapar en immutable version med vem, när, vilken källa, vilken modell och vilken validator som användes.

## 10. Rättning och resultatanalys

### 10.1 Deterministisk rättning

Använd kod eller ämnesspecifik verifierare när svaret är entydigt:

- single- och multiple-choice,
- sant/falskt,
- numeriska svar med explicit tolerans och enhet,
- algebraiska uttryck efter kanonisering,
- matchning och ordning,
- programmering eller beräkning med isolerade testfall.

LLM:n får formulera en pedagogisk förklaring från resultatet men får inte ändra det deterministiska resultatet utan en separat, flaggad process.

### 10.2 LLM-bedömning av öppna svar

Öppna svar, resonemang, textanalys och alternativa lösningar kan skickas till rubricerad LLM-analys. Den ska returnera:

```text
Assessment {
  attemptId,
  rubricVersion,
  criterionResults[],
  evidenceSpans[],
  misconceptionIds[],
  suggestedFeedback,
  confidence,
  needsTeacherReview,
  abstentionReason?,
  modelRunId
}
```

Krav:

- bedöm delkriterier, inte bara en totalscore,
- citera elevens evidens för varje bedömning,
- stöd `insufficient_evidence`, `alternative_correct_solution` och `needs_teacher_review`,
- spara råsvar och modellbedömning separat,
- lås inte progression på en ensam LLM-bedömning,
- skicka låg confidence, nya svarstyper, konflikter och högkonsekvensfall till lärare.

### 10.3 Golden set och kalibrering

Ett versionshanterat golden set ska innehålla lärarbedömda svar från olika ämnen, nivåer, språkvariationer, svarslängder, vanliga fel och alternativa korrekta lösningar.

Mät minst:

- överensstämmelse med minst två lärarbedömningar när det är möjligt,
- false-positive och false-negative per kriterium,
- precision/recall för `needsTeacherReview`,
- confidence calibration,
- skillnad mellan elevgrupper och svarslängder,
- stabilitet vid omskrivning, språkbyte och annan svarspresentation,
- drift mellan modell-, prompt- och rubricversioner.

## 11. Learner model och adaptiv selektion

### 11.1 Separata signaler

Motorn ska inte blanda ihop följande:

- `masteryNeed`: svag eller osäker evidens på målet,
- `spacingNeed`: målet är dags för återbesök,
- `transferNeed`: eleven har klarat ytan men inte nytt exempel eller ny svarstyp,
- `preferenceFit`: valt format, hjälpmedel, språk och tillfällig kontext,
- `novelty`: ny representation eller kontrast inom samma mål,
- `engagementSignal`: avhopp, frivilligt återbesök och självrapporterad upplevelse.

Preferenser ska vara mjuk urvalssignal, förutom faktiska tillgänglighetsbehov. De får inte representera en fast learning style.

### 11.2 Resultatpost

Varje försök ska kunna bidra med:

- `objectiveId`, `activityId`, `profileId`,
- svar och svarstid,
- automatisk/deterministisk rättning,
- confidence från eleven,
- feltyp och missuppfattning,
- feedbacknivå som visades,
- om svaret reparerades,
- `directResult`, `delayedResult`, `transferResult`,
- lärarens eventuella ändring,
- interaktionstyp och tillgänglighetsväg.

### 11.3 `SelectionDecision`

```text
SelectionDecision {
  profileId,
  objectiveId,
  evidenceUsed: [attemptIds, delayedResultIds, teacherSignals],
  need: remediation|retrieval|transfer|extension|none,
  reasonCode,
  selectedActivityId?,
  generatedDraftId?,
  excludedCandidates: [{ id, reason }],
  confidence,
  policyVersion
}
```

### 11.4 När fler uppgifter ska skapas eller väljas

Fler uppgifter behövs när:

- målet är due för spacing och saknar fördröjd evidens,
- en identifierad missuppfattning behöver adresseras direkt,
- eleven klarar igenkänning men inte fri återkallning, förklaring eller transfer,
- svaret är ofullständigt eller osäkert,
- en progressionregel kräver flera oberoende evidenser,
- en lärarändring visar att föregående övning eller feedback var fel.

Fler uppgifter ska inte skapas när de bara upprepar samma yta, när målet redan har tillräcklig transfer-evidens eller när eleven behöver undervisning/lärarintervention snarare än fler frågor.

### 11.5 Urvalsordning

1. Hårda säkerhets-, åtkomst- och tillgänglighetskrav.
2. Kunskapsmål och förkunskaper.
3. `masteryNeed`, spacing och transfer.
4. Lärarens uttryckliga prioritering.
5. `preferenceFit` mellan pedagogiskt likvärdiga kandidater.
6. Variation och novelty inom fastställda svårighetsgränser.
7. Engagemangssignal som tie-breaker, aldrig ensam belöning.

Bandit- eller annan explorativ optimering får inte användas i elevpilot innan golden holdout, fördröjt transferprov, coveragekrav, kill switch och lärarkontroll finns.

## 12. Modellorkestrering

### 12.1 Provider-neutral gateway

Bygg en gateway med samma interna operationer oavsett provider:

- `generate`
- `structuredGenerate`
- `embed`
- `rerank`
- `classify`
- `judge`
- `health`

Adaptern ska hantera skillnader i structured output, tool use, citations, tokenräkning, rate limits, fel, dataretention och multimodalitet. Varje anrop loggar faktiskt provider-, modell- och snapshot-ID.

### 12.2 Initial routinghypotes

Detta är en hypotes som måste benchmarkas:

- **RTX 3090 lokalt:** embeddings, PII-redaktion, dokumentklassning, retrieval/reranking, deterministiska solvers och lokal modell när kvaliteten räcker.
- **OpenAI, Claude, Mistral och Gemini:** uppgifter som kräver validerad multimodalitet, resonemang, språk eller tool/structured-output-kapacitet och som får skickas enligt dataklassning.
- **Lärargranskning:** batchgenerering kan använda högkvalitetsmodell, men samma validator och approval gate gäller.
- **Elevens synkrona flöde:** använd publicerade aktiviteter och deterministisk rättning där möjligt; ett modellfel ska ge begriplig fallback, inte tyst modellbyte.

RTX 3090 är inte ett prestandalöfte. Modellstorlek, kvantisering, batch, minne, latency och kvalitet ska mätas på serverns faktiska hårdvara.

### 12.3 Dataklassning

| Klass | Exempel | Standardrouting |
|---|---|---|
| A | publikt eller anonymiserat lärarmaterial | lokal eller godkänd extern route |
| B | intern skolplanering | lokal först; extern endast enligt policy |
| C | elevsvar, resultat och preferenser | lokalt som standard; extern endast minimerat och godkänt |
| D | identifierare och känsliga elevförhållanden | lokal behandling eller ingen modellbehandling |

API-nycklar ska ligga på servern i secret manager, aldrig i webbläsaren eller i prompten. Pseudonymisera profil-ID före extern route och skicka inte namn eller rå elevtext när abstraherade features räcker.

### 12.4 Routingbenchmark

Jämför providers på samma prompts, källor, språk och seeds där det är möjligt. Mät per ämne, uppgiftstyp, nivå och dataklass:

- groundedness och citation precision/recall,
- schema-pass och validation-failure rate,
- entydigt facit, distraktorkvalitet och transfervariation,
- teacher agreement, false-positive/negative och abstention,
- bias över språk, svarslängd och tillgänglighetsväg,
- p50/p95 latency, timeout och rate-limit-frekvens,
- tokens, kronor per arbetsflöde och kostnad per godkänd övning,
- lokal GPU-minne, throughput, queue depth och driftkostnad,
- injection, dataläckage, tool misuse och promptläckage.

En route får inte väljas för låg latency eller kostnad om den missar miniminivåer för correctness, grounding eller safety.

### 12.5 Fallback

- timeout: visa sparad godkänd aktivitet eller deterministisk lokal aktivitet,
- providerfel: retry med exponential backoff och idempotency key, därefter godkänd alternativ route,
- schemafel: högst ett reparationsanrop, sedan abstention/validator,
- groundingfel: ingen fri modelltext till elev,
- modellbyte: logga trace och content-version,
- ökade fel, kostnad eller säkerhetsfynd: circuit breaker.

## 13. Observability och evals

Varje elevsynligt resultat och varje batchartefakt ska ha `traceId` och logga:

- profile pseudonym och tenant,
- activity-, objective-, content- och source-version,
- document/chunk-ID och retrievalrank,
- prompt/policy-version och adapterversion,
- provider, modell och parametrar,
- tool-resultat och approval gates,
- schema-, semantic- och safety-resultat,
- tokens, kostnadsestimat, kötid, latency och retries,
- fallback, error code, output hash och human override.

Fulla elevsvar och källor ska inte hamna i vanliga observability-loggar som standard. De ska ligga i separata åtkomstskyddade evidensposter.

### Golden set

Golden set ska innehålla:

- godkända källor och source refs,
- mål, förkunskaper och mental handling,
- godkända och förbjudna övningar,
- facit, alternativa korrekta lösningar, distraktorer och feedback,
- lärarbedömda öppna svar,
- fall där rätt utfall är `needsTeacherReview`,
- injection, OCR-fel, tabell/figur, språkvariation och tillgänglighetsfall,
- fördröjda och transfererande testfall.

### Releaseblockerande mätetal

| Område | Exempel på mätetal |
|---|---|
| Retrieval | recall@k, MRR/nDCG, godkänd source coverage |
| Grounding | citation precision/recall, unsupported-claim rate, abstention |
| Struktur | schema-pass, repair rate, unknown enum/extra field |
| Övningskvalitet | solver-pass, entydigt facit, dubblettgrad, transfervariation |
| Bedömning | teacher agreement, FPR/FNR, calibration, abstention |
| Pedagogik | delayed retrieval, transfer, repair completion, feedback-use |
| Säkerhet | injection success, data leakage, tool misuse, prompt leakage |
| Drift | modellversion, p50/p95 latency, error rate, token/kostnad |

En release blockeras om safety, provenance, schema eller deterministisk correctness försämras, även om engagemang eller kostnad förbättras.

## 14. API och händelser

Exakta URL:er och teknikval är öppna, men följande domänoperationer ska finnas:

- `ingestDocument`
- `approveDocumentVersion`
- `extractLearningObjectives`
- `generateActivityDrafts`
- `validateActivityDraft`
- `reviewActivityDraft`
- `publishContentVersion`
- `assessAttempt`
- `analyzeLearningEvidence`
- `createSelectionDecision`
- `generateRemediationActivity`
- `getTeacherReviewQueue`
- `stopContentVersion`

Viktiga händelser:

- `DocumentIngested`
- `ObjectiveDrafted`
- `ActivityDrafted`
- `ActivityValidationFailed`
- `ActivityApproved`
- `ActivityPublished`
- `AttemptSubmitted`
- `AttemptAssessed`
- `TeacherOverrideRecorded`
- `SelectionDecisionCreated`
- `ContentVersionRetired`

Alla kommandon ska vara idempotenta där retry kan förekomma. Alla händelser ska kunna kopplas till `traceId`, `profileId` och relevant content/source-version utan att råa elevsvar hamnar i vanliga loggar.

## 15. Prioriterad leverans

### P0: säker lärarassisterad motor

- filuppladdning med karantän, parsing/OCR och provenance,
- målmodell med lärargodkännande,
- LLM-genererade övningsutkast med structured output,
- schema-, grounding-, solver- och säkerhetsvalidering,
- lärargranskning före publicering,
- deterministisk rättning av objektiva svar,
- learner model med feltyp, confidence, spacing och transfer,
- selection decision med synlig reason code,
- ny övning skapas endast vid diagnostiskt skäl,
- provider-neutral gateway med lokal route och minst en extern route,
- trace, golden set, regression suite och kill switch.

### P1: bredare analys och multimodalitet

- öppna svar med rubricerad LLM-bedömning och lärartriage,
- flera externa providers med benchmarkad routing,
- bilder, handskrivning, tabeller och figurer,
- multimodala övningar och simuleringar,
- automatiserad dubblett- och transfervariationskontroll,
- teacher dashboard för felmönster och content-repair.

### P2: optimering och experiment

- contextual bandit under safe-exploration-regler,
- personlig preferensmodell baserad på uppmätt deltagande och lärande,
- automatisk golden-set-kandidat från lärarens verifierade ändringar,
- avancerad kostnads- och latencystyrning,
- provideroberoende batch- och offlinegenerering.

## 16. Säkerhet, integritet och ansvar

- Följ [Profiler och användare](profiles-and-users-spec.md) för profilisolering, roller, export, radering och åtkomst.
- Följ dataklassning innan material eller elevsvar skickas till extern modell.
- Inga API-nycklar i klienten eller i modellkontext.
- Least privilege för verktyg, lagring och model gateway.
- Lärargodkännande krävs före elevpublicering som standard.
- LLM får inte autonomt skriva till elevprogression, publicera innehåll eller ändra behörigheter.
- All automatisk bedömning ska kunna avstå och skickas till människa.
- Dataskyddsombud/huvudman ska besluta om rättslig grund, biträdesavtal, tredjelandsöverföring, DPIA, retention och incidentflöde.

## 17. Specacceptans för första release

Första releasen får inte gå till elevpilot innan följande kan demonstreras:

- varje elevsynlig övning har godkänt mål, källversion och publiceringshistorik,
- dold instruktion i uppladdad fil kan inte ändra policy, anropa verktyg eller publicera,
- objektiva svar rättas deterministiskt och testas för tolerans, enhet och alternativa korrekta uttryck,
- öppna svar visar rubric, evidens, confidence och lärarens överskrivning,
- feedback leder till reparation eller ny tillämpning,
- repetitionskön använder fördröjd evidens och synlig reason code,
- modell-, prompt-, käll- och fallbackbyte syns i trace och kan återskapas,
- golden set, regression suite, cost/latency/error budgets och kill switch finns,
- dataskydds- och leverantörsbeslut är dokumenterade,
- pilotens primära utfall är delayed retrieval och transfer, inte klick eller omedelbar rättprocent.

## 18. Referenser

- [Forskningsunderlag: LLM-driven övningsmotor](exercise-engine-research.md)
- [Forskningsunderlag: interaktioner för digital lärare](learning-interactions-research.md)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)
- [OWASP Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [EU AI Act policy overview](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai)
- [UNESCO: Guidance for Generative AI in Education and Research](https://unesdoc.unesco.org/ark:/48223/pf0000386693)
- [IMY: personuppgifter om barn](https://www.imy.se/verksamhet/dataskydd/det-har-galler-enligt-gdpr/introduktion-till-gdpr/personuppgifter/personuppgifter-om-barn/)
- [OpenAI Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling)
- [Anthropic Tool Use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)
- [Mistral Function Calling](https://docs.mistral.ai/capabilities/function_calling/)
- [Gemini Structured Output](https://ai.google.dev/gemini-api/docs/structured-output)
- [OpenTelemetry Trace](https://opentelemetry.io/docs/specs/otel/trace/)
