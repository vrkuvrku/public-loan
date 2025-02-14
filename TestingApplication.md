# Testovací žádost u poskytovatele XY, s.r.o

Pomocí tohoto postupu jde projít celým procesem žádosti, schvalování,
vyplacení, dočerpání, prodloužení, vrácení a opakování žádosti.

Fakticky je žádost vytvořena v databázi poskytovatele XY, s.r.o.,
nicméně je označena za testovací a je poskytovatelem ignorována.

Kromě ad-hoc ručního testování je tento postup pouštěn v rámci testů
i pravidelně během dne.

**!!! POSTUP SI KLIDNĚ BEZ OBAV MŮŽETE SAMI KDYKOLIV VYZKOUŠET !!!**

Na rozdíl od reálných žádostí:

```
src/Evaluate.ts
585:      // TESTING: Activity stitek neni mazan (proc?)

src/Case.ts
280:  // TESTING: RefNo pripadu se urci natvrdo

src/Repeat.ts
202:  // TESTING: nove zadosti je pridan Testing stitek (nema na nic vliv)
869:  // TESTING: opakovane pujcce se prida Testing stitek

src/IdCard.ts
215:    // TESTING: podvrhni falešné údaje z občanky (vdovec, Brno)
232:  // TESTING: na obcance neni kontrolovane jmeno, DOB, MRZ, RUIAN.

src/Payout.ts
244:    // TESTING: pujcka neni realne vyplacena

src/Config.ts
125:	// TESTING: produkt (batch pripadu) je misto SPL nastaven na MTP

src/Data/Drafting.ts
76:  // TESTING: na telefon 608123456 (a email @sokordia.cz) neodchazi zpravy

src/Event.ts
530:  // TESTING: nevytváří se úkoly pro call-centrum

src/Proving.ts
549:    // TESTING: misto parsovani kontomatiku se vrati vypis s jednou 20kKc txn

src/Screen.ts
205:		// TESTING: lustruje se jenom v ISIRu
223:		// TESTING: lustruje se jenom v ISIRu a RUIANu (t.j. nepousti placene dotazy)
224:		// TESTING: CRM/Register/NRKI: pouziva Lukasuv nrki.json
```

(Seznam byl vygenrován pomocí `ack TESTING: src`)

## Postup

1. Reset testovací žádosti: Klikněte na https://flexifin.cz/fe/testingReset

2. Na https://flexifin.cz vyplnte novou žádost jako:

   - Filip Flekáč, RČ 8309130005, filip.flekac@sokordia.cz,
   - tel. 721138961, ověřovací kód 1981
   - příjmy a výdaje si vymyslete

3. Místo dokladů klikněte na

   - https://flexifin.cz/fe/testingUpload/Statement1
   - https://flexifin.cz/fe/testingUpload/Statement2
   - https://flexifin.cz/fe/testingUpload/Statement3

4. Vrátit se zpět na https://flexifin.cz/zadost/preaccept a kliknout Pokračovat

5. Navolit Doplnkove sluzby

6. Místo občanky klikněte na odkazy:

   - https://flexifin.cz/fe/testingUpload/IdCard1
   - https://flexifin.cz/fe/testingUpload/IdCard2

7. Místo Odeslání ověřovací platby (AuthFee) klikněte na:

   - https://flexifin.cz/fe/testingAuthFee

8. Pro schválení 6000 Kč klikněte na

   - https://flexifin.cz/fe/testingAccept/6000

9. Pro úhradu půjčky klikněte na

   - https://flexifin.cz/fe/testingTxn/lender/20019999/10000
