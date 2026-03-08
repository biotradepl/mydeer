# CLAUDE.md

This file provides guidance for AI assistants working in this repository.

## Project Overview

This is a **Microsoft Dynamics 365 Business Central** extension project written in **AL (Application Language)**. The repository is in early/initial state — no AL source files exist yet. The project is licensed under **Apache License 2.0**.

## Technology Stack

- **Language**: AL (Application Language) — the proprietary language for Business Central extensions
- **Platform**: Microsoft Dynamics 365 Business Central
- **Tooling**: AL Language extension for VS Code (`ms-dynamics-smb.al`)
- **License**: Apache 2.0

## Repository Structure (Expected)

Based on the `.gitignore`, the standard AL project layout is:

```
/
├── CLAUDE.md
├── LICENSE
├── README
├── app.json              # Extension manifest (name, version, publisher, dependencies)
├── .gitignore
├── src/                  # AL source files (.al)
│   ├── table/            # Table and TableExtension objects
│   ├── page/             # Page and PageExtension objects
│   ├── codeunit/         # Codeunit objects
│   ├── report/           # Report objects
│   ├── enum/             # Enum and EnumExtension objects
│   └── ...
├── .vscode/              # VS Code workspace settings (gitignored)
│   └── launch.json       # BC server connection config (gitignored)
├── .alcache/             # AL compiler cache (gitignored)
├── .alpackages/          # Dependency symbols (gitignored)
├── .snapshots/           # Page inspector snapshots (gitignored)
└── .output/              # Test output (gitignored)
```

**Gitignored artifacts** (never commit these):
- `.vscode/` — contains local server connection credentials
- `.alcache/`, `.alpackages/`, `.snapshots/`, `.output/` — generated/local artifacts
- `*.app` — compiled extension packages
- `rad.json` — Rapid Application Development state file
- `*.g.xlf` — auto-generated translation base files
- `*.flf` — license files
- `TestResults.xml` — test output

## AL Development Conventions

### Object Numbering
- Every AL object (Table, Page, Codeunit, etc.) requires a unique integer ID
- IDs come from a registered range assigned by Microsoft (for AppSource apps) or agreed range for per-tenant extensions (PTEs)
- Do not reuse or guess IDs — confirm the assigned range before creating objects

### File Naming
- One object per file
- Convention: `<ObjectType><ObjectId>.<ObjectName>.al`
- Examples:
  - `Table50100.MyTable.al`
  - `Page50100.MyPage.al`
  - `Codeunit50100.MyCodeunit.al`

### Code Style
- Use `PascalCase` for object names, field names, variable names, and procedure names
- Use `camelCase` for local variables only when following team convention
- Always add `Caption` properties for all user-facing objects and fields
- Use the `#pragma warning disable/restore` pattern sparingly and only when justified
- Prefer `procedure` over `local procedure` when cross-object access is needed
- Always specify `Access = Public/Internal/Local` on procedures in modules

### Object Types Reference
| Type | Keyword | Purpose |
|------|---------|---------|
| Table | `table` | Data storage |
| Table Extension | `tableextension` | Extend base app tables |
| Page | `page` | UI forms/lists |
| Page Extension | `pageextension` | Extend base app pages |
| Codeunit | `codeunit` | Business logic |
| Report | `report` | Printed/processed output |
| Enum | `enum` | Typed enumeration values |
| Enum Extension | `enumextension` | Extend base enums |
| Query | `query` | Data retrieval/aggregation |
| XMLport | `xmlport` | Data import/export |

### app.json
The `app.json` manifest is required and must include:
- `id` — unique GUID for the extension
- `name`, `publisher`, `version` (semver: `Major.Minor.Build.Revision`)
- `platform` and `application` minimum version constraints
- `dependencies` — list of required extensions with `id`, `name`, `publisher`, `version`
- `idRanges` — the assigned object ID ranges

## Development Workflow

### Setup
1. Install [VS Code](https://code.visualstudio.com/) with the **AL Language** extension
2. Run **AL: Download Symbols** (Ctrl+Shift+P) to populate `.alpackages/`
3. Configure `.vscode/launch.json` with your BC sandbox/on-prem connection

### Building
- **Compile**: `Ctrl+Shift+B` in VS Code (or `AL: Package`)
- **Publish**: `F5` to deploy to the connected BC environment
- The compiled output is a `*.app` file (gitignored)

### Testing
- AL test codeunits use `Subtype = Test` on the codeunit
- Test methods are tagged with `[Test]` attribute
- Run tests via the Test Tool page in BC or via `AL: Run Tests`
- Results are written to `TestResults.xml` (gitignored)

### Translation
- Source strings go in `.xlf` translation files under a `Translations/` folder
- The `*.g.xlf` base file is auto-generated (gitignored); only commit the language-specific `.xlf` files

## Key Constraints for AI Assistants

- **Never commit** `.vscode/launch.json` — it may contain credentials
- **Never commit** `*.app` binaries or `.alpackages/` symbols
- **Do not invent object IDs** — always ask for or verify the project's assigned ID range
- **AL is not C#** — though syntax is similar, AL has BC-specific constructs (`Record`, `RecordRef`, `FieldRef`, triggers, etc.)
- When modifying base application objects, use extension objects (`tableextension`, `pageextension`) — never modify base objects directly
- AL procedures in codeunits run within BC's transaction model; be mindful of `Commit()` usage and error handling with `Error()`, `TestField()`, and `FieldError()`

## Git Workflow

- Default branch: `master`
- Feature branches follow the pattern: `claude/<description>-<session-id>`
- Commit messages should be descriptive and reference the object/feature changed
- Apache 2.0 license applies to all contributions
