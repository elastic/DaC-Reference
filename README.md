# Detections as Code (DaC) - Elastic Reference

This repository is dedicated to the management of Elastic Security SIEM Rules using the Detections as Code (DaC) methodology. DaC applies software development best practices to the creation, management, and deployment of detection rules, allowing organizations to leverage version control systems, automate testing and deployment processes, and ensure consistency and accuracy across their security infrastructure.

## Description

The DaC approach enhances collaboration among security teams, streamlines updates, and facilitates a more agile response to evolving threats. This repository serves as a comprehensive resource for adopting DaC in managing Elastic Security rules, enabling you to automate rule deployments, enhance rule validation processes, or streamline rule versioning and exception management.

Key aspects covered include:

- Managing detection rules in Kibana vs. Version Control Systems (VCS).
- Syncing rules from VCS to Kibana and vice versa.
- Creating, testing, and managing rules directly in Kibana, while considering backup and versioning strategies.
- Utilizing CI/CD pipelines for automated rule deployment and updates.

## How to Use This Guide

This guide offers a modular set of practices and components for detection rule management. You're encouraged to select and combine options relevant to your specific needs and operational context, customizing your workflow for rule management.

#### DaC Requirements

To effectively use this repository, you will need:

- Access to a VCS (e.g., GitHub, GitLab).
- Access to an Elastic Stack environment.
- Familiarity with CI/CD concepts and tools for automation (optional but recommended).
- Basic knowledge of Kibana's UI and Elastic Security features.


## Documentation

#### Doc Prerequisites

- Python 3.12+
- [Poetry](https://python-poetry.org/docs/): This project uses Poetry for dependency management and to streamline the build process.

#### Getting Started

To get started with this project run the following commands:

1. **Clone the Repository**:

   ```bash
   # Clone the repository
   git clone https://github.com/elastic/DaC-Reference
   cd DaC-Reference

   # Install dependencies with Poetry
   poetry install
   ```

1. **Edit or Add Documentation Files**: Navigate to the `docs` directory and update existing or add new documentation files. Documentation source files can be in reStructuredText (`.rst`) or Markdown (`.md`) formats, thanks to the MyST-Parser.

1. **Build the Documentation Locally**: To build your documentation and see your changes locally, run the following command:

   ```bash
   poetry run build-docs -b html docs docs/_build
   ```

   This command uses Poetry to invoke Sphinx, building the HTML documentation from the source files in the `docs` directory into the `docs/_build` directory. You can view the built documentation by opening the `docs/_build/index.html` file in a web browser.

1. **Commit Your Changes**: After updating the documentation and verifying your changes locally, commit your documentation source changes to your git repository:

   ```bash
   git add docs/
   git commit -m "Update documentation"
   git push origin main
   ```

For more commands and project details, refer to the project's documentation and `pyproject.toml`.

## License

Everything in this repository is licensed under the [License](LICENSE).
