# Module Decoupling - rozbití Loan modulu na části

Aktuálně Loan API odpovídá na `/loan` (CRM) a `/fe` (front-end a zbytek)
routách, plus má nějaké `cmd*` funkce.

## 1. Logicky

TODO:

- core
- product
- web
- third
- crm

## 1b.

```mermaid
classDiagram
    class FE
    class FEBE
    class CRM
    class Affil
    class Bank
    class Txn
    class Payout
    class Evaluate
    class Orchestrace
    class CreditLimit
    class Upload
    class IdCard
    class Application
    class Active
    class Screening
    class History

    FE --|> FEBE
    FEBE <--|> Application
    CRM --|> FEBE : ?
    Affil --|> FEBE
    Txn --|>  Application
    Payout --|> Active
    Evaluate --|> Application
    Orchestrace --|> Application
    CreditLimit --|> Active
    Upload --|> IdCard
    Upload --|> Txn
    IdCard --|> Application
    Application --|> Active
    Active --|> History
    Active --|> Bank
    Screening --|> Orchestrace
    Application --|> History
```

## 2. Rozdělení dle end-pointů

Jedna možnost je překopat to podle routes na následující:

- `/loan/core` - interní **Loan Core**, viz níž
- `/loan/affil` - API pro affily (původní `/fe/lead`)
- `/loan/web` - jenom webové funkce (původní `/fe`)
- `/loan/cron` - `cmd*` funkce volané automaticky, pravidelně
- `/loan/admin` - ostatní `cmd*` funkce, volané přes nějakou konzoli
- `/loan/testing` - "zkratky" pro testovací žádost (smazat, schválit,,...)
- `/loan/paymaker` - API pro platební roboty
- `/loan/bridge` - hooky pro CRM - co CRM modul nutně potřebuje volat
- `/loan/crm` - "tlačítka" v CRM pro operátory
- `/loan/gateway` - web-hooky (API) pro třetí strany

### 2.1. **Loan Core** (nástřel)

Dlouhodobý cíl, aby do DB přistupoval jen **Loan Core**.

Střednědobý cíl, abý zápis do DB prováděl jen **Loan Core**.

Krátkodobý cíl, mít v **Loan Core** funkce, které potřebuje víc modulů.

Zejména:

- založit žádost (pro affil i web)
- založit úkol při nějaké události (a "vyklikat" procesně předcházející)
- posunout žádost na další krok (při "Pokračovat", authFee, Kontomatiku,...)
- možná: vrátit kompletní informace o osobě/případu (pro dlouhodobý cíl)
- (funkce Config, Data, Project, Appl)

## 3. Rozdělení dle souborů

Druhý přístup je překopat `src/Loan/*.pm` soubory do více podadresářů:

- Core: Appl Config Project Repeat ScoreCard
- ?: Check Data Vars
- Process: Screen Complete Evaluate Payout
- ?: Proving Enjoying Collect DebtSale
- Routes: Affil CRM FE Gateway IS PayMaker
- Tests: TestDuo TestMTP Test TestSmoke TestWest
- Modules: IdCard Txn Income Ext
- Cron: Automat Daily NRKI Update
- Admin: Metrics Fix FixRecalc

### 3.2. Rozdělení dle statické analýzy zdrojáků

Třetí přístup je strojově zanalyzovat `src/Loan/*.pm` soubory a udělat
analýzu závislostí funkcí mezi sebou navzájem.

### 3.3. Závislosti mezi soubory navzájem

V grafu níž chybí závislosti na soubory:
`Project IS Config Complete ScoreCard Appl Check Data Vars`.

