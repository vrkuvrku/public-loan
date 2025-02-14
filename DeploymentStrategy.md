# DeploymentStrategy

- deno check src/controller.ts && git push && ssh loan git pull

- auto deployment do 10 sekund, jinak to budu obchazet
- smoke testy vs FULL maximum report
- ci/cd: ts-check, testy smoke vs FULL, lint, coverage, staging, hot-fix
- long-term code-base governance
- bezpecnost: DB ucty, secrets, upgrady?,...
- vsconfig, extensions, vetve/pull-requesty, code-reviews

## Lokální PostgreSQL databáze (optional)

> Upozornění: Systém jde spustit oproti lokální databázi, ale volané CRM služby do ní nebudou moct zapisovat!

```bash
psql < resource/ddl.sql # vytvorte databazove schema
psql < resource/tester.sql # vytvorte uzivatelsky ucet
export PSQL='postgres://tester:Zi7Iv9if@localhost/$USER' # $USER - databaze
```
