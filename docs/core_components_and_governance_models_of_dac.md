# Core components and Governance Models of DaC

================================

## Core components of Detections as Code

For any DaC implementation, the following core components are required:

1. Maintaining rules within a Version Control System (VCS)
1. Syncing rules from VCS to their respective platform
1. Managing rules within the platform
1. Syncing rules from the platform to VCS

As applied to the **Elastic Security** solution, this breaks down as follows:

1. Maintaining rules within a Version Control System (VCS)
1. Syncing rules from VCS to Elastic Security
1. Managing rules within Elastic Security (consistent with a DaC approach)
1. Syncing rules from Elastic Security to VCS

## Governance Models Dictating the Core Components of DaC

For any DaC implementation, the following governance models are required:

1. VCS as authoritative
1. Platform as authoritative
1. Dual sync between VCS and the platform

As applied to the **Elastic Security** this breaks down as follows:

1. VCS as authoritative
1. Elastic security as authoritative
1. Dual sync between VCS and Elastic Security

## Sub-Components and Options

Some of the core components are composed of smaller chunks of sub-components. These sub-components can consist of several options for implementation. For those core components without sub-components, they can have options as well.

These are the sub-components specific to this guide and the implementation of DaC on Elastic Security rules:

1. Maintaining rules within a Version Control System (VCS)
   1. Unit testing
   1. Rule schema validation
   1. Detection logic validation
   1. Creating detection rules
   1. Rule versioning
1. Exceptions lists and actions
1. Syncing rules from VCS to Elastic Security
1. Managing rules within Elastic Security (consistent with a DaC approach)
1. Syncing rules from Elastic Security to VCS

For details on the various options, refer to those specific core components sections of this guide.

## Detailed Considerations of the Different Governance Models

### VCS as authoritative

WIP

### Elastic security as authoritative

WIP

### Dual Syncing Rules and Data between Kibana and VCS

#### Overview

Dual syncing between Elastic Security and a Version Control System (VCS) embodies the preferred method for managing detection rules, merging the operational efficiency of Elastic Security with the structured control of VCS. This advanced strategy ensures that any rule modifications in Elastic Security are accurately reflected in VCS and vice versa, supporting a cohesive rule management ecosystem. It allows analysts to use the intuitive Elastic Security UI and detection engineers to leverage VCS for rule development, with critical syncing occurring ideally within Continuous Integration (CI) workflows. This approach is designed to abstract the complexities of rule versioning and syncing from detection engineers, ensuring that they can focus on rule quality and effectiveness.

|                                                                                        |
| -------------------------------------------------------------------------------------- |
| <img src="_static/dual-sync-overall-diagram.png" style="width:5.94271in;height:5.84793in" alt="Dual Sync Diagram"/> |
| <center>*Figure 2: Combining Multiple Options to Duel Sync Rules* </center>                                    |

#### Considerations

Implementing a dual syncing strategy requires careful planning and consideration of several factors to ensure its success. A successful dual syncing strategy involves integrating automation tools to handle the bidirectional flow of rule data. It requires a robust setup that can automatically compare rules by version or hash, ensuring that updates made within Elastic Security are synchronized back into VCS before final deployment. Utilizing the version lock file from the detection rules repository, or a bespoke versioning system, plays a pivotal role in managing this synchronization process seamlessly. Additionally, team-specific processes may be required to ensure production rules remain in sync.

##### Consideration 1: Syncing Processes

Leveraging automation tools and CI/CD pipelines facilitates a constant sync between Elastic Security and VCS. This setup should include processes for pulling rules from Elastic Security, comparing them against the VCS repository (either by version or hash), and ensuring updates are committed back into VCS. This ensures that both Elastic Security and VCS maintain the latest rule versions, abstracting this process from detection engineers.

###### Option 1: Automation Tools for Syncing

| Pros | Cons |
| - | - |
| - Minimizes manual intervention, automating rule synchronization. </br> - Supports collaborative rule development with integrated version control. | - Requires significant initial setup and maintenance of automation scripts and CI/CD pipelines. </br> - Potential for configuration complexities and the need to handle merge conflicts or discrepancies between the two environments.|

**Steps:**

1. Set up CI/CD pipelines with triggers for rule changes in both Elastic Security and VCS.
1. Utilize Elastic Security's Detection Engine APIs for rule exports and imports.
1. Configure scripts to handle rule format conversions, versioning, and conflict resolution between syncing.
1. Regularly monitor and test the synchronization process to identify and rectify any versioning issues.

###### Option 2: Pre-Merge Sync

Incorporate a sync check step within the PR workflow, running an automated job to compare rule versions or hashes between Elastic Security and VCS. This step aims to identify and resolve any discrepancies before merging changes into the main branch or deploying rules to production. It serves as a final check to ensure all rules are correctly synchronized and ready for deployment.

| Pros | Cons |
| - | - |
| - Ensures a final validation of rule synchronization, enhancing rule integrity before production deployment. </br> - Streamlines the resolution of any version conflicts or discrepancies, maintaining rule consistency. | - Introduces potential delays in rule synchronization due to the manual review process. </br> - Requires clear documentation and understanding of the sync process among team members. |

**Steps:**

1. Integrate a sync check script into the CI/CD pipeline, triggered as part of the PR process.
2. The script compares rule data between Elastic Security and VCS, highlighting discrepancies for resolution.
3. Resolve any conflicts or discrepancies identified by the sync check before merging the PR.
4. Upon successful sync check completion, proceed with merging the PR and deploying the rules to production.
