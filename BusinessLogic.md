# Business logika poskytovatelů

V Loan modulu je business logika všech poskytovatelů hard-kódována, konkrétní
větev se pak řídí buď pomocí _feature flagů_ v konfiguráku nebo občas přímo
podmínkou na klienta ve zdrojácích.

Business logika obsahuje typicky _know-how_ poskytovatele a zahrnuje zejména:

- odladění procesu žádosti - front-end a navazující kroky back-endu
- podrobnosti AML procesu - např. dvou-krokové ověření identity žadatele, různé
  požadavky
- orchestrace lustraci - cena dotazů do jednotlivých rejstříků vs. práce
  call-centra na pak stejně zamítnutých žádostech
- dohledávání příjmů a metodika jejich uznávání
- nastavení produktu - výpočty poplatků, úroků, prodloužení, úvěrového rámce,
  minimální úhradě apod.
- logika uznávání leadu více affiliate partnerům
- časování marketingových zpráv - částečně přes CRM, částočně customizováno
- práce call-centra - organizace práce,
- pomoc žadatelům - vytváření úkolů pro call-centrum a jejich automatické
  vyřizování při akcích žadatele
- systém oprávnění zaměstnanců a organizace práce

## Product specification - SPL

- krátkodobý nezajištěný úvěr
- nastavený inzerovaný fixní limit pro první úvěr a pro opakovaný (např. až do
  15 tis. u první, do 30 tis. u opakovaného)
- úročen nastavenou denní úrokovou sazbu
- účtován poplatek za poskytnutí (určen fixně nebo procentem z jistiny)
- při vyhodnocení žádosti je určen úvěrový rámec, zákazník může čerpat míň
- rámec a výše úvěru ("main" pohledávky) uvedeny v rámcové smlouvě o úvěru
- splatnost úvěru sjednána vždy na 30 dnů
- možnost čerpat další zpoplatněné služby - zejména okamžité výplaty peněz
- případně jiné jednorázově nebo měsíčně účtované služby
- v klientském profilu zobrazovaná výše dluhu ke dni splatnosti úvěru
- v průběhu čerpání může čerpat další tranši ("add" pohledávky) do výše rámce
- čerpání "add" zpoplatněné poplatkem
- úrok kapitalizovaný denně (z účetních důvodů evidován rozdělený po měsících)
- několik možností prodloužení splatnosti úvěru za poplatek (o zadané počty dnů)
- příchozí platba, pokud odpovídá částce za prodloužení, hradí prodloužení a
  posouvá splatnost
- platba v jiné výši je distribuována na jistinu/úrok/poplatky/sankce dle zákona
- smluvní úrok z prodlení - účtován denně nastaveným procentem
- zákonný úrok z prodlení - účtován denně dle zákona
- smluvní pokuta denně procentem (kontroluje nepřevýšení 50 % poskytnuté
  jistiny)
- možnost jednorázové smluvní pokuty
- možnost účtování poplatků za upomínání (nákladů na vymáhání)
- po plném uhrazení může zrychleně opakovat žádost - nová smlouva o úvěru
