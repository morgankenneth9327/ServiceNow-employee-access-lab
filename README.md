# Employee Access & Onboarding Automation — ServiceNow Lab

## Overview

This project is a ServiceNow portfolio lab focused on designing and implementing a structured employee onboarding and access-request process.

The solution is intended to replace informal email-based onboarding requests with a standardized process for collecting employee information, requesting equipment and system access, applying additional controls to privileged-access requests, routing fulfillment work, and tracking the request through completion.

The project is being developed incrementally, beginning with business analysis and requirements documentation before platform configuration and implementation.

## Project Status

**In Progress — ServiceNow Implementation**

Completed:

* Project charter and scope definition
* Functional and non-functional requirements
* Actor and stakeholder definition
* Business process modeling and process diagram
* Alternate and exception-path definition
* Requirement traceability
* Logical solution architecture
* Component responsibility and requirement mapping
* Data and control flow design
* ServiceNow implementation mapping
* Solution architecture diagram
* Architecture constraints and design decisions
* Architecture Decision Record (ADR)
* Architecture requirement traceability
* Scoped ServiceNow application creation and source control
* Test users, fulfillment groups, and group memberships
* Employee Services catalog category
* Employee Onboarding & Access Request catalog item
* Ten request variables
* Requester-facing catalog rendering validation
* PDI performance troubleshooting, backup, migration, and restoration
* Post-migration performance validation

In progress:

* Approval and fulfillment workflow

Planned:

* Conditional privileged-access behavior
* Functional workflow testing and evidence collection
* Build Agent-assisted implementation with human review
* Additional version-controlled ServiceNow configuration

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
