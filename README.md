# SaltStack Plugin for JetBrains IDEs

Full-featured IDE support for **SaltStack** `.sls` formula files — the YAML + Jinja2 hybrid format used for Salt states, pillars, top files, reactors, and orchestration. Works in IntelliJ IDEA, PyCharm, and all other JetBrains IDEs based on the IntelliJ Platform.

## Codebase reports
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/8068eafbb1d54c7c9a4b39aa1f993aa0)](https://app.codacy.com?utm_source=gh&utm_medium=referral&utm_content=&utm_campaign=Badge_grade)
[![Codacy Badge](https://app.codacy.com/project/badge/Coverage/8068eafbb1d54c7c9a4b39aa1f993aa0)](https://app.codacy.com?utm_source=gh&utm_medium=referral&utm_content=&utm_campaign=Badge_coverage)
- Covered / Coverable lines: 6k / 8k
- Files above coverage goal: 172
- Files below coverage goal: 105

## Features

### Smart Completion

- **State functions** — suggests `module.function` entries (e.g. `pkg.installed`, `file.managed`, `service.running`) based on Salt metadata. Filters by prefix as you type.
- **Arguments** — inside a state function block, suggests valid arguments for the specific function along with global state arguments (`onlyif`, `unless`, `order`, `listen`, etc.). Already-used keys are deprioritized.
- **Requisites** — suggests requisite keywords (`require`, `watch`, `onchanges`, `onfail`, etc.) including reverse forms (`require_in`, `watch_in`, etc.) and `_any` variants. Completes module names and state IDs inside requisite lists.
- **Smart Enter** — pressing Enter inside a `- key: value` argument block automatically continues with `- ` at the correct indentation.
- **Jinja2 import paths** — path completion for `import_yaml` / `import_json` / `import_text` tags and `{% include %}` / `{% extends %}` template paths.

### Validation and Diagnostics

- **State ID checks** — detects empty IDs, reserved prefixes, trailing whitespace, and naming convention violations.
- **Duplicate state IDs** — highlights duplicate top-level state IDs within a single file. Jinja2-templated IDs (`{{ id }}`) are safely skipped.
- **Unknown state functions** — warns when a `module.function` key does not match known Salt metadata.
- **Unknown arguments** — warns on unrecognized arguments in a state function call. Global args and requisites are never flagged.
- **Requisite structure** — checks requisite list shape, item format, and resolves referenced state IDs within the file.
- **Global args** — validates `onlyif`, `unless`, `creates`, `order`, and other global state arguments.
- **Directives** — validates `include`, `extend`, and `exclude` blocks for correct structure and resolvable targets.
- **Top file validation** — checks matcher syntax (glob, grain, pillar, compound, etc.) and validates the environment field structure.
- **Reactor and Orchestrate** — validates reactor event declarations and orchestration step structure.
- **Jinja2 import paths** — warns on unresolved `import_yaml` / `import_json` / `import_text` paths.
- **Duplicate key false positives suppressed** — the standard YAML "Duplicated keys" inspection is automatically silenced for keys separated by `{% if %}/{% else %}` blocks — a valid and common Salt pattern.

### Navigation (Ctrl+Click)

- **Go to state declaration** — click on a requisite state ID to jump to its declaration.
- **Go to included file** — click on an `include` entry to open the target `.sls` file. Falls back to `init.sls` when resolving package-style paths (e.g. `nginx.config` -> `nginx/config/init.sls`).
- **salt:// URL navigation** — click on `salt://` paths to open the referenced file relative to the configured state roots.
- **Pillar target navigation** — click on targets in pillar top files to jump to the corresponding pillar data files.
- **Jinja2 import navigation** — click on `import_yaml` / `import_json` / `import_text` paths to open the imported file.
- **Jinja2 alias navigation** — click on an alias defined via `import_yaml ... as config` or `{% import ... as X %}` to jump to the import binding declaration.
- **Find Usages** and **Rename** refactoring are supported for all reference types above.

### Quick Documentation (Ctrl+Q)

- Hover or press Ctrl+Q on a **state function** to see its description, supported arguments, and types.
- Documentation is also available for individual **function arguments**, **requisite keywords**, and **global state arguments**.
- **Top file**, **pillar**, and **Saltfile** elements have documentation popups as well.
- When the Jinja2 Enhanced plugin is active, Jinja2 variables (`grains`, `pillar`, `opts`, etc.) and filters show Salt-specific documentation.

### Syntax Highlighting

- Full YAML highlighting inside state and pillar files.
- Distinct colors for Jinja2 expressions (`{{ }}`), statements (`{% %}`), and comments (`{# #}`).
- Customizable via **Settings -> Editor -> Color Scheme -> SaltStack**.

### Editor Helpers

- **Smart Jinja2 delimiter closing** — typing `{{`, `{%`, or `{#` automatically inserts the matching closing delimiter.
- **Multi-language commenter** — Ctrl+/ inserts `#` comments in YAML regions and `{# #}` block comments inside Jinja2 regions.
- **Import snippet inlay hints** — `import_yaml` / `import_json` statements render the imported data structure as inlay hints directly on the import line.
- **Spellcheck suppression** — spellcheck is automatically disabled inside Jinja2 expressions and statements.

### File Role Detection

- Automatically classifies each `.sls` file as **State**, **Pillar**, **Top**, **Reactor**, or **Orchestrate** based on filename, directory structure, and configured roots.
- Distinct file icons for each role — visible in the project tree and editor tabs.
- `Saltfile` (Salt CLI config) is detected and highlighted separately.
- Render mode (`yaml`, `jinja|yaml`, `py`) is detected from the shebang line and applied per file.

### Project Configuration

- Mark directories as **State Root**, **Pillar Root**, **Master Root**, or **Module Dirs** via right-click -> *Mark Directory As*. This drives file role detection, path completion, and reference resolution.
- Configure the target **Salt version** for metadata matching.
- Configure a **remote metadata URL** and **cache TTL** for up-to-date module and function metadata (falls back to bundled resources when unavailable).
- Per-domain **feature flags** — enable or disable IDE features for individual Salt domains (state, pillar, top, reactor, orchestrate) independently.
- All settings are **project-scoped** — different projects can use different configurations.

## Jinja2 Integration

When the [Jinja2 Enhanced](https://plugins.jetbrains.com/plugin/29804-jinja2-enhanced) plugin is installed, the following additional features become available:

- Full Jinja2 PSI inside Salt files — syntax checks, variable resolution, filter completion.
- Salt-specific Jinja2 variable documentation (`grains`, `pillar`, `opts`, `saltenv`, etc.).
- Salt-specific Jinja2 filter documentation (`tojson`, `yaml_encode`, etc.).
- Template path completion for `{% include %}` and similar tags.
- Alias binding resolution — variables bound via `import_yaml ... as X` are tracked for completion, Find Usages, and Rename refactoring.
- Type schema inference — the structure of imported YAML/JSON files is used to validate and complete attribute access on alias variables.

The plugin works standalone without Jinja2 Enhanced — Jinja2-specific features are simply not active.

## Requirements

- IntelliJ IDEA 2024.1+, PyCharm 2024.1+, or any JetBrains IDE based on the IntelliJ Platform 2024.1+
- [Jinja2 Enhanced](https://plugins.jetbrains.com/plugin/29804-jinja2-enhanced) plugin — required for full template support

## Roadmap

### Legend

| Symbol | Status                               |
|--------|--------------------------------------|
| ✅     | Done — fully implemented             |
| ⚠     | Partial — implemented but incomplete |
| ❌     | Not yet implemented                  |

---

### 1. Core Language Support

#### 1.1 File System and Types

| Feature                                                            | Status |
|--------------------------------------------------------------------|--------|
| `.sls` file extension registration                                 | ✅     |
| Auto-detection of file type                                        | ✅     |
| File icons per role (state, pillar, top, reactor, orchestrate)     | ✅     |
| File kind detection (State / Pillar / Top / Reactor / Orchestrate) | ✅     |
| Saltfile (Salt CLI config) without extension                       | ✅     |

#### 1.2 Lexer and Parser

| Feature                       | Status |
|-------------------------------|--------|
| JFlex-based grammar and lexer | ✅     |
| PSI tree parser               | ✅     |
| AST node factory              | ✅     |

#### 1.3 Multi-Language Support (Salt + YAML + Jinja2)

| Feature                                          | Status |
|--------------------------------------------------|--------|
| Three-language file view (Salt, YAML, Jinja2)    | ✅     |
| Jinja2 placeholder substitution for YAML parsing | ✅     |
| Jinja2 as secondary PSI (not per-fragment)       | ✅     |

#### 1.4 Preprocessor

| Feature                                               | Status |
|-------------------------------------------------------|--------|
| Jinja2-to-placeholder preprocessing                   | ✅     |
| Offset mapping between original and preprocessed text | ✅     |

---

### 2. Basic IDE Features

| Feature                                                           | Status |
|-------------------------------------------------------------------|--------|
| Syntax highlighting                                               | ✅     |
| Code formatting                                                   | ✅     |
| Jinja2 post-format processing                                     | ✅     |
| Jinja2 auto-close delimiters (`{{`, `{%`, `{#`)                   | ✅     |
| Multi-language commenter (YAML `#` / Jinja2 `{# #}`)              | ✅     |
| Spellcheck suppression inside Jinja2                              | ✅     |
| Code completion                                                   | ✅     |
| Quick documentation (Ctrl+Q)                                      | ✅     |
| YAML duplicate key inspection suppression for Jinja2 conditionals | ✅     |
| Reference resolution (YAML layer)                                 | ✅     |

---

### 3. Salt Domain Support

#### 3.1 State (`.sls`)

**Requisites**

| Feature                                                                                     | Status                 |
|---------------------------------------------------------------------------------------------|------------------------|
| Direct requisites: `require`, `watch`, `onchanges`, `onfail`, `prereq`, `listen`, `use`     | ✅                     |
| Reverse forms: `require_in`, `watch_in`, `onchanges_in`, `onfail_in`, `prereq_in`, `use_in` | ⚠ `listen_in` missing |
| Alternative forms: `require_any`, `watch_any`, `onchanges_any`, `onfail_any`                | ❌                     |
| `onfail_all` (AND-logic for onfail)                                                         | ❌                     |
| `sls: <name>` target (require an entire SLS file)                                           | ✅ validation only     |
| Wildcards in requisites (`*`, `?`, `[]`)                                                    | ❌                     |
| Bare-id form in requisites (`- vim` instead of `- pkg: vim`)                                | ❌                     |

**Validation**

| Feature                                                                                                       | Status                  |
|---------------------------------------------------------------------------------------------------------------|-------------------------|
| Duplicate state ID detection                                                                                  | ⚠ temporarily disabled |
| Argument validation                                                                                           | ✅                      |
| State function validation                                                                                     | ✅                      |
| Requisite validation (direct forms)                                                                           | ✅                      |
| State ID syntax validation                                                                                    | ✅                      |
| Global args: `onlyif`, `unless`, `creates`, `order`, `failhard`, `runas`, `check_cmd`, `retry`                | ✅                      |
| Global args: `umask`, `parallel`, `reload_modules`, `reload_pillar`, `reload_grains`, `fire_event`, `saltenv` | ❌                      |
| `include:` directive validation                                                                               | ✅                      |
| `extend:` declaration validation                                                                              | ✅                      |
| `exclude:` directive validation                                                                               | ✅                      |
| Full-form state declaration validation (`pkg: - installed`)                                                   | ❌                      |
| Short-form state declaration validation (`pkg.installed`)                                                     | ❌                      |
| Multiple state declarations under one ID                                                                      | ❌                      |
| Implicit `name` from state ID                                                                                 | ❌                      |
| `names:` list expansion validation                                                                            | ❌                      |
| Argument type checking (beyond name matching)                                                                 | ❌                      |
| Cross-file state ID uniqueness                                                                                | ❌                      |
| Circular requisite dependency detection                                                                       | ❌                      |

**Completion**

| Feature                                       | Status                                     |
|-----------------------------------------------|--------------------------------------------|
| State module functions from metadata          | ✅                                         |
| Function arguments + global args + requisites | ⚠ global args partially covered (9 of 17) |
| Requisite completion (direct, module + ID)    | ✅                                         |
| Reverse requisite completion (`*_in`)         | ✅                                         |
| Alternative requisite completion (`*_any`)    | ❌                                         |
| `onfail_all` completion                       | ❌                                         |

**Documentation and Navigation**

| Feature                                                          | Status |
|------------------------------------------------------------------|--------|
| Documentation for functions, parameters, requisites, global args | ✅     |
| Go to included file (`include` -> `.sls`)                        | ✅     |
| Go to state ID from requisites (Ctrl+Click)                      | ✅     |
| `salt://` path navigation and completion                         | ✅     |

#### 3.2 Top (`top.sls`)

**Matcher Types (13 types per Salt documentation)**

| Matcher        | Example                        | Status                          |
|----------------|--------------------------------|---------------------------------|
| `glob`         | `'web*'`, `'minion123'`        | ✅ validation + docs            |
| `pcre`         | `'^web[0-3]'`                  | ✅ validation + docs            |
| `grain`        | `'os:Ubuntu'`                  | ✅ validation + docs            |
| `grain_pcre`   | `'os:(Free\|Open)BSD'`         | ✅ validation + docs            |
| `list`         | `'minion1,minion2'`            | ✅ validation + docs            |
| `compound`     | `'nag1* or G@role:monitoring'` | ✅ structural validation + docs |
| `ipcidr`       | `'172.17.0.0/16'`              | ✅ validation + docs            |
| `pillar`       | `'role:webserver'`             | ✅ validation + docs            |
| `pillar_pcre`  | `'role:web(server\|proxy)'`    | ✅ validation + docs            |
| `pillar_exact` | `'role:webserver'` (exact)     | ✅ validation + docs            |
| `nodegroup`    | pre-defined in master config   | ✅ structural validation + docs |
| `range`        | range cluster syntax           | ✅ structural validation + docs |
| `data`         | minion datastore               | ✅ validation + docs            |

**Top File Structure**

| Feature                                                                  | Status                |
|--------------------------------------------------------------------------|-----------------------|
| Environment validation (`base`, `dev`, `prod`, ...)                      | ✅                    |
| `match: <type>` declaration validation and completion                    | ✅                    |
| Completion for environments, matchers, and SLS paths                     | ✅                    |
| Documentation for environments and matcher types                         | ✅                    |
| SLS path validation (dot-notation, e.g. `apache.config`)                 | ✅                    |
| Default matcher (`compound`) when `match:` is absent                     | ✅                    |
| Scalar shorthand (`'*': common`)                                         | ❌                    |
| Full compound matcher parsing (`G@`, `P@`, `I@`, operators, parentheses) | ❌                    |
| Target expression syntax validation per `match:` type                    | ❌                    |
| Environment-aware SLS resolution                                         | ❌                    |
| Separate State Top vs. Pillar Top semantics                              | ❌                    |
| Pillar Top `ignore_missing: true`                                        | ❌                    |
| Jinja environment key (`{{ saltenv }}:`)                                 | ❌                    |
| Top file merging strategy support                                        | ⚠ documentation only |

#### 3.3 Pillar

| Feature                                                                | Status |
|------------------------------------------------------------------------|--------|
| `include:` completion at top level                                     | ✅     |
| Key path documentation (dot-notation + depth)                          | ✅     |
| Key validation                                                         | ❌     |
| Structure validation                                                   | ❌     |
| Key references (Go to / Find Usages)                                   | ❌     |
| Pillar Top `ignore_missing: true`                                      | ❌     |
| Relative includes (`.relative`, `..parent`)                            | ❌     |
| Environment-specific includes                                          | ❌     |
| Pillar template variables (`__opts__`, `__grains__`, `__pillar__`)     | ❌     |
| Full `include:` form with `defaults:` and `key:`                       | ❌     |
| Pillar merge order and conflict diagnostics                            | ❌     |
| Navigation from Pillar Top to Pillar SLS                               | ❌     |
| Completion for `pillar.get()`, `pillar['key']`, `salt['pillar.get']()` | ❌     |
| Multiple Pillar environments (`pillarenv`)                             | ❌     |

#### 3.4 Orchestrate

| Feature                                                                             | Status |
|-------------------------------------------------------------------------------------|--------|
| `salt.state`, `salt.function`, `salt.runner`, `salt.wheel` completion               | ❌     |
| `salt.parallel_runners` and `salt.wait_for_event`                                   | ❌     |
| Arguments for orchestration functions (`tgt`, `tgt_type`, `batch`, `timeout`, etc.) | ❌     |
| Requisites between orchestration steps                                              | ❌     |
| Navigation from orchestration steps to State SLS / Top / functions                  | ❌     |

#### 3.5 Reactor

| Feature                                                                      | Status |
|------------------------------------------------------------------------------|--------|
| Event tag pattern completion (`salt/minion/*/start`)                         | ❌     |
| Reaction type completion (`local`, `runner`, `wheel`, `caller`)              | ❌     |
| Access to event data in Jinja (`{{ data['id'] }}`)                           | ❌     |
| Reactor-specific Jinja context (`tag`, `data`, `salt`; no `grains`/`pillar`) | ❌     |
| Reaction schema validation (`args:`, `arg:`/`kwarg:`)                        | ❌     |
| `tgt`, `tgt_type` and function argument validation                           | ❌     |

#### 3.6 Salt System Constructs

| Feature                                                                | Status |
|------------------------------------------------------------------------|--------|
| **Mine**: `mine.get()` completion, `mine_functions:` navigation        | ❌     |
| **SDB**: `sdb://profile/key` highlighting and validation               | ❌     |
| **Slots**: `__slot__:salt:module.function(args)` syntax and completion | ❌     |
| **`names:`** with per-item dict overrides                              | ❌     |
| **`fire_event:`** as a global state argument                           | ❌     |

---

### 4. Jinja2 Integration

#### 4.1 Extension Providers (requires Jinja2 Enhanced plugin)

| Feature                                            | Status                      |
|----------------------------------------------------|-----------------------------|
| Filter provider (Salt-specific Jinja2 filters)     | ✅                          |
| Variable provider (Salt-specific Jinja2 variables) | ⚠ see variable table below |
| Type schema provider                               | ✅                          |
| Inspection rule provider                           | ✅                          |
| Tag behavior provider                              | ✅                          |
| Assignment tag provider (`import_yaml ... as X`)   | ✅                          |
| Scope variable provider                            | ✅                          |
| Element documentation provider                     | ✅                          |
| Injection language provider                        | ✅                          |
| Template root provider                             | ✅                          |

**Jinja2 Variables Available in All Templates**

| Variable  | Description                            | Status |
|-----------|----------------------------------------|--------|
| `salt`    | Dict of all execution module functions | ✅     |
| `opts`    | Minion config                          | ✅     |
| `pillar`  | Pillar data                            | ✅     |
| `grains`  | Grains data                            | ✅     |
| `saltenv` | Current environment name               | ❌     |
| `env`     | Alias for `saltenv` (deprecated)       | ❌     |

**Jinja2 Variables Available Only in SLS Files**

| Variable       | Description                                        | Status          |
|----------------|----------------------------------------------------|-----------------|
| `sls`          | Import path of the current SLS                     | ⚠ not verified |
| `slspath`      | Directory path relative to file_roots              | ⚠ not verified |
| `slsdotpath`   | `slspath` with dots instead of slashes             | ⚠ not verified |
| `sls_path`     | `slspath` with underscores (added in Salt 3005)    | ❌              |
| `slscolonpath` | `slspath` with colons                              | ❌              |
| `tplpath`      | Full filesystem path                               | ❌              |
| `tplfile`      | Relative path from file_roots (added in Salt 3005) | ❌              |
| `tpldir`       | Directory from file_roots (added in Salt 3005)     | ❌              |
| `tpldot`       | `tpldir` with dots                                 | ❌              |

**File-Kind-Specific Jinja2 Context**

| Context        | Expected Variables                                            | Status                         |
|----------------|---------------------------------------------------------------|--------------------------------|
| State / Pillar | `salt`, `opts`, `pillar`, `grains`, `saltenv` + SLS variables | ❌ no file-kind-specific model |
| Orchestrate    | Master-side context and functions                             | ❌                             |
| Reactor        | Only `salt`, `tag`, `data` (no `grains`/`pillar`)             | ❌                             |

**Salt-Specific Jinja2 Constructs**

| Feature                                                       | Status                         |
|---------------------------------------------------------------|--------------------------------|
| `{% load_yaml %}`, `{% load_json %}`, `{% load_text %}`       | ❌                             |
| `{% import_yaml %}`, `{% import_json %}`, `{% import_text %}` | ⚠ paths and aliases supported |
| `{% profile as 'name' %}...{% endprofile %}`                  | ❌                             |
| `ifelse()` global function                                    | ❌                             |
| Full Salt filter/test catalog with signatures                 | ❌                             |
| `salt['module.function'](...)` call indexing and completion   | ❌                             |
| `with context` for include/import                             | ❌                             |

#### 4.2 Completion

| Feature                                                       | Status |
|---------------------------------------------------------------|--------|
| `import_yaml` / `import_json` / `import_text` path completion | ✅     |
| Template path completion for `{% include %}`                  | ✅     |

#### 4.3 Navigation

| Feature                                           | Status       |
|---------------------------------------------------|--------------|
| Go to file by import path                         | ✅           |
| Go to alias binding declaration                   | ✅           |
| Find Usages for variables                         | ✅           |
| Rename refactoring for variables                  | ✅           |
| Ctrl+Click inside `{% if %}` blocks (import path) | ❌ known bug |

#### 4.4 References

| Feature                          | Status |
|----------------------------------|--------|
| Import file reference resolution | ✅     |
| Alias reference resolution       | ✅     |
| Snippet file resolution          | ✅     |

#### 4.5 Alias Binding (Scope)

| Feature                                 | Status |
|-----------------------------------------|--------|
| Bindings from `{% import %}`            | ✅     |
| Bindings from `{% load_* %}`            | ✅     |
| Static values (YAML/JSON/text)          | ✅     |
| File content loading for type inference | ✅     |

#### 4.6 Inlay Hints

| Feature                               | Status                       |
|---------------------------------------|------------------------------|
| Inline hints for imported data blocks | ⚠ known bug with stale data |

#### 4.7 Inspection

| Feature                                                       | Status |
|---------------------------------------------------------------|--------|
| Unresolved `import_yaml` / `import_json` / `import_text` path | ✅     |

#### 4.8 Documentation

| Feature                     | Status |
|-----------------------------|--------|
| Import path documentation   | ✅     |
| Salt variable documentation | ✅     |
| Salt filter documentation   | ✅     |

---

### 5. Metadata

| Feature                                                 | Status |
|---------------------------------------------------------|--------|
| Data models (Module, Function, Param, Requisite, Grain) | ✅     |
| JSON metadata parsing                                   | ✅     |
| Metadata service and cache                              | ✅     |
| Bundled metadata files (states, requisites)             | ✅     |
| Metadata loader (dedicated class)                       | ❌     |

---

### 6. Settings

| Feature                              | Status |
|--------------------------------------|--------|
| Settings page in IDE                 | ✅     |
| Core Salt settings (version, roots)  | ✅     |
| Feature flags per domain             | ✅     |
| Mark Directory As: State Root        | ✅     |
| Mark Directory As: Pillar Root       | ✅     |
| Mark Directory As: Master Root       | ✅     |
| Mark Directory As: Module Dirs       | ✅     |
| Mark Directory As: Extension Modules | ✅     |

---

### 7. Known Issues

| Issue                                                          | Priority |
|----------------------------------------------------------------|----------|
| Go to Declaration inside `{% if %}` blocks returns null        | High     |
| Inlay hint provider may show stale data                        | High     |
| Find Usages handler may read PSI without proper locking        | High     |
| `import_yaml` path inside `{% if %}` not resolved              | Medium   |
| `DuplicateIdValidator` intentionally disabled pending redesign | Medium   |

---

### 8. Planned Improvements (by priority)

**Critical (unblocks other work)**

1. Fix Go to Declaration inside `{% if %}` blocks
2. Fix stale PSI in inlay hint provider

**Quick wins (small effort, high value)**

3. Add `listen_in` to reverse requisites
4. Add `onfail_all` constant, completion, and documentation
5. Add missing global args validation: `umask`, `parallel`, `reload_modules`, `reload_pillar`, `reload_grains`, `fire_event`, `saltenv`
6. Sync global args list between completion and validation
7. Add missing Jinja2 SLS variables: `saltenv`, `env`, `sls_path`, `slscolonpath`, `tplfile`, `tpldir`, `tpldot`

**Functional minimum**

8. Implement duplicate state ID validation (redesigned)
9. Add `*_any` requisite completion (`require_any`, `watch_any`, etc.)
10. Support wildcards in requisites (`*`, `?`, `[]`)
11. Pillar `include:` options — `defaults:`, `key:`, relative and env-specific includes
12. Top domain — full matcher validation for all 13 types
13. Pillar domain — key validation, structure validation, key references
14. Reactor — event tag patterns, reaction types, `{{ data }}` access

**New functionality**

15. Pillar Top — navigation to Pillar SLS, `ignore_missing`, separate pillar root semantics
16. `import_yaml` path resolution via `pillar_roots`
17. Include preview — preview of included file content
18. Orchestrate — `salt.state`, `salt.function`, `salt.runner`, `salt.wheel` completion and validation
19. Mine — `mine.get()` completion, `mine_functions:` navigation
20. SDB — `sdb://profile/key` highlighting and validation
21. Slots — `__slot__:salt:...` highlighting, `.field` and `~ suffix` syntax
22. `fire_event:` — global state arg in completion and validation
23. `names:` with dict overrides — structure validation
24. State High Data — full and short declaration forms, `name`, `names`, multiple declarations per ID
25. Cross-file state tree — ID uniqueness after `include`, circular requisites, dependency graph
26. Global state argument type schemas — `onlyif`, `unless`, `creates`, `order`, `retry`, `fire_event`, `umask`
27. Top shorthand and default matcher — scalar shorthand, correct default `compound`
28. Compound matcher parser — all matcher prefixes, operators, and parentheses
29. State Top / Pillar Top — separate context, roots, and environment-aware resolution
30. Pillar include — full form with `defaults` and `key`
31. Pillar merge model — order, recursive dictionary merge, conflict diagnostics
32. Pillar key references — completion and navigation for `pillar.get`, `pillar['key']`, `salt['pillar.get']`
33. Orchestrate `saltmod` — `salt.wait_for_event` and full schemas for all six state functions
34. Orchestrate navigation — references from `sls`, `top`, and `name` to targets
35. Reactor schemas — unified `args`, legacy `arg`/`kwarg`, reaction types
36. Reactor Jinja2 context — `tag`, `data`, `salt` and restriction of unavailable constructs
37. Salt Jinja2 tags — `load_*`, `import_*`, `profile`, and `with context`
38. Salt Jinja2 API — `ifelse`, filter/test catalog, `salt['module.function']` signatures

## License

Proprietary. See [LICENSE](LICENSE) for details.