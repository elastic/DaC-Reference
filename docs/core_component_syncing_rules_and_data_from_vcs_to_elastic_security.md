# Core Component: Syncing Rules and Data from VCS to Elastic Security
================================

## Overview

In [Core Component: Managing Detection Rules in a VCS](./core_component_managing_detection_rules_in_a_vcs.md), we highlight managing detection rules through Version Control Systems (VCS) — encompassing validation, unit testing, and managing exceptions among other practices. This workflow progresses to detail the synchronization of detection rules into Elastic Security. *Syncing* in this context signifies the bidirectional movement of rules — either importing/exporting or pushing/pulling — between VCS and Elastic Security. This section is framed from the perspective of **pushing rules from VCS into Elastic Security**, although the processes involved can facilitate movement in either direction.

|                                                                                                                   |
| ----------------------------------------------------------------------------------------------------------------- |
| <img src="_static/sync_with_cicd_workflows.png" alt="Sync with CI/CD Workflows"/> |
| <center>*Figure 7: Syncing with Elastic Security Through CI/CD API Workflows* </center>                                                               |

## Considerations

The workflows described here integrate concepts familiar to those versed in Continuous Integration/Continuous Deployment (CI/CD) practices. While examples may leverage [GitHub Actions](https://github.com/features/actions) to illustrate these processes, the methods outlined are intended as guides and should be customized to suit specific operational requirements. It's important to note that independent of the CI/CD strategy employed, interaction with Elastic's APIs remains a fundamental aspect of the rule synchronization process. In general, here are the end to end steps:

1. Rule Preparation and Validation: Begin by ensuring all rules within the VCS repository have passed validation and unit tests. This step is crucial to avoid syncing rules that could potentially cause issues within Elastic Security.
1. CI/CD Configuration: Configure your CI/CD pipeline (e.g., GitHub Actions) to automate the rule syncing process. This setup should include steps for rule validation, packaging, and deployment triggers based on specified conditions (e.g., merges into a main branch).
1. API Integration for Rule Syncing: Utilize Elastic Security's Detection Engine API to push rules from VCS to Elastic Security. This involves crafting API requests to create or update rules in Elastic Security based on the rule definitions stored in VCS. Ensure API keys or authentication credentials are securely managed within the CI/CD pipeline to facilitate secure API interactions.
1. Exception and Action List Management: If your rules utilize exception lists or response actions, ensure these dependencies are also managed and synced appropriately. This may involve additional API calls to manage these entities in Elastic Security.
1. Monitoring and Feedback: Implement monitoring within the CI/CD pipeline to capture the status of rule deployments. This could include notifications on success or failure, enabling quick response to deployment issues. Consider feedback loops where deployment outcomes can inform rule development practices, potentially triggering additional tests or validations before future deployments.
1. Documentation and Change Management: Maintain documentation of the CI/CD process, including any custom scripts or configurations used for rule syncing. This ensures transparency and facilitates onboarding for new team members. Implement change management practices to track modifications to the rule set, including version control of rule definitions and a log of deployments to Elastic Security.

## Sub-Component 1: Deploying via Direct HTTP Request API Calls

### Option 1:  Elastic Security APIs

The [Elastic Security APIs](https://www.elastic.co/guide/en/security/current/security-apis.html) offer comprehensive endpoints for managing Create, Read, Update, Delete (CRUD) operations within Elastic Security through REST API calls. These APIs can be utilized directly, bypassing the CLI options provided by the detection-rules repository. The CLI aids in simplifying connection setup, wrapping specific endpoints to integrate core Detection as Code (DAC) components like validation, field transformation, and conversion to JSON.

|Pros|Cons|
|-|-|
|- Comprehensive CRUD Support: Directly supported standard APIs for managing security rules.</br> - CLI Integration: Simplifies rule syncing for CI/CD workflows, providing a bridge for direct API interaction.|- Limited API Coverage: Not all Elastic Security APIs are integrated within the detection-rules repo, which might be necessary for advanced DAC workflows (e.g., timelines, cases)|

**Steps:**

1. **Authenticate** using one of the several Elastic API [authentication](https://www.elastic.co/guide/en/security/current/rule-api-overview.html#_authentication_2) options available or using one of the detection-rules options.

CLI commands which are tied to Elastic Security and Elasticsearch are capable of parsing auth-related keyword args from a config file or environment variables. If a value is set in multiple places, such as config file and environment variable, the order of precedence will be as follows:
- explicitly passed args (such as `--user joe`)
- environment variables
- config values
- prompt (this only applies to certain values)

In the root directory of this repo, create the file **.detection-rules-cfg.json** and add relevant values, where kibana credentials provide access to the Elastic Security stack.

```json
{
  "elasticsearch_url": "http://localhost:9200",
  "kibana_url": "http://localhost:5601",
  "cloud_id": "",
  "es_username": "elastic",
  "es_password": "password",
  "kibana_username": "elastic",
  "kibana_password": "password"
}
```

Currently supported arguments:

```config
 elasticsearch_url
 kibana_url
 cloud_id
 *_username (kibana and es)
 *_password (kibana and es)
```

2. Configure your [custom rules directory](./internals_of_the_detection_rules_repo.md#option-1-using-the-built-in-configuration)
3. Once authenticated, you can use [API calls](https://www.elastic.co/guide/en/security/current/security-apis.html#_api_calls) directly. If using the detection-rules CLI, the following DAC commands are available. Deploy the latest set of rules by running the following command:

```bash
<TODO> Fill in with example execution when commands are available.
```

4. Step to sync / deploy exception/action lists with the the specific DAC command

### Option 2:  Fleet Engine

[Fleet](https://www.elastic.co/guide/en/fleet/current/fleet-overview.html) offers an alternative method primarily used within Elastic to deploy prebuilt rules. This involves building a Fleet-compatible package containing the rules and pushing it using one of the Fleet developer APIs (http://my-kibana:5601/api/fleet/epm/packages). An end-to-end approach might consider building a package, testing the ruleset against an Elastic test stack, and parsing the HTTP response for errors. This approach, while consistent with Elastic's prebuilt rules workflow, is not directly recommended for external workflows as it may bypass critical rule versioning processes in Elastic Security.

|Pros|Cons|
|-|-|
|- Consistent Packaging: Bundles rule packages efficiently, updating Kibana [saved objects](https://www.elastic.co/guide/en/kibana/current/managing-saved-objects.html) to align with the prebuilt rules workflow.|- Workflow Disruption: Potentially overrides existing rule management workflows in Elastic.</br> - Complex Package Creation: Requires understanding and adherence to specific package specifications.</br> - Management Complexity: Can be cumbersome to maintain rules via Fleet packages.|

|                                                                                         |
| --------------------------------------------------------------------------------------- |
| <img src="_static/build_with_fleet.png" alt="Building Fleet Package"/>       |
| <center>*Figure 8: E2E build fleet rules package workflow* </center> |

**Steps:**

1. **Build** a rules package using the [elastic-package](https://github.com/elastic/elastic-package) go binary. Alternatively, use the CLI to build a package using the rules repo. This command alone builds everything in the **releases** directory needed except for the manifest update that happens usually in a separate internal workflow.

```bash
python -m detection_rules dev build-release
```

2. Update the versions to match the latest package version within the **manifest.yml** build if needed.
3. Check formatting by running **elastic-package format**, then **elastic-package lint** against the package built.
4. If using the detection-rules CLI, compress the package into a zip file of the **fleet** folder with the **security_detection_engine** prefix (e.g. security_detection_engine-8.13.0-beta.0.zip) to include the **manifest.yml** and **changelog.yml** files.
5. Deploy the package to the Elastic Security environment using the API.

```bash
curl -XPOST -H 'content-type: application/zip' -H 'kbn-xsrf: true' https://my-kibana:9243/api/fleet/epm/packages -u elastic:<password> --data-binary @security_detection_engine-8.13.0-beta.0.zip
```

6. Step to sync / deploy exception/action lists with the the specific DAC command

## Sub-Component 2: Deploying via CI/CD

CI/CD (Continuous Integration/Continuous Deployment) can be a powerful mechanism for managing detections as code, ensuring that security detections are consistently integrated, tested, and deployed in an automated and efficient manner. Leveraging CI/CD not only streamlines the update and deployment processes but also helps in maintaining the reliability and effectiveness of detection rules over time. Note: All of the reference examples are built on [GitHub action](https://github.com/features/actions) features that should be adapted to fit your version control product if possible.

In practical application, a combination of these deployment options can offer enhanced flexibility and control over the rule management process. For instance, leveraging "Push on Merge" for continuous integration of reviewed changes, "Manual Dispatch Push" for critical updates or exceptions, and "Cron Scheduling Push" for regular, non-urgent updates can provide a comprehensive CI/CD strategy that accommodates various operational needs and urgency levels. This blended approach allows teams to balance automation efficiency with the necessity for manual oversight in certain scenarios.

|Pros|Cons|
|-|-|
|- Automated Deployment: Ensures changes are automatically deployed upon merge, reducing manual intervention and potential human error.</br> - Change Tracking: Keeps a history of changes, allowing for rollbacks and audits of detection rule modifications.|- Dependent on Review Quality: Effectiveness is heavily reliant on the rigor of the code review process. Poorly reviewed changes can still be deployed.</br> - Merge Conflicts: Without careful management, merges can lead to conflicts or overwrite useful changes, especially in fast-moving environments|

### Option 1:  Push on Merge

Automatically deploying rule changes upon merging into the main branch is a common strategy that emphasizes the deployment of only reviewed and approved modifications. It helps to streamline deployment and aggregate several DAC rule management commands into a unified test and release process.

**Steps:**

1. Configure a CI/CD pipeline workflow following [GitHub actions](https://docs.github.com/en/actions) documentation in your version control system to trigger on merge events to the main branch. Add the DAC CLI commands to ensure rules are properly published.

```yaml
# Example GitHub Action Workflow
on:
  push:
    branches:
      - main
jobs:
  deploy_rules:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Deploy Rules
        run: |
          echo "Deploying rules..."
          # Add DAC CLI commands here
```

2. Set the environment variables to the Elastic Security environment as [GitHub secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) so your pipeline can deploy to the proper environment.
3. Upon merge, the pipeline automatically deploys the changes to the relevant detection systems.
4. Monitor deployments closely to adjust schedules as needed based on operational feedback and ensure rules are successfully deployed to production.

### Option 2: Manual Dispatch Push
This method involves manually triggering the deployment of detection rules through the CI/CD pipeline using the GitHub Dispatch configuration. It provides control over when changes are deployed and essentially allows you to deploy production rules on demand.

|Pros|Cons|
|-|-|
|- Deployment Control: Gives teams the ability to choose optimal deployment times, reducing the risk of introducing issues during critical periods.</br> - Review Opportunity: Allows for a final review or confirmation step before deployment, potentially catching issues missed during initial reviews.|- Process Delays: Can slow down the deployment process and introduce delays in getting new or updated detections live.</br> - Manual Error Risk: Manual processes are susceptible to human error, including forgetting to trigger deployments or mismanaging deployment sequences.|

**Steps:**
1. Configure a manual [GitHub Dispatch](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow) trigger in the rules for production deployment.

```yaml
# Example GitHub Action Manual Trigger
on:
  workflow_dispatch:
```

2. Team members responsible for deployment review the final changes and manually initiate the deployment process when ready.
3. Monitor deployments closely to adjust schedules as needed based on operational feedback and ensure rules are successfully deployed to production.

### Option 3: Cron Scheduling Push

This workflow is similar to other GitHub action workflows. However, deployments are scheduled to occur at specific times, using a cron job in the CI/CD pipeline. This approach can balance between immediate deployments and fully manual control.

|Pros|Cons|
|-|-|
|- Scheduled Deployments: Allows teams to plan around known deployment times, potentially reducing the impact on operational activities.</br> - Batched Changes: Changes can be batched and deployed together, reducing the frequency of deployments and associated risks.|- Misaligned Release Timing: Scheduled deployments may not align with the urgency of some changes, delaying critical updates.</br> - Potential Disruptions: If deployments are scheduled during peak times, they could introduce performance issues or downtime.|

**Steps:**
1. Configure a cron job [GitHub schedule](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule) within your CI/CD pipeline to trigger deployments at specific intervals (e.g., nightly or weekly).

```yaml
# Example GitHub Action Cron Job
on:
  schedule:
    - cron: '0 2 * * *' # Runs at 2 AM UTC daily
```

2. Bundle changes merged into the main branch for deployment at the next scheduled interval.
3. Monitor deployments closely to adjust schedules as needed based on operational feedback and ensure rules are successfully deployed to production.

## Sub-Component 3: Pre-Production Testing

Before deploying detection rules to the production environment, it's crucial to perform comprehensive end-to-end testing. This process involves validating the efficacy and accuracy of detection rules in a controlled environment that closely mimics production. The goal is to ensure that all rules behave as expected, generating alerts for known threats without producing excessive false positives or negatives. Prerequisites include a dedicated testing environment or dev stack, access to a suite of emulation tools or test scripts that mimic malicious activities, and a clear understanding of the expected outcomes for each detection rule.

Testing can occur at two critical junctures: before opening a Pull Request (PR), where individual detection engineers test rules in a development or dedicated test stack, and as a consolidated package of rules merged into the main branch. The latter involves deploying rules via CI to a test stack, where comprehensive emulation of threat scenarios verifies rule efficacy. If tests confirm rules perform correctly—both in generating alerts and accurately querying against generated events—a subsequent CI job or manual dispatch promotes the rules to the production environment.

Testing frameworks play a crucial role in simulating real-world threats to validate rule effectiveness. These frameworks, such as Red Team Automation (RTA) scripts or third-party platforms like Atomic Red Team, provide a structured way to generate synthetic events that mimic attack behaviors. Such rigorous testing ensures that rules not only trigger alerts as expected but also accurately match query criteria against the events generated in Elasticsearch, providing a nuanced measure of rule performance beyond mere alert firing.

:bulb: Note: The complexity of testing may vary based on the specific nature of the detection rules and the available testing infrastructure. Considerations include the scalability of the testing environment, the representativeness of the test data, and the ability to automate the testing process. Automated CI/CD pipelines can facilitate the deployment of rules to the testing environment, execution of test scenarios, and collection of results for analysis.

### Option 1 - Pre-PR Individual Rule Testing

Individual rule testing occurs before opening a pull request (PR). Detection engineers deploy their rules to a development Elastic Security environment where they manually trigger conditions expected to generate alerts. This hands-on approach allows for immediate feedback and adjustments. This process may include utilizing test frameworks to emulate threat behaviors and manually reviewing Elasticsearch events to ensure rule queries match expected outputs.

|Pros|Cons|
|-|-|
|- Early identification and resolution of issues.</br> - Direct control over the testing process and immediate feedback.|- Time-consuming if done manually for a large set of rules.</br> - May not cover complex attack scenarios that involve multiple rules or dependencies.|

**Steps:**

1. Deploy the new or modified rule to a dev testing environment.
1. Run emulation scripts or manual tests to generate the conditions expected to trigger the rule or Elasticsearch events to confirm they align with the rule's query criteria.
1. Analyze the alerts generated and adjust the rule as necessary.

### Option 2 - Post-merge Package Testing

After rules are merged into the main branch, a tester or automated CI job deploys the rule package to a test stack. Comprehensive testing, including the execution of attack emulations, is conducted to verify that all rules generate alerts as expected. If successful, another CI job or manual GitHub dispatch promotes the rules to production.

|Pros|Cons|
|-|-|
|- Validates the entire rule set in a scenario that mimics real-world conditions.</br> - Leverages automation for efficient and repeatable testing.|- Requires a robust CI/CD setup and test environment.</br> - Potentially delays deployment if issues are found late in the process.|

**Steps:**
1. Aggregate rules from the main branch into a deployable package.
1. Use CI to deploy the rule package to a dedicated test stack.
1. Run a comprehensive set of emulated threat scenarios against the test stack.
1. Analyze test results to confirm rules trigger alerts as expected and accurately query against generated events.
1. Upon successful testing, use CI or a manual dispatch to promote the rules to the production environment.
