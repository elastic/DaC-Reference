# E2E Reference Example
================================

## Example Rule Lifecycle

If you’re starting from scratch and would like to get quickly started, here are step by step instructions. These steps assume you are familiar with the considerations associated with the various steps and elect to follow the detection-rules VCS approach to manage rules. It also assumes advanced configurations are not applied.

**Steps:**

1. Fork the detection-rules repo
1. Configure Elastic Security authentication
1. Install the python requirements
1. Initialize the custom rules directory (optionally configure schema and versioning files)
1. Configure unit tests (optionally bypass specific test cases)
1. Create a detection rule (optionally create exception and action list)
1. Validate the rules (optionally test queries in development environment)
1. Open pull request to store in VCS and peer review
1. Push merged rules to production environment (optionally version lock and sync via CICD)
