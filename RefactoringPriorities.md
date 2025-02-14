# RefactoringPriorities

Dump nějakých poznámek co vznikajících v průběhu projektu, co se můžeme
postupně refaktorovat.

Pozor: Může být outdated.

### drobnosti co nestoji za rec

- deno fmt, deno.json: "fmt": { "options": { "semiColons": false } }
- opravit viceradkove /\*\* ... \* jsdocs (parser vidi jen posledni radek)
- Project.isInState(oneOf: ProjectStates[]) misto state.match
- opravit POD formatovani v jsdoc

### chyby

- projit ///
- bezpecnost (connectClientId)
- bug: Person.createPerson - Lustrace.pairNewPersonByBirthNo (?)
- bug FE: nefunguje zruseni presta

### specificke ohranicene veci (nemajici globalni vyznam)

- inputs.sql - je pomale, potrebuje ulozene fields
- tag/.ts - pomale (moc pravidel, moc transakci), nejlip prepsat do rustu
- zdokumentovat fields, labels,... viz [GlossaryTerms.md](./GlossaryTerms.md)
- zjednodusit udalosti CRM kolem transakci, mozna i distribuce v loan modulu?
- SettingsGetLoanConfig/GetEnvironment z CRM do Loan modulu
- Data/\* exporty do Data.ts, neexportovat interni fce (napr FE.doFileUpload)
- log.\* API nejak lip (args..., KeyValue, fatal.ident)

### drobna vylepseni kodu

- vyhodit pozustatky perlu (středníky, throw \n, zavorky, ||'')
- predelat signatury na Opt (runScreening, missingCosi)
- seznam deno závislostí do jednoho souboru, revize
- určit a jednorázově splnit "ukazatele kvality kódu"
- zavést jmenné konvence funkcí (find vs list, OrFail, OrNull,...)

### vetsi zmeny

- "stavové" fce/soubory jinak, do nějakého stavového automatu?
- popřesouvat funkce do souborů, kam patří, zejména když jsou použity jen 1x
- zrušit (limitovat) přístup k FS (jen --allow-read=resources, write=log,tmp?)
- zlepsit konfiguraci a customizaci (ne isFFCZ, custom mechanizmy)
- číst kompletní údaje o případě jedním selectem (viz db/Project.ts:10)

### zasadni zmeny

- modularizace, zavislosti, interni API, interfaces
- hierarchie souborů a učesání filesystému (vše v db, lib, buss.l. CamelCase)
- services vs helpers (ScoreCard.getResult vs finishTasks)
- n-tier hierarchie systemu (a tomu odpovidajici struktura zdrojaku)
- zavislosti mezi moduly, callbacky (napr ExtBankId dostane fnPostId) nebo DI

### rozvoj

- ScreeningSave - CRM by nemela vracet vysledek volani pres DB
- fronty mezi Loan modul a CRM (zprávy, lustrace)
- reimplementace služeb CRM (viz CRMAPIMethod)
- rozdeleni Project na Appl a Loan (casem dve microservices s vlasni db)

### deployment

- deploy jinak než ve screenu
- hot deployment support (restart bez killnuti bezicich requestu)
- automatizace testů (aktualne poustene na produkci v 7:00 a 16:00, i FULL=1 e2e)
- testovací instance, DB včetně CRM
- viz starší poznámky v [DeploymentStrategy.md](./DeploymentStrategy.md)

### maintenance

- urcit code-style a hlidat ho
- deno lint
- coverage - aktualne 45 %
- npm install -g madge && madge --circular src/controller.ts
- vice businessovych testu (-able, psd2-timing, spl/mtp, lead-ownership)
- hlídat ukazatele kvality kódu (délka fcí, komentáře exportů, test coverage)

### DX

- seznameni: misto FF Flekace mit testovaci zadost v tester DB
- vscode config
- generator jsdoc dokumentace
