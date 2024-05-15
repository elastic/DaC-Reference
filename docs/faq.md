# Frequently Asked Questions

Here are some frequently asked questions about Detections as Code (DaC). There are known [limitations](./known_limitations.md), so please review if you do not see your question answered here. More questions and answers will be added over time to address common queries and concerns.

**Q1:** What is Detections as Code (DaC)?
**A1**: Detections as Code is a methodology that applies software development practices to the creation, management, and deployment of security detection rules. By treating detection rules as code, it enables version control, automated testing, and deployment processes, enhancing collaboration, consistency, and agility in response to threats.

**Q2**: How does DaC benefit my security team?
**A2**: DaC streamlines the detection rule lifecycle, ensuring high-quality detections through peer reviews and automated tests. It facilitates a scalable approach to managing an ever-growing number of detection rules and adapting quickly to the evolving threat landscape. This methodology supports compliance with change management requirements and fosters a mature security posture.

**Q3**: Can I integrate DaC with my existing CI/CD pipelines?
**A3**: Yes, DaC is designed to integrate seamlessly with existing CI/CD pipelines. By leveraging the detection-rules repository and its tooling within your CI/CD workflows, you can automate the testing, validation, and deployment of detection rules directly into Elastic Security or other SIEM platforms.

**Q4**: What are the prerequisites for adopting DaC with Elastic?
**A4**: To adopt DaC, you'll need access to the Elastic detection-rules repository, a version control system (like GitHub), and familiarity with CI/CD principles and tools. Basic knowledge of Elastic Security and its API is also beneficial for customizing and deploying rules.

**Q5**: How do I start using DaC in my organization?
**A5**: Begin by exploring the detection-rules repository and familiarizing yourself with its structure and tooling. Consider your governance model (VCS as authoritative, Elastic Security as authoritative, or dual sync) and plan your workflow accordingly. Experiment with the alpha branch to test new features and configurations that suit your needs.

**Q6**: Are there any resources available for learning more about DaC?
**A6**: Elastic provides comprehensive documentation, reference guides, and slide decks to help you understand and implement DaC. Joining the Elastic community forums and Slack channels, such as #security-rules-dac, can also provide valuable insights and support from fellow users and Elastic experts.

**Q7**: How can I contribute to the DaC methodology or detection-rules repo?
**A7**: Contributions are welcome! You can contribute by testing the alpha features, reporting issues, suggesting enhancements, or sharing your own use cases and scripts. Contributions help improve DaC for everyone and are a great way to give back to the community.

**Q8**: How should exception & action files be deployed to Kibana?
**A8**: Currently, there two approaches.

1. You could directly add the correct exception or action item in the toml files themselves. We have two fields available: `actions: Optional[list]` and `exceptions_list: Optional[list]`. This approach is a bit difficult because we do not define the entire schema for these fields that are expected by Kibana. It's not really a feature we use within our prebuilt ruleset, so its left open to the user to properly supply the correct fields and format.
2. The second approach to define the actions and /or exceptions directories in the `_config.yaml`. This will load action list toml files or exception list toml files defined in their folders. These toml files will have the `rule id`  mapping so you know which list are associated to the rule. When you upload rules to kibana, it will automatically bring the defined exception/action lists.

Note this is one way at the moment (toml --> kibana). So you can not export lists and import them into toml files.

