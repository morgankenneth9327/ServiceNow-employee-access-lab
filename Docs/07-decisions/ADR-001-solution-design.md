## ADR-001 — Native Catalog Request Model for Version 1

### Status

Accepted

### Context

Version 1 of the Employee Access & Onboarding solution must support standardized request intake, privileged-access approval, hardware and access fulfillment, request-status visibility, completion tracking, and requester notification.

ServiceNow already provides a native Service Catalog request model using catalog items, Requested Item records, and Catalog Tasks. The alternative would be to create custom onboarding and fulfillment tables with custom relationships and lifecycle behavior.

### Decision

Version 1 will use the native Service Catalog request model rather than introducing custom onboarding and fulfillment tables.

### Rationale

The native request model satisfies the documented Version 1 requirements without introducing additional data-model and lifecycle complexity.

This approach also aligns with NFR-003, which directs the solution to prefer native ServiceNow functionality when it can satisfy the requirement.

Custom tables or more specialized data structures may be introduced in later versions if implementation experience or validated business requirements demonstrate a need that the native model cannot adequately address.

### Alternatives Considered

* Custom onboarding request table
* Custom IT fulfillment table
* Custom IAM fulfillment table
* Hybrid model using a custom parent record with native fulfillment tasks

These alternatives provide greater control over the data model but would introduce additional configuration, relationships, lifecycle logic, security considerations, and maintenance overhead before a documented need has been established.

### Consequences

**Positive**

* Faster and simpler Version 1 implementation
* Better alignment with native ServiceNow behavior
* Reduced custom configuration and maintenance
* Lower risk of designing unnecessary structures before requirements are validated

**Negative**

* The native request model may be less specialized than a custom onboarding data model
* Future requirements may require additional configuration or migration to more specialized structures
