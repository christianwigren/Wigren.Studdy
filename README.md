# Wigren.Studdy

Wigren.Studdy är en planerad webbaserad digital lärare och övningsledare för grundskolan. Applikationen ska hjälpa elever att öva, få återkoppling, reparera svar och återkomma till kunskap över tid.

## Börja här

Läs [huvudspecifikationen](docs/main-specification.md) först. Den förklarar produktens syfte, pedagogiska flöde, arkitektur och hur alla andra dokument hänger ihop.

## Projektstatus

Repot är i dokumentations- och designfas. Det finns ännu ingen körbar applikation, installerad frontend eller backend. De nuvarande filerna beskriver produktens pedagogik, data, LLM-motor, visuella språk och ämnesunderlag.

## Dokumentation

### Produkt och pedagogik

- [Huvudspecifikation](docs/main-specification.md)
- [Digital lärare och övningsledare](docs/digital-teacher-interaction-spec.md)
- [LLM-driven övningsmotor](docs/exercise-engine-spec.md)
- [Profiler och användare](docs/profiles-and-users-spec.md)
- [Styleguide](docs/application-style-guide.md)

### Forskning

- [Forskningsunderlag: lärinteraktioner](docs/learning-interactions-research.md)
- [Forskningsunderlag: LLM-övningsmotor](docs/exercise-engine-research.md)

### Innehållsresurser

- [Årskurs 8: ämnesöversikt och Lgr22](resources/knut/8th-grade/README.md)
- [Alla årskurs 8-resurser](resources/knut/8th-grade/)

## Repo-struktur

```text
.
├── docs/
│   ├── main-specification.md
│   ├── digital-teacher-interaction-spec.md
│   ├── exercise-engine-spec.md
│   ├── exercise-engine-research.md
│   ├── profiles-and-users-spec.md
│   ├── application-style-guide.md
│   └── learning-interactions-research.md
├── resources/
│   └── knut/
│       └── 8th-grade/
│           ├── README.md
│           └── <ämneskataloger>/
└── README.md
```

## Utvecklarprinciper

- Börja med huvudspecifikationen och följ läsordningen där.
- Håll profil- och åtkomstlogik separerad från interaktionslogik.
- Knyt varje övning till ett versionerat kunskapsmål och en källa.
- Publicera inte LLM-genererat material utan schema-, käll-, pedagogik- och säkerhetsvalidering samt lärargodkännande enligt övningsmotorns spec.
- Använd deterministisk rättning när svaret kan verifieras med kod eller ämnesspecifik logik.
- Behandla LLM-bedömning av öppna svar som beslutsstöd med möjlighet att avstå och skicka till lärare.
- Lägg aldrig API-nycklar i frontend, elevprofil eller prompt som kan nås av klienten.
- Separera Nils och Knuts data på serversidan, inte bara genom frontendlogik.
- Ändra inte ämnesmål eller källsammanfattningar utan att kontrollera aktuell Skolverket-källa.
- Följ styleguiden för färger, typografi, komponenter och tillgänglighet.

## Utvecklingsläge

Det finns inga standardkommandon för build, test eller start ännu. När kod läggs till ska den här sektionen kompletteras med:

- installationssteg,
- utvecklingsserver,
- test- och lintkommandon,
- miljövariabler utan hemliga värden,
- databas- och migrationssteg,
- modell- och providerkonfiguration.

## Ändra dokumentationen

Ändra den mest specifika dokumentet först. Uppdatera sedan [huvudspecifikationen](docs/main-specification.md) om ändringen påverkar produktens helhet, dokumentkarta eller prioriteringar.
