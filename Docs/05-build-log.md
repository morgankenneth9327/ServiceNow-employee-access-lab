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

### Conditional Privileged-Access Behavior

Implemented native Catalog UI Policy behavior for the privileged-access justification requirement.

#### Catalog UI Policy

| Item | Value |
| --- | --- |
| Catalog Item | Employee Onboarding & Access Request |
| Policy | Require justification for privileged access |
| Condition | `privileged_access_required` is not Yes |
| On load | Yes |
| Reverse if false | Yes |
| Custom scripting | None |

#### UI Policy Action

| Variable | False/No State | Yes State |
| --- | --- | --- |
| `business_justification` | Hidden, optional, value cleared | Visible and mandatory |

The policy ensures that business justification is required only when privileged access is requested.

Build Agent was used as a configuration reviewer after the policy was implemented manually. The review identified a stale-data edge case in which a previously entered justification could remain populated after privileged access was changed back to No. The policy was adjusted so that the justification value is cleared when privileged access is not required.

Validation confirmed:
- Business justification is hidden and optional when privileged access is No.
- Business justification becomes visible and mandatory when privileged access is Yes.
- An empty justification prevents submission while privileged access is Yes.
- Changing privileged access from Yes to No clears the previously entered justification.
- Returning privileged access to Yes displays an empty mandatory justification field.
- No custom client script was required.

### Workflow Studio Flow Skeleton

Created the initial orchestration flow for the Employee Onboarding & Access Request.

| Item | Value |
| --- | --- |
| Flow | Employee Onboarding & Access Fulfillment |
| Application | Employee Access & Onboarding |
| Status | Draft / inactive |
| Trigger | Service Catalog |
| Initial action | Get Catalog Variables |
| Submitted Request | Requested Item Record from Service Catalog trigger |
| Template Catalog Item | Employee Onboarding & Access Request |

The Get Catalog Variables action retrieves all ten request variables for use by later approval, fulfillment, completion, and notification logic.

Build Agent was used to generate and compile a Fluent SDK representation of the proposed action. The generated implementation compiled with zero errors and zero warnings but was intentionally not installed because the available Build Agent installation path could not guarantee preservation of the flow's draft/inactive state.

The action was therefore configured manually in Workflow Studio and validated using a draft-flow test.

Validation confirmed:
- The draft flow accepted a Requested Item as test input.
- Get Catalog Variables completed successfully.
- All ten catalog variables were exposed as flow outputs.
- Runtime values matched the submitted test request.
- The flow remains inactive and is not yet associated with the catalog item.

### Privileged-Access Security Approval Gate

Implemented the Version 1 Security approval gate for privileged-access requests in the Employee Onboarding & Access Fulfillment flow.

#### Approval Configuration

| Item | Value |
| --- | --- |
| Approval condition | `privileged_access_required` = Yes |
| Record approved | Requested Item from Service Catalog trigger |
| Approval group | EAO - Security Approver |
| Approval rule | Approve when anyone approves |
| Rejection rule | Reject when anyone rejects |
| Approved-path condition | Approval State = Approved |
| Custom scripting | None |

The Security approval logic executes only when privileged access is requested. An approved decision allows the privileged-access branch to continue. A rejected decision prevents the approved-only branch from executing without terminating the overall onboarding flow.

The `approver_user` role was assigned to the Test Security Approver after implementation demonstrated that the role was required for the test approver to access and act on records through My Approvals.

#### Implementation Troubleshooting

The initial approval configuration used a combined Approves-or-Rejects rule. During testing, Ask for Approval returned an Approval State of `skipped`. Execution logs identified the approval rule as invalid.

The combined rule was replaced with separate approval and rejection rule sets:

- Approve when anyone approves from EAO - Security Approver.
- Reject when anyone rejects from EAO - Security Approver.

After correction, Ask for Approval entered a waiting state and generated a valid approval record.

#### Validation

Validated three workflow paths:

| Scenario | Result |
| --- | --- |
| Privileged access not requested | Security approval bypassed; flow completed |
| Privileged access requested and approved | Approval completed; approved branch evaluated true; flow completed |
| Privileged access requested and rejected | Approval completed with rejected state; approved branch evaluated false; flow completed |

Approval records retained the approver identity, approval decision, and activity timestamp, providing the audit information required by NFR-002.

### Parallel Fulfillment Tasks

Implemented independent hardware, standard-access, and privileged-access fulfillment paths using native Workflow Studio parallel flow logic and Catalog Tasks.

#### Fulfillment Structure

| Path | Condition | Fulfillment | Assignment Group |
| --- | --- | --- | --- |
| Hardware | `laptop_required` = Yes | Prepare employee hardware | EAO - IT Support |
| Standard IAM | `vpn_access_required` = Yes OR `standard_application_access_required` = Yes | Provision standard employee access | EAO - IAM |
| Privileged IAM | `privileged_access_required` = Yes AND Security approval = Approved | Provision approved privileged access | EAO - IAM |

