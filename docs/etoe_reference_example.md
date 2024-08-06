# E2E Reference
================================

## Quick Start Slides

Check out the [Quick Start Slides](./_static/DaC_Rolling_your_own_Detections_as_Code.pdf) to an overview of DaC and how to get started. It provides some example CLI commands using the detection-rules repo.

⚠️ Note: The Detection Rules DaC feature is still in Beta. This phase includes continued testing to include edge cases and feedback from the community. This reference guide and associated content are subject to change. 

If you’re starting from scratch and would like to get quickly started, here are the high-level instructions. These steps assume you are familiar with the considerations associated with the various steps and elect to follow the detection-rules VCS approach to manage rules. It also assumes advanced configurations are not applied.

### Demo Video

Take a look at an example of how you can use some of our DaC features. The repo used in this example can be found [here](https://github.com/eric-forte-elastic/detection-rules-dac-demo)


<div style="position: relative; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <iframe src="https://drive.google.com/file/d/1XMPSdgjZipa94xufv_4byVrMm-0XaKZh/preview" width="640" height="480" allow="autoplay"></iframe>
</div>

### Quick Start Example Detection Rules CLI Commands

**Steps:**

1. Clone the detection rules repo and install python requirements, see [getting started](https://github.com/elastic/detection-rules?tab=readme-ov-file#getting-started).

1. Initialize a custom rules directory via the setup config command. 

    `python -m detection_rules custom-rules setup-config dac_custom_rules_dir`

1. Edit the `_config` file in your `dac_custom_rules_dir` directory. 

    Most users will want to add these additional parameters for ease of use:

    ```yaml
    bypass_version_lock: True
    normalize_kql_keywords: True
    auto_gen_schema_file: "etc/schemas/auto_gen.json"
    bypass_optional_elastic_validation: True
    ```

1. Set your environment variable to use the custom configuration and rules directory you just made.

    `export CUSTOM_RULES_DIR="<full_path_to_dac_custom_rules_dir>"`

1. If running locally, set your ``.detection-rules-cfg.json` in the root of the detection rules directory. See [setup a config file](https://github.com/elastic/detection-rules/blob/main/CLI.md#setup-a-config-file) for more details.

    ```json
    {
        "cloud_id": "example:example",
        "api_key": "example",
    }
    ```

1. Export custom rules and related exceptions to a Kibana Instance, overwriting existing, stripping version, and skipping errors.

    `python -m detection_rules kibana export-rules -s -sv -e -ac`

1. Run local unit tests.

   `make test`

1. Import custom rules and related exceptions to a Kibana Instance, overwriting existing.

    `python -m detection_rules kibana import-rules --overwrite -e -ac`

1. Or if you prefer to import and export using ndjson file(s) instead of the Kibana API use the following:

    For moving rules from a Kibana rules export to the repo
    `python -m detection_rules import-rules-to-repo <ndjson_file> --required-only -e -da DefaultAuthor -ske -ac`

    For moving rules from the repo to an ndjson file that is compatible with Kibana rule import.
    ` python -m detection_rules export-rules-from-repo -ac -e`

For more information on these CLI commands please see CLI.md and docs/custom-rules.md in Detection Rules.

### Locally

**Steps:**

1. Fork the detection-rules repo
1. Configure Elastic Security authentication
1. Install the python requirements
1. Initialize the custom rules directory (optionally configure schema and versioning files)
1. Configure unit tests (optionally bypass specific test cases)
1. Create a detection rule (optionally create exception and action list)
1. Validate the rules (optionally test queries in development environment)
1. Open pull request to store in VCS and peer review
1. Version lock your rules
1. Push merged rules to production environment (optionally version lock and sync via CICD)

### In VCS

**Steps:**

1. Setup branch protections and code owners
1. Setup environment secrets and variables that will be used in CICD
1. Setup CICD workflows to validate and sync rules
