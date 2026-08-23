# Project Charter — Employee Access & Onboarding Automation

## Project Status

Planning

## Business Problem
A company currently handles employee onboarding and access requests through email and informal communication. This results in inconsistent information collection, unclear fulfillment ownership, inconsistent approval of privileged access, and limited visibility into request status.

## Project Objective
Implement a standardized employee onboarding and access-request process that captures required information, applies appropriate approval requirements, assigns fulfillment work to responsible teams, and provides visibility into request status.

## Scope

### In Scope
-Employee onboarding request submission
-Collection of employee, department, manager, and start-date information
-Equipment and system-access selection
-Business justification for privileged-access requests
-Security approval for privileged access
-Creation and assignment of fulfillment tasks
-Request status tracking
-Completion notification
-Basic test-user and fulfillment-group configuration

### Out of Scope
- Production Active Directory or Microsoft Entra ID integration
- Automated user-account provisioning
- Automated software deployment
- ServiceNow MID Server integration
- External REST API integration
- Production email integration
- HR-system integration
- Offboarding
- Production-grade security architecture

## Personas and Stakeholders
Personas                 -   Responsibilities 

1. Hiring Manager           -   Initiated onboarding/access requests
2. IT Support Technician    -   Fulfills hardware/equipment work
3. IAM Analyst              -   Fulfills identity and access work
4. Security Approver        -   Reviews privileged-access requests
5. ServiceNow Administrator -   Configures and maintains the solution

## Success Criteria
The initial implementation will be considered successful when:

- A hiring manage can submit a complete onboarding request.
- Required information is captured consistently.
- Privileged-access requests require additional justification.
- Privileged-access requests are routed for Security approval.
- Required fulfillment work is assigned to the appropriate groups.
- Rejected privileged-access requests do not proceed through privilege fulfillment.
- The request can reach a completed state after required work is fulfilled.
- The requester receives confirmation of completion.
- Defined test cases demonstrate the expected workflow behavior.

## Assumptions and Constraints

### Assumptions
- The fictional organization already has defined IT Support, IAM, and Security functions.
- Hiring managers are authorized to initiate onboarding requests.
- Standard employee information is available before a request is submitted.

### Constraints
- Development is performed in a ServiceNow Personal Developer Instance.
- The project does not have acces to production enterprise systems.
- Test data will be functional.
- Version 1 will use ServiceNow-native functionality wherever practical.