The three fulfillment paths execute within a **Do the following in Parallel** block so that unrelated hardware and standard-access work can proceed while privileged-access approval is pending.

Each Catalog Task:
- Is associated with the triggering Requested Item.
- Uses `Wait = true` so its branch remains active until fulfillment is completed.
- Exposes relevant request variables directly to the fulfiller.
- Uses native ServiceNow functionality without custom scripting.

#### Catalog Task Context

Hardware task exposes:
- `employee_name`
- `department`
- `job_title`
- `start_date`
- `laptop_required`

Standard IAM task exposes:
- `employee_name`
- `department`
- `job_title`
- `start_date`
- `vpn_access_required`
- `standard_application_access_required`

Privileged IAM task exposes:
- `employee_name`
- `department`
- `job_title`
- `start_date`
- `privileged_access_required`
- `business_justification`

#### Implementation Troubleshooting

During rejection-path validation, newly submitted requests also received two unrelated Catalog Tasks: `Assess or Scope Task` and `Provide requested service`.

The catalog item was found to have `Execution Plan = DEFAULT`. The default execution plan was cleared so the catalog item would not generate unrelated fulfillment work while the custom Workflow Studio flow remained under development.

Fresh validation after removing the default execution plan produced only the Catalog Tasks defined by the Employee Onboarding & Access Fulfillment flow.

#### Validation

Approved privileged-access scenario:
- Hardware and standard IAM tasks were created immediately while Security approval remained pending.
- Approval allowed the privileged-access branch to proceed.
- A privileged IAM Catalog Task was created only after approval.
- The parallel block remained waiting until all applicable fulfillment tasks were closed.
- The overall flow completed after all required fulfillment work finished.

Rejected privileged-access scenario:
- Hardware and standard IAM tasks were created and completed normally.
- Security rejection caused the approved-only condition to evaluate false.
- No privileged IAM Catalog Task was created.
- Unrelated fulfillment continued despite the rejection.
- The parallel block and overall flow completed after the remaining required tasks were closed.

This behavior satisfies the fulfillment-routing and privileged-access rejection requirements represented by FR-006, FR-007, and FR-008.

### Request Completion and Notification

Implemented final Requested Item completion handling and requester notification after all required fulfillment work is complete.

#### Completion Configuration

| Item | Value |
| --- | --- |
| Record updated | Triggering Requested Item |
| Table | `sc_req_item` |
| Final State | Closed Complete (`state = 3`) |
| Execution point | After all parallel fulfillment paths complete |

The completion update executes only after all applicable Catalog Tasks and approval-dependent fulfillment paths have finished. Closing the final required Catalog Task allowed the parallel block to complete, after which the Requested Item transitioned to its completed lifecycle state.

#### Completion Notification

| Item | Value |
| --- | --- |
| Notification | EAO - Onboarding Request Completed |
| Table | Requested Item [`sc_req_item`] |
| Send method | Triggered through Workflow Studio |
| Recipient | Request → Requested for |
| Flow action | Send Notification |

A fictional email address was assigned to the Test Hiring Manager for notification validation.

#### Fulfiller Access Validation

The Test IT Support user was given the `itil` role after fulfillment testing demonstrated that Catalog Tasks are worked as fulfiller tasks rather than approval records.

The hardware Catalog Task appeared in work assigned to the user's groups. Test IT Support claimed the task, became the individual assignee, and completed it as Closed Complete.

This validated the intended fulfillment model:

- Assignment group identifies the responsible team.
- A fulfiller can claim work from the team queue.
- Individual assignment identifies the person performing the task.
- Closing the required Catalog Task allows the waiting flow branch to continue.

#### Validation

Validation confirmed:

- The flow remained waiting while a required Catalog Task was open.
- Closing the final required Catalog Task allowed the parallel fulfillment block to complete.
- The Requested Item completion action executed only after fulfillment completion.
- The Requested Item transitioned to the completed lifecycle state.
- The completion notification action executed successfully.
- ServiceNow generated an outbound `sys_email` record.
- The generated email targeted the test Requested Item.
- The recipient resolved to `eao.hiring.manager@example.com`.
- The subject contained the correct Requested Item number.
- The completion message was generated successfully.

This implementation satisfies FR-009 and FR-011 and provides the completed request state used to support FR-010.

### Configuration and Version-Control Packaging

Implementation configuration was packaged for reproducibility before final workflow activation and integrated testing.

- Scoped application changes were committed to the ServiceNow source-control repository.
- Global catalog configuration was recaptured in a clean Update Set named `EAO - Catalog Configuration - v0.1 Final`.
- The completed Update Set was exported and stored at `artifacts/update-sets/EAO-Catalog-Configuration-v0.1.xml`.
- Fictional test identities, groups, memberships, and required platform roles were documented in `Docs/10-test-identity-model.md` rather than publishing raw user-record XML.
- The scoped application remains on the active instance development branch pending final activation and smoke testing.
