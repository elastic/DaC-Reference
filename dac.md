[**DaC Concept and Workflows 4**](#dac-concept-and-workflows)

> [Overview: DaC Concept and Workflows
> 5](#overview-dac-concept-and-workflows)
>
> [Applying DaC Principles to Rules in Elastic
> 6](#applying-dac-principles-to-rules-in-elastic)
>
> [Managing Detection Rules in Elastic Security vs VCS
> 8](#managing-detection-rules-in-elastic-security-vs-vcs)
>
> [How to use this reference 8](#how-to-use-this-reference)
>
> [Overall Elastic DaC Diagram 9](#overall-elastic-dac-diagram)
>
> [High-Level Workflow Delineation 10](#high-level-workflow-delineation)

[**Managing Detection Rules in a VCS
12**](#managing-detection-rules-in-a-vcs)

> [Overview 13](#overview-1)
>
> [Considerations 14](#considerations)
>
> [Component 1: Elastic Detection Rules Repo and Usage
> 15](#component-1-elastic-detection-rules-repo-and-usage)
>
> [Component 2: Modifying Prebuilt Rules with Diverging Changes
> 26](#component-2-modifying-prebuilt-rules-with-diverging-changes)
>
> [Component 3: Creating Detection Rules
> 29](#component-3-creating-detection-rules)
>
> [Component 4: Rule Versioning 31](#component-4-rule-versioning)
>
> [Component 5: Exceptions and Actions
> 36](#component-5-exceptions-and-actions)
>
> [Component 6: Unit testing 40](#component-6-unit-testing)
>
> [Component 7: Rule Schema Validation
> 44](#component-7-rule-schema-validation)
>
> [Component 8: Detection Logic Validation
> 47](#component-8-detection-logic-validation)

[**Syncing Rules and Data from VCS to Elastic Security
49**](#syncing-rules-and-data-from-vcs-to-elastic-security)

> [Overview 50](#overview-2)
>
> [Considerations 51](#considerations-1)
>
> [Component 1: Deploying via Direct HTTP Request API Calls
> 52](#component-1-deploying-via-direct-http-request-api-calls)
>
> [Component 2: Deploying via CI/CD 57](#component-2-deploying-via-cicd)
>
> [Component 3: Pre-Production Testing
> 61](#component-3-pre-production-testing)

[**Managing Detection Rules in Elastic Security
64**](#managing-detection-rules-in-elastic-security)

> [Overview 65](#overview-3)
>
> [Considerations 65](#considerations-2)
>
> [Component 1: Source of Truth 67](#component-1-source-of-truth)

[**Syncing Rules and Data from Elastic Security to VCS
70**](#syncing-rules-and-data-from-elastic-security-to-vcs)

> [Overview 71](#overview-4)
>
> [Considerations 72](#considerations-3)
>
> [Component 1: Exporting Rules via Direct HTTP Request API Calls
> 72](#component-1-exporting-rules-via-direct-http-request-api-calls)
>
> [Component 2: Exporting Rules via CI/CD
> 73](#component-2-exporting-rules-via-cicd)

[**Duel Syncing Rules and Data between Kibana and VCS
75**](#duel-syncing-rules-and-data-between-kibana-and-vcs)

> [Overview 76](#overview-5)
>
> [Considerations 77](#considerations-4)
>
> [Consideration 1: Syncing Processes
> 78](#consideration-1-syncing-processes)

[**E2E Reference Example 80**](#e2e-reference-example)

> [Example Rule Lifecycle 81](#example-rule-lifecycle)

[**Feedback and Resources 82**](#feedback-and-resources)

> [Feedback 83](#feedback)
>
> [Updates and Next Steps 83](#updates-and-next-steps)
>
> [Resources 84](#resources)

*Intentionally Left Blank*

#### Overview

# DaC Concept and Workflows

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

##  

## Overview: DaC Concept and Workflows

Detection as Code (DaC) is a modern security approach that applies
software development best practices to the creation, management, and
deployment of security rules. By treating “detections” as traditional
code, organizations can leverage version control systems (VCS), automate
testing and deployment processes, and ensure consistency across their
security information and event management (SIEM) and endpoint detection
and response (EDR) capabilities. This methodology enhances collaboration
among security teams, streamlines updates, and facilitates a more agile
response to evolving threats.

<img src="media/image1.png" style="width:3.24354in;height:3.24354in" />

DaC adoption is driven by several factors:

- Ever-Growing Rule Sets: Maintaining detection rules without DaC
  becomes untenable as the number of rules increases.

- Expanding Threat Landscape: The breadth of coverage needed to protect
  against emerging threats necessitates a scalable approach suitable for
  diverse skill sets amongst the team.

- Drive Towards Security Team Maturity: Implementing DaC encourages the
  development of mature, repeatable processes within security teams. It
  supports the maintenance of high-quality detections through systematic
  peer reviews and rigorous testing.

- Broader Adoption of Automation: The wider movement towards automation
  in technology environments, exemplified by the adoption of
  Infrastructure as Code (IaC) principles, calls for enhanced automation
  capabilities across all tools used by security teams. DaC aligns with
  this trend by integrating rule management into automated workflows,
  ensuring consistency, efficiency, and the ability to swiftly respond
  to new threats.

- Compliance and Governance Requirements: Many organizations are
  adopting DaC to meet certification compliance requirements for peer
  review, change control, and disaster recovery of SIEM detections.
  Implementing DaC ensures that security rules are developed, reviewed,
  and maintained following strict governance standards, providing
  auditable evidence of compliance.

## Applying DaC Principles to Rules in Elastic

Incorporating DaC principles into Elastic Security rules management
workflows involves using VCS to track rule changes, employing continuous
integration (CI) tools for validation and testing, and continuous
deployment (CD) mechanisms to update detection rules within
[<u>Kibana</u>](https://www.elastic.co/guide/en/kibana/current/introduction.html)’s
Elastic Security seamlessly. This practice ensures that rule changes are
systematically reviewed, tested and enables rapid deployment of updates
to respond to new vulnerabilities and attack techniques.

###  Prerequisites

To effectively apply DaC principles to Elastic Security, individuals or
teams should have a foundational understanding of several key concepts
and technologies:

- Version Control Systems (VCS): Familiarity with VCS and tools (e.g.
  particularly Git), is crucial for tracking changes to detection rules
  and collaborating on rule development.

- Continuous Integration and Continuous Deployment (CI/CD): Knowledge of
  CI/CD pipelines and how they facilitate automated testing and
  deployment processes.

- Elastic Security: An understanding of how Elastic Security functions,
  including creating and managing detection rules within Elastic
  Security.

- Elastic Prebuilt Rules repository: Exposure to the detection-rules
  repo

- Scripting and Automation: Basic proficiency in scripting (e.g.,
  Python, Shell) to automate rule deployment and management tasks.

### Who Will Benefit from This Guide

This guide is for security analysts, engineers, and architects who
develop, deploy, and manage detection rules within Elastic Security
environments. It is particularly beneficial for:

- Security Analysts who wish to leverage automation for more efficient
  rule management and to respond more rapidly to emerging threats.

- Detection Engineers looking for methodologies to streamline detection
  logic development, testing, and deployment.

- Security Team Leads seeking to implement best practices for rule
  version control, collaboration, and quality assurance within their
  teams.

- DevOps Engineers involved in integrating security practices into CI/CD
  pipelines, aiming for a more cohesive and automated approach to
  security within their workflows.

- IT Security Architects exploring ways to incorporate as-code
  principles into security operations to enhance agility, repeatability,
  and reliability.

There are many ways to implement DaC within Elastic Security ecosystems,
but this reference provides a few examples to convey the core
principles. It delineates various approaches and architectures to
building DaC, catering to different operational models and
organizational needs. By navigating through this reference, individuals
will gain the insights and practical know-how to establish robust DaC
workflows, leading to more streamlined security operations, enhanced
collaboration across teams, and a solid foundation for integrating
security practices within automated CI/CD pipelines.

## Managing Detection Rules in Elastic Security vs VCS

While Elastic Security provides a user-friendly interface for creating
and managing detection rules, integrating these rules with a VCS
capability offers several advantages. VCS allows for detailed tracking
of rule changes, collaborative rule development, and the integration of
rule management into broader CI/CD workflows. This reference
documentation heavily biases the direct management of rules utilizing
VCS alongside the **detection-rules** repository to enhance detection
rule lifecycle management. It offers high-level alternative options to
considering posterity, but does not recommend implementing all of them.

## How to use this reference

This resource describes principles to adopt a DaC approach to Elastic
Security rule management. Whether you're looking to automate rule
deployments, enhance rule validation processes, or streamline rule
versioning and exception management, this guide offers a modular set of
practices and components. Select and combine the options relevant to
your specific needs and operational context to customize your detection
rule management workflow.

When considering adopting a DaC approach, several discrete components
should be taken into consideration. This viewpoint helps identify the
high-level requirements for implementing DaC end-to-end while
reinforcing the modular approach. This reference includes an explanation
of the different options, with any specific implications across these
components. See the DaC Diagram and Delineation table for more
information on how these components overlap.

Note: Jump to the [<u>quickstart</u>](#quickstart) section to get up and
running quickly.

### Core Workflows to Implement DaC within Elastic

1.  Maintaining rules within a Version Control System (VCS)

2.  Syncing rules from VCS to Elastic Security

3.  Managing rules within Elastic Security (consistent with a DaC
    approach)

4.  Syncing rules from Elastic Security to VCS

5.  Dual sync between VCS and Elastic Security (optional)

## Overall Elastic DaC Diagram

|                                                                         |
|-------------------------------------------------------------------------|
| <img src="media/image2.png" style="width:5.94271in;height:5.84793in" /> |
| *Figure x: High-level components*                                       |

This diagram serves as a visual guide to the three primary environments
where security rules are maintained:

1.  Locally within a cloned repository, as indicated by the
    **user-org/detection-rules** component.

2.  In various version control environments, such as GitHub, GitLab,
    Bitbucket, or Gitea, denoted by the **VCS** component.

3.  Within the **Elastic Security platform**.

The diagram also outlines the synchronization methods between these
environments:

- **Git Sync**, which manages the transfer of rules between the local
  repository and the VCS.

- **CI/CD Sync via APIs**, which handles the deployment and updating of
  rules from the VCS to Elastic Security.

- **REST API Sync**, which facilitates direct communication and rule
  synchronization between the local clone and Elastic Security.

This reference explains multiple strategies for synchronizing rules
between the components. Depending on the chosen method, syncing may
involve pushing rules from a local repository or VCS to Elastic Security
or pulling rules from Elastic Security to be merged into the VCS or
local repositories, ensuring that changes replicate across the
development, version control, and operational environments.

## High-Level Workflow Delineation 

<table>
<colgroup>
<col style="width: 16%" />
<col style="width: 28%" />
<col style="width: 22%" />
<col style="width: 33%" />
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Workflow Topic</strong></td>
<td><strong>Description</strong></td>
<td><strong>Requirements</strong></td>
<td><strong>Key Steps</strong></td>
</tr>
<tr class="even">
<td><strong>Maintaining Rules within VCS</strong></td>
<td>Involves version control, collaboration, and historical tracking of
detection rules within a VCS.</td>
<td><p>- Access to a VCS</p>
<p>- Forked or cloned detection-rules repository</p></td>
<td><p>- Fork/clone the detection-rules repo</p>
<p>- Create, validate, and unit test rules, actions/exceptions list
using CLI</p>
<p>- Commit and push updates regularly</p></td>
</tr>
<tr class="odd">
<td><strong>Syncing Rules from VCS to Elastic Security</strong></td>
<td>Covers the automated or manual processes of deploying or updating
rules in Elastic Security from VCS.</td>
<td><p>- API access to Elastic Stack</p>
<p>- Authentication credentials</p>
<p>- CI/CD pipeline (optional)</p></td>
<td><p>- Import rules into Elastic Security using CLI or API</p>
<p>- Configure CI/CD for automated syncing</p></td>
</tr>
<tr class="even">
<td><strong>Managing Rules within Elastic Security (consistent with
DaC)</strong></td>
<td>Focuses on creating, testing, and managing rules directly in Elastic
Security while considering backup and versioning strategies.</td>
<td><p>- Elastic Security access with permissions</p>
<p>- Knowledge of Elastic Security's UI</p></td>
<td><p>- Directly create, modify, and manage rules in Elastic
Security</p>
<p>- Manually export rules for backup/version control</p></td>
</tr>
<tr class="odd">
<td><strong>Syncing Rules from Elastic Security to VCS</strong></td>
<td>Describes exporting and versioning rules from Elastic Security back
into VCS for tracking and collaboration.</td>
<td><p>- Scripting for API interaction</p>
<p>- Authentication</p>
<p>- CI/CD setup for automation (optional)</p></td>
<td><p>- Export rules using Detection Engine API</p>
<p>- Commit exported rules into VCS</p>
<p>- Use CI/CD workflows to automate the process</p></td>
</tr>
<tr class="even">
<td><strong>(Optional) Dual Sync Between VCS and Elastic
Security</strong></td>
<td>Highlights a hybrid approach that ensures rules are synchronized and
up-to-date in both Elastic Security and VCS.</td>
<td><p>- Setup for bidirectional syncing</p>
<p>- Authentication</p>
<p>- Access</p>
<p>- Automation tools/scripts</p></td>
<td><p>- Establish sync process for both directions</p>
<p>- Automate sync using CLI, API, and CI/CD</p>
<p>- Regularly review and reconcile discrepancies</p></td>
</tr>
</tbody>
</table>

#### Workflow 1

# Managing Detection Rules in a VCS 

## Overview

Version Control System (VCS) adoption for managing detection rules
empowers security teams to implement Detection as Code (DaC) principles
through Elastic's Public APIs. This approach treats the VCS repository
as the authoritative source for custom detection rules. Changes to
detection rules are developed, reviewed, and version-controlled within a
repository before synced with the production Elastic stack. This method
leverages the inherent collaboration, review, auditing, and versioning
capabilities of VCS, ensuring a robust governance framework for the
lifecycle management of detection rules. This strategy is the
recommended approach to getting started with DaC and managing rules with
Elastic’s SIEM.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><h2 id="section-12"><img src="media/image3.png"
style="width:6.53125in;height:3.69444in" /></h2></td>
</tr>
<tr class="even">
<td><em>Diagram X: Syncing Local Rules with VCS Through Git
Workflows</em></td>
</tr>
</tbody>
</table>

By following the recommended steps within this workflow, you will
implement a DaC strategy from scratch that leverages VCS and the
detection-rules repo. In particular, you will:

1.  Fork the detection-rules repo,

2.  Install the Python dependencies,

3.  Initialize and configure your DaC custom rules folder using the CLI

4.  Configure the unit tests to run

5.  Create and version rules using the **detection-rules** capabilities

6.  Create and link exception and action lists

7.  Locally validate rules

## Considerations

While various strategies exist for managing detection rules within a VCS
framework, the [<u>Elastic Detection Rules
Approach</u>](#component-1-elastic-detection-rules-repo-and-usage) is
showcased throughout this reference. It demonstrates effective rule
management techniques utilizing the detection-rules repository alongside
available configurations. It is important to highlight that, among the
discussed methods, the **detection-rules** repository approach remains
the primary and actively supported method for managing security rules
within the Elastic ecosystem. It requires some familiarity with the
[<u>detection-rules</u>](https://github.com/elastic/detection-rules)
repository to take full advantage of the features available. This
strategy ensures that security teams can leverage the full potential of
Elastic's capabilities in a structured, version-controlled manner.

Terraform, as a powerful tool for infrastructure as code (IaC), offers
significant potential for automating many aspects of DaC processes. By
defining infrastructure and configuration details, Terraform can
automate the setup and maintenance of environments crucial for DaC
workflows, such as provisioning Elastic Security instances, configuring
Elastic Security for rule management, or establishing CI/CD pipelines
for rule deployment. However, this guide does not delve into
Terraform-specific examples to maintain a focus on DaC principles
applicable across various tools and platforms. The versatility of
Terraform means it can be integrated into many components of DaC,
providing a layer of automation that enhances efficiency and consistency
across detection rule lifecycle management.

##  

## Component 1: Elastic Detection Rules Repo and Usage

Diagram 2 illustrates the reference architecture, detailing how
components integrate end-to-end. Users initiate the process by forking
the **detection-rules** repository, configuring it to support custom
rules and testing, and then establishing the release mechanism to
synchronize rules with the Elastic Security system. This section
outlines key components considered for managing rules in a Version
Control System (VCS) and how the **detection-rules** repository
facilitates this process. The repository's components are segmented into
independent parts, each provided with detailed guidance for effective
rule management.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><h3 id="section-14"><img src="media/image4.png"
style="width:6.53125in;height:3.13889in" /></h3></td>
</tr>
<tr class="even">
<td><strong>Diagram 2</strong></td>
</tr>
</tbody>
</table>

### Repo Setup and Structure

This section covers the detection-rules repo, how to install
prerequisites, and the overall contents, which is a prerequisite to
using many of the subsequent steps and components.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Open Source Collaboration: Encourages open-source contributions,
allowing for community-driven enhancements and shared
improvements.</p></li>
<li><p>Comprehensive Tooling: The CLI support simplifies DaC processes,
from syncing to validation, making rule management more
efficient.</p></li>
<li><p>Extensive Validation and Testing: Incorporates built-in unit
tests and schemas to validate detection rules against Elastic's
standards before deployment rigorously.</p></li>
<li><p>Flexibility and Customization: Designed to accommodate diverse
operational needs, the repository can be tailored for specific
environments or detection strategies.</p></li>
</ul></td>
<td><ul>
<li><p>Learning Curve: Familiarity with the command line and Git is
necessary to fully utilize the repository’s capabilities, which might
present a hurdle for some users.</p></li>
</ul>
<ul>
<li><p>Limited UI Integrations: While the CLI offers powerful
functionalities, certain rule configurations that are accessible through
Elastic Security's UI might not be directly supported or require manual
adjustments.</p></li>
<li><p>Customization Requirements: To align with unique organizational
requirements or to incorporate non-standard rule types, additional
customization of the repository structure or scripts may be
needed.</p></li>
</ul></td>
</tr>
</tbody>
</table>

##### Steps: 

1.  Navigate to the
    [<u>detection-rules</u>](https://github.com/elastic/detection-rules)
    repo**.** Here you will find the repository information to
    ***fork*** the
    repository<img src="media/image5.png" style="width:5.61013in;height:5.23438in" />

2.  Follow GitHub’s
    [<u>instructions</u>](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)
    to ***fork and clone*** the detection-rules repo.

3.  Download and install
    [<u>Python</u>](https://www.python.org/downloads/). *Note: Versions
    3.12+ are supported.*

4.  Install the detection-rules python **CLI** prerequisites. Installing
    essentially requires you to change to the new directory and use pip
    to install. **cd detection-rules -\> make** (optionally install
    direct using pip **pip install “\[.dev\]”**) See the
    [<u>README</u>](https://github.com/elastic/detection-rules/blob/main/README.md)
    for more details.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><h3 id="section-15"><img src="media/image6.png"
style="width:6.53125in;height:6.08333in" /></h3></td>
</tr>
<tr class="even">
<td><h3
id="note-we-recommend-using-a-python-virtual-environment-for-a-cleaner-management-experience."><em>Note:
We recommend using a <a
href="https://docs.python.org/3/library/venv.html"><u>Python virtual
environment</u></a> for a cleaner management experience.</em></h3></td>
</tr>
</tbody>
</table>

The repo contains code for unit testing in Python, integrating via
[<u>APIs</u>](https://www.elastic.co/guide/en/security/current/security-apis.html)
with the security solution in Elastic Security, Red Team Automation to
emulate attack techniques and a miscellaneous directory that contains
schemas used for query validation.

**Repo structure:**

|                       |                                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **Folder**            | **Description**                                                                                                             |
| detection_rules/      | Python module for rule parsing, validating, and packaging. Also includes miscellaneous files, such as ECS and Beats schemas |
| doc/                  | Documentation on rule management and other useful tips.                                                                     |
| kibana/               | Python library for handling the API calls to Elastic Security and the Detection Engine                                      |
| kql/                  | Python library for parsing and validating Kibana Query Language                                                             |
| rta/                  | Red Team Automation code used to emulate attacker techniques, used for rule testing                                         |
| rules/                | Root directory where rules are stored                                                                                       |
| rules_building_block/ | Root directory where building block rules are stored                                                                        |
| tests/                | Python code for unit testing rules                                                                                          |

The miscellaneous folder
[<u>detection_rules/etc</u>](https://github.com/elastic/detection-rules/tree/main/detection_rules/etc)
contains our schemas used for validation, our versioning files used to
track rule changes, and our stack-schema-map.yml file which instructs
our validation to run on specific Elastic Security environment and
schema version combinations. When electing to fork the detection-rules
repo and manage rules in a **CUSTOM_DIR**, these files enable additional
customization and workflows to test and validate rules. Not all files
are included in the table, however the more prominent ones are
highlighted below.

|                                                                                            |                                                                                                                |
|--------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| **File**                                                                                   | **Description**                                                                                                |
| deprecated_rules.json                                                                      | Version file containing a reference of all deprecated rules                                                    |
| packages.yml                                                                               | Package used to build a fleet package                                                                          |
| rule-mapping.yml                                                                           | Mapping file used to map detection rules to RTAs                                                               |
| version.lock.json                                                                          | Master version lock file used to track the version of a rule based on sha256 calculation                       |
| integration-\*.json.gz                                                                     | Files used to validate rules against Elastic [<u>integration</u>](https://www.elastic.co/integrations) schemas |
| *Note: View the schemas and dataclass section to view the schema files and folders table.* |                                                                                                                |

### CLI

The detection-rules Python command line interface (CLI) is designed
initially to support internal Elastic rule management, however, it also
includes DaC options to manage custom rules. Users can manage rules
locally to validate their TOML files against the defined schema
dataclasses, interact with Elastic Security to sync rule updates, or
even execute RTAs just to name a few. See the
[<u>CLI.md</u>](https://github.com/elastic/detection-rules/blob/main/CLI.md)
for more details.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Integration with Public Elastic APIs: Serves as a convenient
reference for Elastic API utilization.</p></li>
<li><p>Simplified Command Execution: Condenses multiple DAC processes
into singular commands for efficiency.</p></li>
<li><p>Versatility: Harnesses the full potential of CLI for rule
management.</p></li>
</ul></td>
<td><ul>
<li><p>Custom Application Limitations: May not seamlessly integrate with
all custom applications specific to user environments.</p></li>
<li><p>Intentional Coupling: Some commands are intentionally designed to
be closely integrated with specific workflows.</p></li>
</ul></td>
</tr>
</tbody>
</table>

The CLI contains more options than needed to perform standard DAC
operations. Therefore, we have grouped the relevant DAC commands under
\<name\>.

##### Steps: 

1.  Confirm prerequisites are installed by loading the help. For an
    exhaustive list of options run **python -m detection_rules -h** in
    the terminal.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>█▀▀▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄ ▄ █▀▀▄ ▄ ▄ ▄ ▄▄▄ ▄▄▄<br />
█ █ █▄▄ █ █▄▄ █ █ █ █ █ █▀▄ █ █▄▄▀ █ █ █ █▄▄ █▄▄<br />
█▄▄▀ █▄▄ █ █▄▄ █▄▄ █ ▄█▄ █▄█ █ ▀▄█ █ ▀▄ █▄▄█ █▄▄ █▄▄ ▄▄█<br />
<br />
Usage: detection_rules [OPTIONS] COMMAND [ARGS]...<br />
<br />
Commands <strong>for</strong> detection-rules repository.<br />
<br />
Options:<br />
-D, --debug / -N, --no-debug Print full exception stacktrace on
errors<br />
-h, --help Show this message <strong>and</strong> exit.<br />
<br />
Commands:<br />
build-limited-rules Import rules <strong>from</strong> json, toml,
<strong>or</strong> Kibana...<br />
build-threat-map-entry Build a threat map entry.<br />
create-rule Create a detection rule.<br />
dev Commands related to the Elastic Stack rules...<br />
es Commands <strong>for</strong> integrating <strong>with</strong>
Elasticsearch.<br />
export-rules Export rule(s) into an importable ndjson file.<br />
generate-rules-index Generate enriched indexes of rules, based on
a...<br />
<strong>import</strong>-rules Import rules <strong>from</strong> json,
toml, yaml, <strong>or</strong> Kibana...<br />
kibana Commands <strong>for</strong> integrating <strong>with</strong>
Kibana.<br />
mass-update Update multiple rules based on eql results.<br />
normalize-data Normalize Elasticsearch data timestamps
<strong>and</strong> sort.<br />
rta Commands related to Red Team Automation (RTA)...<br />
rule-search Use KQL <strong>or</strong> EQL to find matching
rules.<br />
test Run unit tests over all of the rules.<br />
toml-lint Cleanup files <strong>with</strong> some simple toml
formatting.<br />
typosquat Commands <strong>for</strong> generating typosquat
detections.<br />
validate-all Check <strong>if</strong> all rules validates against a
schema.<br />
validate-rule Check <strong>if</strong> a rule staged
<strong>in</strong> rules dir validates...<br />
view-rule View an internal rule <strong>or</strong> specified rule
file.</td>
</tr>
<tr class="even">
<td><em>Note: Using some of these options with your custom rules
requires repo configuration</em></td>
</tr>
</tbody>
</table>

2.  Explore the **DAC** CLI commands by running **python -m
    detection_rules \<name\> -h** in the terminal.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>█▀▀▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄ ▄ █▀▀▄ ▄ ▄ ▄ ▄▄▄ ▄▄▄<br />
█ █ █▄▄ █ █▄▄ █ █ █ █ █ █▀▄ █ █▄▄▀ █ █ █ █▄▄ █▄▄<br />
█▄▄▀ █▄▄ █ █▄▄ █▄▄ █ ▄█▄ █▄█ █ ▀▄█ █ ▀▄ █▄▄█ █▄▄ █▄▄ ▄▄█<br />
<br />
Usage: detection_rules [OPTIONS] COMMAND [ARGS]...<br />
<br />
Commands <strong>for</strong> detection-rules repository.<br />
<br />
Options:<br />
-D, --debug / -N, --no-debug Print full exception stacktrace on
errors<br />
-h, --help Show this message <strong>and</strong> exit.<br />
<br />
Commands:<br />
build-limited-rules Import rules <strong>from</strong> json, toml,
<strong>or</strong> Kibana...<br />
create-rule Create a detection rule.<br />
export-rules Export rule(s) into an importable ndjson file.<br />
<strong>import</strong>-rules Import rules <strong>from</strong> json,
toml, yaml, <strong>or</strong> Kibana...<br />
kibana Commands <strong>for</strong> integrating <strong>with</strong>
Kibana.<br />
view-rule View an internal rule <strong>or</strong> specified rule
file.</td>
</tr>
<tr class="even">
<td><em>Note: Using these options with your custom rules requires repo
configuration and the Elastic Security Solution available.</em></td>
</tr>
</tbody>
</table>

### Unit tests

The **detection-rules** repository's unit tests serve as a comprehensive
mechanism to verify the compatibility of rule schemas with the expected
format and structure for Elastic Security. These tests ensure
consistency in rule management and adherence to best practices as
defined by Elastic's detection engineering team. By loading all rules
simultaneously, the tests efficiently iterate over the rule set to
evaluate various cases, from syntax validation to logical consistency.
Note: Additional unit testing options and details are available in
[<u>Component 6</u>](#component-6-unit-testing).

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Flexibility in Testing: Allows configuration to either execute or
skip specific unit tests, catering to diverse testing needs.</p></li>
<li><p>Adherence to Best Practices: Incorporates Elastic's recommended
rule development practices, ensuring high-quality rule
creation.</p></li>
<li><p>Independent Validation: Facilitates the validation of query
syntax and schema conformity outside of the Elastic stack, offering an
additional layer of assurance.</p></li>
</ul></td>
<td><ul>
<li><p>Limited Coverage: Some specialized rule features might not be
fully supported, requiring external validation methods.</p></li>
<li><p>Customization Challenges: Adapting tests for unique scenarios or
integrating divergent unit tests with upstream changes may pose
difficulties.</p></li>
<li><p>Time-Consuming: The comprehensive nature of the tests may lead to
longer execution times, particularly when loading extensive rule
sets.</p></li>
</ul></td>
</tr>
</tbody>
</table>

##### Steps: 

1.  Run the unit tests to confirm all prebuilt rules are passing the
    test cases. The CLI provides an option to run the unit tests and
    format the output using the **pytest** python module. Run **python
    -m detection_rules test** to run all tests.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>(detection_dev) ➜ detection-rules git:(main) ✗ python -m
detection_rules test<br />
Loaded config file:
/Users/stryker/workspace/ElasticGitHub/detection-rules/.detection-rules-cfg.json<br />
<br />
█▀▀▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄▄▄ ▄ ▄ █▀▀▄ ▄ ▄ ▄ ▄▄▄ ▄▄▄<br />
█ █ █▄▄ █ █▄▄ █ █ █ █ █ █▀▄ █ █▄▄▀ █ █ █ █▄▄ █▄▄<br />
█▄▄▀ █▄▄ █ █▄▄ █▄▄ █ ▄█▄ █▄█ █ ▀▄█ █ ▀▄ █▄▄█ █▄▄ █▄▄ ▄▄█<br />
<br />
================================================== test session starts
==================================================<br />
platform darwin -- Python 3.11.5, pytest-7.4.2, pluggy-1.3.0 --
/Users/stryker/.virtualenvs/detection_dev/bin/python<br />
cachedir: .pytest_cache<br />
rootdir: /Users/stryker/workspace/ElasticGitHub/detection-rules<br />
configfile: pyproject.toml<br />
plugins: typeguard-3.0.2</td>
</tr>
<tr class="even">
<td><em>Note: You can optionally run <strong>pytest</strong></em> <em>to
run the tests.</em></td>
</tr>
</tbody>
</table>

2.  Running all test cases may not be desirable. In this case, you can
    configure the unit tests glob-style by creating a test config and
    specifying the test cases. More details are provided in the [<u>unit
    testing</u>](#component-6-unit-testing) section. For further
    troubleshooting or unit test customization, refer to the **pytest**
    module documentation, which the CLI leverages to run tests and
    format outputs.

### Schemas and Data Structures

The repository extensively utilizes schemas sourced from upstream (e.g.,
Beats, ECS, integrations) for validating rule fields against expected
indices or data views. Additionally, Python dataclasses define the rule
object's structure, facilitating the TOML rules' formatting and their
translation for import/export processes with Elastic Security. These
components work in tandem to ensure detection rules are both
structurally sound and functionally accurate.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Outside-the-SIEM Validation: Enables rule verification
independently from the Elastic stack, ensuring rules are ready for
deployment.</p></li>
<li><p>Dataclass Support: Provides a structured format for rule
development, accommodating updates as new features are introduced in
Elastic Security.</p></li>
</ul></td>
<td><ul>
<li><p>Feature Limitations: Not all rule functionalities are
encapsulated within the dataclasses, potentially necessitating manual
verification.</p></li>
<li><p>Adaptation Required: Custom applications or some rule fields
(e.g. saved queries) may require additional effort to align with the
provided schemas and dataclasses if needed.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Explore the [<u>rule
    dataclasses</u>](https://github.com/elastic/detection-rules/blob/main/detection_rules/rule.py)
    detailed in the repository to understand the expected format for
    rule definitions. These are periodically updated to reflect new
    capabilities within the Kibana features set.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>@dataclass(frozen=True)<br />
<strong>class</strong>
<strong>RuleMeta</strong>(MarshmallowDataclassMixin):<br />
"""Data stored in a rule's [metadata] section of TOML."""<br />
creation_date: definitions.Date<br />
updated_date: definitions.Date<br />
deprecation_date: Optional[definitions.Date]<br />
<br />
# Optional fields<br />
bypass_bbr_timing: Optional[bool]<br />
comments: Optional[str]<br />
integration: Optional[Union[str, List[str]]]<br />
maturity: Optional[definitions.Maturity]<br />
min_stack_version: Optional[definitions.SemVer]<br />
min_stack_comments: Optional[str]<br />
os_type_list: Optional[List[definitions.OSType]]<br />
query_schema_validation: Optional[bool]<br />
related_endpoint_rules: Optional[List[str]]<br />
promotion: Optional[bool]<br />
<br />
# Extended information as an arbitrary dictionary<br />
extended: Optional[Dict[str, Any]]</td>
</tr>
<tr class="even">
<td><em>Example: Rule metadata dataclass.</em></td>
</tr>
</tbody>
</table>

2.  Visit the
    [<u>etc/</u>](https://github.com/elastic/detection-rules/tree/main/detection_rules/etc)
    directory within the repository to review additional resources,
    including validation schemas and configuration files required for
    rule management and development.

|                               |                                                                                                  |
|-------------------------------|--------------------------------------------------------------------------------------------------|
| **File / Folder**             | **Description**                                                                                  |
| api_schemas/                  | JSON schemas used to aid rule downgrade migrations                                               |
| beats_schemas/                | JSON schemas used within query validation for all beats’ versions                                |
| ecs_schemas/                  | JSON schemas used within query validation for all ECS versions                                   |
| endgame_schemas/              | JSON schemas used within query validation for multiple Endgame versions                          |
| endpoint_schemas/             | JSON schema used within query validation for all endpoint schemas                                |
| attack-v13.1.0.json.gz        | MITRE ATT&CK mapping file to validate threat mapping within the detection rule TOML file         |
| integration-manifests.json.gz | Manifest of integrations used to determine which integration schemas to download.                |
| integration-schemas.json.gz   | JSON schema used within query validation for all integration schemas used within the TOML files. |
| stack-schema-map.yaml         | Mapping of all Elastic Security version / schema combinations to perform query validation.       |
| non-ecs-schema.json           | JSON schema for all fields not mapped in any other schema.                                       |

## 

3.  To update these schemas, use the built-in CLI integration commands
    to update the manifest and download the schemas.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>python -m detection_rules dev integrations build-manifests<br />
python -m detection_rules dev integrations build-schemas</td>
</tr>
</tbody>
</table>

##  

## Component 2: Modifying Prebuilt Rules with Diverging Changes

### Option 1: Fork and Map Rules

This method involves creating a custom directory within your forked
version of the **detection-rules** repository to manage and map rules.
By setting an environment variable to this directory's location and
initializing it via the CLI, users can effectively manage custom rule
sets alongside the benefits of schema validation and unit testing
provided by the original repository.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Ease of Customization: Requires minimal adjustments to
accommodate custom rules.</p></li>
<li><p>Schema Control: Offers direct control over schemas, allowing for
further customization.</p></li>
<li><p>Organizational Flexibility: Enables the segregation of custom
rules tailored to specific Elastic stacks or spaces, enhancing
manageability.</p></li>
</ul></td>
<td><ul>
<li><p>In-depth Knowledge Required: Users need a thorough understanding
of detection-rules internals for leveraging custom schemas or
implementing unique versioning mechanisms.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Set the **CUSTOM_RULES** environment variable to a custom directory
    of your choosing.

|                                   |
|-----------------------------------|
| export CUSTOM_RULES=my_custom_dir |

2.  Initialize the custom directory with necessary detection-rules
    specific files by running:

|                                                       |
|-------------------------------------------------------|
| Python -m detection_rules \<name\> init my_custom_dir |

This command will generate the custom directory supplied to the command
line and initialize the folder with the detection-rule specific files
necessary.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td># example structure:<br />
# my_custom_dir<br />
# ├── _config.yaml<br />
# ├── example_rule_1.toml<br />
# ├── example_rule_2.toml<br />
# └── etc<br />
# ├── deprecated_rules.json<br />
# ├── packages.yml<br />
# ├── stack-schema-map.yaml<br />
# └── version.lock.json<br />
# └── actions<br />
## ├── action_1.toml<br />
## ├── action_2.toml<br />
# └── exceptions<br />
## ├── exception_1.toml<br />
## ├── exception_2.toml</td>
</tr>
</tbody>
</table>

The **\_config.yaml** will contain a configuration that points to custom
directory-specific configuration files defining how to load, package,
and validate the custom rules. Here are some management notes associated
with each file.

- deprecated_rules: etc/deprecated_rules.json

  - Upon rule deprecation, **maturity = “deprecated”** is set in the
    TOML rule and the rule is moved to the **\_deprecated** folder

  - Rules are recorded in the deprecated_rules.json when **version
    lock** update occur

- packages: etc/packages.yml

  - Update **name: “\<version\>** to the current Elastic Security
    version managed

- stack_schema_map: etc/stack-schema-map.yaml

  - Add the version combinations of the different schemas preferred to
    validate security rules

  - Update the schema manifest as needed, download schemas and migrate
    the schemas to the custom directory

- version_lock: etc/version.lock.json

|                                                                          |
|--------------------------------------------------------------------------|
| \<TODO\> Fill in with example DAC execution when commands are available. |

3.  Validate and test your custom rules by executing:

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>CUSTOM_RULES_DIR=my_custom_dir make test</p>
<p>CUSTOM_RULES_DIR=my_cusom_dir python -m detection_rules test</p></td>
</tr>
</tbody>
</table>

### Option 2: Custom Repo Changes

Alternatively, cloning or forking the **detection-rules** repo and
applying custom modifications enables the integration of custom
detection rules directly. This could involve altering the
**DEFAULT_RULES_DIR** python variable to change the default rules
directory or incorporating custom rules within the prebuilt structure.
Such adjustments, however, might introduce challenges, especially when
integrating with upstream updates or running tests.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Custom Use Case Adaptability: Provides the flexibility to tailor
the repository to unique requirements.</p></li>
<li><p>Deep Customization Potential: Allows for significant
modifications to accommodate specific needs.</p></li>
</ul></td>
<td><ul>
<li><p>Advanced Knowledge Necessary: Demands a comprehensive
understanding of the codebase for effective customization.</p></li>
<li><p>Potential for Merge Conflicts: Custom changes might lead to
conflicts with upstream updates, complicating future
integrations.</p></li>
<li><p>Opaque Issues: Custom modifications could introduce unforeseen
problems, particularly when integrating or testing rules.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

###  

## Component 3: Creating Detection Rules

### Option 1 - Manually Creating Rules based on Template

Creating detection rules manually by using existing rules as templates
involves selecting a pre-existing rule within the repo, copying its file
structure, and then modifying the values to fit the new detection
scenario. This approach allows for granular control over rule creation,
leveraging the familiarity of established rule patterns while tailoring
the specifics to new requirements.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Style: Enables rule creation to stylistically remain
consistent.</p></li>
<li><p>Familiarity: Utilizes existing rules as a foundation, easing the
creation process for those familiar with rule structures.</p></li>
</ul></td>
<td><ul>
<li><p>Time-consuming: Manually adjusting each rule can be
labor-intensive, especially for complex detection scenarios.</p></li>
<li><p>Error-prone: Manual entry increases the risk of errors or
omissions in rule configuration (e.g. incidentally duplicating
fields).</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Select an existing rule (or manually created rule template) that
    closely matches the detection scenario you aim to cover.

2.  Copy the rule's structure as a template and modify the necessary
    fields, such as rule name, description, conditions, and actions, to
    suit the new use case.

3.  Validate the rule to ensure it meets the schema requirements and
    test it within your environment for efficacy.

##  

### Option 2 - Semi-Automation create-rule CLI

The **create-rule** command offers a semi-automated approach to rule
creation, guiding users through the available rule options via an
interactive CLI. This method streamlines the rule creation process by
providing prompts for required fields and stripping out unnecessary
additional fields, ensuring schema compliance and facilitating
validation.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Efficiency: Speeds up the rule creation process by automating the
structure and validation steps.</p></li>
<li><p>Guidance: Offers prompts for required fields, reducing the
likelihood of missing critical rule components.</p></li>
</ul></td>
<td><ul>
<li><p>Limited Customization: While efficient, it may not allow for as
deep customization as manually editing rule files.</p></li>
<li><p>Learning Curve: Requires familiarity with the CLI and its
commands for effective use.</p></li>
<li><p>Limited Field Support: Not all fields are supported by fully
implemented schemas and require knowledge of the specific format
required to be input.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Use the **detection_rules create-rule PATH** command to initiate the
    rule creation process. Optionally, you can specify a configuration
    file with the **-c** or **--config** parameter for predefined rule
    attributes.

2.  Choose the rule type with the **-t** or **--rule-type** option if
    desired, or follow the CLI prompts to define the rule type and other
    required attributes.

3.  After completing the interactive prompts, the command will generate
    a rule file in the specified path. Validate and test this rule to
    ensure it behaves as expected within your environment.

## Component 4: Rule Versioning

Understanding and managing the versioning of detection rules is crucial
for maintaining a reliable security posture. Knowing the version of
deployed rules aids in quick issue triage and ensures consistency across
different environments. This aspect of DaC is primarily used when
syncing rules to the production environment and effectively is a
dependency for automated duel-syncing. Versioning can be managed in
multiple ways (e.g. force overwriting based on the latest push,
comparing rule versions, comparing sha256 hashes of the rule, etc.). The
detection-rules repo enables versioning by comparing rule hashes and
storing the associated versions within a lock file for future reference.

### Option 1: Built-in Version Lock Flow

The recommended approach is to manage production rule versions using a
**version.lock.json** file, with deprecated rule versions handled
separately in a **deprecated_rules.json** file. This approach mirrors
Elastic's best practices for version control, allowing for precise
tracking of rule changes and facilitating smoother deployments to
multiple stacks or spaces.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Clarity: Uses human-readable version files stored in VCS for
straightforward version tracking.</p></li>
<li><p>Control: Allows for multiple changes before syncing, with a
dedicated locking step ensuring version consistency.</p></li>
<li><p>Completely optional: By default, empty versioning is not a
prerequisite to other workflows.</p></li>
</ul></td>
<td><ul>
<li><p>Maintenance: Requires ongoing management to keep versioning
accurate and up to date.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Initialize the custom directory configuration to generate
    **version.lock.json** and **deprecated_rules.json**. Avoid modifying
    these files if versioning isn't needed. Note: The files generated
    for the custom rules directory both default to empty **{}**,
    symbolizing no rules will be tracked.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>{</p>
<p>"00140285-b827-4aee-aa09-8113f58a08f3": {<br />
"min_stack_version": "8.3",<br />
"rule_name": "Potential Credential Access via Windows Utilities",<br />
"sha256":
"456e5ed43e056841aea460851e9e496aa85a9828fcb4bebade3a4f8b1d2a637e",<br />
"type": "eql",<br />
"version": 110<br />
},</p>
<p>}</p></td>
</tr>
<tr class="even">
<td><em>Example version.lock.json</em></td>
</tr>
</tbody>
</table>

The **.version.lock.json** file contains several fields nested under the
rule ID:

- min_stack_version: The minimum Elastic Security version that the rule
  supports.

- rule_name: The name of the rule

- sha256: The hash of the rule contents generated

- type: The rule type (e.g. query, saved_query, machine_learning, eql,
  esql, threshold, threat_match, new_terms)

- version: Integer that increments when the sha256 hash changes

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>"041d4d41-9589-43e2-ba13-5680af75ebc2": {<br />
"deprecation_date": "2023/09/25",<br />
"rule_name": "Deprecated - Potential DNS Tunneling via Iodine",<br />
"stack_version": "8.3"<br />
},</td>
</tr>
<tr class="even">
<td>Example deprecated_lock.json</td>
</tr>
</tbody>
</table>

The **deprecated_rules.json** also contains several fields nested under
the rule ID:

- stack_version: The minimum Elastic Security version that the rule
  supported.

- rule_name: The name of the rule

- Deprecation_date: The date the rule was deprecated

Collectively, these two files are used as the source of truth and used
to properly sync files between Elastic Security.

2.  To update these version lock files, run the **python -m
    detection_rules dev build-release --update-version-lock** command.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><strong>+ python -m detection_rules dev build-release
--update-version-lock<br />
[+] Building package 8.3<br />
- 5 rules excluded from package<br />
Rule changes detected!<br />
- 10 changed rules<br />
- 11 new rules<br />
- 0 newly deprecated rules<br />
run `build-release --update-version-lock` to update version.lock.json
and deprecated_rules.json<br />
Rule changes detected!</strong></td>
</tr>
</tbody>
</table>

### 

### Option 2: Defer to Elastic Security

Elastic Security by default will manage versions for detection rules.
This option effectively forces all versioning concepts to rely on
Elastic Security’s calculations for changes. Therefore any required sync
workflows based on the rule version (e.g. pushing a later version of a
rule based on a higher version number versus simply a hash difference)
will require custom logic to do this.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Simplicity: Elastic Security automatically handles versioning,
requiring no manual intervention.</p></li>
<li><p>Immediate Updates: Rule versions are updated with each change
made through the UI.</p></li>
</ul></td>
<td><ul>
<li><p>Custom Logic Needed: Requires additional development work to
implement hash comparison and version tracking.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  If default versioning suffices, no action is needed. Otherwise,
    develop custom logic to manage rule versioning effectively.

2.  Ensure the versioning mechanism implemented is used within your CICD
    workflows to compare rules before updating the production
    environment.

### Option 3: Custom Insert Versions on Push

Another option is to calculate rule versions when syncing rules to the
production environment and insert the version within the rule object for
Elastic Security to ingest. This approach would not use the version lock
file; rather it would dynamically build the version and insert it into
the JSON rule to be sent upstream. Elastic Security will then use the
version supplied on import. This option effectively means that the
latest rule synced to production will have the latest versions. The
added benefit here is that the rule version will match any additional
custom versioning scheme developed to support rule comparisons.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Custom Versioning: Allows for tailored version control, fitting
unique operational requirements.</p></li>
</ul></td>
<td><ul>
<li><p>Development Overhead: Necessitates custom logic for version
calculations and rule updates.</p></li>
</ul></td>
</tr>
</tbody>
</table>

### Option 4: Custom Add Versions Manually

Instead of using a version lock file or building versions dynamically to
insert into the built object, another option would be to manually add
rule versions directly to each detection rule TOML file. Upon building
the JSON to sync with Elastic Security, the versions in this case would
automatically be included in the rule JSON. This approach would put the
onus on the rule author to manually increment the version when
appropriate or build custom logic to support this workflow. Currently,
the expectation is that versioning is abstracted away from the rule
author and managed by the engineer packaging and deploying the
production ruleset.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Visibility: Makes rule versions explicit within the rule
definition files.</p></li>
</ul></td>
<td><ul>
<li><p>Manual Effort: Requires consistent manual updates or custom
automation to manage versions, which may be more error-prone.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Add a **version** or similar field to the **RuleMeta** class

2.  Manually (or through automation) add version information to each
    rule's TOML file.

3.  Implement a mechanism to **increment versions** as needed, ensuring
    consistency and accuracy in version tracking.

## Component 5: Exceptions and Actions

Rule
[<u>exceptions</u>](https://www.elastic.co/guide/en/security/current/detections-ui-exceptions.html)
and [<u>response
actions</u>](https://www.elastic.co/guide/en/security/current/response-actions.html)
lists are two security features that are natively supported within
Elastic Security. Here, we explore methods to manage these elements in
code, outside of Elastic Security, leveraging the Detection as Code
(DaC) approach.

### Option 1: Built-in Lists Management

Rule exceptions and response actions are integral security features
natively supported within Elastic Security. Utilizing VCS for DaC allows
for varied methodologies in managing exception lists and their rule
associations, given their intricate structure. This approach focuses on
managing exception lists and action lists in separate dedicated files.
After defining a custom rules management directory, exception and action
lists can be defined in the **\_config.yaml** file.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Flexibility: Exception lists can be managed independently of the
rules, enabling them to be linked to multiple rules
simultaneously.</p></li>
</ul></td>
<td><ul>
<li><p>Complexity: Tracking which rules are linked to various exception
lists can become challenging.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  In the **\_config.yaml**, specify the action and exception folder
    names

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>directories:<br />
# actions_dir: exceptions<br />
# exceptions_dir: actions</td>
</tr>
</tbody>
</table>

2.  Manually create the action lists (one TOML file per rule) based on
    the [<u>Actions
    schema</u>](https://www.elastic.co/guide/en/security/current/bulk-actions-rules-api.html#actions-object-schema-bulk).

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>[metadata]<br />
creation_date = "2024-02-21"<br />
rule_id = "5d1e96c6-1ee8-4f19-9416-1d8d81428f59"<br />
rule_name = "Example Rule Name"<br />
updated_date = "2024-02-22"<br />
deprecation_date = "2025-01-01" # optional<br />
comments = "This is an example action list" # optional<br />
maturity = "beta" # optional<br />
<br />
[[actions]]<br />
action_type_id = ".email"<br />
group = "default"<br />
params.message = "Action triggered: Example Rule Name"<br />
id = "action_001" # optional<br />
frequency = { "throttle": "5m" } # optional<br />
<br />
[[actions]]<br />
action_type_id = ".slack"<br />
group = "default"<br />
params.message = "Some other notification"</td>
</tr>
</tbody>
</table>

3.  Manually create the exception lists (one TOML file per rule), based
    on the Exceptions schema.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>[metadata]<br />
creation_date = "2024-02-21"<br />
rule_id = "5d1e96c6-1ee8-4f19-9416-1d8d81428f59"<br />
rule_name = "Example Rule Name"<br />
updated_date = "2024-02-22"<br />
comments = "This is an example exception list."<br />
maturity = "development"<br />
<br />
[[exceptions]]<br />
description = "Example exception container"<br />
list_id = "exception_list_01"<br />
name = "Sample Exception List"<br />
namespace_type = "single"<br />
tags = ["tag1", "tag2"]<br />
type = "detection"<br />
<br />
[[exceptions.items]]<br />
description = "Exception item description"<br />
list_id = "item_list_01"<br />
name = "Exception Item Name"<br />
namespace_type = "single"<br />
tags = ["exception_item_tag1"]<br />
type = "simple"<br />
expire_time = "2024-12-31T23:59:59Z"<br />
<br />
[[exceptions.items.entries]]<br />
field = "process.name"<br />
type = "match_any"<br />
operator = "included"<br />
value = ["malicious_process", "another_process"]<br />
<br />
[[exceptions.items.entries]]<br />
field = "nested.field.example"<br />
type = "nested"<br />
operator = "included"</p>
<p>[[exceptions.items.entries.entries]]<br />
field = "nested.inside"<br />
type = "match"<br />
operator = "included"<br />
value = "specific_value"</p></td>
</tr>
</tbody>
</table>

4.  Validate the lists by running the unit tests

### Option 2: Manually Leverage Rule Schema Support

Another approach is to use the available action and exception fields in
the detection-rules rule data classes. These basic fields are complex
structures within Elastic Security. Therefore to take advantage of these
fields, the lists need to be populated with the exact format expected by
Elastic Security to successfully import.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Direct Insertion: Action and exception lists fields are available
to insert manually defined content</p></li>
<li><p>Context: Rule behavior is defined in the same file</p></li>
</ul></td>
<td><ul>
<li><p>Management Overhead: Managing exception lists and actions list
within a single rule may become duplicative and cumbersome to
manage</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Directly edit the detection rule's TOML file to include an exception
    list or action fields, following Elastic Security's expected format.

\<TODO add example when this is implemented\>

2.  Leverage built-in dataclasses to validate and format these fields
    correctly before deploying them to Elastic Security.

### Option 3: Custom List Management

Managing exception lists separately from the detection-rules framework
allows for bespoke logic and file structures, enabling a more tailored
approach to linking lists to rules.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Customization: Offers the ability to develop unique logic and
structures to manage exception and action lists, catering to specific
operational needs.</p></li>
</ul></td>
<td><ul>
<li><p>Complex Development: Crafting and maintaining custom logic for
list management can be intricate and time-consuming.</p></li>
</ul></td>
</tr>
</tbody>
</table>

##### 

##### Steps: 

1.  Create separate files or databases to store exception lists and
    response actions, structuring them according to your requirements.

2.  Develop custom scripts or applications that read from these storage
    mechanisms and apply the lists to rules via API calls to Elastic
    Security.

3.  Ensure that your custom logic includes mechanisms for linking
    exceptions and actions to the appropriate rules, potentially using
    rule tags or naming conventions for the association.

## Component 6: Unit testing

### Option 1: Built-in Test Configuration

Elastic Security's Detection as Code approach supports making unit tests
configurable by name, pattern, and rule validation by rule ID, using a
**test_config.yaml** file. This file allows teams to tailor their
testing approach, specifying which tests to run or bypass based on their
unique needs. It is especially useful for managing tests on prebuilt
rules where modifying the built-in **\_config.yaml** is not feasible.
The environment variable **DETECTION_RULES_TEST_CONFIG** can be set to
the path of your test configuration, or it can be referenced from within
the **\_config.yaml** for more integrated control.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Customizable: Flexibility to specify exactly which tests to run
or bypass, optimizing the testing process.</p></li>
<li><p>Avoids Merge Conflicts: Ability to manage tests without directly
altering the main configuration files, preserving the integrity of
prebuilt rules.</p></li>
</ul></td>
<td><ul>
<li><p>Learning Curve: Requires an understanding of the available tests
and their impact on rule validation.</p></li>
<li><p>Complex: Potential complexity in managing a separate
configuration for testing alongside the primary rule
configurations.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Define your testing preferences in a **test_config.yaml**. Specify
    tests to bypass or explicitly run using their full names or
    glob-style patterns.

**Opting in / out of tests**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><em># `bypass` and `test_only` are mutually exclusive and will cause
an error if both are specified.<br />
#<br />
# tests can be defined by their full name or using glob-style patterns
with the following notation<br />
# pattern:*rule*<br />
# the patterns are case sensitive<br />
<br />
unit_tests:<br />
# define tests to explicitly bypass, with all others being run<br />
#<br />
# to run all tests, set bypass to empty or leave this file commented
out<br />
bypass:<br />
# - tests.test_all_rules.TestRuleMetadata.test_event_dataset<br />
# - tests.test_all_rules.TestRuleMetadata.test_integration_tag<br />
# -
tests.test_gh_workflows.TestWorkflows.test_matrix_to_lock_version_defaults<br />
# - pattern:*rule*<br />
# - pattern:*kuery*<br />
<br />
# define tests to explicitly run, with all others being bypassed<br />
#<br />
# to bypass all tests, set test_only to empty<br />
test_only:<br />
# - tests.test_all_rules.TestRuleMetadata.test_event_dataset<br />
# - pattern:*rule*</em></td>
</tr>
</tbody>
</table>

**Opting out of rule validation**

\# \`bypass\` and \`test_only\` are mutually exclusive and will cause an
error if both are specified.  
\#  
\# both variables require a list of rule_ids  
rule_validation:  
  
bypass:  
\# - "34fde489-94b0-4500-a76f-b8a157cf9269"  
  
  
test_only:  
\# - "34fde489-94b0**-4500-a76f-b8a157cf9269"**

2.  Set the environment variable **DETECTION_RULES_TEST_CONFIG** to the
    path of your **test_config.yaml** or reference it within the
    **\_config.yaml**.

3.  Execute the tests using:

    1.  For default tests (all tests run): **make test** or **python -m
        detection_rules test**

    2.  For custom test configurations:
        **DETECTION_RULES_TEST_CONFIG=path/to/test_config.yaml make
        test**

    3.  For custom rules directory: **CUSTOM_RULES_DIR=custom-rules make
        test** with the **testing.config** set to
        **etc/test_config.yaml** in your **\_config.yaml**.

This approach streamlines the process of validating detection rules,
ensuring that only the most relevant tests are applied based on the
development and testing needs of your security team.

### Option 2: Custom Unit Testing

For teams requiring beyond the built-in testing framework, custom unit
testing allows for the creation of tailor-made tests. This flexibility
supports unique testing scenarios that might not be covered by the
default setup. Custom tests can focus on specific areas of concern, such
as regression testing, performance benchmarks, or testing against custom
data sets.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Targeted Testing: High degree of customization to target very
specific rule behaviors or scenarios.</p></li>
<li><p>User-Specific Workflows: Potential for deeper integration with
custom development workflows and CI/CD pipelines.</p></li>
</ul></td>
<td><ul>
<li><p>Expensive: Increased complexity in test creation and
maintenance.</p></li>
<li><p>Expertise Required: Requires additional expertise in both
detection engineering and software testing methodologies.</p></li>
<li><p>Merge Conflicts: Syncing with the upstream detection-rules unit
tests may cause merge conflicts if changes to the current test cases
exist</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Identify the specific areas of your detection rules that require
    custom testing.

2.  Develop custom test scripts or use existing testing frameworks that
    can interact with your detection rule sets.

3.  Integrate your custom tests into your CI/CD pipeline, ensuring they
    run automatically during your rule development lifecycle.

4.  Review and refine your custom tests regularly to ensure they remain
    effective and aligned with your evolving detection strategies.

Custom unit testing complements the built-in testing capabilities,
offering a path for security teams to apply their specific expertise and
requirements to the rule validation process, enhancing the overall
robustness of their detection capabilities.

##  

## Component 7: Rule Schema Validation

Efficient and accurate rule schema validation is critical in ensuring
that security detection rules are correctly formatted and compatible
with the Elastic Security application within Elastic Security. This
component explores various options for validating rule schemas, each
with its own set of advantages and considerations.

### Option 1: Built-in Dataclass Validation

One of the more prominent features of the detection-rules repo is the
extensive dataclass support mapping TOML rules to the Elastic Security
rule object format. This mapping ensures rules can properly import and
export into Elastic Security. Not all fields are supported on every
Elastic Security version and therefore some fields have minimum version
restrictions associated with the fields. Some fields upstream are
build-time fields and are abstracted away from the TOML rule objects for
convenience to rule authors. Along with query validation, dataclass
validation is a core component to manage rules following detections as
code principles. Essentially, every time a rule is loaded within the
detection-rules repo, marshmallow dataclass schema validation occurs on
the TOML files.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Built-in Validation: Utilizes dataclasses for rule format
validation, ensuring compatibility with Elastic Security.</p></li>
<li><p>Schema Support: Facilitates the import and export of rules
without external dependencies (e.g. persistent stack).</p></li>
<li><p>Feature Support: Continuously updated to include new Elastic
Security features.</p></li>
</ul></td>
<td><ul>
<li><p>Limited Field Support: Not all Elastic Security fields may be
fully supported or validated by the built-in dataclasses.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Create custom rules in TOML format, referencing existing rules as
    templates or using the **create** rule CLI command for guided rule
    creation.

\<Add example of create command if we want to keep this\>

|                                                              |
|--------------------------------------------------------------|
| python -m detection_rules create-rule /path/to/new_rule.toml |

2.  Validate the rule format with the **validate-rule** CLI command to
    ensure compliance.

|                                                                    |
|--------------------------------------------------------------------|
| **python -m detection_rules validate-rule /path/to/new_rule.toml** |

3.  Save the validated rule in the custom rules directory for
    deployment.

### Option 2: Elastic Security API Schema Validation

One option to validate custom rules is to check the Elastic API
response. This approach relies on external Elastic resources (e.g.
persistent Elastic stack,
[<u>ECP</u>](https://github.com/peasead/elastic-container)) to test
rules against. Validating custom rules against the Elastic Security API
allows for direct feedback from Elastic Security on rule compatibility.
This method is beneficial for testing rules against a live Elastic
Security environment before production deployment.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Flexibility: Offers an alternative validation method for rules
with minimal features or single rule types.</p></li>
<li><p>Language Agnostic: This can be implemented regardless of the
programming language used for rule development.</p></li>
</ul></td>
<td><ul>
<li><p>Dependency on Elastic Stack: Requires access to an Elastic
Security instance for validation.</p></li>
<li><p>Validation Time: Can be time-consuming to validate a large set of
rules individually.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Use the [<u>Detection Engine
    API</u>](https://www.elastic.co/guide/en/security/current/rule-api-overview.html)
    to import a rule and analyze the response for validation feedback.

2.  Check the API's response for any errors or issues with the rule
    definition.

### Option 3: Custom Schema Validation

The built-in schemas attempt to provide coverage for a broad set of rule
types and restrictions. Depending on the use case, the robust built-in
schema validation may not be required. For use cases requiring
validation against specific Elastic Security versions or minimal feature
sets, creating tailored schemas for rule validation might be more
appropriate. This allows for custom validation logic that fits unique
requirements.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Customization: Enables validation tailored to specific use cases
or Elastic Security versions.</p></li>
<li><p>Expanded Format Support: Allows for rule definitions in formats
beyond TOML, such as YAML or JSON.</p></li>
<li><p>Language Versatility: Validation can be implemented in various
programming languages.</p></li>
</ul></td>
<td><ul>
<li><p>Development Overhead: Requires the creation and maintenance of
custom validation logic.</p></li>
<li><p>Maintenance Challenges: Keeping validation logic updated with new
Elastic features can be demanding.</p></li>
</ul></td>
</tr>
</tbody>
</table>

##### Steps: 

1.  Develop custom schema validation logic tailored to the specific
    requirements of your ruleset and the features supported by your
    Elastic Security version.

2.  Apply the custom validation to your rule files, regardless of format
    (TOML, YAML, JSON, etc.), to ensure compatibility before deployment.

3.  Regularly update your validation logic to align with new features
    and changes in the Elastic Security application.

## Component 8: Detection Logic Validation

Ensuring the accuracy and effectiveness of detection logic is pivotal
for maintaining a robust security posture. Validation mechanisms can
vary in complexity, from built-in syntax checks to more sophisticated
semantic analyses and functional testing. Whether leveraging built-in
capabilities or developing custom solutions, the fundamental goal is to
validate detection logic thoroughly before deploying production rules.

### Option 1: Built-in Query Validation

The **detection-rules** repo offers built-in query validation mechanisms
that perform both syntax and semantic checks across various Elastic
query languages, including EQL (Event Query Language) and KQL (Kibana
Query Language). This validation process includes checking field schemas
against expected data sources and parsing queries into AST (Abstract
Syntax Tree) objects for thorough examination.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Comprehensive Checks: Validates queries against multiple schemas
and across different Elastic Security versions, ensuring broad
compatibility.</p></li>
<li><p>Integrated Testing: Facilitates rapid unit testing with built-in
validation tools, streamlining the development process.</p></li>
</ul></td>
<td><ul>
<li><p>Limited Language Support: Not all Elastic query languages, such
as Lucene syntax, are fully supported within the built-in validation
framework.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Query validation occurs each time the rule loads (e.g. unit tests,
    explicit CLI commands to **validate,** etc.). Run the unit tests:

|                                    |
|------------------------------------|
| **python -m detection_rules test** |

2.  Run the **validate-rule** CLIi command against a TOML rule. This
    example command checks the query syntax and field validity,
    providing feedback on any issues detected.

### Option 2: Custom Query Validation

For teams requiring specialized validation beyond what's provided out of
the box, developing a custom query validation mechanism offers the
flexibility to tailor checks to specific needs. For example, you may
want to validate queries based on semantic constraints defined by the
rule types ('query', 'saved_query', 'machine_learning', 'eql', 'esql',
'threshold', 'threat_match', 'new_terms') or only a subset of query
languages ("eql", "esql", "kuery", "lucene") based on the syntax. If the
latter is preferred, one approach would be to start with a grammar file
and build a parser using [<u>anlt4</u>](https://www.antlr.org).

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Customization: Allows for the development of validation logic
that precisely fits organizational requirements and rule
specifics.</p></li>
<li><p>Performance: Custom implementations can potentially validate
large rule sets more efficiently than the default methods provided by
the detection rules repository.</p></li>
</ul></td>
<td><ul>
<li><p>Maintenance Effort: Keeping custom validation logic up-to-date
with evolving rule types and query languages can be challenging and
resource-intensive.</p></li>
</ul></td>
</tr>
</tbody>
</table>

##### Steps: 

1.  Define the scope and level of your validation needs, such as
    specific query languages or rule types to focus on.

2.  Consider starting with a grammar file for the targeted query
    language(s) and developing a parser using tools like ANTLR4 to
    analyze query syntax and semantics.

3.  Integrate your custom validation logic into the rule development
    lifecycle, ensuring queries are checked before deployment.

#### Workflow 2

# Syncing Rules and Data from VCS to Elastic Security 

##  

## Overview

In [<u>Workflow 1</u>](#_r1srpur9t99c), we explored managing detection
rules through Version Control Systems (VCS) — encompassing validation,
unit testing, and managing exceptions among other practices. This
workflow progresses to detail the synchronization of detection rules
into Elastic Security. ***Syncing*** in this context signifies the
bidirectional movement of rules — either importing/exporting or
pushing/pulling — between VCS and Elastic Security. This section is
framed from the perspective of **pushing rules from VCS into Elastic
Security**, although the processes involved can facilitate movement in
either direction.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td><h2 id="section-39"><img src="media/image7.png"
style="width:1.99375in;height:4.98438in" /></h2></td>
</tr>
<tr class="even">
<td><em>Diagram X: Syncing with Elastic Security Through CI/CD API
Workflows</em></td>
</tr>
</tbody>
</table>

## Considerations

The workflows described here integrate concepts familiar to those versed
in Continuous Integration/Continuous Deployment (CI/CD) practices. While
examples may leverage [<u>GitHub
Actions</u>](https://github.com/features/actions) to illustrate these
processes, the methods outlined are intended as guides and should be
customized to suit specific operational requirements. It's important to
note that independent of the CI/CD strategy employed, interaction with
Elastic's APIs remains a fundamental aspect of the rule synchronization
process. In general, here are the end to end steps:

1.  Rule Preparation and Validation: Begin by ensuring all rules within
    the VCS repository have passed validation and unit tests. This step
    is crucial to avoid syncing rules that could potentially cause
    issues within Elastic Security.

2.  CI/CD Configuration: Configure your CI/CD pipeline (e.g., GitHub
    Actions) to automate the rule syncing process. This setup should
    include steps for rule validation, packaging, and deployment
    triggers based on specified conditions (e.g., merges into a main
    branch).

3.  API Integration for Rule Syncing: Utilize Elastic Security's
    Detection Engine API to push rules from VCS to Elastic Security.
    This involves crafting API requests to create or update rules in
    Elastic Security based on the rule definitions stored in VCS. Ensure
    API keys or authentication credentials are securely managed within
    the CI/CD pipeline to facilitate secure API interactions.

4.  Exception and Action List Management: If your rules utilize
    exception lists or response actions, ensure these dependencies are
    also managed and synced appropriately. This may involve additional
    API calls to manage these entities in Elastic Security.

5.  Monitoring and Feedback: Implement monitoring within the CI/CD
    pipeline to capture the status of rule deployments. This could
    include notifications on success or failure, enabling quick response
    to deployment issues. Consider feedback loops where deployment
    outcomes can inform rule development practices, potentially
    triggering additional tests or validations before future
    deployments.

6.  Documentation and Change Management: Maintain documentation of the
    CI/CD process, including any custom scripts or configurations used
    for rule syncing. This ensures transparency and facilitates
    onboarding for new team members. Implement change management
    practices to track modifications to the rule set, including version
    control of rule definitions and a log of deployments to Elastic
    Security.

##  

## Component 1: Deploying via Direct HTTP Request API Calls

### Option 1: Elastic Security APIs

The [<u>Elastic Security
APIs</u>](https://www.elastic.co/guide/en/security/current/security-apis.html)
offer comprehensive endpoints for managing Create, Read, Update, Delete
(CRUD) operations within Elastic Security through REST API calls. These
APIs can be utilized directly, bypassing the CLI options provided by the
detection-rules repository. The CLI aids in simplifying connection
setup, wrapping specific endpoints to integrate core Detection as Code
(DAC) components like validation, field transformation, and conversion
to JSON.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Comprehensive CRUD Support: Directly supported standard APIs for
managing security rules.</p></li>
<li><p>CLI Integration: Simplifies rule syncing for CI/CD workflows,
providing a bridge for direct API interaction.</p></li>
</ul></td>
<td><ul>
<li><p>Limited API Coverage: Not all Elastic Security APIs are
integrated within the detection-rules repo, which might be necessary for
advanced DAC workflows (e.g., timelines, cases)</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  **Authenticate** using one of the several Elastic API
    [<u>authentication</u>](https://www.elastic.co/guide/en/security/current/rule-api-overview.html#_authentication_2)
    options available or using one of the detection-rules options.

CLI commands which are tied to Elastic Security and Elasticsearch are
capable of parsing auth-related keyword args from a config file or
environment variables. If a value is set in multiple places, such as
config file and environment variable, the order of precedence will be as
follows:

1.  explicitly passed args (such as \`--user joe\`)

2.  environment variables

3.  config values

4.  prompt (this only applies to certain values)

In the root directory of this repo, create the file
**.detection-rules-cfg.json** and add relevant values, where kibana
credentials provide access to the Elastic Security stack.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>{<br />
"elasticsearch_url": "http://localhost:9200",<br />
"kibana_url": "http://localhost:5601",<br />
"cloud_id": "",<br />
"es_username": "elastic",<br />
"es_password": "password",<br />
"kibana_username": "elastic",<br />
"kibana_password": "password"<br />
}</td>
</tr>
</tbody>
</table>

Currently supported arguments:

elasticsearch_url  
kibana_url  
cloud_id  
\*\_username (kibana **and** es)  
\*\_password (kibana **and** es)

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td>elasticsearch_url<br />
kibana_url<br />
cloud_id<br />
*_username (kibana <strong>and</strong> es)<br />
*_password (kibana <strong>and</strong> es)</td>
</tr>
</tbody>
</table>

2.  Configure your [<u>custom rules
    directory</u>](#option-1-fork-and-map-rules)

3.  Once authenticated, you can use [<u>API
    calls</u>](https://www.elastic.co/guide/en/security/current/security-apis.html#_api_calls)
    directly. If using the detection-rules CLI, the following DAC
    commands are available. **Deploy** the latest set of rules by
    running the following command:

\<TODO\> Fill in with example execution when commands are available.

4.  Step to sync / deploy exception/action lists with the the specific
    DAC command

### Option 2: Fleet Engine

[<u>Fleet</u>](https://www.elastic.co/guide/en/fleet/current/fleet-overview.html)
offers an alternative method primarily used within Elastic to deploy
prebuilt rules. This involves building a Fleet-compatible package
containing the rules and pushing it using one of the Fleet developer
APIs
([**<u>http://my-kibana:5601/api/fleet/epm/packages</u>**](http://my-kibana:5601/api/fleet/epm/packages)).
An end-to-end approach might consider building a package, testing the
ruleset against an Elastic test stack, and parsing the HTTP response for
errors. This approach, while consistent with Elastic's prebuilt rules
workflow, is not directly recommended for external workflows as it may
bypass critical rule versioning processes in Elastic Security.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Consistent Packaging: Bundles rule packages efficiently, updating
Kibana <a
href="https://www.elastic.co/guide/en/kibana/current/managing-saved-objects.html"><u>saved
objects</u></a> to align with the prebuilt rules workflow.</p></li>
</ul></td>
<td><ul>
<li><p>Workflow Disruption: Potentially overrides existing rule
management workflows in Elastic.</p></li>
<li><p>Complex Package Creation: Requires understanding and adherence to
specific package specifications.</p></li>
<li><p>Management Complexity: Can be cumbersome to maintain rules via
Fleet packages.</p></li>
</ul></td>
</tr>
</tbody>
</table>

|                                                                         |
|-------------------------------------------------------------------------|
| <img src="media/image8.png" style="width:3.77056in;height:5.19271in" /> |
| *E2E build fleet rules package workflow*                                |

##### Steps: 

1.  **Build** a rules package using the
    [<u>elastic-package</u>](https://github.com/elastic/elastic-package)
    go binary. Alternatively, use the CLI to build a package using the
    rules repo. This command alone builds everything in the **releases**
    directory needed except for the manifest update that happens usually
    in a separate internal workflow.

|                                                 |
|-------------------------------------------------|
| **python -m detection_rules dev build-release** |

2.  Update the versions to match the latest package version within the
    **manifest.yml** build if needed.

3.  Check formatting by running **elastic-package format**, then
    **elastic-package lint** against the package built.

4.  If using the detection-rules CLI, compress the package into a zip
    file of the **fleet** folder with the **security_detection_engine**
    prefix (e.g. security_detection_engine-8.13.0-beta.0.zip) to include
    the **manifest.yml** and **changelog.yml** files.

5.  Deploy the package to the Elastic Security environment using the
    API.

|                                                                                                                                                                                                     |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| curl -XPOST -H 'content-type: application/zip' -H 'kbn-xsrf: true' https://my-kibana:9243/api/fleet/epm/packages -u elastic:\<password\> --data-binary @security_detection_engine-8.13.0-beta.0.zip |

6.  Step to sync / deploy exception/action lists with the the specific
    DAC command

##  

## Component 2: Deploying via CI/CD

CI/CD (Continuous Integration/Continuous Deployment) can be a powerful
mechanism for managing detections as code, ensuring that security
detections are consistently integrated, tested, and deployed in an
automated and efficient manner. Leveraging CI/CD not only streamlines
the update and deployment processes but also helps in maintaining the
reliability and effectiveness of detection rules over time. Note: All of
the reference examples are built on [<u>GitHub
action</u>](https://github.com/features/actions) features that should be
adapted to fit your version control product if possible.

In practical application, a combination of these deployment options can
offer enhanced flexibility and control over the rule management process.
For instance, leveraging "Push on Merge" for continuous integration of
reviewed changes, "Manual Dispatch Push" for critical updates or
exceptions, and "Cron Scheduling Push" for regular, non-urgent updates
can provide a comprehensive CI/CD strategy that accommodates various
operational needs and urgency levels. This blended approach allows teams
to balance automation efficiency with the necessity for manual oversight
in certain scenarios.

### Option 1: Push on Merge

Automatically deploying rule changes upon merging into the main branch
is a common strategy that emphasizes the deployment of only reviewed and
approved modifications. It helps to streamline deployment and aggregate
several DAC rule management commands into a unified test and release
process.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Automated Deployment: Ensures changes are automatically deployed
upon merge, reducing manual intervention and potential human
error.</p></li>
<li><p>Change Tracking: Keeps a history of changes, allowing for
rollbacks and audits of detection rule modifications.</p></li>
</ul></td>
<td><ul>
<li><p>Dependent on Review Quality: Effectiveness is heavily reliant on
the rigor of the code review process. Poorly reviewed changes can still
be deployed.</p></li>
<li><p>Merge Conflicts: Without careful management, merges can lead to
conflicts or overwrite useful changes, especially in fast-moving
environments</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Configure a CI/CD pipeline workflow following [<u>GitHub actions
    documentation</u>](https://docs.github.com/en/actions) in your
    version control system to trigger on merge events to the main
    branch. Add the DAC CLI commands to ensure rules are properly
    published.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td># Example GitHub Action Workflow<br />
on:<br />
push:<br />
branches:<br />
- main<br />
jobs:<br />
deploy_rules:<br />
runs-on: ubuntu-latest<br />
steps:<br />
- name: Checkout code<br />
uses: actions/checkout@v2<br />
- name: Deploy Rules<br />
run: |<br />
echo "Deploying rules..."<br />
# Add DAC CLI commands here</td>
</tr>
</tbody>
</table>

\<Update workflow\>

2.  Set the environment variables to the Elastic Security environment as
    [<u>GitHub
    secrets</u>](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
    so your pipeline can deploy to the proper environment.

3.  Upon merge, the pipeline automatically deploys the changes to the
    relevant detection systems.

4.  Monitor deployments closely to adjust schedules as needed based on
    operational feedback and ensure rules are successfully deployed to
    production.

### Option 2: Manual Dispatch Push

This method involves manually triggering the deployment of detection
rules through the CI/CD pipeline using the GitHub Dispatch
configuration. It provides control over when changes are deployed and
essentially allows you to deploy production rules on demand.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Deployment Control: Gives teams the ability to choose optimal
deployment times, reducing the risk of introducing issues during
critical periods.</p></li>
<li><p>Review Opportunity: Allows for a final review or confirmation
step before deployment, potentially catching issues missed during
initial reviews.</p></li>
</ul></td>
<td><ul>
<li><p>Process Delays: Can slow down the deployment process and
introduce delays in getting new or updated detections live.</p></li>
<li><p>Manual Error Risk: Manual processes are susceptible to human
error, including forgetting to trigger deployments or mismanaging
deployment sequences.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Configure a manual [<u>GitHub
    Dispatch</u>](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow)
    trigger in the rules for production deployment.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td># Example GitHub Action Manual Trigger<br />
on:<br />
workflow_dispatch:</td>
</tr>
</tbody>
</table>

2.  Team members responsible for deployment review the final changes and
    manually initiate the deployment process when ready.

3.  Monitor deployments closely to adjust schedules as needed based on
    operational feedback and ensure rules are successfully deployed to
    production.

### Option 3: Cron Scheduling Push

This workflow is similar to other GitHub action workflows. However,
deployments are scheduled to occur at specific times, using a cron job
in the CI/CD pipeline. This approach can balance between immediate
deployments and fully manual control.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Scheduled Deployments: Allows teams to plan around known
deployment times, potentially reducing the impact on operational
activities.</p></li>
<li><p>Batched Changes: Changes can be batched and deployed together,
reducing the frequency of deployments and associated risks.</p></li>
</ul></td>
<td><ul>
<li><p>Misaligned Release Timing: Scheduled deployments may not align
with the urgency of some changes, delaying critical updates.</p></li>
<li><p>Potential Disruptions: If deployments are scheduled during peak
times, they could introduce performance issues or downtime.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Configure a cron job [<u>GitHub
    schedule</u>](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule)
    within your CI/CD pipeline to trigger deployments at specific
    intervals (e.g., nightly or weekly).

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr class="odd">
<td># Example GitHub Action Cron Job<br />
on:<br />
schedule:<br />
- cron: '0 2 * * *' # Runs at 2 AM UTC daily</td>
</tr>
</tbody>
</table>

2.  Bundle changes merged into the main branch for deployment at the
    next scheduled interval.

3.  Monitor deployments closely to adjust schedules as needed based on
    operational feedback and ensure rules are successfully deployed to
    production.

## Component 3: Pre-Production Testing

Before deploying detection rules to the production environment, it's
crucial to perform comprehensive end-to-end testing. This process
involves validating the efficacy and accuracy of detection rules in a
controlled environment that closely mimics production. The goal is to
ensure that all rules behave as expected, generating alerts for known
threats without producing excessive false positives or negatives.
Prerequisites include a dedicated testing environment or dev stack,
access to a suite of emulation tools or test scripts that mimic
malicious activities, and a clear understanding of the expected outcomes
for each detection rule.

Testing can occur at two critical junctures: before opening a Pull
Request (PR), where individual detection engineers test rules in a
development or dedicated test stack, and as a consolidated package of
rules merged into the main branch. The latter involves deploying rules
via CI to a test stack, where comprehensive emulation of threat
scenarios verifies rule efficacy. If tests confirm rules perform
correctly—both in generating alerts and accurately querying against
generated events—a subsequent CI job or manual dispatch promotes the
rules to the production environment.

Testing frameworks play a crucial role in simulating real-world threats
to validate rule effectiveness. These frameworks, such as Red Team
Automation (RTA) scripts or third-party platforms like Atomic Red Team,
provide a structured way to generate synthetic events that mimic attack
behaviors. Such rigorous testing ensures that rules not only trigger
alerts as expected but also accurately match query criteria against the
events generated in Elasticsearch, providing a nuanced measure of rule
performance beyond mere alert firing.

Note: The complexity of testing may vary based on the specific nature of
the detection rules and the available testing infrastructure.
Considerations include the scalability of the testing environment, the
representativeness of the test data, and the ability to automate the
testing process. Automated CI/CD pipelines can facilitate the deployment
of rules to the testing environment, execution of test scenarios, and
collection of results for analysis.

### Option 1 - Pre-PR Individual Rule Testing

Individual rule testing occurs before opening a pull request (PR).
Detection engineers deploy their rules to a development Elastic Security
environment where they manually trigger conditions expected to generate
alerts. This hands-on approach allows for immediate feedback and
adjustments. This process may include utilizing test frameworks to
emulate threat behaviors and manually reviewing Elasticsearch events to
ensure rule queries match expected outputs.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Early identification and resolution of issues.</p></li>
<li><p>Direct control over the testing process and immediate
feedback.</p></li>
</ul></td>
<td><ul>
<li><p>Time-consuming if done manually for a large set of
rules.</p></li>
<li><p>May not cover complex attack scenarios that involve multiple
rules or dependencies.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

### Steps: 

1.  Deploy the new or modified rule to a dev testing environment.

2.  Run emulation scripts or manual tests to generate the conditions
    expected to trigger the rule or Elasticsearch events to confirm they
    align with the rule's query criteria.

3.  Analyze the alerts generated and adjust the rule as necessary.

### Option 2 - Post-merge Package Testing

After rules are merged into the main branch, a tester or automated CI
job deploys the rule package to a test stack. Comprehensive testing,
including the execution of attack emulations, is conducted to verify
that all rules generate alerts as expected. If successful, another CI
job or manual GitHub dispatch promotes the rules to production.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Validates the entire rule set in a scenario that mimics
real-world conditions.</p></li>
<li><p>Leverages automation for efficient and repeatable
testing.</p></li>
</ul></td>
<td><ul>
<li><p>Requires a robust CI/CD setup and test environment.</p></li>
<li><p>Potentially delays deployment if issues are found late in the
process.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

### Steps: 

1.  Aggregate rules from the main branch into a deployable package.

2.  Use CI to deploy the rule package to a dedicated test stack.

3.  Run a comprehensive set of emulated threat scenarios against the
    test stack.

4.  Analyze test results to confirm rules trigger alerts as expected and
    accurately query against generated events.

5.  Upon successful testing, use CI or a manual dispatch to promote the
    rules to the production environment.

#### Workflow 3

# Managing Detection Rules in Elastic Security 

##  

## Overview

This section explores the intricacies of managing detection rules within
Elastic Security as an integral component of Detection as Code (DaC). It
examines different approaches to rule management, focusing on scenarios
where the source of truth for rules is maintained within a Version
Control System (VCS), directly in Elastic Security, or through a
synchronized method that leverages both platforms. It highlights why
security analysts might opt for rule management in Elastic Security over
VCS, even when the latest rule versions are stored externally.
<img src="media/image9.png" style="width:6.69in;height:4.11111in" />

<img src="media/image10.png" style="width:4.67063in;height:3.19663in" />

**Elastic Security Rules Management UI**

## Considerations

Effective r[<u>ule management in Elastic
Security</u>](https://www.elastic.co/guide/en/security/current/rules-ui-management.html)
requires understanding the available security features, workflows, and
how they compare to managing rules in VCS. It's important to evaluate
the benefits of immediate rule deployment and testing in Elastic
Security against the structured version control and collaboration
offered by VCS. The ability to quickly deploy and test rules within
Elastic Security is balanced against the structured version control,
collaboration, and historical tracking provided by VCS systems. This
balance is crucial for teams that, while primarily operating within
Elastic Security for daily tasks, require robust external processes for
rule oversight and version management. Note: More detail on syncing is
provided in the [<u>Workflow 2</u>](#workflow-2) and [<u>Workflow
4</u>](#workflow-4).

## Component 1: Source of Truth

### Option 1: VCS as the Source of Truth

In this option, rules, exception/action lists are managed and edited
within Elastic Security, but the latest version or "source of truth" of
these rules is stored and versioned in a VCS repository. This approach
allows for detailed change tracking, collaboration, and rule versioning
outside of Elastic Security.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Version Control: Leverages VCS capabilities for detailed tracking
of rule changes and history.</p></li>
<li><p>Collaboration: Facilitates collaborative rule development and
peer review processes.</p></li>
</ul></td>
<td><ul>
<li><p>Syncing Complexity: Requires mechanisms to regularly sync rule
changes from Elastic Security to VCS.</p></li>
<li><p>Potential for Drift: Risk of rule versions drifting between
Elastic Security and VCS without strict syncing protocols.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Develop rules directly in Elastic Security for immediate testing and
    deployment.

2.  Use Elastic API calls, CICD workflows, or manual processes to export
    updated rules from Elastic Security to the VCS repository.

3.  Implement regular synchronization checks to ensure the VCS
    repository accurately reflects the latest rule versions from Elastic
    Security.

### Option 2: Elastic Security as the Source of Truth

In this approach, rules are created, managed, and stored directly in
Elastic Security, with Elastic Security acting as the primary source of
truth. This approach emphasizes the use of Elastic Security's UI and
built-in features for rule management, relying on Elastic Security's
versioning and export capabilities for rule backups and audits.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Immediate Deployment: Enables quick rule testing, tuning, and
deployment directly within the security platform.</p></li>
<li><p>Integrated Management: Utilizes Elastic Security's UI for rule
creation, modification, and management without the need for external
tools.</p></li>
</ul></td>
<td><ul>
<li><p>Limited Version Control: Does not offer the same level of
detailed version history and rollback capabilities as VCS.</p></li>
<li><p>Collaboration Challenges: May limit collaborative development and
peer review processes available in VCS platforms.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Utilize Elastic Security's Security Solution interface to create and
    manage detection rules.

2.  Since Elastic Security is the source of truth, it may be wise to
    regularly export rules for backup purposes and store them in a
    secure location.

### Option 3: Hybrid Rule Management

This hybrid approach ensures rules are managed in Elastic Security with
capabilities to sync changes both to and from VCS. It combines the
immediate operational advantages of Elastic Security with the structured
version control and collaboration benefits of VCS.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Best of Both Worlds: Combines the operational efficiency of
Elastic Security with the version control strengths of VCS.</p></li>
<li><p>Flexible Workflow: Supports diverse team workflows, allowing for
both immediate rule deployment and detailed change tracking.</p></li>
</ul></td>
<td><ul>
<li><p>Syncing Overhead: Requires additional tools or processes to
maintain rule synchronization between Elastic Security and VCS.</p></li>
<li><p>Complex Configuration: May necessitate complex setup to automate
the syncing process effectively.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Establish rules and workflows for when and how rules are synced
    between Elastic Security and VCS.

2.  Implement automation tools or scripts to facilitate the regular
    syncing of rule definitions and updates in both directions.

3.  Monitor the syncing process closely to identify and resolve any
    discrepancies or conflicts that arise between the two platforms.

#### Workflow 4

# Syncing Rules and Data from Elastic Security to VCS 

##  

## Overview

This workflow focuses on the process of transferring detection rules
from Elastic Security to a VCS, leveraging external tools and workflows.
Currently, direct integrations to automate this process from Elastic
Security's end are limited, requiring custom solutions to ensure that
detection rules are consistently and accurately backed up in VCS. Some
of these approaches overlap with syncing options discussed in
[<u>Workflow 2</u>](#workflow-2).

<img src="media/image11.png" style="width:5.74479in;height:5.01103in" />

*Diagram X: Direct REST API Sync: From Local Repo to Elastic Security w/
Optional VCS Preservation*

Alternatively to using **git** workflows, the available REST API calls
can be used directly to manage syncing rules. In this workflow, VCS can
still be used, however the rule path traverses from the local repo
directly to Elastic Security, and then optionally preserved in VCS.

## Considerations

When planning to sync rules from Elastic Security to VCS, it's important
to consider the frequency of updates, the potential for rule
tuning/updates, and the need for historical tracking of rule changes.

## Component 1: Exporting Rules via Direct HTTP Request API Calls

### Option 1: Detection Rules Engine

Utilizing the Detection Rules API, security teams can export detection
rules from Elastic Security for storage in a VCS. This method requires
familiarity with Elastic's API and scripting capabilities to automate
the export process. This approach is similar to syncing rules from VCS
to Elastic Security, only rules are pulled from Elastic Security and
stored. Elastic Security provides UI options to export rules, however
the API may provide a more automated opportunity to sync rules. Prior to
opening a PR for review, the rules should be broken up into separate
files to ease the rule review process.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Automation Potential: Can be automated through scripting to
regularly update rule sets in VCS.</p></li>
<li><p>Rule Format: Able to store the rule in any format suitable for
the team (e.g. JSON, YAML, etc.)</p></li>
</ul></td>
<td><ul>
<li><p>Complex Setup: Requires initial configuration and understanding
of Elastic's API.</p></li>
<li><p>Manual Intervention: Without full automation, manual steps may be
necessary to initiate exports.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Authenticate with the Elastic Security environment using API keys or
    user credentials.

2.  Utilize the Detection Engine API to export the desired detection
    rules.

3.  Format the exported data as needed and commit to the VCS repository.

## 

##  

## Component 2: Exporting Rules via CI/CD

### Option 1: Manual Dispatch Pull

This approach involves creating a CI/CD workflow, such as a GitHub
Action, that manually triggers the export of detection rules from
Elastic Security into VCS, potentially opening a pull request (PR) for
review. Robust workflows are able to export rules, transform to a rule
spec, and also integration with GitHub APIs to facilitate reviews.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Controlled Updates: Provides the ability to trigger exports at
desired times, offering control over when updates are made.</p></li>
<li><p>Review Process: Can automatically generate PRs for team review
before merging rule changes.</p></li>
</ul></td>
<td><ul>
<li><p>Manual Triggers: Requires an action to initiate the export,
adding a step to the workflow.</p></li>
<li><p>Integration Complexity: Setup of CI/CD workflows can be
intricate, depending on the complexity of the rule sets.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Configure a GitHub Action or other CI/CD workflow with a manual
    dispatch trigger for rule exports.

2.  Script the API calls to Elastic Security for rule extraction.

\<\>

3.  Format and commit the extracted rules into VCS, optionally creating
    a PR for review.

\<\>

### Option 2: Cron Scheduling Pull

Setting up a CI/CD workflow that periodically triggers, such as through
a cron job, automates the process of pulling updates from Elastic
Security into VCS and opening PRs for review.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Regular Updates: Ensures that rule updates are regularly pulled
into VCS without manual intervention.</p></li>
<li><p>Automated Process: Streamlines the update process, making rule
management more efficient.</p></li>
</ul></td>
<td><ul>
<li><p>Setup Complexity: Requires initial setup of cron jobs and
scripting for automation.</p></li>
<li><p>Potential for Overlooked Changes: Automated processes may pull in
changes that have not been thoroughly reviewed in Elastic
Security.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Establish a cron job within your CI/CD platform to periodically
    trigger rule exports.

2.  Automate the extraction and formatting of rules from Elastic
    Security using the Detection Engine API.

3.  Commit the updated rules to VCS and optionally open PRs for team
    review.

#### Workflow 5

# Duel Syncing Rules and Data between Kibana and VCS 

##  

## Overview

Dual syncing between Elastic Security and a Version Control System (VCS)
embodies the preferred method for managing detection rules, merging the
operational efficiency of Elastic Security with the structured control
of VCS. This advanced strategy ensures that any rule modifications in
Elastic Security are accurately reflected in VCS and vice versa,
supporting a cohesive rule management ecosystem. It allows analysts to
use the intuitive Elastic Security UI and detection engineers to
leverage VCS for rule development, with critical syncing occurring
ideally within Continuous Integration (CI) workflows. This approach is
designed to abstract the complexities of rule versioning and syncing
from detection engineers, ensuring that they can focus on rule quality
and effectiveness.

## 

|                                                                          |
|--------------------------------------------------------------------------|
| <img src="media/image12.png" style="width:6.53125in;height:5.69444in" /> |
| *Figure x: Combining Multiple Options to Duel Sync Rules*                |

## Considerations

Implementing a dual syncing strategy requires careful planning and
consideration of several factors to ensure its success. A successful
dual syncing strategy involves integrating automation tools to handle
the bidirectional flow of rule data. It requires a robust setup that can
automatically compare rules by version or hash, ensuring that updates
made within Elastic Security are synchronized back into VCS before final
deployment. Utilizing the version lock file from the detection rules
repository, or a bespoke versioning system, plays a pivotal role in
managing this synchronization process seamlessly. Additionally,
team-specific processes may be required to ensure production rules
remain in sync.

## Consideration 1: Syncing Processes

### Option 1: Automation Tools for Syncing

Leveraging automation tools and CI/CD pipelines facilitates a constant
sync between Elastic Security and VCS. This setup should include
processes for pulling rules from Elastic Security, comparing them
against the VCS repository (either by version or hash), and ensuring
updates are committed back into VCS. This ensures that both Elastic
Security and VCS maintain the latest rule versions, abstracting this
process from detection engineers.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Minimizes manual intervention, automating rule
synchronization.</p></li>
<li><p>Supports collaborative rule development with integrated version
control.</p></li>
</ul></td>
<td><ul>
<li><p>Requires significant initial setup and maintenance of automation
scripts and CI/CD pipelines.</p></li>
<li><p>Potential for configuration complexities and the need to handle
merge conflicts or discrepancies between the two environments.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Set up CI/CD pipelines with triggers for rule changes in both
    Elastic Security and VCS.

2.  Utilize Elastic Security's Detection Engine APIs for rule exports
    and imports.

3.  Configure scripts to handle rule format conversions, versioning, and
    conflict resolution between syncing.

4.  Regularly monitor and test the synchronization process to identify
    and rectify any versioning issues.

### Option 2: Pre-Merge Sync

Incorporate a sync check step within the PR workflow, running an
automated job to compare rule versions or hashes between Elastic
Security and VCS. This step aims to identify and resolve any
discrepancies before merging changes into the main branch or deploying
rules to production. It serves as a final check to ensure all rules are
correctly synchronized and ready for deployment.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Pros</td>
<td>Cons</td>
</tr>
<tr class="even">
<td><ul>
<li><p>Ensures a final validation of rule synchronization, enhancing
rule integrity before production deployment.</p></li>
<li><p>Streamlines the resolution of any version conflicts or
discrepancies, maintaining rule consistency.</p></li>
</ul></td>
<td><ul>
<li><p>Introduces potential delays in rule synchronization due to the
manual review process.</p></li>
<li><p>Requires clear documentation and understanding of the sync
process among team members.</p></li>
</ul></td>
</tr>
</tbody>
</table>

## 

##### Steps: 

1.  Integrate a sync check script into the CI/CD pipeline, triggered as
    part of the PR process.

2.  The script compares rule data between Elastic Security and VCS,
    highlighting discrepancies for resolution.

3.  Resolve any conflicts or discrepancies identified by the sync check
    before merging the PR.

4.  Upon successful sync check completion, proceed with merging the PR
    and deploying the rules to production.

###  

#### Quickstart

# E2E Reference Example

#### 

#### 

##  Example Rule Lifecycle

If you’re starting from scratch and would like to get quickly started,
here are step by step instructions. These steps assume you are familiar
with the considerations associated with the various steps and elect to
follow the detection-rules VCS approach to manage rules. It also assumes
advanced configurations are not applied.

##### Steps: 

1.  Fork the detection-rules repo

2.  Configure Elastic Security authentication

3.  Install the python requirements

4.  Initialize the custom rules directory (optionally configure schema
    and versioning files)

5.  Configure unit tests (optionally bypass specific test cases)

6.  Create a detection rule (optionally create exception and action
    list)

7.  Validate the rules (optionally test queries in development
    environment)

8.  Open pull request to store in VCS and peer review

9.  Push merged rules to production environment (optionally version lock
    and sync via CICD)

#### Final Remarks

# Feedback and Resources

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

#### 

## Feedback

We’re always interested in hearing from the community and our partners!
If you have use cases, workflows, or any feedback related to Detection
as Code (DaC), we encourage you to share them with us. Your input is
crucial for continuous improvement and fostering a collaborative
environment. Please reach out through the following channels:

- **[<u>GitHub
  Issues</u>](https://github.com/elastic/detection-rules/issues):** For
  technical issues, enhancements, or feature requests.

- **[<u>Community Slack</u>](http://ela.st/slack):** Join our
  conversations for real-time discussions.

- **[<u>Discuss
  Forums</u>](https://discuss.elastic.co/c/security/endpoint-security/80):**
  Post your questions, share insights, and engage with the Elastic
  community.

## Updates and Next Steps

<img src="media/image13.png" style="width:5.47917in;height:3.75in" />

As the cybersecurity landscape evolves, so does the need for adaptable
and robust security practices. Detection as Code is a dynamic approach,
continuously refined through community feedback, technological
advancements, and the ever-changing threat environment. We're committed
to updating this documentation with the latest methodologies, tools, and
best practices as we adopt them. Stay tuned for updates, and consider
contributing to the development of these workflows.

## Resources

- [<u>Detection as Code: Detection Development Using
  CI/CD</u>](https://www.jedi.be/blog/2022/02/23/trends-and-inventory-of-50-as-code-concepts/?ref=hackernoon.com)

- [<u>Detection as Code
  Explained</u>](https://panther.com/cyber-explained/detections-as-code/)

- [<u>Detection as Code on Splunk
  Blog</u>](https://www.splunk.com/en_us/blog/learn/detection-as-code.html)

- [<u>Building a Detection as Code
  Pipeline</u>](https://medium.com/threatpunter/from-soup-to-nuts-building-a-detection-as-code-pipeline-59b96c875ca1)

- [<u>Can We Have Detection as
  Code?</u>](https://medium.com/anton-on-security/can-we-have-detection-as-code-96f869cfdc79)

- [<u>The GitHubification of
  InfoSec</u>](https://medium.com/@johnlatwc/the-githubification-of-infosec-afbdbfaad1d1)

- [<u>The Detection as Code (DaC) Approach to
  Cybersecurity</u>](https://web.archive.org/web/20221129120955/https://softtek.eu/en/tech-magazine-en/cybersecurity-en/the-detection-as-code-dac-approach-to-cybersecurity/)

- [<u>Automating Detection as
  Code</u>](https://www.tines.com/blog/automating-detection-as-code)

- [<u>Getting Started with Detection as Code and Chronicle
  Security</u>](https://www.googlecloudcommunity.com/gc/Community-Blog/Getting-Started-with-Detection-as-Code-and-Chronicle-Security/ba-p/702154?nobounce)

- [<u>What is Everything as
  Code</u>](https://octopus.com/blog/what-is-everything-as-code)

- [<u>Infrastructure as Code on
  Wikipedia</u>](https://en.wikipedia.org/wiki/Infrastructure_as_code)

- [<u>Everything as Code
  Explained</u>](https://hackernoon.com/everything-as-code-explained-0ibg32a3)

- [<u>What is Infrastructure as Code
  (IaC)?</u>](https://www.redhat.com/en/topics/automation/what-is-infrastructure-as-code-iac)

- [<u>Infrastructure as Code Overview by
  Microsoft</u>](https://learn.microsoft.com/en-us/devops/deliver/what-is-infrastructure-as-code)

- [<u>6 Lessons on Everything as
  Code</u>](https://techbeacon.com/enterprise-it/6-lessons-everything-code-shift)

- [<u>Infrastructure as Code (IaC) Guide by
  Turing</u>](https://www.turing.com/blog/infrastructure-as-code-iac-guide/)

- [<u>Infrastructure as Code 101 by
  env0</u>](https://www.env0.com/blog/infrastructure-as-code-101)

These resources provide a comprehensive overview of the DaC concept, its
applications, and broader discussions around 'as Code' practices in the
tech industry. Whether you're a beginner or an experienced practitioner,
these readings offer valuable insights into modern rule management and
DAC practices.
