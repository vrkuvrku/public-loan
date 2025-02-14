# Glossary - slovník pojmů

## Obecné termíny

- glossary - slovník/legenda - používané pojmy, které nemusí být všem jasné
- application - žádost
- screening - lustrace - ověření žadatele v rejstřících
- lender - klient / dlužník / žadatel o úvěr ?
- ? - rozložená splátka
- ? - odložená splátka
- presto - expresní vyplacení - půjčka odeslána klientovi okamžitou platbou, jinak může být nějakou dobu uměle zpožděna
- insurance - klidné spaní - odklad splatnosti o 2 měsíce, pokud doloží důvod
- infosms - info SMS servis - odesílá víc SMS výzev
- prolongation - odložení splatnosti může posunout termín vrácení půjčky, max 5x
- evaluation - posouzení žádosti
- approve - schválení
- rejected - zamítnutí
- ? - exekuce (brit. an. "be in bailiff" ?)
- ? - insolvence
- ? - první žádost/půjčka
- ? - opakovaná půjčka
- ? - pohledávka
- ? - příslušenství
- delinquent - má nějaký KO záznam v nějakém rejstříku (včetně naší historie)
- underwritting - ?
- disbursed amount - vyplacená jistina
- grace period - neúčtování pokut při splacení do nějaké doby od splatnosti
- loan sequence - repeated (počet)
- paymaker - robot vyplácející peníze přes banku

## Zkratky

- SPL - subprime loan (short-term personal/payday)
- SECCI - standard european consumer credit information - parametry půjčky
- TOS - VOP - terms of servicesa
- APR - RPSN - roční procentuální sazba nákladů
- PoC - proof of concept - musí jít půjčit
- MVP - minimal viable product - jde to ukázat ven
- DPD - days past due - počet dnů po splatnosti půjčky (DPD-3 vs. DPD3)
- MTP - produkt "Minimum To Pay", neboli revolvingový úvěr
- DII - daily interest instalment - denní splátka úroku
- DPI - daily principal instalment - denní splátka jistiny
- MMR - minimal monthly repayment - částka, kterou musí klient v měsíci zaplatit
-
  - priority payment - část splátky, která bude připsána na jistinu

## Štítky (TODO: jinam)

- `NewLead` - příchod nové žádosti přes partnera (API)
- `Testing` - testovací případ (ignorujte)
- `KontomatikPending` - klient dal údaje, čeká se na stažení
- `First` - první žádost, i zamítnutá (není u opakovaných)
- `Repeated` - opakovaná
- `IdCard` - verifikace občanky seběhla (kdysi, u opakovaných, i kdyz teď zadal jiné číslo)
- `Statements` - máme korektní bankovní výpisy (kdysi)
- `AuthFee` - 1 Kč platba byla zaplacena (nebo byl kontomatik)
- `PaidOut` - vyplacena žadost
- `Replaced` - nahrazeno novější žádostí
- `Abandoned` - žadatel nedokončil žádost (přidáváno ručně nebo po 3 dnech nečinnosti automaticky)
- `Resumed` - žadatel se po opuštění vrátil k žádosti (např. přes Covid stránku)
- `ToBeSent` - případ se má poslat na externí collection (a štítek zrušit)
- `ForSale` - případ je k prodeji (pak se má štítek zrušit)
- `Covid` - případy do 26.3.2020, mají také štítek LateFeeStopped a DontSendNotices
- `LateFeeStopped` - u těchto případů nejsou připočítávány denní úroky
- `LegalInterestStopped` - dtto
- `DontSendNotices` - na tyto případy neodchází automatické výzvy ze šablon (jde ale odeslat ručně přes web)
- `Insolvence` - systém přidává všem případům, kde klientovi bylo povolené oddlužení
- `NoAnswer` - klient nezvedá telefon
- `IsInterested` - klient zvedá telefon, má zájem o půjčku
- `LastAttempt` - po NoAnswer/IsInterested je označen, že ho systém zkusí ještě naposled oslovit
- `ContractWithdrawal` - odstoupení od smlouvy v zákonem stanovených 14 dnech od sjednaní smlouvy, klient vraci pouze jistinu, ostatni je potřeba
- `DuplicitAppl` - klient s aktivní půjčkou, omylem umožňeno vytvořit opakovanou žádost
- `WebNotSeen` - přidáno při vytvoření případu, zrušeno při zobrazení webu žadateli
- `MorList` - případy, kterésplňují Lex Covid parametry a pozádali si o odložení splátek (u jiných nebankovek). Tj. 1.11.2020 budou mít problém splatit závazky
- ... - a další

