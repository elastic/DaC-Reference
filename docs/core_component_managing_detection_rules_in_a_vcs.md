# Core Component: Managing Detection Rules in a VCS
================================

## Overview

Version Control System (VCS) adoption for managing detection rules empowers security teams to implement Detection as Code (DaC) principles through Elastic's Public APIs. With the *VCS as authoritative* or Dual Sync governance models, changes to detection rules are developed, reviewed, and version-controlled within a repository before synced with the production Elastic stack. This method leverages the inherent collaboration, review, auditing, and versioning capabilities of VCS, ensuring a robust governance framework for the lifecycle management of detection rules.  This strategy is the recommended approach to getting started with DaC and managing rules with Elastic’s SIEM.

If the *Elastic security as authoritative* governance model is employed, the VCS component should be largely static, and would serve more as an external store for maintaining versions and diff history, than as a medium for driving changes.

|                                                                         |
| ----------------------------------------------------------------------- |
| <img src="_static/sync_vcs_with_git.png" alt="Sync with git"/> |
| <center>*Figure 6: Syncing Local Rules with VCS Through Git Workflows* </center> |

By following the recommended steps within this workflow, you will implement a DaC strategy from scratch that leverages VCS and the detection-rules repo. In particular, you will:

1. Fork the detection-rules repo
1. Install the Python dependencies
1. Initialize and configure your DaC custom rules folder using the CLI
1. Configure the unit tests to run
1. Create and version rules using the **detection-rules** capabilities
1. Create and link exception and action lists
1. Locally validate rules

## Considerations

