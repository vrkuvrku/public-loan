# Loan modul API - služby aplikačního serveru (end-pointy)

Aplikační server poskytuje API pro:

1. front-end - web v brandu poskytovatele úvěrů, sloužící pro podávání žádostí
2. služby pro affiliate partnery či web-hooky třetích stran
3. back-office část pro posuzování a správu úvěrů zaměstnanci poskytovatele
4. pomocné nástroje potřebné pro běh firmy poskytovatele

Seznam rout viz `Loan.pm`, cmd-line nástroje jsou `sub cmd...` funkce.

## 1. provider.cz/fe služby pro front-end

Swagger definice viz [ispis.com/api/swagger/fe](https://ispis.com/api/swagger/fe)

O kterého jde poskytovatele úvěrů se určí dle názvu domény z requestu.

Žadatel je autentizován pomocí tokenu vráceného při `signUp` nebo `login`.

- `initData` - veřejná část configu,
  (viz [/fe/initData](https://poskytovatel.cz/fe/initData))

- `previewText`, `sendPhoneCode`, `sendResetLink`, `resetPassword` - neautentizované služby

- žádost: `alreadyRegistered` (test), `signUp`, `uploadFile`, `saveField` (?),
  `savePage`, `sendSignatureCode`, `launchDecisionEngine`, `launchScreening`,
  `screeningStatus`
- žádost (optional): `webpushSubscribe`, `promoInterested`, `callRequested`,
  `smsIdCardLink`, `goalSeekingDone`, `attractedBySMS`, `sendLegalDrafts`

- auth-sekce: `login`, `clientData`, `repeatLoan`, `waitFor`(?),
  `addChoosen`, `savePresto`, `addAmountPayout`
- auth-sekce (optional): `openProlongationInvoice`(?)

- `kontomatik*` a `psd2*` (kontomatik), `stInit`, `stData` (stech-psd2) - PSD2
- `bankid*`
- `oauthGoogle`, `oauthSeznam` - přihlašování

- zbytek: `qrCode`, `logEvent`, `brokerList`, `bankSync`, `proxyUrl`

## 2. provider.cz/fe služby pro 3rd party

O kterého jde poskytovatele úvěrů se určí dle názvu domény z requestu.

- `leadFindPerson`, `leadSignUp` -
  [API pro leadaře](https://docs.google.com/document/d/1bDtEIjF3QJGo427Wf9bwCR20qKQw03T-9iqzm7JMPRc)

- `gatewayATSReply` - příjem SMS od ATS
- `gatewayAgencyEvent` - příjem e-PR dat od ext. inkasní agentury
- `testing*` - zkratky pro žádost podávanou v rámci testů
- `on` - zpracování události z ispis CRM (viz config.yaml - triggers)
- `customVar` - generování Loan* proměnných do šablon (viz config.yaml)
- `customAction`/`bulkAction` - akce nad případy (viz config.yaml - `customActions`)

## 3. ispis.cz/loan - pro iSpis CRM

Na pár málo místech iSpis CRM back-office web volá napřímo API Loan modulu
(Inbox Live "dlaždice", Loan Application a Income Verification sekce případu).

Volání `/loan/*` URL je přesměrováno na úrovni webové proxy.

Autentizace zaměstnance probíhá pomocí JWT tokenu (t.j. už musí být přihlášen).

Swagger definice [ispis.com/api/swagger/loan](https://ispis.com/api/swagger/loan)

- `paymaker*` - API pro platební roboty

- `loanApplData`, `loanStatementsData` - informace pro Loan Application a Income Verification sekce
- `getPEData/:entryId/:formatName` - tlačítka nad projectEntry
- `runGoToAuthUrl` - přesměruj na AuthURL klienta
- `openProlong` - vytvoř prolongaci (?)
- `checkIdCard` - uložení občanky
- `saveMVI` - uložení ověřených příjmů
- `acceptApplication`, `repeatProject`, `abandonCase`, `applCompleted` - tlačítka
- `goalSeekingDone`, `offerPromo`, `runScoreCard`, `runScreening` - další tlačítka
- `runAsync/:which` - a další tlačítka

## 4. pravidelně spouštěné akce

Krom obecných akcí spouštěných pro poskytovatele v CRM modulu
(typu tel. ústředna, import plateb) jsou pro poskytovatele úvěrů pouštěny
ještě:

- `Affil::cmdReportPostBacks` - zaslání postbacků affilům (pokud neprošlo on-line)
- `Appl::cmdEphemeralActivity` - mazání Activity štítku
- `Appl::cmdAbandon` - opuštěné žádosti

- `Automat::cmdAutomatCron` - propočti CreditLimit a odešli pushHasAA
- `Automat::cmdPersonAutomatCron` - odeslání mailingClosed a closedRenewal
- `Automat::cmdComeback` - odeslání Comeback nebo pushLead
- `loan/cronFFTukTuk` - odesílání tuktuk/promoSigning, úkoly promo a negotiate

- `Daily::cmdAutomatDaily` - DPD logika (EarlyLate, ExtCollection)
- `Automat::cmdDueDate` - vytváření DueDate úkolů
- `Daily::cmdRunningInt` - denní kapitalizace LateInterest
- `Daily::cmdComputeDailyFee` - napočítávání pokut
- `NRKI::cmdUltimum` real, `NRKI::cmdUltimumAllCompute` - napočítávání pro reporting