## Údaje osoby - viz Editovat - Údaje u osoby (TODO: jinam)

- `IdCardNo` - platné číslo občanky (např. při aktualizaci operátorem)
- `IdCardPhoto` - ID fotky s obličejem a geometrie jeho výřezu
- `PasswordHash` - zašifrované heslo (prázdné, pokud ještě nenastavil)
- ... - a další

## Formulář žádosti (Appl\* údaje - TODO: jinam)

- `IdCardExp` - datum expirace občanky, YYYY-MM-DD
- `SignTOS` - "on" - souhlasil s VOP
- `CheckNotPolExpPerson` - není tzv. politicky exponovanou osobou
- `RequestedAmount` - žádaná částka
- `PrincipalAmount` - zvolená částka k vyplacení
- ... - a další Appl\* údaje

## Údaje případu - (mimo Appl\* žádost - TODO: jinam)

- `Approved` - kdo schválil žádost
- `AuthFeePaid` - příznak, že byla uhrazena ověřovací 1 Kč
- `BaseURL` - přes který náš front-end vyplnil žádost
- `Site` - který konfigurák byl na webu použit
- `DocsWarnings` - systémem detekované problémy s občankou/výpisy
- `IdCardHomeless` - na občance je uvedeno, že adresa pobytu je na úřední adrese
- `IdCardAddress1` - první (analogicky druhý) řádek adresy z OCR občanky
- `IdCardRUIAN` - RUIAN ID adresy z OCR občanky
- `IdCardZIP` - PSČ adresy z OCR občanky (nebo "-")
- `IdCardValid` - nalezená platná občanka (bude předěláno)
- `LastSavedPage` - kde je ve formuláři
- `MaxOffer` - kolik nabídnout - downsell/upsell
- `PhoneCode` - vygenerovaný PIN
- `PrincipalAmount` - ???
- `Reason` - důvod schválení/downsellu/odmítnutí žádosti
- `LastSCResponse` - poslední výstup ze SC (při zobrazování případu)
- `EffectiveSCResponse` - výstup ze SC, který se zobrazil při schvalování/zamítání půjčky
- `ScreeningRunning` - běží cca 30s předschvalování
- `SplKal\*` - údaje o domluveném splátkovém kalendáři
- `Statement\*` - naparsované vypisy z účtu (bude předěláno)
- `TelcoJSON` - raw odpověď z TelCo
- `TelcoScore` - skóre TelCo (0-nenalezen, jinak 1-1000)
- `FE\*` - interní pole front-endu
- `PayoutAccountNo` - číslo účtu, kam má být vyplacena jistina
- `OverrideSC` - ignoruj solvencyAmount od skórovací karty
- `DocumentedIncome` - příjem vypočtený z výpisů/XML (pouziva DE)
- `LastSECCISent` - čas odeslání poslední SMS pro ověření SECCI
- `LastSignatureSent` - čas odeslání poslední SMS pro podpis smlouvy
- `IncomeOldest` -
- `IncomeMiddle` -
- `IncomeNewest` -
- `Source` - partner, který dodal lead
- `AbandonedDate` - datum přidání štítku (pro následnou archivaci nad X dnů)
- `SignatureDate` - den podpisu smlouvy / verze VOP
- `MaxDelayDays` - maximalní DPD, které v průběhu splácení nastalo
- `StatementsFrom` - RefNo žádosti, ve které je uložen platný uznaný výpis
- ... - a další