While various strategies exist for managing detection rules within a VCS framework, the [Sub-Component 1: Elastic Detection Rules Repo and Usage](./core_component_managing_detection_rules_in_a_vcs.md#sub-component-1-elastic-detection-rules-repo-and-usage) approach is showcased throughout this reference. It demonstrates effective rule management techniques utilizing the detection-rules repository alongside available configurations. It is important to highlight that, among the discussed methods, the **detection-rules** repository approach remains the primary and actively supported method for managing security rules within the Elastic ecosystem. It requires some familiarity with the [detection-rules](https://github.com/elastic/detection-rules) repository to take full advantage of the features available. This strategy ensures that security teams can leverage the full potential of Elastic's capabilities in a structured, version-controlled manner.

With that being said, there has been a big initiative to decouple and abstract components where it makes sense, to allow for adoption of principles, with easier customization and extensibility. The intent here is that you can build from within the repo (via a forked version), or you can import the components as needed, whether they are part of the base repo functionality, or one of the embedded modules, such as Kibana and KQL. Finally, there is the option to not directly adopt or employ from the repo at all, but consult it as a reference to build out something more specific (within the constraints of the Elastic v2 License).

Terraform, as a powerful tool for infrastructure as code (IaC), offers significant potential for automating many aspects of DaC processes. By defining infrastructure and configuration details, Terraform can automate the setup and maintenance of environments crucial for DaC workflows, such as provisioning Elastic Security instances, configuring Elastic Security for rule management, or establishing CI/CD pipelines for rule deployment. However, this guide does not delve into Terraform-specific examples to maintain a focus on DaC principles applicable across various tools and platforms. The versatility of Terraform means it can be integrated into many components of DaC, providing a layer of automation that enhances efficiency and consistency across detection rule lifecycle management.

## Sub-Component 1: Elastic Detection Rules Repo and Usage

Reference [Getting Started with the Repo](./internals_of_the_detection_rules_repo.md#getting-started-with-the-repo) internals to get setup with the detection rules repo and for an understanding of the layout. It is also highly recommended to step through all of the internals in order to fully understand functionality and usage.

|Pros|Cons|
|-|-|
|- Open Source Collaboration: Encourages open-source contributions, allowing for community-driven enhancements and shared improvements. </br> - Comprehensive Tooling: The CLI support simplifies DaC processes, from syncing to validation, making rule management more efficient. </br> - Extensive Validation and Testing: Incorporates built-in unit tests and schemas to validate detection rules against Elastic's standards before deployment rigorously. </br> - Flexibility and Customization: Designed to accommodate diverse operational needs, the repository can be tailored for specific environments or detection strategies. </br> - Maintained: constantly updated to reflect upstream changes and bugs addressed.|- Learning Curve: Familiarity with the command line and Git is necessary to fully utilize the repository’s capabilities, which might present a hurdle for some users. </br> - Limited UI Integrations: While the CLI offers powerful functionalities, certain rule configurations that are accessible through Elastic Security's UI might not be directly supported or require manual adjustments. </br> - Customization Requirements: To align with unique organizational requirements or to incorporate non-standard rule types, additional customization of the repository structure or scripts may be needed. </br> - Primary purpose: built for the internal Threat Research and Detection Engineering (TRADE) team to maintain pre-built rule set.|

### CLI

Reference the [CLI](./internals_of_the_detection_rules_repo.md#cli) internals usage of CLI for a better understanding of its intent and how to use it.

|Pros|Cons|
|-|-|
|- Integration with Public Elastic APIs: Serves as a convenient reference for Elastic API utilization.</br> - Simplified Command Execution: Condenses multiple DAC processes into singular commands for efficiency.</br> - Versatility: Harnesses the full potential of CLI for rule management.|- Custom Application Limitations: May not seamlessly integrate with all custom applications specific to user environments.</br> - Intentional Coupling: Some commands are intentionally designed to be closely integrated with specific workflows.|

### Unit Tests

Reference the [Unit Tests](./internals_of_the_detection_rules_repo.md#unit-tests) internals usage of unit tests for a better understanding of the tests that the suite is composed of, and best practices around implementing tests.

|Pros|Cons|
|-|-|
|- Flexibility in Testing: Allows configuration to either execute or skip specific unit tests, catering to diverse testing needs.</br> - Adherence to Best Practices: Incorporates Elastic's recommended rule development practices, ensuring high-quality rule creation.</br> - Independent Validation: Facilitates the validation of query syntax and schema conformity outside of the Elastic stack, offering an additional layer of assurance.|-Limited Coverage: Some specialized rule features might not be fully supported, requiring external validation methods.</br> - Customization Challenges: Adapting tests for unique scenarios or integrating divergent unit tests with upstream changes may pose difficulties.</br> - Time-Consuming: The comprehensive nature of the tests may lead to longer execution times, particularly when loading extensive rule sets.|

### Custom Unit Tests

Custom unit tests enable teams to create and execute their own test suites tailored to their specific detection rules and operational requirements. By developing custom tests, teams can extend the validation beyond the pre-configured tests provided in the detection-rules repository. This approach is crucial for organizations that have unique rule logic or complex detection scenarios not covered by the standard test suite.

##### Sub-Component: Custom Test Development

Developing custom unit tests typically involves leveraging Python’s `pytest` framework, which is already installed as a python dependency into the Elastic detection-rules repo. Teams can create a separate directory for custom tests to ensure they are isolated from the general test suite, which allows for more controlled execution and easier maintenance.

###### Option 1: Isolated Test Environment

| Pros | Cons |
|------|------|
| - Ensures that custom tests do not interfere with general repository tests.</br> - Allows for targeted testing of specific rule logic or configurations unique to the team’s environment. | - Requires additional setup to integrate with existing CI/CD pipelines.</br> - May require duplication of some setup code or fixtures used in the main test suite.|

**Steps:**

1. Create a new directory within the forked detection-rules repository specifically for custom tests (e.g., `custom_tests/`).
2. Develop custom test scripts using `pytest`, focusing on the specific aspects of detection rules that are critical to your operational context.
3. Configure your CI/CD pipeline to include running these custom tests as part of the rule validation process.
4. Ensure that test results are reviewed and acted upon as part of the pull request review process.

###### Option 2: Integrated Test Suite

Integrate custom unit tests directly into the existing `pytest` configuration, using tags or markers to distinguish them from standard tests. This method leverages the full power of the existing testing infrastructure without needing significant modifications to the CI/CD setup.

| Pros | Cons |
|------|------|
| - Leverages existing testing tools and configurations.</br> - Simplifies the CI/CD pipeline by using a single test command. | - Requires careful management of test markers to avoid executing unwanted tests.</br> - Potential for confusion or conflicts with upstream test cases if not managed properly.|

**Steps:**

1. Use `pytest` markers to tag custom tests, e.g., `@pytest.mark.custom`.
2. Modify the CI/CD test execution command to selectively run tests based on tags, e.g., `pytest -m custom`.
3. Maintain a clear documentation and naming convention for custom tests to ensure they are easily identifiable.
4. Regularly review and update the custom tests to align with any changes in detection rule structures or the underlying platform.

### Integration with CI/CD

Ensure that the integration of custom unit tests into the CI/CD pipeline is seamless and provides real-time feedback on the rule validation status. This integration is vital for maintaining the agility and responsiveness of the security operations team, enabling rapid deployment of validated and tested detection rules.

|Pros|Cons|
|-|-|
| - Immediate feedback on rule validation enhances the agility of rule deployment.</br> - Ensures that only thoroughly tested and validated rules are deployed, reducing the risk of false positives or ineffective detections. | - Requires continuous maintenance to align the test suite with evolving detection rules and threat landscapes.</br> - Initial setup and integration can be complex, requiring expertise in both security and CI/CD technologies.|

**Steps:**

1. Configure the CI/CD pipeline to automatically run custom unit tests on every commit to a designated branch or on pull request creation.
2. Set up notifications or integration with development tools to alert developers to test failures or issues detected during CI/CD.
3. Regularly review the test coverage and effectiveness, adjusting the test suite as necessary to cover new threat scenarios or rule changes.

### Schemas and Data Structures

Reference the [Schemas and Data Structures](./internals_of_the_detection_rules_repo.md#schemas-and-data-structures) internals usage of schemas and data structures for a better understanding of their purpose and how to leverage them.

|Pros|Cons|
|-|-|
|- Outside-the-SIEM Validation: Enables rule verification independently from the Elastic stack, ensuring rules are ready for deployment.</br> - Dataclass Support: Provides a structured format for rule development, accommodating updates as new features are introduced in Elastic Security.|- Feature Limitations: Not all rule functionalities are encapsulated within the dataclasses, potentially necessitating manual verification.</br> - Adaptation Required: Custom applications or some rule fields (e.g. saved queries) may require additional effort to align with the provided schemas and dataclasses if needed.|

## Sub-Component 2: Incorporating Custom Rules

Reference the [Incorporating Custom Rules](./internals_of_the_detection_rules_repo.md#incorporating-custom-rules) internals for a better understanding of how to incorporate custom rules.


## Sub-Component 3: Creating Detection Rules

Reference the [Creating Detection Rules](./internals_of_the_detection_rules_repo.md#creating-detection-rules) internals for a better understanding of how to create detection rules.

## Sub-Component 4: Rule Versioning

Reference the [Rule Versioning](./internals_of_the_detection_rules_repo.md#rule-versioning) internals for a better understanding of how to version rules.

## Sub-Component 5: Exceptions and Actions

Reference the [Exceptions and Actions](./internals_of_the_detection_rules_repo.md#exceptions-and-actions) internals for a better understanding of how to create exceptions and actions.

## Sub-Component 6: Unit testing

Reference the [Unit Testing](./internals_of_the_detection_rules_repo.md#unit-testing) internals for a better understanding of how to unit test rules.

## Sub-Component 7: Rule Schema Validation

Reference the [Rule Schema Validation](./internals_of_the_detection_rules_repo.md#rule-schema-validation) internals for a better understanding of how to validate rule schemas.

## Sub-Component 8: Detection Logic Validation

Reference the [Detection Logic Validation](./internals_of_the_detection_rules_repo.md#detection-logic-validation) internals for a better understanding of how to validate detection logic.

