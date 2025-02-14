# Contributing - přispívání

## Úpravy ve zdrojových kódech

Dokument popisuje politiku úprav zdrojových kódů obecného systému
pro půjčování (Loan modulu).

## Zprovoznění prostředí

1. Install VS Code

1. Clone repo <https://github.com/sokordia/loan>

1. VS Code > File > Open workspace from file... loan.vscode-workspace

- tabSize: 2, max cca 80 columns, format on save, bez středníků

1. Install (recommended) deno extension

1. In Code > Preferences > Settings enable Deno > Inlay Hints > Function Like Return Types

- v kódu nejsou návratové typy funkcí - zobrazuje je vscode jako inlay hints díky Deno LTS

- radši testy než komentáře (coverage aspoň 70%+)

## Kontroly před pull requestem

```bash
deno check src/**/*.ts
deno test -A src
deno test -A --coverage=file src && deno coverage file # 70 % +
deno lint
```

## Produkční kontroly

> TODO: lint, ts-check, performance, security, ..., zasilat e-mailem

```bash
export PSQL='postgres://is:...@localhost/is'
FULL=1 deno test -A src
```

## Customizace pro konkrétního tenanta

Customizace řešení pro konkrétního poskytovatele se provádí v repozitáři
poskytovatele. Tento modul pak obsahuje pouze kód, který customizaci integruje.

Příkladem může být vlastní DecisionEngine, PayMaker, ale třeba i vlastní kód
micro-service pro zpracování nějaké API služby, jejíž URL se pak na jinou
instanci přesmeruje pomocí nastavení proxy serveru.