```mermaid
classDiagram
    class Affil
    class Automat
    class CRM
    class Collect
    class Daily
    class DebtSale
    class Enjoying
    class Evaluate
    class Ext
    class FE
    class Gateway
    class IdCard
    class Income
    class Metrics
    class NRKI
    class PayMaker
    class Payout
    class Proving
    class Repeat
    class Screen
    class Txn
    class Update

    Affil --|> Enjoying
    Affil --|> FE
    Affil --|> Repeat
    Affil --|> Screen
    Automat --|> Enjoying
    CRM --|> Enjoying
    CRM --|> Evaluate
    CRM --|> IdCard
    CRM --|> Income
    CRM --|> Metrics
    CRM --|> Proving
    CRM --|> Repeat
    CRM --|> Screen
    CRM --|> Txn
    Daily --|> Enjoying
    Daily --|> Proving
    Enjoying --|> Collect
    Enjoying --|> Payout
    Enjoying --|> Proving
    Evaluate --|> Affil
    Evaluate --|> Payout
    Evaluate --|> Screen
    FE --|> Affil
    FE --|> Collect
    FE --|> DebtSale
    FE --|> Enjoying
    FE --|> Evaluate
    FE --|> Gateway
    FE --|> Payout
    FE --|> Proving
    FE --|> Repeat
    FE --|> Screen
    Gateway --|> Affil
    Gateway --|> Evaluate
    PayMaker --|> Payout
    Payout --|> Affil
    Payout --|> Enjoying
    Payout --|> Screen
    Proving --|> Affil
    Proving --|> IdCard
    Proving --|> Income
    Proving --|> Payout
    Proving --|> Txn
    Repeat --|> Affil
    Repeat --|> Income
    Repeat --|> Payout
    Repeat --|> Screen
    Screen --|> Evaluate
    Screen --|> NRKI
    Screen --|> Repeat
```

### 3.4. Závislosti mezi všemi soubory, včetně jmen funkcí

