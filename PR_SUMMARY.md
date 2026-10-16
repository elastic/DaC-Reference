# Documentation: wording and typo fixes

## Summary

This PR applies wording and typo fixes across the repository’s Markdown docs to improve clarity and consistency. No functional or structural changes.

## Changes

### Typos
- **known_limitations.md:** "parody" → "parity" (with Kibana schemas)
- **core_component_syncing_rules_and_data_from_vcs_to_elastic_security.md:** "fulle" → "full", "ndjon" → "ndjson" (in comments)
- **internals_of_the_detection_rules_repo.md:** "anlt4" → "ANTLR4", "deprecated_lock.json" → "deprecated_rules.json"
- **faq.md:** "its" → "it's", "you" → "your" (path of your custom rules), "list" → "lists" (which lists are associated)

### Grammar and clarity
- **faq.md:** "there two approaches" → "there are two approaches"; "config files setup" → "config files set up"; "can not" → "cannot"; "Note this is" → "Note that this is"
- **dac_concept_and_workflows.md:** Comma fix in "reviewed, tested and enables" → "reviewed and tested, and enables"; "e.g. particularly Git" → "e.g., Git"; "reference aid with design choices" → "reference when making design choices"; "We highly recommended" → "We highly recommend"
- **about.md:** "with intent of this eventually stabilizing" → "with the intent that this eventually stabilizes"
- **README.md:** Shortened "commit the changes to your documentation source files to your git repository" → "commit your documentation source changes to your git repository"
- **internals_of_the_detection_rules_repo.md:** "version lock update occur" → "version lock updates occur"; "There also is several" → "There are also several"; "features set" → "feature set"

### Consistency
- **DaC:** Standardized "DAC" / "Dac" to "DaC" in headings, body text, and code comments (e.g. governance models, CLI descriptions, sync docs).
- **Product names:** "Github" / "Github's" → "GitHub" / "GitHub's" in faq.md; "Elastic security" → "Elastic Security" where it refers to the product; "GitHub action(s)" → "GitHub Actions" where appropriate.
- **Naming:** "Detection as Code" → "Detections as Code" where it matches project naming; "Option 2 - Semi-Automation" → "Option 2: Semi-Automated create-rule CLI"; field name "Deprecation_date" → "deprecation_date" in internals doc.

### Other
- **index.md:** Removed duplicate "Security" in "Elastic Security Security Detection Rules"; fixed invalid CSS `font-size:em` → `font-size:1em`.
- **etoe_reference_example.md:** "to an overview" → "for an overview"; "`_config` file" → "`_config.yaml` file".
- **core_component_managing_detection_rules_in_a_vcs.md:** "before synced with" → "before being synced with".
- **core_component_syncing_rules_and_data_from_elastic_security_to_vcs.md:** "to setup" → "to set up".
- **faq.md:** "actions and /or exceptions" → "actions and/or exceptions"; "associated to" → "associated with".

## Files touched

- `README.md`
- `docs/about.md`
- `docs/index.md`
- `docs/faq.md`
- `docs/dac_concept_and_workflows.md`
- `docs/core_components_and_governance_models_of_dac.md`
- `docs/internals_of_the_detection_rules_repo.md`
- `docs/core_component_managing_detection_rules_in_a_vcs.md`
- `docs/core_component_syncing_rules_and_data_from_vcs_to_elastic_security.md`
- `docs/core_component_syncing_rules_and_data_from_elastic_security_to_vcs.md`
- `docs/etoe_reference_example.md`
- `docs/feedback_and_resources.md`
- `docs/known_limitations.md`

## Verification

- No source or config changes; documentation only.
- Suggested: quick skim of built docs (`poetry run build-docs -b html docs docs/_build`) and of the modified `.md` files in the docs UI.
