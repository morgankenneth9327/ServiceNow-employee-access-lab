# Employee Access & Onboarding Automation — ServiceNow Lab

## Overview

This project is a ServiceNow portfolio lab focused on designing and implementing a structured employee onboarding and access-request process.

The solution is intended to replace informal email-based onboarding requests with a standardized process for collecting employee information, requesting equipment and system access, applying additional controls to privileged-access requests, routing fulfillment work, and tracking the request through completion.

The project is being developed incrementally, beginning with business analysis and requirements documentation before platform configuration and implementation.

## Project Status

**Version 1 Complete — Implemented and Validated**

Completed:

- Project charter, requirements, and stakeholder definition
- Business process modeling and exception-path design
- Solution architecture and Architecture Decision Record
- Scoped ServiceNow application and source control
- Employee Services catalog category
- Employee Onboarding & Access Request catalog item
- Ten request variables
- Conditional privileged-access justification behavior
- Security approval gate for privileged access
- Parallel IT Support and IAM fulfillment
- Approved privileged-access fulfillment
- Requested Item lifecycle completion
- Requester completion notification
- Test-user, group, role, and assignment model
- PDI performance troubleshooting, migration, and recovery
- Clean Global configuration packaging
- Active-flow catalog association packaging
- Live non-privileged workflow validation
- Live privileged-approved workflow validation
- Live privileged-rejected workflow validation
- Hardware and IAM fulfiller validation
- Completion-notification validation
- Scoped application merge to `main`

### Known Future Refinement

The non-privileged workflow path leaves the Requested Item `Approval` field at `Requested` even though no Security approval is required. This does not affect fulfillment or lifecycle completion, but normalization of that field is a possible future state-model improvement.

## V1 Scope

Version 1 includes:

- Employee onboarding request submission
- Collection of employee, department, manager, job-title, and start-date information
- Equipment and system-access selection
- Business justification for privileged-access requests
- Security approval for privileged access
- IT Support and IAM fulfillment routing
- Request completion tracking
- Completion notification

Production Active Directory / Entra ID integration, automated account provisioning, MID Server integration, external APIs, and production-grade security architecture are outside the initial implementation scope.

## What This Project Demonstrates

This repository is intended to demonstrate practical experience with:

- Requirements analysis and refinement
- Functional and non-functional requirements
- Business-process modeling
- Approval and fulfillment logic
- Requirements traceability
- ServiceNow administration and configuration
- ITSM-oriented workflow design
- Technical documentation
- Git and GitHub version control
- ServiceNow source control
- Platform troubleshooting and diagnostic analysis
- PDI backup, recovery, and migration
- Testing and evidence-based validation

## Documentation

| Document | Purpose |
| --- | --- |
| [Project Charter](Docs/01-project-charter.md) | Defines the business problem, objective, scope, stakeholders, assumptions, and success criteria |
| [Solution Requirements](Docs/02-requirements.md) | Defines functional and non-functional solution requirements |
| [Process Flow](Docs/03-process-flow.md) | Documents the onboarding process, decision points, approval paths, and fulfillment handoffs |
| [Solution Architecture](Docs/04-architecture.md) | Defines logical components, technical responsibilities, implementation mappings, data/control flow, and architectural decisions |
| [Implementation Build Log](Docs/05-build-log.md) | Records the actual ServiceNow configuration, implementation milestones, and migration/recovery work |
| [Testing and Validation](Docs/06-testing.md) | Records functional, restore, and environment validation results |
| [Architecture Decisions](Docs/07-decisions/ADR-001-solution-design.md) | Records significant architecture decisions, rationale, alternatives, and consequences |
| [Project Terminology](Docs/08-terminology.md) | Working glossary of project, requirements, ServiceNow, and process terminology |
| [PDI Performance Troubleshooting and Recovery](Docs/09-troubleshooting.md) | Documents the performance incident, diagnostic process, root-cause assessment, migration, recovery, and reusable troubleshooting runbook |
| [Test Identity and Assignment Model](Docs/10-test-identity-model.md) | Defines the fictional users, groups, memberships, roles, and assignment model required to reproduce the test environment |

## Environment

The solution is being implemented in a **ServiceNow Personal Developer Instance (PDI)** using ServiceNow-native functionality where practical.

The original implementation PDI developed severe infrastructure-level performance degradation and was replaced after diagnostic testing, backup, reset validation, and recovery planning. The project is currently hosted in replacement PDI `dev200255` on the Australia release.

Custom scripting will be introduced only where native platform functionality does not reasonably satisfy the documented requirement.

## Project Approach

The project follows a documentation-first development sequence:

**Business Problem → Scope → Requirements → Process Design → Architecture → Implementation → Validation**

Requirements are assigned stable identifiers so that later configuration and test cases can be traced back to the business behavior they are intended to satisfy.

## Repository Notes

This is a portfolio and learning environment rather than a production deployment.

- Organization and employee data are fictional.
- No production credentials or sensitive information are stored in this repository.
- Documentation reflects the current state of the project and will evolve alongside implementation.

## Configuration Packaging

The implementation is versioned across two repositories and an exported ServiceNow Update Set.

- **Portfolio and documentation repository:** `ServiceNow-employee-access-lab`
  - Project documentation and implementation evidence
  - Test identity and assignment model
  - Exported Global catalog configuration Update Set
- **Scoped application repository:** `ServiceNow-employee-access-app`
  - Completed Version 1 scoped application merged into `main`
  - ServiceNow-generated application source
  - Employee onboarding and access fulfillment flow
  - Completion notification and scoped application metadata
  - `sn_instances/dev200255` retained as the PDI working branch
- **Global configuration packages:**
  - `artifacts/update-sets/EAO-Catalog-Configuration-v0.1.xml`
    - Catalog category and catalog item
    - Catalog variables
    - Catalog UI Policy and action
    - Catalog and category associations
  - `artifacts/update-sets/EAO-Flow-Association-v0.1.xml`
    - Final catalog-item-to-flow association

Fictional test users, groups, memberships, and required roles are documented rather than stored as raw `sys_user` XML exports so that the environment can be reproduced without publishing unnecessary authentication or instance-specific user data.
