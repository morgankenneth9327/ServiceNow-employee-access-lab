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
