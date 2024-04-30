# Known Limitations Using Elastic's detection-rules DAC Approach
================================

## No Direct Support

- Managing prebuilt rules via DaC workflows is not supported at the moment (this is being addressed).
- Rule loading
  - Core repo (loading Rule object)
    - Officially supports from  TOML  to  Rule object
    - Support for json yaml will exist but will remain in a dict format
      - This is only a convenience utility and will only be supported across minimal components, since all functionality depends on the validated object (and related pieces)
  - Kibana module does support raw dict's
  - We added a RawDictLoader as a util, but this will only serve to load rules to dicts, bypassing most of the value of the core repo
- Limited support for Exception and Action Lists:
  - Schema validation for exception or action lists are not available within the individual rule TOML files. You can add the fields manually to the TOML files, however validation is only available when managing these lists in separate TOML files in the custom folder. 
    - Data within the exceptions are not validated 
    - We recommend managing exception/action lists in Kibana and exporting the rules, which will include the list information per rule. 
  - The limited action/exception support within the detection-rules repo has one-way support to push mass updates to lists in Kibana. You can not currently export lists from Kibana and save into the detection rule format (however they can be manually set and saved).

## Potential Upcoming Enhancements

- Support for custom schema validation
  - Custom schemas used for validating queries are not supported within the custom rule folder. This means that when validating custom rules with the detection-rules logic, query validation will default to the schemas used by Elastic. 
  - We recommend modifying the files in detection-rules/detection_rules/etc/ and use the schema management commands to specify necessary schemas.
  - This is tracked in: [#3618](https://github.com/elastic/detection-rules/issues/3618)
- Support for Kibana version or revision fields:
  - These two fields are not supported within detection-rules since Elastic uses the version lock file for versioning.
  - This means that for versioning, users have limited built-in approaches to version rules.
  - We recommend adopting the built-in versioning version.lock.json strategy.
  - This is tracked in: [#3620](https://github.com/elastic/detection-rules/issues/3620)
- Support for customizable folder structure:
  - There currently is no support for customizing the folder structure which decides how rules are stored on disk when downloaded from kibana (however, this can also be reconciled post download in whatever manner desired).
  - This means that users have to manually move rules to specific organized folders.
  - This is tracked in: [#3625](https://github.com/elastic/detection-rules/issues/3625)