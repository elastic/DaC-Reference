# E2E Reference
================================

## Quick Start Slides

Check out the [Quick Start Slides](./_static/DaC_Rolling_your_own_Detections_as_Code.pdf) to an overview of DaC and how to get started. It provides some example CLI commands using the detection-rules repo.

⚠️ Note: The alpha detection-rules branch, content within these slides, and this reference guide are subject to change. Once we finally migrate the changes to the `main` branch, we will update the content accordingly.

## Example Rule Lifecycle

If you’re starting from scratch and would like to get quickly started, here are the high-level instructions. These steps assume you are familiar with the considerations associated with the various steps and elect to follow the detection-rules VCS approach to manage rules. It also assumes advanced configurations are not applied.

**Steps:**

### Locally
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
1. Setup branch protections and code owners
1. Setup environment secrets and variables that will be used in CICD
1. Setup CICD workflows to validate and sync rules