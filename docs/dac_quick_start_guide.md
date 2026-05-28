# DaC Quick Start Guide

This guide walks through the **technical steps** to set up the detection-rules repo, connect to an Elastic stack, and develop rules with unit tests, custom configuration, and schema validation. It omits governance models and decision trees; for those, see the [full DaC concept and workflows](dac_concept_and_workflows.md). For common questions and troubleshooting, see the [FAQ](faq.md).

---

## Prerequisites

- **Version control**: Git; fork or clone the [detection-rules](https://github.com/elastic/detection-rules) repository.
- **Python**: 3.12+ (see [detection-rules Getting Started](https://github.com/elastic/detection-rules?tab=readme-ov-file#getting-started)).
- **Elastic**: Access to an Elastic stack (Kibana/Elastic Security) for import/export and optional full ES|QL query testing.
- **Basics**: Familiarity with the CLI, YAML/TOML, and pytest (for writing custom tests).

---

## 1. Set up the detection-rules repo

1. **Fork and clone** the [detection-rules](https://github.com/elastic/detection-rules) repo (see [GitHub forking](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)).
2. **Install dependencies** from the repo root:
   ```bash
   cd detection-rules
   make
   ```
   Or: `pip install "[.dev]"`. Using a [Python virtual environment](https://docs.python.org/3/library/venv.html) is recommended.
3. **Verify the CLI**:
   ```bash
   python -m detection_rules -h
   python -m detection_rules custom-rules setup-config -h
   ```

**Repo layout (relevant parts):**

| Folder / file        | Purpose |
|----------------------|--------|
| `detection_rules/`   | Rule parsing, validation, packaging; ECS/Beats schemas |
| `kibana/`            | Kibana/Detection Engine API integration |
| `rules/`             | Default rules directory (prebuilt) |
| `tests/`             | Unit tests |
| `detection_rules/etc/` | Schemas, `stack-schema-map.yaml`, version lock files |

---

## 2. Custom rules directory

Use a **custom rules directory** so your rules and config stay separate from the prebuilt set.

1. **Create and initialize** the directory:
   ```bash
   python -m detection_rules custom-rules setup-config dac_custom_rules_dir
   ```
   This creates a structure like:
   ```
   dac_custom_rules_dir/
   ├── _config.yaml
   ├── actions/
   ├── action_connectors/
   ├── exceptions/
   ├── etc/
   │   ├── deprecated_rules.json
   │   ├── packages.yaml
   │   ├── stack-schema-map.yaml
   │   ├── test_config.yaml
   │   └── version.lock.json
   ├── rules_building_block/
   └── rules/
   ```

2. **Edit `_config.yaml`** in the custom directory. For most DaC use cases, add:
   ```yaml
   # Skip version-lock checks (useful when not using version.lock.json)
   bypass_version_lock: true
   # Normalize KQL keywords for consistency
   normalize_kql_keywords: true
   # Path for auto-generated schema for non-ECS fields; used in validation
   auto_gen_schema_file: "etc/schemas/auto_gen.json"
   # Skip optional, Elastic specific checks that may not apply to your use case
   bypass_optional_elastic_validation: true
   ```
   The generated `_config.yaml` already points `rule_dirs`, `files`, and `testing.config` to paths within this directory (e.g. `rule_dirs: [rules]`, `testing.config: etc/test_config.yaml`).

3. **Set the environment variable** (required for all subsequent steps):
   ```bash
   export CUSTOM_RULES_DIR="/absolute/path/to/dac_custom_rules_dir"
   ```
   Use the **full path** to the custom directory.

With `CUSTOM_RULES_DIR` set, rule loading and tests run only against the rule dirs defined in that config (by default just `rules/` in the custom directory). See [FAQ Q12](faq.md#q12-is-there-a-way-to-run-the-unit-tests-only-on-custom_rules_dir) for more detail.

---

## 3. Connecting to the Elastic stack

The CLI reads auth from a config file, environment variables, or command-line flags. 

**Precedence:** explicit args → environment variables → config file → prompt (where applicable).

1. **Create a config file** in the **root of the detection-rules repo** (not inside the custom rules dir):
   - **Filename:** `.detection-rules-cfg.json`
   - **Example (self-managed stack with SSL ignore for local dev):**
   ```json
   {
       "kibana_url": "https://127.0.0.1:5601/",
       "elasticsearch_url": "https://127.0.0.1:9200/",
       "api_key": "your-api-key-here",
       "ignore_ssl_errors": "true",
       "cloud_id": ""
   }
   ```
   For Elastic Cloud, set `cloud_id` to your deployment’s cloud ID and keep `api_key`; for self-managed, use `kibana_url` (and optionally `elasticsearch_url`) plus `api_key`. Omit `ignore_ssl_errors` or set to `"false"` for production.  
   **Preferred auth:** API key (e.g. created for a Kibana user). See [FAQ Q4](faq.md#q4-how-can-setup-and-use-an-api-key-for-authentication-with-the-stack) for creating an API key.

2. **Supported config keys:** `elasticsearch_url`, `kibana_url`, `cloud_id`, `api_key`.

3. **SSL verification:** For dev stacks with self-signed certs you can ignore SSL errors in any of three ways:
   - In `.detection-rules-cfg.json`: `"ignore_ssl_errors": "true"`
   - Environment: `export DR_IGNORE_SSL_ERRORS=True`
   - Per command: `python -m detection_rules kibana --ignore-ssl-errors true export-rules ...`

4. **Environment variables** (useful for CI): use the `DR_*` prefix (e.g. `DR_KIBANA_URL`, `DR_API_KEY`, `DR_CLOUD_ID`).

---

## 4. Developing rules

**Option A – Manual (from a template)**  
Copy an existing `.toml` rule from `rules/` (or from an export) into your custom `rules/` directory and edit fields (name, description, query, index, tags, etc.). Keep the same TOML structure and required metadata/rule sections.

**Option B – Semi-automated (CLI)**  
```bash
python -m detection_rules create-rule /path/to/custom_rules_dir/rules/my_new_rule.toml
```
Use `-t` / `--rule-type` to set the rule type, or `-c` / `--config` for a config file with predefined attributes. The CLI prompts for required fields and produces a valid TOML skeleton.

**Validate a single rule:**
```bash
python -m detection_rules validate-rule /path/to/rule.toml
```
This checks structure and (when schemas are configured) query fields.

**Inspect a rule (parse + validate + print):**
```bash
python -m detection_rules view-rule /path/to/rule.toml
```
Useful to confirm custom schema and query validation (see [Custom schema validation](#7-schema-validation) and [FAQ Q15](faq.md#q15-when-using-custom-schemas-do-i-have-to-declare-all-fields-or-only-those-that-are-not-part-of-the-ecs)).

---

## 5. Unit testing

- **Run all tests** (respects `CUSTOM_RULES_DIR` so only rules from your config are loaded when set):
   ```bash
   make test
   ```
   Or: `python -m detection_rules test` or `pytest tests/`.

- **Restrict which tests run** via the custom directory’s `etc/test_config.yaml`:
   - **Bypass** specific tests (run everything else):
     ```yaml
     unit_tests:
       bypass:
         - tests.test_gh_workflows.TestWorkflows.test_matrix_to_lock_version_defaults
         - tests.test_schemas.TestVersionLockSchema.test_version_lock_has_nested_previous
         - tests.test_packages.TestRegistryPackage.test_registry_package_config
         - tests.test_all_rules.TestValidRules.test_schema_and_dupes
         - tests.test_all_rules.TestRuleMetadata.test_invalid_queries
         - tests.test_all_rules.TestValidRules.test_bbr_validation
         - tests.test_all_rules.TestValidRules.test_rule_type_changes
         - tests.test_schemas.TestSchemas.test_eql_validation
     ```
   - **Run only** specific tests: use `test_only` with a list of full test names or glob patterns (e.g. `pattern:*rule*`).  
   You cannot set both `bypass` and `test_only`.

- **Rule-level filtering:** Use `rule_validation.bypass` or `rule_validation.test_only` with lists of `rule_id` values to include or exclude specific rules from validation.

- **Custom rules only:** Set `CUSTOM_RULES_DIR` and ensure `_config.yaml` has `testing.config: etc/test_config.yaml`. Then `make test` (or `python -m detection_rules test`) runs only for rules in that config. See [FAQ Q12](faq.md#q12-is-there-a-way-to-run-the-unit-tests-only-on-custom_rules_dir).

### ES|QL rules and remote validation

**Full unit testing of ES|QL rules requires an Elastic stack.** Unlike KQL and EQL, there is no local-only ES|QL parser in the detection-rules repo, so **syntax and full semantics** are validated by the stack when you use remote validation. Separately, when rules load the repo still runs **local** semantic checks that enforce common Elastic Security expectations for ES|QL—for example that non-aggregate queries use `FROM ... METADATA _id, _version, _index` (or an aggregate `STATS ... BY` style pattern), and that the query includes a `| keep` listing `_id`, `_version`, and `_index` for non-aggregate queries. Other ES|QL checks are unchanged. If you need to skip only those local checks (for example during migration or with queries that are valid in Kibana but fail these heuristics), set `DR_BYPASS_ESQL_METADATA_VALIDATION` and/or `DR_BYPASS_ESQL_KEEP_VALIDATION`, or the matching keys in `_config.yaml` (see [Custom configuration (_config.yaml)](#6-custom-configuration-_configyaml)). For background, see [detection-rules PR #5869](https://github.com/elastic/detection-rules/pull/5869).

**Prerequisites:** Configure cluster access as in [Connecting to the Elastic stack](#3-connecting-to-the-elastic-stack)—either a config file (e.g. `.detection-rules-cfg.json` or `.detection-rules-cfg.yml`) in the repo root or the same `DR_*` environment variables. The remote tests create temporary indices, run the ES|QL query against the cluster, and then tear down the indices. A local containerized stack (e.g. [elastic-container](https://github.com/peasead/elastic-container)) with a dedicated API key is sufficient.

**How to run remote ES|QL validation:**

1. **Single pytest test** (validates ES|QL rules against the configured stack):
   ```bash
   export DR_REMOTE_ESQL_VALIDATION=True
   python -m pytest tests/test_rules_remote.py::TestRemoteRules::test_esql_rules -s -v
   ```
   Use `-v` (or `-vv`) for more verbose output. Ensure your config file or `DR_*` env vars are set. **Note**: if the environment variable is set, unit tests will automatically attempt remote ES|QL validation for any rules with `rule_type: esql` when unit tests are run. If you want to validate a single rule without running the full test suite, use the `view-rule` command as described in step 2.

2. **Validate a single rule via `view-rule`** (enables remote ES|QL validation for that rule):
   ```bash
   export DR_REMOTE_ESQL_VALIDATION=True
   python -m detection_rules view-rule path/to/your_esql_rule.toml
   ```

3. **Validate all ES|QL rules manually separately from unit tests** via the dev command:
   ```bash
   python -m detection_rules dev test esql-remote-validation --verbosity 1
   ```

For implementation details, edge cases (e.g. integration mappings with chained multi-fields), and the rationale for remote validation, see [detection-rules PR #4955](https://github.com/elastic/detection-rules/pull/4955) and the related [issue #5222](https://github.com/elastic/detection-rules/issues/5222).

For **custom unit tests** (e.g. your own pytest files), keep them in a separate directory (e.g. `custom_tests/`) and run them via pytest or CI; see the main guide’s “Custom Unit Tests” section. Place custom code in separate files so upstream updates are easier to merge ([FAQ Q10](faq.md#q10-where-is-the-best-place-to-add-custom-code)).

---

## 6. Custom configuration (_config.yaml)

In your custom directory’s `_config.yaml`, these options are commonly used:

| Option | Purpose |
|--------|--------|
| `bypass_version_lock` | Skip version-lock checks (useful when not using version.lock.json). |
| `normalize_kql_keywords` | Normalize KQL keywords for consistency. |
| `auto_gen_schema_file` | Path (e.g. `etc/schemas/auto_gen.json`) for auto-generated schema for non-ECS fields; used in validation and can be added to stack-schema-map. |
| `bypass_optional_elastic_validation` | When `true`, enables **all** optional Elastic validation bypasses when the config is loaded (each sets the corresponding `DR_BYPASS_*` environment variable). If this is `true`, it **overrides** the individual bypass flags below—they are all treated as enabled. |
| `bypass_note_validation_and_parse` | Fine-grained: sets `DR_BYPASS_NOTE_VALIDATION_AND_PARSE` at load time. |
| `bypass_bbr_lookback_validation` | Fine-grained: sets `DR_BYPASS_BBR_LOOKBACK_VALIDATION` at load time. |
| `bypass_tags_validation` | Fine-grained: sets `DR_BYPASS_TAGS_VALIDATION` at load time. |
| `bypass_timeline_template_validation` | Fine-grained: sets `DR_BYPASS_TIMELINE_TEMPLATE_VALIDATION` at load time. |
| `bypass_esql_keep_validation` | Fine-grained: sets `DR_BYPASS_ESQL_KEEP_VALIDATION` at load time (skips local checks for a `keep` command and metadata fields in `keep` for non-aggregate queries; other ES|QL checks stay on). |
| `bypass_esql_metadata_validation` | Fine-grained: sets `DR_BYPASS_ESQL_METADATA_VALIDATION` at load time (skips local `FROM ... METADATA` / aggregate-pattern checks for non-aggregate queries; other ES|QL checks stay on). |

Use either the single bulk flag **or** pick individual bypasses: when `bypass_optional_elastic_validation` is omitted or `false`, each listed `bypass_*` key defaults to `false` if absent, and any key you set must be a boolean (`true` / `false`). The canonical comments and examples live in the upstream file [`detection_rules/etc/_config.yaml`](https://github.com/elastic/detection-rules/blob/main/detection_rules/etc/_config.yaml). See also [detection-rules PR #5869](https://github.com/elastic/detection-rules/pull/5869).

Other important entries:

- **`files.stack_schema_map`**: Points to `etc/stack-schema-map.yaml`, which maps stack versions to ECS/Beats/integration and optional **custom** schema files.
- **`directories`**: `action_connector_dir`, `action_dir`, `exception_dir` — set to your `action_connectors/`, `actions/`, and `exceptions/` folder names if you use built-in list management.

---

## 7. Schema validation

- **Built-in:** Rules are validated with dataclasses (and Marshmallow) when loaded. Query validation uses ECS/Beats/integration schemas referenced in `stack-schema-map.yaml` (EQL, KQL, etc.). Validation runs on load (e.g. during `test`, `validate-rule`, `view-rule`, `kibana export-rules`, `import-rules-to-repo`). **ES|QL** has no full local parser; **syntax and full semantics** require [remote validation against an Elastic stack](#esql-rules-and-remote-validation). Some **local** ES|QL semantic checks still run on load (metadata/`keep` expectations); you can adjust those with `_config.yaml` or `DR_BYPASS_*` as described in [ES|QL rules and remote validation](#esql-rules-and-remote-validation) and [Custom configuration (_config.yaml)](#6-custom-configuration-_configyaml).

- **Custom schema (non-ECS fields):** In `etc/stack-schema-map.yaml`, add a stack version and a `custom` path to a JSON schema file, e.g.:
  ```yaml
  8.14.0:
    beats: 8.12.2
    ecs: 8.11.0
    endgame: 8.4.0
    custom: schemas/custom-schema.json
  ```
  In `schemas/custom-schema.json`, define **only non-ECS fields** (ECS is already validated). Example:
  ```json
  {
    "custom-index*": {
      "process.NewValue": "keyword",
      "process.AnotherValue": "keyword"
    }
  }
  ```
  See [FAQ Q15](faq.md#q15-when-using-custom-schemas-do-i-have-to-declare-all-fields-or-only-those-that-are-not-part-of-the-ecs) for more detail and an example rule.

- **Auto-generated schema:** Set `auto_gen_schema_file` in `_config.yaml`. When you run commands that validate rules (e.g. `view-rule`, `import-rules-to-repo`, `kibana export-rules`), unknown index/field pairs can be written to this file and used in validation so you don’t have to hand-edit the custom schema for every new field.

- **Updating integration schemas:** From the repo root:
  ```bash
  python -m detection_rules dev integrations build-manifests
  python -m detection_rules dev integrations build-schemas
  ```

Note: Not all Kibana rule fields are supported in the repo schemas; some may be generalized (e.g. `dict`). See [known limitations](known_limitations.md#schema-validation-support).

---

## 8. Exceptions and actions

You can manage **exception lists** and **response actions** (and action connectors) in two main ways:

1. **Built-in list management (recommended):** In `_config.yaml`, set `directories.exception_dir`, `directories.action_dir`, and `directories.action_connector_dir` to your folders. Add TOML files per list/connector; each file references `rule_ids` (and optionally `rule_names`). On `kibana import-rules`, the CLI can export/import these along with rules. Schema for these TOML files is not as strict as rule TOML; see the main guide’s “Option 1: Built-in Lists Management” for the exact structure (metadata, `[[exceptions]]`, `[[actions]]`, action connector `attributes`, etc.). For more on deployment, see [FAQ Q11](faq.md#q11-how-should-exception--action-files-be-deployed-to-kibana).

2. **Inline in rule TOML:** Use `exceptions_list` and `actions` in the rule TOML. You must supply the exact structure expected by Kibana; validation for these fields is limited when embedded in the rule file. See [known limitations](known_limitations.md#no-direct-support) for exception/action support.

---

## 9. Syncing with Kibana

**Backup first:** Before overwriting rules in Kibana, export a backup. See [FAQ Q8](faq.md#q8-how-can-i-backup-my-rules-prior-to-overwriting-rules-in-kibana).

**Export from Kibana to your repo (TOML or YAML):**
```bash
python -m detection_rules kibana export-rules \
  --skip-errors \
  --strip-version \
  --export-exceptions \
  --export-action-connectors \
  --directory my-custom-rules
```
For this command, **`--directory` / `-d`** is the **output** directory only: rules (and, when requested, exceptions and action connectors) are written there as TOML by default. It is **not** the same as **`export-rules-from-repo`**, where **`-d`** selects **input** rule directories—do not assume the flags mean the same thing across both commands.

Add **`--save-as-yaml` / `-sy`** to write **YAML** under that directory instead of TOML (useful for tooling that consumes YAML, such as some Terraform / Elastic provider workflows). Exception and action connector directories still follow **`--exceptions-directory` / `-ed`** and **`--action-connectors-directory` / `-acd`** when set; if omitted, defaults come from your rules config.

Errors are reported; `--skip-errors` continues and skips rules that fail validation.

**Import from repo to Kibana (overwrite existing):**
```bash
python -m detection_rules kibana import-rules \
  --overwrite \
  --overwrite-exceptions \
  --overwrite-action-connectors
```
Optionally add `-d <directory>` or `-f <file>` to limit to a directory or single rule file. Without `--overwrite`, existing rules with the same ID are not updated (409 conflict).

**Using NDJSON instead of the Kibana API:**

- **Kibana export → repo (TOML):** Export rules from the Kibana UI (or API) as NDJSON, then:
  ```bash
  python -m detection_rules import-rules-to-repo <ndjson_file> \
    --required-only \
    --action-connector-import \
    --exceptions-import \
    --default-author DefaultAuthor \
    --skip-errors
  ```
- **Repo → NDJSON (or per-rule YAML) for Kibana import:**
  For **`export-rules-from-repo`**, **`--directory` / `-d`** selects **input**: directories to load rules from (repeatable, same pattern as other multi-collection commands). **`--outfile` / `-o`** is the **NDJSON** path when you are not using YAML mode. **`--save-yaml-dir` / `-syd`** writes **per-rule (and related) YAML files** into that directory instead of a single NDJSON file; when **`-syd`** is set, **`-o`** is ignored.

  If you omit **`-o`** in NDJSON mode, the CLI writes a default file under **`exports/<timestamp>.ndjson`** at the detection-rules repository root (see `export-rules-from-repo --help` for current behavior).

  ```bash
  python -m detection_rules export-rules-from-repo \
    --directory my-custom-rules \
    --outfile my-custom-rules-export.ndjson \
    --include-action-connectors \
    --include-exceptions
  ```

  Example emitting YAML instead of NDJSON:

  ```bash
  python -m detection_rules export-rules-from-repo \
    --directory my-custom-rules \
    --save-yaml-dir yaml_output/ \
    --include-action-connectors \
    --include-exceptions
  ```

  Then import the NDJSON via Kibana UI or API (or use the YAML with external tooling as appropriate). For CI/CD, you can usually use `kibana import-rules` directly instead of NDJSON; see [FAQ Q13](faq.md#q13-i-want-to-use-export-rules-from-repo-in-cicd-to-convert-a-newmodified-rule-from-toml-to-json-and-push-the-json-to-kibana-api-what-is-the-best-way-to-do-that).

---

## 10. Version locking (optional)

Version locking can help when you sync in both directions (repo ↔ Kibana) or use overwrite workflows with multiple sources of truth at a time for rules: the lock file records which version of each rule is “current,” so the CLI can avoid overwriting newer changes or detect conflicts. If you only have a single source of truth at one time, you typically do not need it.

**Option A – Use version locking** (for dual sync or overwrite workflows):

- **version.lock.json** and **deprecated_rules.json** in `etc/` track rule versions (e.g. by sha256) and deprecated rules.
- Update the lock file after changes:
  ```bash
  python -m detection_rules dev build-release --update-version-lock
  ```
  (Requires `packages.yaml` registry config.) Or force-update without building a package:
  ```bash
  python -m detection_rules dev update-lock-versions --force
  ```

**Option B – Skip version locking:** If you have a single, authoritative source of truth for rules, set `bypass_version_lock: true` in `_config.yaml`. The CLI will not check or update version lock files.

---

## Known limitations and FAQ links

- **Schema parity:** Repo schemas can lag behind Kibana; not every Kibana field is validated. See [known limitations](known_limitations.md#schema-validation-support).
- **Rule size / count:** Kibana may limit export/import (e.g. 10,000 rules or payload size). See [known limitations](known_limitations.md#rule-size-limitations).
- **Exception list import:** Intermittent issues with exception list references on large imports; see [known limitations](known_limitations.md#intermittent-exception-list-kibana-import-issue).
- **Private fork:** To keep a private fork in sync with upstream, see [FAQ Q14](faq.md#q14-i-want-to-fork-the-detection-rules-repo-on-github-but-i-want-to-keep-my-fork-private-what-is-the-best-way-to-do-that).

For more topics (backup, API key, custom code, exceptions/actions, unit tests, custom schemas, CI/CD), use the [FAQ](faq.md) and the [full DaC reference](dac_concept_and_workflows.md).