```mermaid
classDiagram
    class Affil
    class Appl
    class Automat
    class CRM
    class Check
    class Collect
    class Complete
    class Config
    class Daily
    class Data
    class DebtSale
    class Enjoying
    class Evaluate
    class Ext
    class FE
    class Gateway
    class IS
    class IdCard
    class Income
    class Metrics
    class NRKI
    class PayMaker
    class Payout
    class Project
    class Proving
    class Repeat
    class ScoreCard
    class Screen
    class Txn
    class Update
    class Vars

    Affil --|> Appl : normalizeEmail\nnormalizePhoneFor\nsendMessage\nsendMessageUnlessTooOften\ntoOsoba
    Affil --|> Config : loanConfig\npublicConfig
    Affil --|> Data : getAuthUrl\nisScreeningKO\nreasonExpired
    Affil --|> Enjoying : getAddAmount
    Affil --|> FE : findPersonAPI\nsignUpUserAPI
    Affil --|> IS : address
    Affil --|> Project : createTask\nfinishTasks
    Affil --|> Repeat : repeatProject
    Affil --|> Screen : ceeCount\nfamilyNameBlackListed\nhasHistoryKO\nidCardStolen\ninISIR\nkoReason\nkoRegistries\nonExtBlackList\nprescreenBirthNo
    Appl --|> Check : run
    Appl --|> Complete : newComplete
    Appl --|> Config : feeAmount\ngetMaturity\nisOldFFSPL\nisUpfront\nloanConfig\nprolongOptions\npublicConfig\nwestRate
    Appl --|> Daily : firstRunningInt
    Appl --|> Data : continueUrl\ndata\ndoBlackListCheck\nnormalizePhone
    Appl --|> Evaluate : acceptOrReject
    Appl --|> IS : address\ncall
    Appl --|> Project : abandonCase\nbalance\nbalanceOnInvoice\ncreateActivityTask\nfind\ngetLoanSOI\ngetNextVS\nincreaseSOI\nsetLoanSOI
    Appl --|> Screen : inISIR
    Appl --|> Vars : loanInfo
    Automat --|> Appl : sendMessage
    Automat --|> Enjoying : computeAddAmount
    Automat --|> Project : createTask
    CRM --|> Appl : createProlongationInvoice
    CRM --|> Complete : newComplete\npostId\nsetLenderAddress
    CRM --|> Data : data\ngetAuthUrl
    CRM --|> Enjoying : doReduceDebt
    CRM --|> Evaluate : acceptOrReject\ngoalSeekingDone
    CRM --|> IS : address\ncall
    CRM --|> IdCard : verifyMRZ\nverifyMRZ1
    CRM --|> Income : finances\npostSavedMVI\nsaveMVI
    CRM --|> Metrics : getData
    CRM --|> Project : abandonCase\nbalance\nfind\nfinishEvent
    CRM --|> Proving : setNegotiate
    CRM --|> Repeat : repeatProject
    CRM --|> ScoreCard : isApprovable\nrun\nrunAndReject\nsavedOutput
    CRM --|> Screen : preScreenAndSwitch\nrunScreening
    CRM --|> Txn : listTxns
    Check --|> Data : dataValid\ndocumentsLookGoodEnough
    Check --|> Project : daysPastDue\nfind
    Collect --|> IS : address
    Collect --|> Project : balance\ndaysPastDue\nfinishTasks\ngetNextVS
    Complete --|> Affil : reportToPartner
    Complete --|> Appl : activated\nmissingFulfilled
    Complete --|> Data : allFormDataValid\ndata\ndocumentsLookGoodEnough\nhasAuthFee\nmissingInfo\notherError
    Complete --|> Evaluate : acceptOrReject\nrejectIfRejected\nrunEvaluation
    Complete --|> IS : address\ncall
    Complete --|> Project : createTask\nfind\nfinishTasks\nswitchTo
    Complete --|> Proving : addAuthFeeFlag\nsomethingIsMissing
    Complete --|> ScoreCard : run\nrunAndReject
    Complete --|> Screen : runScreening
    Config --|> IS : call
    Config --|> Project : balance\nbalanceOnPrincipal\ngetLoanSOI
    Config --|> ScoreCard : getEffectiveOutput
    Daily --|> Config : interestPerDay\nisOldFFSPL\nisUpfront\npublicConfig
    Daily --|> Enjoying : updateAvailableAddAmount
    Daily --|> IS : call
    Daily --|> Project : balanceOnInvoice\ndaysPastDue\nfind\nincreaseSOI\nswitchTo
    Daily --|> Proving : duePromiseRunning
    Data --|> Appl : inUrl\nphoneCode\nprojectHasAProof\nprojectHasAllIdPhotos\nprojectHasAllStatements\nprojectSignatureCode
    Data --|> Complete : missingItems
    Data --|> Config : loanConfig\nprolongOptions\npublicConfig
    Data --|> IdCard : verifyIdCard
    Data --|> Payout : getAnyBankNo
    Data --|> Project : fileContent\nfind\ngetLastScreening
    Data --|> ScoreCard : getOutput\ngetResult\nisApprovable\nsavedOutput
    Data --|> Screen : allRegistries\nregMaxCacheDays
    DebtSale --|> IS : call
    DebtSale --|> Project : fileContent\nfind\nfindLoanAuthTxn\ngetLastScreening
    Enjoying --|> Appl : chargeAddServices\ncreateProlongationInvoice\nsendMessage
    Enjoying --|> Collect : cancelCollection
    Enjoying --|> Config : isOldFFSPL\nprolongOptions
    Enjoying --|> IS : call
    Enjoying --|> Payout : sendMoney
    Enjoying --|> Project : balance\nbalanceExceptLoanInterest\nbalanceOnInvoice\nbalanceOnPrincipal\ncreateTask\nfind\nfinishTasks\ngetLoanSOI\nsetLoanSOI\nswitchTo\nurlAbsolute
    Enjoying --|> Proving : addAuthFeeFlag
    Enjoying --|> ScoreCard : computeCreditLimit
    Evaluate --|> Affil : reportToPartner
    Evaluate --|> Appl : activated\ncreditworthiness\nfinalCheck\ninUrl\nsendMessage\nsendNotification
    Evaluate --|> Complete : newComplete
    Evaluate --|> Config : publicConfig
    Evaluate --|> Payout : getAnyBankNo
    Evaluate --|> Project : createTask\nfinishEvent\nfinishStmtsTasks\nfinishTasks\nswitchTo
    Evaluate --|> ScoreCard : getResult\nrun
    Evaluate --|> Screen : runScreening
    Ext --|> Config : loanConfig
    Ext --|> Project : fileContent
    FE --|> Affil : auth\nbySource\nfindBroker\ngetReport\nmarkBroker\nprojectPartner\nreportToPartner
    FE --|> Appl : activated\nauthToken\nauthUrl\nhasPresto\ninUrl\nnormalizeAddressLine\nnormalizeDate\nnormalizeExtraId\nnormalizePhoneFor\nphoneCode\nprojectHasAllIdPhotos\nprojectSignatureCode\nproofNeeded\nresetHash\nresetUrl\nsendLegalDrafts\nsendMessage\ntoOsoba
    FE --|> Collect : actionTransferCase\neprRows\nfetchAgencyData
    FE --|> Complete : bankidFill\nsetLenderAddress\ntryToAutoProve
    FE --|> Config : loanConfig\nprolongOptions\npublicConfig
    FE --|> Data : assertPartialDataValid\nassertSubsetValid\ncontinueUrl\nhasAuthFee\nisSTDemo\nkonsEligible\nkonsOptions\nmissingInfo\npromoEligible\nreasonExpired
    FE --|> DebtSale : actionEPRDocs\nactionEPRFiles\nactionExportAll
    FE --|> Enjoying : getAddAmount
    FE --|> Evaluate : acceptOrReject\ngoalSeekingDone
    FE --|> Gateway : loanSignUpUser\nloanUserDetail
    FE --|> IS : call
    FE --|> Payout : runPayoutAdd
    FE --|> Project : allocNewRefno\nbalance\nbalanceOnInvoice\ncreateActivityTask\ncreateLoanProject\ncreateTask\ncreateTaskNewCall\ncreateUploadedTask\ndeleteLoanProject\nfileBySHA1\nfileContent\nfind\nfinishTasks\ngetLoanAuthSourceVariable\ngetLoanAuthTxnVariable\ngetLoanSOI\nstopWatching
    FE --|> Proving : addAuthFeeFlag\nonAddedFile\nonPostSave\nrunTagTxns
    FE --|> Repeat : repeatProject
    FE --|> ScoreCard : run\nrunAndReject
    FE --|> Screen : launchPrescreen\nrunScreening
    FE --|> Vars : loanInfo
    Gateway --|> Affil : acquireLead\nauth\nconvertPartnerData\nextractFromC\nextractFromData
    Gateway --|> Appl : inUrl
    Gateway --|> Evaluate : tuktukCron
    Gateway --|> Project : find
    IS --|> Config : invalidateCache
    IS --|> Enjoying : afterPayment\nclosedBecausePaid\nonAddedPaid\nonAssignTxnToProject
    IS --|> Project : find\nonAddInsolvence\nonAddedPE\nonCloseProject
    IS --|> Proving : onAddedFile\nonFormSave
    IdCard --|> Complete : postId
    IdCard --|> IS : address
    IdCard --|> Project : fileContent\nfind
    Income --|> Appl : missingFulfilled\nprojectHasAllStatements
    Income --|> Complete : newComplete
    Income --|> Project : fileContent
    Income --|> ScoreCard : runAndReject
    NRKI --|> Config : getMaturity
    NRKI --|> Data : loanFees
    NRKI --|> IS : address
    NRKI --|> Project : balance\nbalanceOnPrincipal\ndaysPastDue\nfind\ngetLoanSOI\nprojectOverDueDebt
    PayMaker --|> Data : normalizeBankNo
    PayMaker --|> IS : call
    PayMaker --|> Payout : parseOrder\nrsPending
    PayMaker --|> Project : urlAbsolute
    Payout --|> Affil : reportToPartner
    Payout --|> Appl : createLoanInvoice\nfinalCheck\ngetRemainingDays\nhasPresto\nloanContractPdf\nsendMessage
    Payout --|> Config : feeAmount\ngetMaturity\npublicConfig
    Payout --|> Data : data\ndoBlackListCheck\ngetPSD2BNo\nkonsSelected\nnormalizeBankNo
    Payout --|> Enjoying : getAddAmount\nupdateAvailableAddAmount
    Payout --|> IS : call
    Payout --|> Project : createTask\nfind\nfinishEvent\nfinishTasks\nswitchTo
    Payout --|> Screen : runScreening
    Project --|> Appl : sendMessage
    Project --|> Data : getLastFromLustrace
    Project --|> IS : call
    Project --|> Proving : onAddedPE
    Project --|> Vars : loanInfo
    Proving --|> Affil : confirmOwnership
    Proving --|> Appl : activated\nmissingFulfilled\nprojectHasAllIdPhotos\nsendMessage
    Proving --|> Complete : missingItems\nnewComplete\ntryToAutoProve
    Proving --|> Config : loanConfig
    Proving --|> Data : continueUrl\npsd2KYC
    Proving --|> IS : call
    Proving --|> IdCard : detectIdCard
    Proving --|> Income : recomputeAndSave
    Proving --|> Payout : runPayoutMain
    Proving --|> Project : abandonCase\ncreateTask\ncreateUploadedTask\nfileContent\nfinishStmtsTasks\nfinishTasks\nstartWatching\nstopWatching\nswitchTo
    Proving --|> ScoreCard : run
    Proving --|> Txn : tagPE
    Repeat --|> Affil : findBroker
    Repeat --|> Appl : copyFields
    Repeat --|> Complete : tryToAutoProve
    Repeat --|> Config : loanConfig
    Repeat --|> Data : formValid\nreasonExpired
    Repeat --|> IS : call
    Repeat --|> Income : saveMVI
    Repeat --|> Payout : getAnyBankNo
    Repeat --|> Project : allocNewRefno\ncreateActivityTask\ncreateLoanProject\nfinishEvent\nfinishTasks
    Repeat --|> Screen : preScreenAndSwitch
    ScoreCard --|> Config : loanConfig
    ScoreCard --|> Data : data
    ScoreCard --|> Evaluate : rejectIfRejected
    ScoreCard --|> Screen : runScreening
    Screen --|> Appl : sendLegalDrafts
    Screen --|> Config : loanConfig\npublicConfig
    Screen --|> Data : data\ndoBlackListCheck\ngetLastFromLustrace\nisOldEnough\nmissingInfo\nreasonExpired
    Screen --|> Evaluate : acceptOrReject
    Screen --|> IS : address\ncall
    Screen --|> NRKI : exportBureauRow\nfillDB
    Screen --|> Project : find\ngetLastScreening\nswitchTo
    Screen --|> Repeat : searchRejectReason
    Update --|> Config : publicConfig
    Update --|> Project : createTask\ndaysPastDue\ngetNextVS\noverDueDebt\nswitchTo
    Vars --|> Appl : authToken\nhasPresto\ninUrl\nresetUrl
    Vars --|> Config : feeAmount\ngetMaturity\nprolongOptions\npublicConfig
    Vars --|> Data : konsSelected
    Vars --|> Enjoying : getAddAmount
    Vars --|> Project : daysPastDue\nfind\nprojectOverDueDebt
    Vars --|> ScoreCard : savedOutput
```

> Grafy byly vygenerovány pomocí `tool/analyze.pl`
