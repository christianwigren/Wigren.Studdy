# Spec: profiler och användare

**Status:** Första produktgrund
**Ägare:** Plattform och data
**Relaterad spec:** [Digital lärare och övningsledare](digital-teacher-interaction-spec.md)

## 1. Syfte

Detta dokument samlar alla krav för webbappens användare, elevprofiler, åtkomst och profilspecifik lagring. Interaktionsspecen beskriver lärandet och övningarna; denna spec beskriver vem som får komma åt vilken data.

## 2. Begrepp

### Användare

En användare är den identitet som får åtkomst till webbappen. En användare kan i framtiden ha ett konto och flera roller.

### Profil

En profil är elevens separata studieyta. Profilen äger progression, svar, repetitionskö, personliga inställningar och sparade studieprodukter.

En profil är inte samma sak som en användare. I version 1 kan två profiler finnas i samma lokala studieinstallation utan full kontoinloggning. Datamodellen ska ändå vara redo för användare och behörigheter.

### Roll

En roll beskriver vad en användare får göra:

- **Elev:** arbetar endast med aktiv profil och ser sin egen data.
- **Lärare:** kan tilldelas åtkomst till valda elevers data och granska svar.
- **Administratör:** hanterar användare, profiler, åtkomst och lagringslivscykel.

Rollerna ska inte införas implicit genom att någon kan välja Nils eller Knut i profilväljaren.

## 3. Version 1

Version 1 ska ha exakt två elevprofiler:

| ID | Visningsnamn |
|---|---|
| `nils` | Nils |
| `knut` | Knut |

Profil-ID:n ska vara stabila och användas som relation på all elevrelaterad data. Visningsnamn får ändras utan att progressionen byter ägare.

## 4. Profilväljare

- Profilväljaren ska visas innan elevens innehåll öppnas.
- Den aktiva profilen ska vara tydlig under hela sessionen.
- Det ska gå att byta profil utan att logga ut ur webbappen.
- Profilbyte ska rensa eller byta aktiv profil, klientcache, rekommendationer och osparade utkast säkert.
- Ett byte får inte visa föregående profils svar, statistik, autosvar eller personliga inställningar.
- Om det finns osparade data ska användaren få spara, kassera eller avbryta bytet.
- Profilväljaren ska inte exponera känsligare data än visningsnamn.

## 5. Webbarkitektur och lagring

- Programmet ska vara en responsiv webbapp för dator, surfplatta och mobil.
- Webbläsaren är klient; en server/API är källa till sanning för användare, profiler och elevdata.
- Klientcache eller `localStorage` får användas för tillfällig offlinefunktion, men får inte vara enda permanenta lagringen.
- Webbappen ska visa om data är sparad, väntar på synkronisering eller endast finns lokalt.
- Skrivningar ska vara idempotenta eller ha versionskontroll så att dubbelklick och återförsök inte skapar dubbla försök.
- Nätverksfel får inte tyst kasta bort elevsvar. Osynkade poster ska märkas och kunna skickas igen.
- Servern ska kunna exportera och radera data per profil utan att påverka den andra profilen.

## 6. Dataseparation

Nils och Knut är helt separata studieytor:

- Nils får aldrig se Knuts svar, progression, rekommendationer, personliga inställningar eller evidens.
- Knut får aldrig se Nils motsvarande data.
- Rekommendationer, repetitionsköer, mastery-status och statistik beräknas per profil.
- Delat kursinnehåll och aktivitetsmallar får vara gemensamma.
- Elevförsök, resultat, råsvar, feedbackhistorik och sparade produkter får aldrig vara gemensamma.
- Backup, export, analys och AI-kontext ska behålla profilgränsen.

Alla läs- och skrivoperationer ska vara profilscopade på serversidan. Klienten får inte kunna läsa en annan profils data genom att ändra ett ID i en URL eller request.

## 7. Datamodell

### `User`

- `id`
- `roles`
- `createdAt`
- `updatedAt`
- `status`

### `Profile`

- `id` (`nils` eller `knut` i version 1)
- `displayName`
- `createdAt`
- `updatedAt`
- `settings`
- `privacyPreferences`

### `ProfileMembership`

Användarens rätt till en profil.

- `userId`
- `profileId`
- `role`
- `permissions`
- `createdAt`
- `revokedAt`

### `PersonalData`

- `profileId`
- `displayName`
- valfria inställningar, exempelvis språk, textstorlek och hjälpmedel
- `createdAt`
- `updatedAt`

### Profilägda pedagogiska poster


- `Attempt`
- `EvidenceArtifact`
- `ReviewItem`
- progress per `LearningObjective`
- feedbackhistorik
- rekommendationer och elevens repetitionskö

`profileId` ska vara en serverkontrollerad relation, inte enbart ett fält som klienten skickar och servern litar på.

## 8. Personlig data och integritet

- V1 ska lagra minsta möjliga identitetsdata, exempelvis visningsnamn och valfria inställningar.
- Progression och elevsvar är personliga data och ska ha en tydlig profilägare.
- Råa elevsvar ska bevaras separat från automatiska tolkningar och lärarbedömningar.
- Automatisk bedömning får aldrig skriva över råsvaret.
- Ingen profil får slås ihop med en annan i analys, rekommendationer eller export.
- Lärar- och administratörsåtkomst ska vara explicit tilldelad och återkallelig.
- Offentlig ranking eller jämförelse mellan Nils och Knut ska inte finnas som standard.
- Radering ska kunna genomföras per profil och ska även hantera tillhörande cache, exportmarkeringar och backupens gallringspolicy.

Autentisering, konton och exakt lagringstid är senare produktbeslut, men datamodellen ska från början tåla att den lokala profilväljaren ersätts eller kompletteras med inloggning.

## 9. API- och säkerhetsinvarianter

Följande ska alltid vara sant:

1. En elevrelaterad post har exakt en profilägare.
2. En användare kan bara läsa en profil genom en aktiv behörighet.
3. En klientrequest kan inte utöka sin behörighet genom att byta `profileId`.
4. En profilväxling påverkar inte serverns åtkomstkontroll.
5. Ett svar som sparats för Nils kan aldrig returneras i Knuts elevvy.
6. En återförsökt skrivning skapar inte ett andra försök eller en andra progresshändelse.
7. Alla ändringar i åtkomst ska kunna loggas.

## 10. Acceptanskriterier

- När Nils väljs visas Nils profil och endast Nils progressdata.
- När Knut väljs visas Knuts profil och endast Knuts progressdata.
- Ett svar som lämnas i Nils profil syns inte efter byte till Knut.
- Ett svar som lämnas i Nils profil finns kvar efter omladdning av sidan.
- Ett tillfälligt nätverksfel visar att svaret inte är synkroniserat och förlorar inte svaret tyst.
- En direkt request med Knuts `profileId` från Nils klient nekas av servern.
- Radering av Nils profil raderar inte Knuts profil eller data.
- En export för Nils innehåller inte Knuts data.
- Test kan verifiera att rekommendationer och repetitionsköer utvecklas separat.

## 11. Öppna beslut

- Ska version 1 ha lokal profilväljare eller autentiserade användarkonton?
- Vilken användare får växla mellan Nils och Knut?
- Ska lärare kunna se båda profilerna, och krävs samtycke för det?
- Vilken databas och vilken backup-/gallringspolicy ska användas?
- Ska offlinearbete stödjas i första versionen eller endast tydlig felhantering?
- Vilken export ska erbjudas: JSON, CSV, PDF eller lärarrapport?
