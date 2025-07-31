# Known Issues and Limitations Using Elastic's detection-rules DAC Approach
================================

## Intermittent Exception List Kibana Import Issue

- There is an intermittent issue when importing many rules and exception lists at the same time that has been investigated related to [this Kibana issue](https://github.com/elastic/kibana/issues/143864). This may cause exception list references to be stripped when importing rules in some cases. We implemented a helper message that will attempt to identify when this occurs and provide a command template that you can run to re-try to upload the rules that might have failed erroneously. This message will appear from the output of `kibana import-rules` if this situation is detected. We expect the issue to be resolved as of 8.16.1 and 8.15.5, but this workaround is available for those who are using older stacks. See the following Kibana references for more information: [190447](https://github.com/elastic/kibana/pull/190447) and [193471](https://github.com/elastic/kibana/pull/193471).

## Rule Size Limitations

- The Kibana API has limitations on number/size of rules for importing and exporting that may impact the way one interacts with our DaC commands. For instance, the export, by default, the API cannot export more than 10,000 rules or have a max byte payload of 10,485,760 bytes. If one tries to export more than these values, one should expect to receive a message similar to the following: `{"message":"Can't export more than 10000 rules","status_code":400}` or `413 Client Error: Request Entity Too Large for url`. These values are enforced via configuration and can be changed in `xpack.securitySolution.maxRuleImportExportSize` and `xpack.securitySolution.maxRuleImportPayloadBytes` respectively. Furthermore, there is a known bug documented in [176207](https://github.com/elastic/kibana/issues/176207) where importing a large number of rules can create extra rules and is being actively addressed. 

## Schema Validation Support

- While we try to maintain parody with Kibana schemas; however, when Kibana releases a schema update our schema updates lag behind theirs. If there is a schema validation issue where a schema needs updating, first check the [issues in detection rules](https://github.com/elastic/detection-rules/issues) to see if it is already being tracked. If not, please let us know and we will update the schemas as soon as we are able.

## No Direct Support

- Rule loading
  - Core repo (loading Rule object)
    - Officially supports loading from TOML to Rule object
    - Support for json and yaml exists but will remain in a dict format (as opposed to the nested TOML format)
      - This is only a convenience utility and will only be supported across minimal components, since all functionality depends on the validated object (and related pieces)
  - Kibana module does support raw dict's
  - We added a RawDictLoader as a util, but this will only serve to load rules to dicts, bypassing most of the value of the core repo
- Limited support for Exception and Action Lists:
  - Schema validation for exception or action lists are not available within the individual rule TOML files. You can add the fields manually to the TOML files, however validation is only available when managing these lists in separate TOML files in the custom folder. 
    - Data within the exceptions are not validated 
    - We recommend managing exception/action lists in Kibana and exporting the rules, which will include the list information per rule. 
  - The limited action/exception support within the detection-rules repo has two-way support to push mass updates to lists in Kibana. You can not currently export a single list from Kibana and save into the detection rule format.

## Potential Upcoming Enhancements

- Support for customizable folder structure:
  - There currently is not full support for customizing the folder structure which decides how rules are stored on disk.
  - This means that users have to manually move rules to specific organized folders.
  - This is tracked in: [#3625](https://github.com/elastic/detection-rules/issues/3625)
  - However, there is support for using an existing folder structure when importing rules from a Kibana export. This is done by using the `--load-rule-loading / -lr` option in the `kibana export-rules` and `import-rules-to-repo` commands. See [#4891](https://github.com/elastic/detection-rules/issues/4891) and its associated PR for more details.