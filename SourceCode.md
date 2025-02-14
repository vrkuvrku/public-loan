# Loan modul - zdrojové kódy

Popis zdrojových kódů se týká primárně TypeScript verze, i když vychází
z Perl varianty.

<https://www.youtube.com/watch?v=Uo3cL4nrGOk>

### Perl verze

iSpis Loan byl původně součástí iSpis CRM, následně byl z iSpis CRM oddělen
do samostatného modulu (s nímž sdílí část zdrojových kódů). Loan modul
je tvořen `perl/Loan.pm` a `perl/Loan/` složkou, zbytek v `perl/` je kód
sdílený s CRM. Úpravy Perl verze je nutno provádět v CRM modulu,
odkud se automaticky přenesou do tohoto repozitáře.

### TypeScript verze

ORM vrstva a pomocné běhové knihovny jsou v TypeScript verzi je psány nanovo.

Business logika je z `perl/Loan/` převáděna transpilací do JavaScriptu
(pomocí tool/p2js.pl nástroje) a následně ručně opravována (syntaktické
chyby, přidání typů, testy, postupné zavádění do produkčního prostředí).

## 1. Pomocná část, bez business logiky

- `controller` - pro spouštění funkcí jako HTTP API metod nebo z CLI
- `db/` - Loan-specific ORM databázová abstrakce
- `lite/` - nezávislé pomocné třídy
- `lite/Money`, `lite/Osoba`, - memory only objekty, pro CZ/SK specifika
- `runtime` - často používané zkratky

## 2. Databázové objekty

Pro databázové tabulky jsou ve `db/*` vytvořeny ORM třídy, doplněné
o základní logiku datového modelu (ale bez business logiky).

- `Client` - databáze je multi-tenant - v jedné databázi můžou být data více poskytovatelů
- `Login` - uživatel systému, typicky zaměstnanec klienta (poskytovatele)
- `Project` - obchodní případ - žádost, pak půjčka (dle stavu)
- `Person` - evidované osoby (případ může mít víc osob, osoba víc případů)
- `PPerson` - (ProjectPerson) mapování osob na případy (typicky vždy jen jeden žadatel)
- `Lustrace` - výsledek ověření osoby žadatele v externím rejstříku (exekuce, ISIR,...)
- `PE` - (ProjectEntry) záznam týkající se případu (poznámka, úkol, dokument,...)
- `LeadEvent` - záznam kolem přijaté žádosti od affiliate partnera, pro reporting
- `Invoice` - pohledávka mezi žadatelem a poskytovatelem
- `Debt` - část pohledávky, její typ a výše (jistina, úrok, poplatek, pokuta,...)

- `BankStatement` - integrace na banku poskytovatele - metadata bankovního výpisu
- `BankTxn` - transakce na bankovním výpisu poskytovatele (read-only)
- `Paid` - mapování (rozpad) transakce na části pohledávky

## 3. Business logika - Loan složka

Business logika neobsahuje nové třídy, pracuje většinou s ORM objekty, případně volá funkce iSpis CRM, případně iSpis CRM volá business logiku (skrz mechanizmus triggerů, custom akcí a custom proměnných v šablonách).

Funkce jsou po souborech rozdělené víceméně volně, jen tématicky.

- `FE` - API pro webový front-end (a pár web-hooků třetích stran)
- `CRM` - API pro iSpis CRM - stav žádosti/půjčky, "tlačítka" v CRM
- `Affil`, `Gateway` - API pro partnery ("leadaře") pro přijímání nových leadů
- `PayMaker` - API pro platební roboty

- `IS` - vzdálené volání služeb iSpisu, plus zpracování událostí zasílaných z iSpis CRM

- `Vars` - výpočet proměnných / výrazů používaných v šablonách

- `Config` - konfigurák (čten přes API z iSpis CRM), plus pár vlastností produktu

- `Project` - loan-specific nadstavba nad DB/Result/Project
- `Data` - stav a vlastnosti žádosti nebo půjčky (read-only)
- `Check` - finální kontroly správnosti žádosti

- `Ext` - integrace na třetí strany (specifické, nepatřící do CRM)
- `NRKI` - měsíční reporting žádostí do nebankovního úvěrového rejstříku
- `ScoreCard` - volání externího decision enginu pro vyhodnocení žádosti
- `IdCard` - kontrola občanky na fotce, OCR nebo bankid
- `TagPE` - (bylo `Txn`) zpracování transakcí na výpisu z účtu nebo z PSD2 (Kontomatiku)
- `Income` - výpočet příjmu žadatele
- `Repeat` - založení opakované žádosti nebo opakovaného úvěru

- `Appl` - metody týkající se žádosti, plus odesílání zpráv
- `Proving` - metody týkající se žádostí (dokládání příjmů, ověření identity)
- `Screen` - dohledávání informací o žadateli v rejstřících
- `Complete` - ověřování formální kompletnosti (úplnosti) žádosti
- `Evaluate` - metody kolem rozhodovacího procesu
- `Payout` - vyplacení schválené půjčky (zaevidování pohledávky apod)
- `Enjoying` - přijetí platby, částečné úhrady, prodloužení splatnosti
- `Collect` - předání dat externí inkasní agentuře (úvěry po splatnosti)
- `DebtSale` - export pro soudní žalobu (e-PR) nebo pro prodej pohledávek

- `Automat` - automatické hromadné akce (remarketing, early inkaso,...)
- `Daily` - účtování opakovaných doplňkových služeb, účtování úroků a pokut
- `Update?` - počítání pokut?? (NEPOUZITO?)

- `Fix` - pro ad-hoc opravy v datech
- `FixRecalc` - obsolete (rozdělení transakcí na úhrady částí pohledávek)
- `Metrics` - výpočet businessových výkonnostních KPI statistik
