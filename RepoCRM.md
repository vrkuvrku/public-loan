# Repo CRM - back-office část Loan modulu

Sokordia iSpis CRM, běžící na [ispis.cz](https://ispis.cz), je
obecný CRM systém, který je provozován pro celou řadu klientů - advokátů,
inkasních agentur, poskytovatelů úvěrů,...

iSpis CRM, kromě základní obecné funkcionality, obsahuje služby zaměřené
na evidenci a správu pohledávek a na "legal" procesy na česko-slovenském trhu
(konektory na státní weby, podpora místních bank či poskytovatelů dalších
služeb).

## 1. Integrace iSpis CRM směrem do Loan modulu

iSpis CRM je pro poskytovatele úvěrů mírně upraven - obchodním případem
je úvěr (resp. nejdřív žádost, která se pak změní na vydaný úvěr), přičemž
na pár místech v iSpis CRM jsou zobrazené údaje vypočtené Loan modulem,
plus obsahuje pár tlačítek, kterými operátoři volají funkce Loan modulu
(např. zkontrolovat občanku, schválit žádost, zamítnout,...).

## 2. Integrace iSpis CRM směrem do repozitáře zákazníka

iSpis CRM čte konfigurační soubor zákazníka (`config.yaml` v repozitáři
konkrétního poskytovatele) a využívá:

- `triggers`: při událostech v CRM (přijatá platba, insolvence osoby,...)
  je možné provolat nastavené URL (typicky nějakou službu Loan modulu)

- `templateCustomVariables`: v šablonách je možno použít výrazy, které
  bude vypočítavat externí služba (typicky Loan modul) - v URL parametru
  dostane RefNo, služba má vrátit JSON.

- `customActions`: nad případem (jednotlivě nebo hromadně), osobou
  nebo pohledávkou jde spustit vlastní akci - iSpis CRM volbu zobrazí
  v menu a provolá zadanou URL (databázové ID objektu předá v URL)

## 3. Služby iSpis CRM využívané Loan modulem

Řadu věcí Loan modul řeší pomocí CRM, se kterým komunikuje jednak
prostřednictvím databáze (přímo do ní zapisuje) a jednak pomocí iSpis CRM
API.

Zejména využívá služeb:

- načtení konfiguračního souboru
- určení oprávnění zadaného operátora
- spuštení lustrace (ověření v rejstříku)
- dohledání existence adresy
- vygenerování šablony
- odeslání zprávy (e-mailu nebo SMS) žadateli
- znovuzatřízení bankovní transakce
- a pár dalších ...

API je popsané Swagger definicí na
[ispis.com/api/swagger/module](https://ispis.com/api/swagger/module)
