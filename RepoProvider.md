# Repo poskytovatele - customizace

Zákazníci iSpis CRM, kteří si chtějí systém sami modifikovat, tak můžou
učinit pomocí vlastního GIT repozitáře.

- config.yaml - konfigurační soubor, pro CRM, Loan modul a front-end (`public` část)
- layout - grafika používaná pro e-maily nebo PDF soubory generované z Markdown šablon
- inbox - dash-board pro operátory - fronty úkolů k vyřizování v [Inbox Live](https://ispis.cz/inbox/live)
- decision_engine - vyhodnocení žádosti (na základě tzv. skórovací karty)
- txn - tagování transakcí a ověření příjmů
- paymaker - robot pro odesílání plateb přes banku poskytovatele
- dokumentace, směrnice - obsah repozitáře (nebo část) se zaměstnancům zobrazuje ala GitHub na [iSpis Přehled](https://ispis.cz/prehled)

Do repozitáře si však můžou přidat i cokoliv dalšího, co potřebují k provozu (reporting, účto, integrace na 3rd parties, ale i kód vlastních micro-services apod...)
