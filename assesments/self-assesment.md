*IMPORTANT* THIS IS ONLY A DEMO MD
CNCF BOILERPLATE TEXT: https://github.com/cncf/tag-security/blob/main/assessments/guide/self-assessment.md#self-assessment-use

# Privateer Self-Assessment

Security reviewers: Eddie Knight

This document is intended to aid in roadmapping, and the onboarding of new maintainers.

## Table of Contents

* [Metadata](#metadata)
  * [Security links](#security-links)
* [Overview](#overview)
  * [Actors](#actors)
  * [Actions](#actions)
  * [Background](#background)
  * [Goals](#goals)
  * [Non-goals](#non-goals)
* [Self-assessment use](#self-assessment-use)
* [Security functions and features](#security-functions-and-features)
* [Project compliance](#project-compliance)
* [Secure development practices](#secure-development-practices)
* [Security issue resolution](#security-issue-resolution)
* [Appendix](#appendix)

## Metadata

| | |
|-----------|------|
| Software | https://privateerproj/privateer<‌br‌>https://privateerproj/privateer-sdk<‌br‌>https://privateerproj/privateer-raid-wireframe |
| Security Provider? | No. Privateer is designed to facilitate security or compliance validation, but it should not be considered a security provider. |
| Languages | Go |
| Software Bill of Materials | Known Weakness. Automated generation of each repo's SBOM is not yet complete, and should be added to the roadmap. |
| Security Links | Known Weakness. Creation of a security-insights.yml should be added to the roadmap. |

## Overview

Privateer is a test harness that is specially designed to combine any number of validation test packs into a single runtime that will harmonize inputs, executions, and outputs. The Privateer SDK enables the creation of test plugins, nicknamed _raids_, which can be selected and executed by Privateer users one at a time or in groups.

### Background

Historically, runtime validation tests are notoriously specific to the resource they're validating. While the validators are powerful, they typically only address a single use case.

In situations where engineers need to validate a wide array of deployed resources, they must build a custom solution that incorporates the commands and configurations for each validation tool.

Privateer seeks to remediate this problem by allowing validation tests to be built as "raids" which receive their configuration from the Privateer executable, and subsequently pass their outputs back to the Privateer. When multiple raids are executed by a user, only a single config is required, all executions log their status together, and the output will always be provided together in a matching format.

### Actors

1. The core executable, [Privateer](htt‌ps://github.com/privateerproj/privateer), serves to initialize all plugins. This process will continue running concurrently while sequentially initializing Raids as secondary processes.
2. Raid plugins, built in the style of the [raid wireframe](ht‌tps://github.com/privateerproj/privateer-raid-wireframe), are executed independently of the core process and are fully unaware of other Raid processes.

### Actions

The Privateer Core will read the user config to determine which Raids are to be read, and customize the runtime behavior based on options such as loglevel.

One at a time, Privateer will initialize Raids by calling the respective subprocess by name, based on user input. Each Raid subprocess will independently read the configuration file for information such as runtime customization or resource authentication values.

The Privateer SDK enables each Raid to print and write logs or output for the execution. Upon completion of all Raids, Privateer will print a summary of the Raid results.

![Flowchart of the actors discussed above](actors_actions.png)

### Goals

The Privateer project intends to create an ecosystem of post-deployment validation tools that can be easily incorporated into any automation pipeline.

In order to mitigate the risk of compromised open source dependencies, Privateer ensures that sensitive information stored within a configuration may be fully isolated between plugins ("Raids") when multiple implementations are executed simultaneously.

### Non-Goals

The Privateer project does not maintain packages, instead relying on the greater open source community to maintain each package independently of the main project. Privateer does not attempt to validate the security of plugins that are requested by users.

#### Plugin Validation

The Privateer project does not currently provide validation or assurances of safety for plugins ("Raids") that users choose to execute.

A roadmap candidate is being considered to address this by offering a "--safe" flag to the CLI, which will only execute plugins that are retrieved from an official source. This is currently not a goal due to the large scope of the process: Adding this feature will require (at minimum) an approval process, a list or registry of approved plugins, and automated provenance validation built into the --safe flag.

For the foreseeable future, Privateer will treat all plugins selected by users as fully-trusted entities.

#### Subprocess Command to execute plugins

When Privateer calls the raids as subprocesses, no validation is performed to restrict subprocesses to safe executables. To exploit this, the Privateer executable or respective configuration file must already be compromised by an attacker. In either case, there is no additional opportunity provided to attackers by restricting subprocess commands further.

## Self-assessment Use

This self-assessment is created by the Privateer team to perform an internal analysis of the project's security. It is not intended to provide a security audit of Privateer, or function as an independent assessment or attestation of Privateer's security health.

This document serves to provide Privateer users with an initial understanding of Privateer's security, where to find existing security documentation, Privateer plans for security, and general overview of Privateer security practices, both for development of Privateer as well as security of Privateer.

This document provides Privateer maintainers and stakeholders with additional context to help inform the roadmap creation process, so that security and feature improvements can be prioritized accordingly.

## Security functions and features

| Component | Applicability | Description of Importance |
| --------- | ------------- | ------------------------- |
| Hashicorp Go-Plugin | Critical | The `Go-Plugin` component enables Privateer to segment Raids as fully independent processes that communicate with the core via RPC on a local network, thereby allowing plugins to operate side-by-side without opportunity for configuration collision or side-channeling. |
| YAML Configuration | Relevant | The YAML configuration handling enables Privateer to safely read user configuration and secrets across multiple Raid executions while encrypting or masking them when appropriate |

## Project Compliance

Privateer does not currently adhere to any compliance standards. This is because the currently supported usage of Privateer is to execute raids on non-production environments.

### Future State

The Privateer roadmap includes preparation for eventual production support, which is why we are seeking to hold Privateer to a high security standard. We hope to include prod support sometime after the official v1 release.

## Secure Development Practices

Although the Privateer project is still incubating, we strive to implement the highest standard of secure development best practices, as noted below.

### Deployment Pipeline

In order to secure the SDLC from development to deployment, the following measures are in place. Please consult the roadmap for information about how this list is growing.

- Branch protection on the default (`main`) branch:
  - Require signed commits
  - Require a pull request before merging
    - Require approvals: 1
    - Dismiss stale pull request approvals when new commits are pushed
    - Require review from Code Owners
    - Require approval of the most recent reviewable push
    - Require conversation resolution before merging
  - Require status checks to pass before merging
    - Require branches to be up to date before merging

### Communication Channels

Internal communications among Privateer maintainers and contributors are handled through the public Slack channel and direct messages. Inbound communications are accepted through GitHub Issues or the public slack channel and direct messages. Outbound messages to users are made primarily via documentation or release notes, and secondarily via the public slack channel.

## Security Issue Resolution

The Privateer security policy is maintained in the SECURITY.md file and can be quickly found through the [GitHub Security Overview](htt‌ps://github.com/privateerproj/privateer/security/policy).

### Responsible Disclosure Practice

The Privateer project accepts vulnerability reports through the [GitHub Vulnerability Reporting](htt‌ps://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing/privately-reporting-a-security-vulnerability) tool.

Anyone can make a report by going to the [reporting form](htt‌ps://github.com/privateerproj/privateer/security/advisories/new) in the GitHub repo. In the event that a report is received, a maintainer will collaborate directly with the reporter through the Security Advisory until it is resolved.

### Incident Response

In the event that a vulnerability is reported, the maintainer team will collaborate to determine the validity and criticality of the report. Based on these findings, the fix will be triaged and the maintainer team will work to issue a patch in a timely manner.

Patches will be made to all versions that are currently supported under the project's security policy. Information will be disseminated to the community through all appropriate outbound channels as soon as possible based on the circumstance.

## Appendix

- Known Issues Over Time
  - Known issues are currently tracked in the project roadmap. There are currently no known vulnerabilities in any version.
- OpenSSF Best Practices
  - The process to get a Best Practices badge is not yet on the roadmap.
- Case Studies
  - This project is used by the Compliant Financial Infrastructure community to create runtime validation tests for their IaC resources. These raids are built and maintained within the FINOS ecosystem.
- Related Projects / Vendors
  - Many technologies have externally maintained validation tools that are created for niche purposes. Independently, these may fill the same gaps as an individual Raid, though each tool has its own method of configuration, execution, and output. The differentiator for Privateer is that it streamlines validation testing at scale, by harmonizing the inputs and outputs for any number of different Raids.
