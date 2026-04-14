# Ur/Web Automated Translator

A Python-based automated translator that generates valid Ur/Web application code
from a structured JSON specification. Developed as part of a Masters-level study
into secure web application development using the Ur/Web framework.

---

## What It Does

This tool takes a JSON file (`app_spec.json`) describing a web application's
structure and automatically generates three Ur/Web source files:

| Output File | Purpose |
|---|---|
| `output/main.ur` | Full application logic — table, validation, queries, UI |
| `output/main.urs` | Module signature (public interface) |
| `output/db.sql` | PostgreSQL-compatible database schema |

The generated code is immediately usable in an Ur/Web project and demonstrates
security-by-construction properties including type-safe SQL and XSS prevention.

---

## Repository Structure

urweb-translator/
├── translator.py     ← The translator program
├── app_spec.json     ← Input: JSON specification of the app
├── output/
│   ├── main.ur       ← Generated: Ur/Web application logic
│   └── db.sql        ← Generated: Database schema
└── README.md

---

## How to Run

Requires Python 3. No external libraries needed.

```bash
python translator.py
```

Or with a custom spec file:

```bash
python translator.py app_spec.json
```

Generated files will appear in the `output/` directory.

---

## Input Format — `app_spec.json`

```json
{
  "app_name": "Main",
  "page_title": "Secure Message Board",
  "table": {
    "name": "messages",
    "fields": [
      { "name": "Username", "type": "string", "max_length": 50 },
      { "name": "Content",  "type": "string", "max_length": 500 }
    ]
  },
  "sequence": "msgSeq",
  "features": {
    "input_validation": true,
    "reactive_refresh": true,
    "order_by_desc": true
  }
}
```

The `features` block controls which security and UI properties are generated:

- `input_validation` — adds server-side empty and length checks
- `reactive_refresh` — generates reactive UI using Ur/Web's `source`/`dyn` system
- `order_by_desc` — displays newest messages first

---

## Security Properties of Generated Code

Because the output targets Ur/Web, the generated application inherits the
following security guarantees by construction:

- **SQL Injection Prevention** — all database values use Ur/Web's typed
  parameterisation via `{[value]}` syntax; string concatenation into queries
  is impossible at the type level
- **XSS Prevention** — all user content rendered in HTML uses `{[value]}`
  which the compiler automatically escapes
- **CSRF Protection** — Ur/Web inserts CSRF tokens automatically for all
  RPC calls; the translator does not need to handle this explicitly
- **Input Validation** — the translator generates server-side length and
  empty-string checks based on field specifications in the JSON

---

## Relationship to Ur/Web Application Repo

The output of this translator directly corresponds to the application in:
[https://github.com/DuMMy9813/Urweb-secure-message-board](https://github.com/DuMMy9813/Urweb-secure-message-board)

This demonstrates end-to-end automated translation: from a declarative JSON
model of functional requirements, to a fully working secure Ur/Web application.
