# Knowledge base

Exported on 10 November 2026 from the Compliance Copilot production database. One folder per country, in
the same layout.

| | Tanzania (`TZ/`) | United Arab Emirates (`AE/`) |
|---|---|---|
| Documents (`documents.csv`) | 61 laws (55 Acts, 6 regulations) | 95 laws and official procedure pages, 24 of them in Arabic |
| Sections (`sections.jsonl`) | 6,871 | 4,465 |
| Verified steps (`obligations.jsonl`) | 507, with 492 links between them | 189, with 154 links between them |
| Regulators (`regulators.csv`) | 47 | 25 |
| Languages | English and Swahili | Arabic and English |
| Profile questions (`profile_schema.yaml`) | yes | yes |
| Step order from official guides (`journey.yaml`) | | yes |
| Manifest in the ITU AI Readiness layout (`Inputs.md`) | yes | yes |

## What each file holds
- **`documents.csv`** holds each document's title, type, language, the regulators it concerns, its official
  `source_url` and the SHA-256 fingerprint of the file we used. `translation_of` links a law to its version
  in another language (UAE laws exist in Arabic and English). `text_published` (UAE) says whether its section
  text is included.
- **`sections.jsonl`** has one legal section or article per line, with its id, the document it belongs to, its
  reference (for example `s.14` or `art.24`), heading path, pages, language and text. `text_source` says
  whether the text came from the PDF's text layer or from OCR.
- **`obligations.jsonl`** is the verified register. What a business must do, which regulator it answers to,
  when it applies, what must come first, and the quoted sections that support each part.
- **`regulators.csv`** lists each regulator's acronym, name and website.
- **`profile_schema.yaml`** holds the facts about a business (legal form, employees, activities and others)
  that the steps' conditions refer to.
- **`journey.yaml`** (UAE) gives the order of steps a business follows, written from the authorities' official
  service guides, with each step's quote. The platform's journey importer loads it into the register.

## How to read it
- **Each law links to its official copy.** Download it from `source_url` and check it against `sha256`.
- **Sections** follow each law's own structure (Part, section, article, schedule), so a citation always
  points to a real section.
- **Steps**
  - `applies_when` says in plain words which businesses a step applies to, and `conditions` is the same rule
    in JSON Logic over the facts in `profile_schema.yaml`. `universal: true` means every business.
  - `necessity` is `essential` (needed to start operating), `recurring` (returns, payments, renewals) or
    `conduct` (rules to follow).
  - `prerequisites` lists what must come first. `hard` links are required by law, and `soft` links set a
    sensible order.
  - `citations` link each part of a step to a section (`section_id`) with the exact words quoted.
  - An empty `fee` or `processing_time` means the documents here do not state it, so confirm with the
    regulator.
- **No embeddings** are included, because vectors depend on the model. Compliance Copilot embeds each section
  as `heading path + text` with `intfloat/multilingual-e5-small` (384 dimensions), and any model can be used.

## Licence
The structure, register, metadata, schemas and journey are released under CC BY 4.0 (see `../LICENSE`). The
laws belong to their official publishers, named in each `documents.csv`.

For 17 UAE documents the publisher's copy carries terms against redistribution (copies typeset by LexisNexis
Middle East, pages of the UAE government portal and the Central Bank Rulebook). For those, `sections.jsonl`
keeps each section's reference, heading and pages with an empty `text` and `text_withheld: true`, and the
quotes in `obligations.jsonl` remain as short citations. The full text is at each `source_url`.
