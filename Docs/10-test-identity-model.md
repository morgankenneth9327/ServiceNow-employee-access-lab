# Test Identity and Assignment Model

## Purpose

This document defines the fictional users, groups, memberships, and platform roles required to reproduce and validate the Version 1 Employee Access & Onboarding workflow.

All identities and email addresses are fictional and are intended only for a ServiceNow development or test environment.

## Groups

| Group | Purpose |
| --- | --- |
| EAO - IT Support | Hardware and equipment fulfillment |
| EAO - IAM | Identity and standard/privileged access fulfillment |
| EAO - Security Approver | Privileged-access approval |

## Test Users

| User ID | Display Name | Email | Purpose |
| --- | --- | --- | --- |
| `eao.hiring.manager` | Test Hiring Manager | `eao.hiring.manager@example.com` | Submits onboarding requests and receives completion notifications |
| `eao.security.approver` | Test Security Approver | `eao.security.approver@example.com` | Reviews privileged-access requests |
| `eao.it.support` | Test IT Support | `eao.it.support@example.com` | Performs hardware fulfillment |
| `eao.iam.analyst` | Test IAM Analyst | `eao.iam.analyst@example.com` | Performs identity and access fulfillment |

## Group Memberships

| User | Group |
| --- | --- |
| Test Security Approver | EAO - Security Approver |
| Test IT Support | EAO - IT Support |
| Test IAM Analyst | EAO - IAM |

Test Hiring Manager does not require a fulfillment-group membership.

## Platform Roles

| User | Role | Reason |
| --- | --- | --- |
| Test Security Approver | `approver_user` | Allows the user to view and act on approval records |
| Test IT Support | `itil` | Allows the user to access and fulfill assigned Catalog Tasks |
| Test IAM Analyst | `itil` | Allows the user to access and fulfill assigned Catalog Tasks |
| Test Hiring Manager | None required | Uses requester-facing Service Catalog functionality |

## Assignment Model

Fulfillment Catalog Tasks are assigned first to a responsible group rather than directly to an individual.

- Hardware tasks are assigned to **EAO - IT Support**.
- Standard-access tasks are assigned to **EAO - IAM**.
- Approved privileged-access tasks are assigned to **EAO - IAM**.
- Privileged-access approvals are routed to **EAO - Security Approver**.

A fulfiller may claim work from their group's queue, which populates the individual **Assigned to** field.

## Reproduction Notes

1. Create the three EAO groups.
2. Create the four fictional test users.
3. Add users to their documented groups.
4. Assign `approver_user` to Test Security Approver.
5. Assign `itil` to the IT Support and IAM test users.
6. Do not assign administrative roles to the fictional test users.
7. Use only fictional test data and reserved `example.com` email addresses.
