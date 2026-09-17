# Forskningsunderlag: LLM-driven övningsmotor för årskurs 8

**Syfte:** ge en specförfattare verifierat underlag för en webbaserad övningsmotor som ingestera lärarens material och skolplan, genererar övningar, använder elevens resultat och interaktionspreferenser, rättar och analyserar svar samt väljer om fler uppgifter behövs.

**Målgrupp:** grundskola, initialt årskurs 8. Underlaget är inte en juridisk bedömning eller en ämnesdidaktisk läroplan. Svensk årskurs 8 är en viktig avgränsning för pilot och utvärdering, men mycket av forskningen är gjord på andra åldrar, ämnen eller laboratorieuppgifter.

**Forskningsläge:** sammanställt 2026-09-17. Länkarna i källistan kontrollerades genom att öppnas eller hämtas där det var tekniskt möjligt. DOI- och arXiv-länkar kan vara åtkomstbegränsade utan att källans identitet är oklar.

## 1. Slutsatser för specen

### Högprioriterade beslut

1. **Kunskapsmål före generering.** Varje övning måste vara knuten till ett versionerat kunskapsmål, förkunskapsrelationer, mental handling, bedömningskriterier och källstöd. Modellen får inte själv hitta på vad eleven förväntas kunna.
2. **Försök, feedback, reparation och transfer.** En rätt/fel-etikett är inte tillräcklig. Kärnloopen ska innehålla ett eget försök, minsta hjälpsamma ledtråd eller förklaring, ny handling och senare uppgift i nytt exempel eller ny svarstyp.
3. **Spacing och retrieval som standard.** Planera återbesök över dagar och veckor. Direkt precision ska inte vara det enda måttet; fördröjd återkallning och transfer ska påverka om målet anses etablerat.
4. **Provenance är en del av innehållet.** Spara exakt dokumentversion, sida/avsnitt eller teckenintervall, chunk-id, hash, åtkomststatus och vilka källor som faktiskt användes. En citation utan spårbar evidens räknas som ogiltig.
5. **Lärargodkännande före elevpublicering.** Nygenererade mål, facit, rubriker, feedback och övningar ska ligga i `draft` tills en behörig lärare godkänt dem eller en uttryckligen avgränsad, validerad mall har passerat automatisk kontroll.
6. **Deterministiskt där det räcker.** Flervalsfrågor, exakta tal, enheter, symboliska uttryck och enkla matchningar ska rättas med kod eller verifierad ämnesmotor. En LLM får förklara resultatet men ska inte ersätta den deterministiska kontrollen.
7. **Öppna svar är beslutsstöd, inte autonom betygsättning.** LLM-bedömning får ge preliminär bedömning, evidens och osäkerhet. Den ska kunna avstå, skickas till lärare och aldrig skriva över elevens råsvar eller lärarens beslut.
8. **Rådata, tolkning och beslut separeras.** Spara elevens exakta svar, automatisk bedömning, modell/prompt/rubrik-version, lärarens bedömning och publiceringsbeslut som separata poster.
9. **Extern modell är inte standard för barns råsvar.** Klassificera data innan routing. Lägg elevidentifierare, råsvar och känsliga uppgifter lokalt som standard; skicka bara minimerad/redigerad data till extern leverantör efter rättslig, avtalsmässig och pedagogisk bedömning.
10. **Ingen prestandapromise utan benchmark.** RTX 3090 och fyra API-leverantörer är en tillgänglighets- och arkitekturförutsättning, inte ett bevis på kapacitet, kostnad eller svarstid. Alla routingbeslut ska komma från en reproducerbar benchmark.

### Evidensnivåer i dokumentet

- **Stark:** meta-analys eller flera experiment med relativt tydlig mekanism och relevans för lärande.
- **Måttlig/villkorad:** positivt stöd, men beroende av ämne, förkunskap, uppgift, ålder eller implementering.
- **Svag/oklar:** rimlig designhypotes men otillräckligt stöd för en generell effekt.
- **Ingen evidens för formen:** mekanismen kan vara bra, men det följer inte att en viss UI-kontroll eller modellfunktion förbättrar lärande.
- **Ingenjörskrav:** rekommendation för säkerhet, spårbarhet eller testbarhet, inte ett påstående om en kausal utbildningseffekt.

## 2. Lärandeevidens och produktkrav

### 2.1 Retrieval, spacing och feedback

