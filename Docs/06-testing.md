# Testing and Validation

## Purpose

This document records validation activities performed against the Employee Access & Onboarding solution and its supporting ServiceNow environment.

## Catalog Item Validation

### Test: Requester-facing catalog rendering

**Component:** Employee Onboarding & Access Request  
**Method:** ServiceNow **Try It** view  
**Result:** Passed

Validated that the catalog item renders with all 10 expected variables in the intended order:

1. Employee name
2. Department
3. Job title
4. Manager name
5. Start date
6. Laptop required?
7. VPN access required?
8. Standard application access required?
9. Privileged access required?
10. Business justification

The catalog item was also confirmed to be active and associated with the **Employee Services** category in the **Service Catalog**.

## PDI Migration Validation

Following migration from the degraded `dev421826` PDI to replacement PDI `dev200255`, the restored environment was checked before development resumed.

### Identity and assignment data

**Result:** Passed

Confirmed restoration of:

- Four fictional EAO test users
- Three EAO project groups
- Group membership relationships

### Catalog configuration

**Result:** Passed

Confirmed restoration of:

- Employee Services category
- Employee Onboarding & Access Request catalog item
- All 10 catalog variables
- Catalog-item/category relationship

### Scoped application

**Result:** Passed

Confirmed that the **Employee Access & Onboarding** scoped application was successfully imported from ServiceNow source control and that the new PDI is using working branch:

`sn_instances/dev200255`

## Post-Migration Performance Validation

A `stats.do` snapshot was captured after project restoration.

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

**Result:** Passed

The replacement PDI remained responsive after the scoped application and supporting XML records were restored. This provided a clean post-migration baseline and supported resumption of implementation work.

See [PDI Performance Troubleshooting and Recovery](09-troubleshooting.md) for the full incident timeline, diagnostic evidence, recovery actions, and troubleshooting runbook.

## Integrated Workflow Validation

After implementation was completed, the flow was activated and associated with the **Employee Onboarding & Access Request** catalog item. Validation was then performed through normal requester-facing Service Catalog submissions rather than Workflow Studio draft tests.

### Test: Non-Privileged Request

**Requested Item:** `RITM0010012`  
**Result:** Passed

Test conditions:

- Laptop required = Yes
- VPN access required = Yes
- Standard application access required = Yes
- Privileged access required = No

Observed behavior:

- The active flow triggered automatically from the Service Catalog submission.
- A **Prepare employee hardware** Catalog Task was created for EAO - IT Support.
- A **Provision standard employee access** Catalog Task was created for EAO - IAM.
- No privileged-access fulfillment task was created.
- No Security approval was required by the flow path.
- Test IT Support successfully claimed and completed the hardware task.
- Test IAM Analyst successfully claimed and completed the standard-access task.
- The Requested Item automatically transitioned to **Closed Complete**.
- The Requested Item became inactive.
- A completion notification was generated for the Test Hiring Manager.

The Requested Item retained `Approval = Requested` on this non-privileged path even though no Security approval was required. This did not prevent fulfillment or completion and is retained as an observed platform-state behavior for possible future refinement.

### Test: Privileged Request — Approved

**Requested Item:** `RITM0010013`  
**Result:** Passed

Test conditions:

- Laptop required = Yes
- VPN access required = Yes
- Standard application access required = Yes
- Privileged access required = Yes
- Business justification supplied

Observed behavior before approval:

- The active flow triggered automatically.
- **Prepare employee hardware** was created immediately.
- **Provision standard employee access** was created immediately.
- The privileged-access branch entered Security approval.
- No privileged-access fulfillment task existed before approval.

Observed behavior after Security approval:

- Requested Item approval changed to **Approved**.
- **Provision approved privileged access** was created for EAO - IAM.
- Hardware and standard-access work remained independent of the approval path.
- Test IT Support completed the hardware task.
- Test IAM Analyst completed both IAM fulfillment tasks.
- The Requested Item automatically transitioned to **Closed Complete**.
- The Requested Item became inactive.
- A completion notification was generated for the Test Hiring Manager.

This validates that privileged fulfillment is gated by Security approval while unrelated fulfillment may proceed in parallel.

### Test: Privileged Request — Rejected

**Requested Item:** `RITM0010014`  
**Result:** Passed

Test conditions:

- Laptop required = Yes
- VPN access required = Yes
- Standard application access required = Yes
- Privileged access required = Yes
- Business justification supplied

Observed behavior after Security rejection:

- Requested Item approval changed to **Rejected**.
- **Prepare employee hardware** remained available for fulfillment.
- **Provision standard employee access** remained available for fulfillment.
- No **Provision approved privileged access** task was created.
- Test IT Support completed the hardware task.
- Test IAM Analyst completed the standard-access task.
- The Requested Item automatically transitioned to **Closed Complete**.
- The Requested Item became inactive.
- A completion notification was generated for the Test Hiring Manager.

This validates that Security rejection blocks only the privileged-access component and does not terminate unrelated onboarding fulfillment.

## Fulfiller Access Validation

**Result:** Passed

Validation confirmed the required roles for the fictional test identities:

| Test User | Role | Validation |
| --- | --- | --- |
| Test Security Approver | `approver_user` | Successfully accessed and acted on privileged-access approvals |
| Test IT Support | `itil` | Successfully accessed, claimed, and completed hardware Catalog Tasks |
| Test IAM Analyst | `itil` | Successfully accessed, claimed, and completed standard and privileged IAM Catalog Tasks |

The tests also confirmed the intended assignment model:

- Catalog Tasks are assigned to fulfillment groups.
- Individual fulfillers can claim work from their group queue.
- Claiming a task populates the individual assignment.
- Closing a required task allows the waiting flow branch to continue.

## Completion Notification Validation

**Result:** Passed

Outbound email records confirmed successful generation of the custom completion notification for all three integrated test Requested Items:

| Requested Item | Expected Subject | Recipient | Result |
| --- | --- | --- | --- |
| `RITM0010012` | Employee onboarding request RITM0010012 is complete | `eao.hiring.manager@example.com` | Passed |
| `RITM0010013` | Employee onboarding request RITM0010013 is complete | `eao.hiring.manager@example.com` | Passed |
| `RITM0010014` | Employee onboarding request RITM0010014 is complete | `eao.hiring.manager@example.com` | Passed |

The generated messages were recorded as `send-ready` outbound email records in the PDI.

## Integrated Validation Summary

| Scenario | Expected Outcome | Result |
| --- | --- | --- |
| Non-privileged onboarding | Standard hardware/IAM fulfillment without privileged task | Passed |
| Privileged access approved | Privileged task created only after Security approval | Passed |
| Privileged access rejected | Privileged task blocked; unrelated fulfillment continues | Passed |
| Hardware fulfillment | IT Support can claim and complete assigned task | Passed |
| IAM fulfillment | IAM Analyst can claim and complete assigned tasks | Passed |
| Request completion | RITM closes automatically after all required fulfillment completes | Passed |
| Completion notification | Requester receives generated completion notification | Passed |

The live validation confirms the Version 1 workflow behaves as designed across its primary, approved privileged-access, and rejected privileged-access paths.
