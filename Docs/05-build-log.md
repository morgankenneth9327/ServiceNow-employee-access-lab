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
| EAO - Security Approvers | Privileged-access approval |

#### Test Users

| User ID | Identity | Group Membership |
| --- | --- | --- |
| eao.hiring.manager | Test Hiring Manager | None |
| eao.security.approver | Test Security Approver | EAO - Security Approvers |
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
