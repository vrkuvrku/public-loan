# ERD diagram iSpis databáze

## 1. Zjednodušené schema

```mermaid
erDiagram
    lustrace { }
    person { }
    project { }
    project_person { }
    project_entry { }
    invoice { }
    paid { }
    debt { }
    bank_txn { }
    lead_event { }

    debt }o--|| invoice : ""
    paid }o--|| debt : ""
    lustrace }o--|| person : ""
    bank_txn }o--|| project : ""
    paid }o--|| bank_txn : ""
    invoice }o--|| project : ""
    project_entry }o--|| project : ""
    project_person }o--|| project : ""
    project_person }o--|| person : ""
    lead_event }o--|| project : ""
    lead_event }o--|| person : ""
```

## 2. Schema včetně sloupců

Popis důležitých sloupců viz [DatabaseSchema.md](DatabaseSchema.md)

```mermaid
erDiagram
    template {
        integer id
        timestamp_without_time_zone created
        integer client
        character_varying name
        USER-DEFINED format
        character_varying sender
        integer hybrid_delivery
        character_varying subject
        character_varying isds_nase
        character_varying isds_vase
        bytea content
        integer overdue_hours
        character_varying category
        character_varying states_filter
        character_varying change_status
        character_varying batch
        character_varying event_trigger
        json filter
        integer send_as
        boolean slozenka
        character_varying bill
        ARRAY overdues
        integer bill_fee_cents
        timestamp_without_time_zone last_used
        integer used_count
        character_varying note
    }

    debt {
        integer id
        timestamp_without_time_zone created
        timestamp_without_time_zone updated
        integer client
        character_varying project_path
        integer invoice
        character_varying kind
        json meta_json
        bigint amount_cents
    }

    lead_event {
        integer id
        timestamp_with_time_zone created
        integer client
        character_varying birth_no
        character_varying project_path
        character_varying kind
        character_varying partner
        integer status
        character_varying detail
        json meta_json
        integer correlation_id
        character_varying token
    }

    lustrace {
        integer id
        timestamp_without_time_zone created
        integer client
        character_varying dateseq
        integer line_no
        integer project
        character_varying c_profile
        integer person
        text input_json
        text output_json
        text errors
        timestamp_without_time_zone changed
        integer person_deleted
        character_varying output_json_deleted
        character_varying birth_no
        timestamp_without_time_zone waiting
    }

    project_person {
        integer id
        timestamp_without_time_zone created
        integer client
        integer project
        integer person
        character_varying kind
        text note_html
        integer line_no
    }

    bank_txn {
        integer id
        timestamp_without_time_zone created
        integer client
        integer statement
        character_varying bank_id
        character_varying party_name
        character_varying party_no
        character_varying reference
        integer project
        integer paid_cents
        date paid_date
        text user_note
        timestamp_without_time_zone snoozed
        character_varying snoozed_by
        timestamp_without_time_zone done
        character_varying done_by
        character_varying class
        boolean private
        character_varying kind
        character_varying extra_id
    }

    paid {
        integer id
        timestamp_without_time_zone created
        timestamp_without_time_zone updated
        integer client
        character_varying bank_no
        numeric vs
        character_varying ss
        character_varying txn_id
        character_varying project_path
        integer payee
        integer payer
        bigint paid_cents
        date paid_date
        text user_note
        integer invoice
        character_varying author
        integer fee_cents
        json meta_json
        character_varying recipient
        character_varying batch_short
        integer balance
        timestamp_without_time_zone checked
        character currency
        timestamp_without_time_zone reported
        character_varying report_id
        integer txn
        character_varying kind
        integer principal_cents
        integer clinterest_cents
        integer ccinterest_cents
        integer fine_cents
        integer other_cents
        integer overpaid_cents
        integer running_cents
        integer debt
    }

    person {
        integer id
        timestamp_without_time_zone created
        integer client
        character_varying call_no
        character_varying cached_full_name
        character_varying first_name
        character_varying last_name
        character_varying prefix
        character_varying suffix
        date birth_date
        character_varying birth_no
        bigint company_no
        character_varying company_name
        character_varying key
        character_varying note_html
        character_varying address_main
        character_varying address_company
        character_varying address_extra
        character_varying phone
        character_varying isds
        character_varying email
        character_varying web
        character_varying extra_id
        character_varying tax_id
        timestamp_without_time_zone isir_warned
        character_varying isir_spzn
        timestamp_without_time_zone isir_added
        text monitor_obchr_html
        text monitor_dump_json
        timestamp_without_time_zone updated
        text detail_json
        character_varying address_contact
        character company_country
        timestamp_without_time_zone merged
        character_varying merged_as
        json meta_json
        boolean gender_male
        character country
        character_varying skov_spzn
        character_varying facebook_id
        character_varying code
        character_varying city
        character_varying phone_alt
        character_varying cee_known
        character_varying lustrace_list
        json monitor_json
        json changes_json
        date isir_ended
    }

    invoice {
        integer client
        numeric vs
        timestamp_without_time_zone created
        character_varying project_path
        integer supplier
        integer purchaser
        bigint charge_cents
        date issued_date
        date due_date
        text interest_json
        text user_note
        bytea content
        integer id
        json debt_json
        integer accession_cents
        bigint actual_cents
        timestamp_without_time_zone computed
        character currency
        character_varying extra_id
        character_varying last_json
        character_varying state
        integer billed
        character_varying kind
        date contract_date
    }

    project_entry {
        integer id
        timestamp_with_time_zone created
        timestamp_without_time_zone updated
        integer client
        integer project
        character_varying kind
        text note_text
        numeric work_quantity
        integer work_cents
        timestamp_without_time_zone task_start
        character_varying task_location
        character_varying file_name
        bytea file_content
        character_varying sent_id
        character_varying sent_external_id
        timestamp_without_time_zone task_finished
        character_varying task_result
        character_varying task_login
        character_varying author
        character_varying file_type
        character_varying nepr_batch
        integer nepr_trial
        character_varying task_verb
        character_varying form_name
        json form_json
        integer reply_from
        integer work_invoice
        timestamp_without_time_zone work_date
        timestamp_without_time_zone snoozed
        character_varying snoozed_by
        integer file_next
        character_varying el_json
        character_varying note_color
        character_varying task_priority
        json meta_json
        integer work_client
        timestamp_without_time_zone file_nearline
    }

    project {
        integer id
        character box
        timestamp_without_time_zone created
        character_varying path
        text meta_json
        integer client
        character_varying pl_people
        character_varying batch
        timestamp_without_time_zone c_le_created
        character_varying c_le_kind
        character_varying c_le_note
        character_varying c_le_login
        character_varying category
        character_varying state
        character_varying note_html
        character_varying task_login
        text fulltext
        tsvector searchable
        character_varying c_le_verb
        ARRAY labels
        json eye_json
        jsonb fields
        character_varying extra_id
        character_varying substate
        timestamp_without_time_zone changed
        timestamp_without_time_zone archived
        character_varying archived_by
        character_varying dateseq
        integer value
        ARRAY audit_list
    }

    debt }o--|| invoice : "invoice"
    paid }o--|| debt : "debt"
    lustrace }o--|| person : "person"
    lustrace }o--|| project : "project"
    project_person }o--|| person : "person"
    project_person }o--|| project : "project"
    bank_txn }o--|| project : "project"
    paid }o--|| bank_txn : "txn"
    paid }o--|| invoice : "invoice"
    paid }o--|| person : "payee"
    paid }o--|| person : "payer"
    invoice }o--|| person : "purchaser"
    invoice }o--|| person : "supplier"
    project_entry }o--|| person : "reply_from"
    project_entry }o--|| person : "work_client"
    invoice }o--|| invoice : "billed"
    project_entry }o--|| invoice : "work_invoice"
    project_entry }o--|| project_entry : "file_next"
    project_entry }o--|| project : "project"
```

Schema bylo vygenerováno pomocí [mermerd](https://github.com/KarnerTh/mermerd):

```bash
mermerd -c "postgresql://is:...@localhost:5432/is" -s public --selectedTables project,invoice,debt,paid,project_entry,person,lustrace,bank_txn,lead_event,project_person
```

Ve schematech chybí některé irelevantní tabulky.
