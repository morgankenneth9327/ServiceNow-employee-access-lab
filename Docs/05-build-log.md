# Implementation Build Log

## Purpose

This document records the actual configuration and implementation of the Employee Access & Onboarding solution in the ServiceNow Personal Developer Instance. Entries document what was configured, significant implementation decisions or deviations from the planned architecture, and information required to reproduce or validate the solution.

## Implementation Baseline

| Item                            | Value                                                                                |
| ------------------------------- | ------------------------------------------------------------------------------------ |
| Environment                     | ServiceNow Personal Developer Instance                                               |
| ServiceNow Release              | Australia                                                                            |
| Solution Version                | v0.1                                                                                 |
| Application                     | Employee Access & Onboarding                                                         |
| Application Scope               | Scoped application                                                                   |
| Global Configuration Update Set | EAO - Catalog Configuration - v0.1                                                   |
| Implementation Approach         | Native ServiceNow functionality preferred where it satisfies documented requirements |

## Build Entries

### Implementation Setup

- Established the Employee Access & Onboarding scoped application for application-owned solution artifacts.
- Established a dedicated global Update Set for Service Catalog configuration.
- Separated scoped application artifacts from global catalog configuration in accordance with the documented architecture.

### Test Identity and Assignment Model

Created the fictional users and fulfillment groups required to exercise the Version 1 workflow.

#### Groups

| Group | Purpose |
| --- | --- |
| EAO - IT Support | Hardware and equipment fulfillment |
| EAO - IAM | Identity and access fulfillment |
| EAO - Security Approver | Privileged-access approval |

#### Test Users

| User ID | Identity | Group Membership |
| --- | --- | --- |
| eao.hiring.manager | Test Hiring Manager | None |
| eao.security.approver | Test Security Approver | EAO - Security Approver |
| eao.it.support | Test IT Support | EAO - IT Support |
| eao.iam.analyst | Test IAM Analyst | EAO - IAM |

- Test identities are fictional and contain no real employee information.
- No additional platform roles were assigned at this stage.
- Group membership establishes the assignment and approval structure required by the documented architecture.

### Catalog Item and Request Variables

Created the requester-facing Service Catalog structure for the Version 1 onboarding process.

#### Catalog Configuration

| Item | Value |
| --- | --- |
| Category | Employee Services |
| Catalog Item | Employee Onboarding & Access Request |
| Catalog | Service Catalog |
| Status | Active |

#### Request Variables

| Order | Variable | Name | Type | Mandatory |
| ---: | --- | --- | --- | --- |
| 100 | Employee name | `employee_name` | Single Line Text | Yes |
| 200 | Department | `department` | Single Line Text | Yes |
| 300 | Job title | `job_title` | Single Line Text | Yes |
| 400 | Manager name | `manager_name` | Single Line Text | Yes |
| 500 | Start date | `start_date` | Date | Yes |
| 600 | Laptop required? | `laptop_required` | Yes/No | Yes |
| 700 | VPN access required? | `vpn_access_required` | Yes/No | Yes |
| 800 | Standard application access required? | `standard_application_access_required` | Yes/No | Yes |
| 900 | Privileged access required? | `privileged_access_required` | Yes/No | Yes |
| 1000 | Business justification | `business_justification` | Multi Line Text | No |

- Verified the catalog item using the requester-facing **Try It** view.
- Confirmed variable order, required-field behavior, date input, and Yes/No inputs render as expected.
- Business justification remains optional at this stage; conditional visibility and mandatory behavior will be implemented separately.


### PDI Performance Incident, Recovery, and Migration

During implementation, the original PDI (`dev421826`) developed severe and persistent server-side performance degradation. Transaction Log and `stats.do` evidence showed multi-second user-initiated response times and extremely elevated Linux load averages while database, scheduler, and semaphore snapshots did not show corresponding saturation.

Troubleshooting included:

- Transaction Log review
- `stats.do` baseline capture
- `cache.do` cache flush
- Servlet-memory comparison
- Semaphore, database-pool, and scheduler review
- Pre-reset backup through ServiceNow source control, update-set export, and XML record exports
- Reset/wipe validation
- Replacement-PDI validation

Resetting the original PDI did not resolve the issue because it returned on the same ServiceNow node. The project was therefore migrated to a newly provisioned Australia PDI (`dev200255`) on a different host.

The replacement PDI used the same Australia Patch 3 build family but immediately demonstrated normal performance. This supported the conclusion that the original problem was host/shared-infrastructure contention rather than the Employee Access & Onboarding application or Australia Patch 3 itself.

#### Restore Actions

- Imported the scoped Employee Access & Onboarding application from the dedicated ServiceNow source-control repository.
- Established working source-control branch `sn_instances/dev200255`.
- Restored test users, groups, and group memberships from XML.
- Restored the Employee Services category and Employee Onboarding & Access Request catalog item.
- Restored all 10 catalog variables.
- Verified the catalog item/category relationship.
- Verified requester-facing rendering using **Try It**.

#### Post-Restore Baseline

| Metric | Result |
| --- | ---: |
| Database latency | 1 ms |
| User-initiated response, 1 minute | 217 ms |
| User-initiated response, 5 minutes | 165 ms |
| User-initiated response, 15 minutes | 124 ms |
| Default response, 5 minutes | 222 ms |
| Database connections busy | 0 |
| Database connections available | 13 |
| Scheduler queue length | 0 |

Detailed incident analysis and the reusable troubleshooting runbook are maintained in [PDI Performance Troubleshooting and Recovery](09-troubleshooting.md).
