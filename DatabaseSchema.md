# Database schema

Celý systém běží nad PostreSQL databází. Některé moduly spolu komunikují
přes API, některé přistupují přímo do DB.

iSpis CRM používá celou databázi, Loan modul pouze její část - pouze
některé tabulky.

Je použit tzv. _multi-tenant_ model, kdy v jedné databázi jsou data více
subjektů - klientů iSpis CRM systému, resp. poskytovatelů úvěrů Loan modulu.

Každá služba Loan modulu potřebuje vědět, který klient ji volá.

Seznam klientů je v tabulce `client`, každá tabulka pak má sloupec client
jako cizí klíč do `client` tabulky.

Databáze používá tzv. _row level security_ (RLS), kdy databázový účet
jde omezit, že vidí pouze na data zvoleného klienta
(viz taky [Row Security Policies](https://www.postgresql.org/docs/current/ddl-rowsecurity.html])).
Pokud poskytovatel dostane do DB přístup, je mu vytvořen účet, který vidí
pouze na jeho data.

Zde jsou uvedeny jen tabulky používané Loan modulem, a z nich jen
důležité sloupce. Kompletní seznam viz SQL DDL.

ERD diagram viz [EntityRelations](EntityRelations.md),
ORM popis viz [SourceCode](SourceCode.md).

## `project`: případ (žádost nebo úvěr)

- id - databázové ID (neplést s RefNo)
- created
- path - RefNo
- state - stav případu
- labels - štítky (tagy) případu
- fields - údaje případu (klíč-hodnota)

## `person`: všechny evidované osoby (žadatelé, affil partneři,...)

- id
- created
- birth\_no - RČ
- code - u CZ/SK totéž co RČ
- first\_name
- last\_name
- birth\_date
- phone
- email
- meta_json - údaje osoby (klíč-hodnota)

## `lustrace`: výsledek ověření žadatele v rejstřících

- id
- dateseq
- c\_profile
- birth\_no
- person
- errors
- input\_json - odeslaná data
- output\_json - výsledek dotazu

## `project_person`: vztah osoby k případu

- id
- created
- kind - u Loan modulu vždy "client", a vždy jedna osoba u případu
- project - ID případu
- person - ID osoby

## `invoice`: pohledávka

- id
- created
- kind
- project\_path
- vs - identifikátor pohledávky, typicky RefNo + "01", "02",...
- supplier - osoba poskytovatele
- purchaser - osoba žadatele

## `debt`: část pohledávky

- id
- created
- project\_path
- invoice
- kind - jistina, poplatek, úrok, pokuta,...
- amount\_cents
- meta\_json - další podrobnosti (klíč hodnota)

## `project_entry`: událost na případě

- id
- created
- project
- kind - note, file, task, form, sent, reply, cron
- note\_text - u všech
- file\_type - jen u file
- file\_nearline - obsah souboru je uložen mimo PostgreSQL
- form\_json - údaje z formuláře (typicky telefonátu)
- task\_start - jen u task,...
- task\_verb - typ úkolu (podtyp je v note\_text)
- snoozed - úkol je posunut
- task\_finished - úkol je hotov
- meta\_json - údaje události (klíč-hodnota)

## `bank_txn`: transakce na účtu poskytovatele

- id
- created
- statement - u API integrace na banku (FIO) nepoužito
- bank\_id - bankou evidovaný identifikátor
- party\_name - protistrana
- reference - VS
- paid\_cents
- paid\_date
- project - NULL u nezatřízených
- done - transakce je plně zaúčtovaná, vyřízená

## `paid`: úhrada - část transakce připsaná na danou část pohledávky

- id
- created
- txn - pozor, existuje i txn\_id, a historicky jsou tam i NULL
- debt
- paid\_cents
- payer - osoba žadatele
- invoice
- project\_path

## `lead_event`: záznam o přijaté žádosti

- id
- created
- partner
- kind - druh události
- birth\_no
- project\_path - může být NULL
- meta_json - údaje od partnera (klíč-hodnota)
- status - výsledek zpracování
- correlation\_id - s kterým jiným lead\_eventem souvisí

## `template`: šablona pro komunikaci

- id
- created
- name
- format
- sender
- event\_trigger
- filter
- last\_used
- used\_count
