# Knowledge base: Tanzania

Exported on 2 November 2026 from the Compliance Copilot production database.

| File | Rows | What it holds |
|---|---|---|
| `documents.csv` | 61 | The laws in the knowledge base (55 Acts, 6 regulations): title, type, language, the regulators they concern, the official source URL and the SHA-256 fingerprint of the file we used. |
| `sections.jsonl` | 6,871 | One legal section per line: its id, the law it belongs to, its reference (for example `s.14`), its heading path, pages, language and full text. |
| `obligations.jsonl` | 507 | The verified register: what a business must do, which regulator it answers to, when it applies, what must come first, and the quoted sections that support each part. |
| `regulators.csv` | 47 | Each regulator's acronym, name and website. |
| `profile_schema.yaml` | — | The facts about a business (legal form, employees, activities and others) that the obligations' conditions refer to. |
| `Inputs.md` | 61 | The same documents as a manifest in the layout of the ITU AI Readiness reference code. |

## How to read it
- **Laws are linked, not copied.** Download each law from its `source_url` and check it against the `sha256`
  to confirm it is the same file the sections were taken from.
- **Sections** are cut along each law's own structure (Act, Part, section, schedule), so a citation always
  points to a real section. `text_source` says whether the text came from the PDF's text layer or from OCR.
- **Obligations**
  - `applies_when` states, in plain words, which businesses an obligation applies to; `conditions` is the same
    rule in JSON Logic over the facts in `profile_schema.yaml`. `universal: true` means it applies to every
    business.
  - `necessity` is `essential` (needed to start operating), `recurring` (returns, payments, renewals) or
    `conduct` (rules to follow).
  - `prerequisites` lists what must come first: `hard` links are required by law, `soft` links set a
    sensible order. There are 492 links across 322 obligations.
  - `citations` link each part of an obligation to a section (`section_id`) with the exact words quoted.
  - An empty `fee` or `processing_time` means the laws here do not state it; confirm with the regulator.
- **No embeddings** are included, because vectors depend on the model. Compliance Copilot embeds each section
  as `heading path + text` with `intfloat/multilingual-e5-small` (384 dimensions); any model can be used.

## Licence
The structure, register, metadata and schema are released under CC BY 4.0 (see `../LICENSE`). The laws
themselves belong to their official publishers: the Office of the Attorney General of Tanzania and the
regulators named in `documents.csv`.