**Evidens: stark.** Practice testing/retrieval ger i genomsnitt bättre senare återkallning än enbart omläsning; meta-analysen av Adesope, Trevisan och Sundararajan omfattar många experiment och visar att effekten inte begränsas till en viss frågeform ([Adesope et al., 2017](https://doi.org/10.3102/0034654316689306)). Roediger och Karpicke visade experimentellt testförstärkning över tid, men också att direkt återgivning och fördröjd återgivning inte är samma utfall ([Roediger & Karpicke, 2006](https://doi.org/10.1111/j.1467-9280.2006.01693.x)).

**Evidens: stark för distribuerad övning.** Cepeda et al. syntetiserade distribuerad kontra massad övning över många intervall och material; spacing brukar ge bättre långtidsåterkallning, men den optimala luckan beror på önskad retentionstid och material ([Cepeda et al., 2006](https://doi.org/10.1037/0033-2909.132.3.354)). Det stöder inte en enda universell repetitionskalender.

**Evidens: stark men villkorad för feedback.** Feedbackens effekt beror på kvalitet, uppgift, mål, förkunskap och vad eleven gör därefter. Shutes översikt argumenterar för information som hjälper eleven att korrigera arbetet, inte bara kunskap om resultatet ([Shute, 2008](https://doi.org/10.3102/0034654307313795)). EEF:s officiella guidance betonar också att feedback bör leda till vidare lärande och inte bedömas efter kanal eller mängd text ([EEF, Teacher Feedback to Improve Pupil Learning](https://educationendowmentfoundation.org.uk/education-evidence/guidance-reports/feedback)).

**Beslut:**

- Separera `direct_result` från `delayed_result` och `transfer_result`.
- Kräv verkligt försök före full lösning, med undantag för uttryckligt exempelläge.
- Skikta feedback: mål/påminnelse, ledtråd, kontrast, förklaring, modell, reparationsuppgift.
- Logga om feedback öppnades, vilken nivå som visades, om svaret ändrades och hur nästa/fördröjda uppgift gick.
- Välj repetitionsintervall med målbehov, senaste försök, tid, säkerhet och fördröjt resultat. Låt inte omedelbar träffsäkerhet ensam styra.
- Avsluta en session efter ett meningsfullt försök; fler uppgifter ska kräva diagnostiskt eller pedagogiskt skäl.

**Risk:** ett system som visar lösningen snabbt kan maximera nöjdhet och kortsiktig precision men minska egen retrieval. Ett system som fortsätter tills eleven blir rätt kan överträna lätt material och missa fördröjd transfer.

### 2.2 Transfer och variation

**Evidens: viktig men inte automatisk.** Att klara samma ytliga fråga igen bevisar inte transfer. Befintligt underlag i [`learning-interactions-research.md`](learning-interactions-research.md) stöder nya exempel, blandade problemtyper och fördröjda test; det ska läsas som en designprincip, inte som en garanti för varje genererad uppgift. Interleaving har måttligt och modererat stöd: effekten beror bland annat på likheten mellan problemtyper och på vad som mäts ([Brunmair & Richter, 2019](https://doi.org/10.1037/bul0000209)).

**Beslut:** en lärandemålsprofil ska minst ange:

- `target_behavior`: vad eleven ska göra;
- `surface_features`: vad som får variera;
- `invariants`: vad som måste bevaras;
- `acceptable_evidence`: vilka svar och resonemang som räknas;
- `transfer_cases`: minst ett nytt exempel och, när möjligt, ny svarstyp;
- `prerequisite_objectives` och `common_misconceptions`.

**Acceptanskriterium:** en övningsserie är inte godkänd om alla frågor kan lösas genom att känna igen samma text, svarsalternativens mönster eller den senast visade metoden.

### 2.3 Worked examples och self-explanation

**Evidens: stark för nybörjare, villkorad vid fading.** Worked examples kan minska onödig sökbelastning och visa procedurens principer; effekten beror på förkunskap och hur exempel växlas med eget arbete ([Atkinson et al., 2000](https://doi.org/10.3102/00346543070002181); [van Gog & Rummel, 2010](https://doi.org/10.1007/s10648-010-9134-7)). Self-explanation har positiv men heterogen genomsnittseffekt ([Bisra et al., 2018](https://doi.org/10.1007/s10648-018-9434-x)).

**Beslut:** modellen får föreslå exempel och varför-frågor, men validatorn ska kontrollera att varje steg är kopplat till en regel eller princip. Stöd ska tas bort först efter visad kompetens, inte efter antal klick. En förklaring ska leda till en ny handling, inte endast till mer text.

### 2.4 Interaktionspreferenser, format och tillgänglighet

**Evidens: svag för preferensmatchning som lärandeeffekt.** Pashler et al. fann inte tillräckligt stöd för påståendet att undervisning blir bättre genom att matcha en elevs påstådda "learning style" med motsvarande presentationsform ([Pashler et al., 2008](https://doi.org/10.1111/j.1539-6053.2009.01038.x)). Det bör därför inte finnas en modellregel som antar att en elev lär sig bäst visuellt, auditivt eller genom en viss spelgest.

**Evidens: villkorad för elevens val och representation.** Befintligt underlag pekar på att text, tangentbord, ljud, penna och strukturerade objekt kan vara olika vägar att uttrycka samma kunskap, men att en UI-form i sig inte är lärandemekanismen. Val kan däremot vara nödvändigt för tillgänglighet, språk, motorik, motivation och möjlighet att faktiskt visa kunnande. Det är en produkt- och likvärdighetsprincip, inte ett belägg för bättre mastery.

**Beslut:**

- Modellera preferenser som `accessibility_need`, `interaction_preference` och `temporary_context`, inte som elevens fasta kognitiva typ.
- Använd preferenser som hårt tillgänglighetskrav eller mjuk tie-breaker efter kunskapsbehov, spacing och transfer.
- Erbjud textbaserad och tangentbordsbaserad kärnväg för alla centrala mål; drag-and-drop, färg, ljud eller handskrift får inte vara enda vägen.
- Mät om preferensvalet påverkar deltagande och avhopp, men gör fördröjt lärande till primärt utfall.
- Låt eleven ändra val och förklara varför en aktivitet valdes. Låt lärare se när en preferens eller hjälpmedelsinställning styrde valet.

**Risk:** ett system som optimerar för "roligast" eller "bekvämast" kan ge för lite retrieval, för låg svårighet eller för få kontraster. Preferensdata kan dessutom avslöja funktionsnedsättning eller andra känsliga förhållanden; samla därför minsta nödvändiga signal och separera den från pedagogisk bedömning.

## 3. Ingestion, RAG och lärargodkännande

### 3.1 Vad forskningen och API-dokumentationen faktiskt stödjer

RAG kombinerar en språkmodells parametriska minne med en extern, sökbar minneskälla. Lewis et al. visade i en primär NeurIPS-studie att RAG kunde förbättra specificitet, diversitet och factualitet i deras kunskapsintensiva benchmark, och pekade samtidigt ut provenance och uppdatering som öppna problem ([Lewis et al., 2020](https://arxiv.org/abs/2005.11401)). Detta är inte evidens för att en godtycklig skol-RAG automatiskt är korrekt eller pedagogiskt lämplig.

OpenAI:s officiella Retrieval-dokumentation beskriver semantic search, metadatafilter, score, filursprung och konfigurerbar chunking. Dokumentationen anger som ett konkret implementationsexempel 800 tokens per chunk och 400 tokens överlapp som standard, med möjlighet att ändra detta ([OpenAI Retrieval guide](https://platform.openai.com/docs/guides/retrieval)). Det är en leverantörsdefault, inte en pedagogisk optimum för skolmaterial. Chunking ska därför benchmarkas på skolans dokumenttyper.

**Beslut:** bygg en egen provenance- och godkännandemodell även om en leverantörs vector store används. Extern retrievalmetadata är inte tillräcklig garanti för att en elev kan se eller kontrollera källan.

### 3.2 Föreslagen ingestion-pipeline

1. **Uppladdning och karantän:** tilldela `document_id`, `tenant_id`, originalfilens hash, MIME-typ, storlek, skapare, rättighetsstatus och uppladdningstid. Skanna filen efter skadligt innehåll och avvisa eller isolera makro-/skriptinnehåll innan parser eller modell får läsa den.
2. **Åtkomstkontroll:** lagra vem som får använda dokumentet, om det är lärarens material, skolplan, elevmaterial eller extern källa, samt om materialet får skickas till en extern leverantör.
3. **Parsing/OCR:** behåll sidnummer, rubriker, listor, tabeller, figurer, fotnoter, läsriktning och OCR-confidence. Spara originalet separat från normaliserad text. En parser får inte tyst ersätta ett oläsligt område med gissad text.
4. **Strukturbevarande segmentering:** dela först vid dokumentstruktur, därefter vid stycke/mening och sist vid tokenstorlek. Lägg metadata på varje chunk: `document_version`, `page`, `heading_path`, `char_start`, `char_end`, `content_hash`, `language`, `subject`, `grade`, `approval_status` och `access_policy`.
5. **Indexering:** kombinera semantisk sökning med lexical/hybrid sökning för formler, namn, begrepp och exakta hänvisningar. Filtrera på ämne, årskurs, språk, dokumentversion och lärargodkänd status före eller under ranking.
6. **Retrieval:** returnera chunk-text, källa, rank, score och version till generatorn. Logga även kandidater som inte valdes om de behövs för felsökning, med dataminimering.
7. **Grounded generation:** generatorn får endast göra sakpåståenden som kan länkas till ett eller flera chunks eller till en versionerad deterministisk ämnesmotor. Om täckningen är för låg ska den fråga efter mer källunderlag eller avstå, inte fylla i med allmän modellkunskap.
8. **Citation validation:** varje citation måste referera till ett faktiskt chunk-id och stödja påståendet. Kontrollera både referensens existens och textuell/semantisk entailment; en källista som inte bär upp svaret är inte source grounding.
9. **Teacher review:** visa källutdrag, sid-/avsnittshänvisning, föreslaget kunskapsmål, elevnivå, facit, distraktorer, rubric, feedback och osäkerhet i en granskningsvy. Läraren ska kunna godkänna, redigera, avvisa eller ange bättre källa.
10. **Publicering:** publicera bara en immutable `content_version` med godkännandehistorik. Om en källa ändras ska beroende mål, övningar, facit och feedback markeras för omgranskning.

### 3.3 Provenance-kontrakt

Minsta fält i en källhänvisning:

```text
sourceRef = {
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

Minsta krav för en genererad övning:

- varje sakligt innehållsfält har noll eller flera `sourceRef`;
- `sourceRef` får inte peka på en annan version än den som användes vid generering;
- citationer får inte lämnas bort när `grounding_required=true`;
- frågan och facit får inte motsäga en godkänd källa utan ett explicit lärarbeslut;
- eleven ska kunna se en åldersanpassad källa eller få besked att läraren har verifierat materialet;
- råkälla, extraherad text, chunk och genererad artefakt måste kunna följas i en trace.

### 3.4 Prompt injection i uppladdade filer

**Risk: hög.** OWASP beskriver indirekt prompt injection som instruktioner i externa källor, till exempel filer eller webbsidor, som ändrar modellens beteende. OWASP anger att RAG och finetuning inte fullt ut eliminerar risken och rekommenderar bland annat strikt outputvalidering, separering av extern data, minsta privilegium, human approval och adversarial testing ([OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)). Greshake et al. visar i en primär säkerhetsstudie hur hämtade instruktioner kan ändra applikationsfunktion, leda till datastöld och påverka API-anrop ([Greshake et al., 2023](https://arxiv.org/abs/2302.12173)).

**Hårda skyddskrav:**

- behandla all uppladdad och hämtad text som data, aldrig som system- eller utvecklarinstruktion;
- lägg källmaterial i tydligt avgränsade, icke-privilegierade contextfält;
- låt inte källtext välja verktyg, modell, mottagare, filväg, SQL-fråga eller publiceringsstatus;
- ge modellen inga direkta API-nycklar och inga verktyg som kan ändra elevdata eller publicera innehåll;
- allowlista verktyg och validera argument i kod före exekvering;
- kör parser/OCR och eventuella verktyg i sandbox med timeout, fil-/nätverksbegränsning och auditlogg;
- testa dolda instruktioner, Base64, flerspråkighet, bildtext, tabeller och payload splitting i varje regression suite;
- stoppa och eskalera när modellen försöker följa källans instruktioner eller lämnar grounding-kontraktet.

## 4. Genererade övningar och structured outputs

### 4.1 Vad leverantörernas API:er ger

Structured output/function calling gör ett maskinvaliderbart gränssnitt möjligt, men gör inte innehållet sant eller pedagogiskt. OpenAI beskriver JSON Schema-baserade Structured Outputs som schemaföljsamma och skiljer dem från JSON mode, samt beskriver function calling som en fler-stegsloop där applikationen själv exekverar och returnerar tool-resultat ([Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs), [Function Calling](https://platform.openai.com/docs/guides/function-calling)). Anthropic beskriver client tools, `tool_use`/`tool_result` och strict tool use för schemaföljsamhet ([Anthropic Tool Use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)). Mistral beskriver samma femstegsloop och `tool_choice`/`parallel_tool_calls` ([Mistral Function Calling](https://docs.mistral.ai/capabilities/function_calling/)). Googles Gemini API har motsvarande officiell structured-output och function-calling-dokumentation ([Gemini structured output](https://ai.google.dev/gemini-api/docs/structured-output), [Gemini function calling](https://ai.google.dev/gemini-api/docs/function-calling)).

**Viktig avgränsning:** schemaföljsamhet betyder att fälten kan parsas. Det bevisar inte att en fråga har ett entydigt svar, att distraktorerna är rimliga, att facit är källgrundat eller att bedömningen är rätt.

### 4.2 Versionerat aktivitetskontrakt

En förenklad domänmodell för specen:

```text
ActivityDraft {
  id, contentVersion, status: draft|approved|retired,
  objectiveIds[], subject, gradeBand, language,
  prompt, stimulus?, type,
  expectedMentalAction, difficulty, prerequisiteObjectiveIds[],
  answerSchema, referenceAnswer, scoringRules, rubric?,
  distractors[]: { text, misconceptionId, rationale },
  hintLevels[], feedbackPolicy, transferVariants[],
  accessibilityAlternatives[], sourceRefs[],
  generatedBy: { provider, modelId, modelVersion, promptVersion },
  validationReport, approvalHistory[]
}
```

### 4.3 Valideringspipeline

Validera i lager, med billig och deterministisk kontroll först:

1. **Schema:** JSON Schema/Pydantic/Zod-liknande servervalidering, `additionalProperties=false` där kontraktet kräver det, enumkontroll, längdgränser och identifierarvalidering.
2. **Domäninvarianter:** exakt ett huvudmål, godkänd målversion, språk/årskurs, svarstyp som matchar mental handling, minst ett bedömningskriterium och minst en källa när grounding krävs.
3. **Svarbarhet:** deterministisk solver, facitkontroll, enhetskontroll, symbolisk algebra eller körbar referenslösning där ämnet tillåter. Kontrollera att det inte finns två korrekta alternativ i en single-choice-fråga.
4. **Distraktorer:** varje distraktor ska mappa till dokumenterad missuppfattning eller metodförväxling, inte bara vara slumpmässigt fel. Testa permutation, position, längd och grammatisk ledtråd.
5. **Grounding:** verifiera källans version och citationens stöd för fråga, facit, feedback och eventuella förklaringar. Underkänn unsupported claim.
6. **Pedagogik:** kontrollera mental handling, förkunskapskrav, svårighetsnivå, transfervariant, kognitiv belastning och att feedbacken inte avslöjar svaret före försök.
7. **Säkerhet och likvärdighet:** moderera elevnära text, upptäck olämpligt eller diskriminerande innehåll, granska språk, namn, exempel och eventuella stereotypa antaganden. Detta får inte ersätta lärarens granskning.
8. **Kvalitet:** deduplicera mot befintliga frågor, kontrollera facit/feedback-konsistens, läsbarhet, alt-text, tangentbordsvariant och att prompten inte läcker systeminstruktioner.
9. **Mänsklig granskning:** visa validatorfynd och källor. Läraren godkänner inte en totalscore utan en konkret artefakt och dess evidens.

**Publiceringsregel:** en misslyckad kontroll får inte repareras genom att modellen själv får sista ordet utan ny validering. Efter automatisk eller manuell ändring skapas ny `contentVersion`.

## 5. Automatisk bedömning och LLM-as-judge

### 5.1 När deterministisk rättning räcker

Använd kod eller ämnesspecifik verifierare när facit kan uttryckas entydigt:

- single/multiple choice med godkänd optionsmängd;
- sant/falskt;
- numeriska svar med explicit tolerans och enhet;
- algebraiska uttryck efter kanonisering;
- matchning och ordning med definierade relationer;
- programmerings- eller beräkningsuppgifter med säkra testfall, om exekvering är isolerad.

LLM:n kan skriva en begriplig förklaring från den deterministiska bedömningen, men ska inte ändra den utan en separat flaggad process.

### 5.2 När LLM-bedömning kan hjälpa

Öppna svar, förklaringar, resonemang, textanalys och oväntade korrekta lösningar kräver ofta rubric och professionellt omdöme. LLM-as-judge kan vara ett skalbart beslutsstöd, men Zheng et al. fann position-, längd/verbosity- och self-enhancement-bias samt begränsningar i resonemang; deras överensstämmelse över 80 procent gäller specifika chatbotpreferenser och ska inte generaliseras till svenska elevers ämnesbedömning ([Zheng et al., 2023](https://arxiv.org/abs/2306.05685)). G-Eval visar både förbättrad korrelation med mänskliga bedömningar i vissa NLG-uppgifter och risk för bias mot LLM-genererad text ([Liu et al., 2023](https://arxiv.org/abs/2303.16634)).

**Beslut:** använd LLM-bedömning som rubricerad, kalibrerad triage och feedbackhjälp, aldrig som ensam beslutsfattare för betyg, progression över spärrar eller disciplinära åtgärder.

### 5.3 Kalibrering och dubbelkontroll

Varje öppet svar ska bedömas mot en rubric med observerbara kriterier, exempel på nivåer och explicit `insufficient_evidence`. Systemet ska spara:

- delkriterier, inte bara totalpoäng;
- elevens evidensutdrag som motiverar varje delkriterium;
- bedömarens confidence och abstention;
- modell, version, prompt, rubric-version och temperatur/sampling;
- lärarens korrigering och skäl;
- om svaret varit påverkad av språkstöd, hjälpmedel eller tidigare feedback.

Kalibrera mot ett stratifierat golden set med lärarbedömning: ämne, mål, språkvariation, korta/långa svar, vanliga missuppfattningar, svaga/starka svar, alternativa korrekta lösningar och adversarialt plausibla fel. Mät minst:

- överensstämmelse med två eller fler lärare, med inter-rater reliability där det är meningsfullt;
- confusion matrix per delkriterium, false-positive och false-negative;
- precision/recall för `needs_teacher_review`;
- confidence calibration, till exempel reliability diagram och ECE;
- skillnad mellan elevgrupper och svarslängder;
- stabilitet under omskrivning, språkbyte och svarspresentation;
- drift över modell-, prompt- och rubricversioner.

För riskfyllda öppna svar krävs minst två oberoende bedömningsvägar, till exempel regelbaserad kontroll plus LLM, eller två modeller med olika promptar följt av konfliktregel. Två LLM:er som delar samma bias är inte oberoende bevis. Vid konflikt, låg confidence, ny svarstyp eller hög konsekvens ska systemet avstå och skicka till lärare.

### 5.4 Human-in-the-loop

Läraren ska kunna:

- se råsvar, fråga, källa, rubric och automatisk motivering;
- ändra varje delkriterium och slutbeslut;
- korrigera facit och feedbackmall;
- markera att ett svar är en ny godkänd lösning;
- skicka en bedömning till golden set efter kvalitetssäkring;
- stoppa en övning eller hela content-versionen;
- se vilka elever som fått en felaktig eller ändrad bedömning och reparera deras kö.

Eleven ska få en pedagogisk feedbacksignal, inte en falsk exakthet. Vid osäker automatisk bedömning ska svaret säga att det behöver granskas eller be eleven förtydliga, inte låsa progression.

## 6. Säkerhet, barn och styrning

### 6.1 Relevanta ramverk och rättsliga signaler

NIST AI RMF är ett frivilligt ramverk med funktionerna Govern, Map, Measure och Manage, avsett att föra in tillförlitlighet i design, utveckling, användning och utvärdering ([NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework), [NIST Playbook](https://www.nist.gov/itl/ai-risk-management-framework/nist-ai-rmf-playbook)). För generativ AI finns NIST:s särskilda GenAI Profile kopplad från samma sida.

OWASP:s LLM Top 10 bör användas som hotmodell, minst för prompt injection, sensitive information disclosure, supply chain, data/model poisoning, improper output handling, excessive agency, system prompt leakage, vector/embedding weaknesses, misinformation och unbounded consumption ([OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)).

EU-kommissionens AI Act-sammanfattning beskriver utbildningsanvändningar som kan bestämma tillgång till utbildning eller utbildnings-/yrkesbana, exempelvis exam scoring, som high-risk use cases och nämner krav på riskhantering, datakvalitet, loggning/traceability, dokumentation, human oversight, robusthet, cybersäkerhet och noggrannhet ([EU AI Act overview](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai)). Exakt klassificering måste avgöras utifrån den faktiska avsedda användningen och gällande text, inte enbart produktnamnet.

EU:s riktlinjer för trustworthy AI lyfter human agency and oversight, technical robustness, privacy and data governance, transparency, diversity/non-discrimination, societal well-being och accountability/auditability ([EU Ethics Guidelines](https://digital-strategy.ec.europa.eu/en/library/ethics-guidelines-trustworthy-ai)). UNESCO:s vägledning för generativ AI i utbildning och forskning betonar ett humancentrerat, åldersanpassat och reglerat införande ([UNESCO Guidance for Generative AI in Education and Research](https://unesdoc.unesco.org/ark:/48223/pf0000386693)).

GDPR gäller personuppgifter i elevsvar, resultat, profiler, preferenser och lärarens bedömning. För svensk skola bör personuppgiftsansvar, rättslig grund, biträdesavtal, tredjelandsöverföring, lagringstid, åtkomst, DPIA och incidenthantering fastställas med huvudman/dataskyddsombud. IMY har särskild vägledning om personuppgifter om barn, dataskydd i skola, inbyggt dataskydd och automatiserade beslut ([IMY: GDPR för verksamheter](https://www.imy.se/verksamhet/dataskydd/introduktion-till-gdpr/), [IMY: personuppgifter om barn](https://www.imy.se/verksamhet/dataskydd/det-har-galler-enligt-gdpr/introduktion-till-gdpr/personuppgifter/personuppgifter-om-barn/)).

### 6.2 Dataklassning och minimisering

Minst fyra klasser bör finnas:

| Klass | Exempel | Standardrouting |
|---|---|---|
| A | publikt eller lärargodkänt anonymiserat kursmaterial | lokal eller extern efter policy |
| B | lärarens interna planering och skolans material | lokal först; extern endast godkänd leverantör/policy |
| C | elevsvar, resultat, preferenser, feedbackhistorik | lokalt som standard; extern endast minimerat och rättsligt godkänt |
| D | identifierare, skyddade uppgifter, känsliga elevförhållanden | lokal behandling eller ingen modellbehandling |

Krav:

- pseudonymisera profil-id innan extern modell;
- skicka inte namn, personnummer, elevgrupp eller rå elevtext om uppgiften kan lösas med abstraherade features;
- håll API-nycklar på servern i secret manager, aldrig i webbläsare eller prompt;
- kryptera transport och lagring, segmentera tenants/profiler och tillämpa least privilege;
- definiera retention per dataklass och radera även embeddings, cache, tracepayload, export och backup enligt policy;
- gör åtkomst, modellförfrågan, läraröverskrivning och publicering auditerbara;
- ge lärare och elev begriplig information om när AI används och med vilka begränsningar.

## 7. Modellorkestrering: lokal RTX 3090 och API-leverantörer

### 7.1 Arkitekturprincip

Bygg en provider-neutral adapter med samma interna kontrakt för `generate`, `structured_generate`, `embed`, `classify`, `judge` och `health`. Varje adapter ska översätta skillnader i schema, tool use, citations, tokenräkning, felkoder, rate limits och dataretention. Leverantörernas dokumentation ändras; logga därför faktisk modell-id/snapshot och capability flags vid varje anrop.

OpenAI dokumenterar Structured Outputs, function calling, semantic retrieval och evals; Anthropic dokumenterar tool use och strict tool schemas; Mistral dokumenterar function calling och modeller för bland annat embedding/OCR; Gemini har motsvarande structured output/function calling. Dessa dokument visar API-funktioner, inte att leverantören är bäst för pedagogisk generering eller bedömning.

### 7.2 Föreslagen initial routing, som hypotes

- **Lokalt på RTX 3090:** embeddings, PII-redaktion, dokumentklassning, enklare retrieval/reranking, deterministiska solvers och en lokalt installerad modell där benchmark visar tillräcklig kvalitet. Ingen prestanda eller modellstorlek antas från GPU-namnet.
- **OpenAI/Claude/Mistral/Gemini:** endast uppgifter som behöver deras validerade multimodala, resonemangs- eller tool/structured-output-kapacitet och som får skickas enligt data policy.
- **Teacher review/generation:** högkvalitetsmodell kan användas offline före publicering, men resultat går samma validator- och godkännandeflöde som lokala resultat.
- **Elevens synkrona svar:** använd förutgenererade/godkända aktiviteter och deterministisk rättning där möjligt. Fallback ska vara begriplig, inte tyst modellbyte.

Detta är en startarkitektur, inte ett kvalitetslöfte. Provider selection ska kunna ändras utan domänmodellens ändring.

### 7.3 Benchmark- och routingkrav

Gör en jämförande, versionslåst benchmark på samma prompts, källor, språk och seeds där API:erna tillåter det. Mät per uppgiftstyp, ämne, svårighet och dataklass:

- groundedness och citation precision/recall;
- schemaföljsamhet och post-validation failure rate;
- övningsbarhet: entydigt facit, svarbarhet, distraktorkvalitet, transfervariation;
- automatisk bedömning mot teacher golden set: agreement, FPR/FNR och abstention;
- bias/fairness över språk, svarslängd, tillgänglighetsväg och elevnivå;
- p50/p95/p99 end-to-end latency, time-to-first-token, timeout/error/rate-limit rate;
- tokens, kronor per arbetsflöde och kostnad per godkänd aktivitet, inklusive retries och judge calls;
- lokal GPU-minne, batchstorlek, throughput, thermal throttling, queue depth och driftkostnad;
- säkerhetsfynd från injection, data exfiltration, tool misuse och prompt-leak tester.

En route får inte väljas för att den har lägsta latency eller pris om den faller under fastställda miniminivåer för grounding, correctness eller safety. Ha separata budgets för synkron elevinteraktion, batchgenerering och lärargranskning.

### 7.4 Fallback och failure policy

- timeout: visa sparad aktivitet eller en deterministisk lokal aktivitet;
- provider error: retry med exponential backoff och idempotency key, sedan annan godkänd route;
- schemafel: reparationsanrop får ske högst en definierad gång och måste sedan gå till validator/abstention;
- groundingfel: ingen fri modelltext till elev; visa att underlag saknas eller skicka till lärare;
- modellbyte: märk trace och content-version; blanda inte bedömningar från modeller utan kalibrerad equivalence;
- circuit breaker vid ökade fel, kostnad eller säkerhetsfynd.

## 8. Adaptiv uppgiftsselektion

### 8.1 Separera tre mål

`mastery_need`, `preference_fit` och `novelty` är olika signaler:

- **Mastery need:** osäker eller svag evidens på ett mål/förkunskapsmål, särskilt efter spacing eller transfer.
- **Preference fit:** valt format, tillgänglighetsbehov, språk och tillfällig kontext.
- **Novelty:** nytt exempel, ny representation eller kontrast, men bara inom mål och svårighetsguardrails.

**Beslut:** hårda pedagogiska och säkerhetsmässiga constraints först; därefter prioriteras mastery need, spacing och transfer. Preference fit får bryta lika alternativ eller lösa åtkomst. Novelty får inte höja svårigheten okontrollerat.

### 8.2 När fler uppgifter behövs

Ett nästa-uppgift-beslut ska vara ett versionerat, inspekterbart objekt:

```text
SelectionDecision {
  objectiveId,
  evidenceUsed: [attemptIds, delayedResultIds, teacherSignals],
  need: remediation|retrieval|transfer|extension|none,
  reasonCode,
  selectedActivityId,
  excludedCandidates: [{ id, reason }],
  confidence,
  policyVersion
}
```

Fortsätt med fler uppgifter när minst ett av följande gäller:

- målet är due för spacing och saknar tillräcklig fördröjd evidens;
- eleven har en identifierad missuppfattning som nästa aktivitet direkt adresserar;
- eleven klarar igenkänning men inte fri återkallning, förklaring eller transfer;
- svaret är osäkert, ofullständigt eller bedömningen behöver lärargranskning;
- en progressionregel kräver flera oberoende evidenser, inte samma fråga flera gånger.

Avsluta eller byt mål när evidensen är tillräcklig, eleven kan transferera och ytterligare uppgifter bara upprepar samma yta. Visa skälet pedagogiskt för elev och lärare.

### 8.3 Bandit- och experimentrisker

Banditoptimering är inte en evidensbaserad genväg till bättre undervisning. Belöningen är fördröjd, lärandemålet kan vara ojämnt observerat, elever exponeras olika, och "engagemang", tid eller klick riskerar att bli proxy för lärande. Explorationsuppgifter kan dessutom ge onödiga misslyckanden och skapa orättvisa skillnader mellan grupper.

Om contextual bandit eller adaptiv experimentering införs senare krävs:

- golden holdout med fast innehåll och fördröjt transferprov;
- förregistrerade primära utfall och minsta coverage per mål, svårighet och elevgrupp;
- spärrar för max svårighet, minsta retrieval/spacing och rätt till lärarstyrd aktivitet;
- ingen optimering mot klick, sessionstid, streak, omedelbar rättprocent eller elevens självrapporterade rolighet ensam;
- safe exploration och kill switch;
- separat analys av access, avhopp, bias, learning gain och långsiktig retention;
- pilot med lärarkontroll innan elevpopulationen används som explorationsyta.

## 9. Observability, evals och regression

### 9.1 Traceability

Varje elevsynligt resultat och varje batchartefakt ska ha ett `trace_id` och minst följande metadata:

- request/session/profile pseudonym och tenant;
- activity, objective, content/source version;
- document/chunk ids, locatorer, ranks och scores;
- prompt template/version/hash och policy-version;
- provider, model-id/snapshot, adapter-version och parameters;
- tool calls, arguments-hash, tool result status och approval gates;
- schema/semantic/safety validator-resultat;
- input/output token counts, cost estimate, queue/network/model latency;
- retry, timeout, fallback och error code;
- output hash, published version och human override.

Logga inte fulla elevsvar eller källor i observability-systemet som standard. Ha separata åtkomstskyddade pedagogiska evidensposter och redaktera loggar. OpenTelemetrys trace-spec ger en leverantörsneutral grund för sammanhängande spans och context propagation ([OpenTelemetry Trace](https://opentelemetry.io/docs/specs/otel/trace/)); NIST och EU:s trustworthy-AI-principer stödjer traceability och auditability som styrningsbehov, men inte en färdig skolimplementation.

### 9.2 Golden set

Golden set ska versionshanteras och innehålla:

- källdokument och exakta godkända source refs;
- mål, förkunskaper, avsedd mental handling och ämnesnivå;
- godkända och förbjudna övningar;
- facit, alternativa korrekta lösningar, distraktorer och feedback;
- öppna elevsvar med lärarbedömning per rubric-kriterium;
- expected abstention/teacher-review cases;
- injection, OCR-fel, tabell/figur, språkvariation och tillgänglighetsfall;
- fördröjda och transfererande testfall där sådana kan samlas.

Håll test, validering och produktionsdata åtskilda. Undvik läckage där exakt golden-fråga återanvänds som elevövning och därefter ger falskt höga evalresultat.

### 9.3 Evals

OpenAI:s evals-dokumentation beskriver testdata med schema, testing criteria, human labels och återkommande eval runs, men aktuell dokumentation anger också en planerad nedstängning av den specifika Evals-plattformen. Använd därför principerna och ett internt evalformat som inte låser specen till en leverantör ([OpenAI Evals guide](https://platform.openai.com/docs/guides/evals)).

Minsta regression suite:

| Område | Mätetal/krav |
|---|---|
| Retrieval | recall@k, MRR/nDCG, coverage av godkänd källa, metadatafilter-fel |
| Grounding | citation precision/recall, entailment, unsupported-claim rate, abstention |
| Struktur | schema-pass, parserfel, repair rate, unknown enum/extra field |
| Övningskvalitet | entydigt facit, solver-pass, distraktor/missuppfattning, läsbarhet, transfervariation |
| Bedömning | teacher agreement, FPR/FNR, calibration, abstention, gruppskillnader |
| Pedagogik | delayed retrieval, transfer, repair completion, feedback-use, avhopp |
| Säkerhet | injection success, data leakage, tool misuse, prompt leakage, unsafe output |
| Drift | modell-/promptversion, latency p50/p95, error rate, token/cost, queue depth |

En release ska blockeras om safety, provenance, schema eller deterministisk correctness försämras över tröskel även om engagement eller kostnad förbättras. Pedagogisk A/B-testning ska jämföra samma mål och innehåll, ha direkt och fördröjt test, mäta transfer och förregistrera primärt utfall. Detta följer samma försiktighet som befintligt forskningsunderlag i [`learning-interactions-research.md`](learning-interactions-research.md).

### 9.4 Budgetar

Definiera innan pilot:

- `availability`: timeout/error budget för synkron elevväg;
- `latency`: p50/p95 per operation och separat first-token/complete;
- `cost`: kronor per elevsession, per godkänd aktivitet och per bedömd öppet svar;
- `quality`: minsta grounding, deterministisk correctness, teacher agreement och delayed-transfer;
- `safety`: noll accepterade policykritiska injection/dataexfiltration-fynd i releasekandidat;
- `human_review`: maximal kötid och andel svar som kräver lärare.

OpenAI:s produktions- och latencyguidance beskriver att modell, outputlängd, antal requests, caching, batching, streaming och retries påverkar kostnad/latency och rekommenderar att välja genom mätning snarare än antaganden ([Production best practices](https://platform.openai.com/docs/guides/production-best-practices), [Latency optimization](https://platform.openai.com/docs/guides/latency-optimization)). Dessa råd är leverantörsspecifika men principen om mätning gäller hela orkestreringen.

## 10. Specacceptans: första release

En första release bör inte godkännas förrän följande kan demonstreras:

- varje elevsynlig aktivitet har godkänt mål, källa/version och publiceringshistorik;
- en uppladdad fil med dold instruktion kan inte ändra systempolicy, anropa verktyg eller publicera innehåll;
- samma mål kan tränas via minst text/tangentbord och en relevant alternativ väg;
- objektiva svar rättas deterministiskt och med tester för tolerans/enhet/alternativa korrekta uttryck;
- öppna svar visar osäkerhet, rubric, evidens och lärarens överskrivning;
- feedback leder till reparation eller ny tillämpning och blockerar inte eleven på en enda maskinpoäng;
- repetitionskön använder fördröjd evidens och har synlig reason code;
- modellbyte, promptbyte, källbyte och fallback syns i trace och kan återskapas;
- golden set, regression suite, cost/latency/error budgets och kill switch finns före elevpilot;
- DPIA-/dataskyddsbeslut, biträdes-/leverantörsbeslut, retention, behörigheter och incidentflöde är dokumenterade av ansvarig organisation;
- pilotens primära utfall är delayed retrieval och transfer, inte klick, sessionstid eller omedelbar rättprocent.

## 11. Källor

### Befintligt lärandeforskningsunderlag

- [Wigren.Studdy: learning-interactions-research.md](learning-interactions-research.md), med källor om retrieval, spacing, feedback, worked examples, self-explanation, multimedia, adaptiv tutoring och begränsningar i UI-former.
- [Adesope, Trevisan & Sundararajan (2017), practice testing meta-analysis](https://doi.org/10.3102/0034654316689306).
- [Cepeda et al. (2006), distributed practice review and quantitative synthesis](https://doi.org/10.1037/0033-2909.132.3.354).
- [Shute (2008), Focus on Formative Feedback](https://doi.org/10.3102/0034654307313795).
- [EEF, Teacher Feedback to Improve Pupil Learning](https://educationendowmentfoundation.org.uk/education-evidence/guidance-reports/feedback).
- [Brunmair & Richter (2019), interleaved learning meta-analysis](https://doi.org/10.1037/bul0000209).
- [Pashler et al. (2008), Learning Styles](https://doi.org/10.1111/j.1539-6053.2009.01038.x).
- [Ma et al. (2014), intelligent tutoring systems meta-analysis](https://doi.org/10.1037/a0037123).

### RAG, security och evaluation

- [Lewis et al. (2020), Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401), primär NeurIPS-studie.
- [Greshake et al. (2023), indirect prompt injection](https://arxiv.org/abs/2302.12173), primär säkerhetsstudie.
- [Zheng et al. (2023), Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena](https://arxiv.org/abs/2306.05685), primär evalstudie.
- [Liu et al. (2023), G-Eval](https://arxiv.org/abs/2303.16634), primär evalstudie.
- [Liang et al. (2023), Holistic Evaluation of Language Models](https://arxiv.org/abs/2211.09110), benchmark/ramverk med multi-metric evaluation.
- [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/).
- [OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/).
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework).
- [NIST AI RMF Playbook](https://www.nist.gov/itl/ai-risk-management-framework/nist-ai-rmf-playbook).
- [OpenTelemetry Trace specification](https://opentelemetry.io/docs/specs/otel/trace/).

### Officiella API- och infrastrukturdokument

- [OpenAI Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs).
- [OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling).
- [OpenAI Retrieval](https://platform.openai.com/docs/guides/retrieval).
- [OpenAI Evals](https://platform.openai.com/docs/guides/evals).
- [OpenAI Production best practices](https://platform.openai.com/docs/guides/production-best-practices).
- [OpenAI Latency optimization](https://platform.openai.com/docs/guides/latency-optimization).
- [Anthropic Tool Use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview).
- [Anthropic Models overview and model IDs](https://platform.claude.com/docs/en/models/overview).
- [Mistral Function Calling](https://docs.mistral.ai/capabilities/function_calling/).
- [Mistral Models overview](https://docs.mistral.ai/getting-started/models/models_overview/).
- [Google Gemini structured output](https://ai.google.dev/gemini-api/docs/structured-output).
- [Google Gemini function calling](https://ai.google.dev/gemini-api/docs/function-calling).
- [NVIDIA GeForce RTX 3090 product page](https://www.nvidia.com/en-us/geforce/graphics-cards/30-series/rtx-3090/).

### Barn, utbildning och EU

- [European Commission, AI Act overview](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai).
- [Regulation (EU) 2024/1689, official EUR-Lex text](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689).
- [European Commission, Ethics Guidelines for Trustworthy AI](https://digital-strategy.ec.europa.eu/en/library/ethics-guidelines-trustworthy-ai).
- [UNESCO, Guidance for Generative AI in Education and Research](https://unesdoc.unesco.org/ark:/48223/pf0000386693).
- [IMY, introduktion till GDPR för verksamheter](https://www.imy.se/verksamhet/dataskydd/introduktion-till-gdpr/).
- [IMY, personuppgifter om barn](https://www.imy.se/verksamhet/dataskydd/det-har-galler-enligt-gdpr/introduktion-till-gdpr/personuppgifter/personuppgifter-om-barn/).
- [European Commission, Digital Education Action Plan](https://education.ec.europa.eu/focus-topics/digital-education/actions/plan).
