# PDI Performance Troubleshooting and Recovery

## Purpose

This document records a ServiceNow Personal Developer Instance (PDI) performance incident encountered during implementation of the Employee Access & Onboarding lab. It documents the observed symptoms, troubleshooting process, evidence used to isolate the issue, backup and recovery actions, and the final migration to a replacement PDI.

The goal is to preserve both the technical evidence and a reusable troubleshooting approach for future ServiceNow instance-performance issues.

## Incident Summary

During implementation on PDI `dev421826`, normal ServiceNow navigation and development operations became intermittently and then persistently slow. Delays affected unrelated platform functionality as well as the project application, indicating that the problem was not isolated to one catalog item, script, flow, or scoped application artifact.

Observed symptoms included:

- Routine navigation taking several seconds or longer.
- Application and Studio operations occasionally taking tens of seconds.
- Transaction Log entries showing server-side requests in the 16–32 second range, with an observed maximum near 96 seconds.
- Slow behavior across unrelated ServiceNow functionality, including Studio and global REST activity.
- Temporary improvement after a cache flush, followed by recurrence.

## Environment

### Original PDI

| Item | Value |
| --- | --- |
| Instance | `dev421826` |
| Release | Australia |
| Build | `glide-australia-02-11-2026__patch3-05-25-2026` |
| Cluster node | `app132010.ord191.service-now.com:dev421826001` |
| Node ID | `e9b55de65e13f20e6dced40aac0edae9` |
| Processor count | 72 |

### Replacement PDI

| Item | Value |
| --- | --- |
| Instance | `dev200255` |
| Release | Australia |
| Build | `glide-australia-02-11-2026__patch3-05-25-2026` |
| Cluster node | `app130045.ord191.service-now.com:dev200255002` |
| Node ID | `7c2aecc97bb229ea151157bc6f89136d` |
| Processor count | 64 |

The replacement instance used the same Australia Patch 3 build family, which provided an important comparison point when determining whether the release/patch itself was the primary cause.

## Troubleshooting Chronology

### 1. Confirmed that the issue was server-side

The ServiceNow Transaction Log was reviewed to distinguish browser/network delay from server-side transaction delay.

Representative slow transactions included requests around:

- 16.6 seconds
- 27.9 seconds
- 32.2 seconds
- approximately 95.8 seconds maximum during the observed period

The delays affected unrelated applications and platform services, making a single project customization an unlikely explanation.

### 2. Reviewed `stats.do`

The ServiceNow `stats.do` diagnostic page was used repeatedly to capture host, servlet, semaphore, response-time, database, and scheduler information.

Early snapshots on the original PDI showed Linux load averages approximately:

`784 / 809 / 789`

on a 72-processor host.

A later pre-reset snapshot still showed approximately:

`540 / 582 / 606`

Linux load average is not the same as CPU-utilization percentage. However, when combined with the very high user-facing response times and the absence of database or scheduler saturation, the sustained values were evidence of severe host/resource contention.

### 3. Flushed the instance cache

A cache flush was performed using `cache.do`.

Servlet memory improved substantially immediately after the flush:

| Measurement | Before | After |
| --- | ---: | ---: |
| Memory in use | ~1379 MB | ~626 MB |
| Free percentage | ~31% | ~68% |

Interactive performance also improved temporarily.

Because the issue later returned, the cache state was treated as a contributing condition or temporary relief mechanism rather than the underlying root cause.

### 4. Checked semaphore, database, and scheduler health

Multiple `stats.do` snapshots showed:

- Little or no Default semaphore queue depth at the time of capture.
- Database connection pools with available capacity.
- No database retry time or rejected connection expansion.
- Background scheduler queue length at or near zero.
- Scheduler workers frequently idle.

These observations reduced the likelihood that the primary issue was a database connection-pool bottleneck, scheduler backlog, or application-generated semaphore saturation.

### 5. Captured a pre-reset baseline

Immediately before destructive recovery work, another `stats.do` snapshot showed that the condition persisted on the same node.

Representative user-initiated response times included:

- 5 minute average: approximately 10.5 seconds
- 15 minute average: approximately 4.4 seconds
- 60 minute average: approximately 3.6 seconds

The host load remained several hundred on the 72-processor system.

### 6. Backed up the project before destructive testing

Before resetting the PDI, the project was protected through multiple recovery mechanisms.

#### Scoped application

The **Employee Access & Onboarding** scoped application was linked to a dedicated GitHub source-control repository:

`morgankenneth9327/ServiceNow-employee-access-app`

The source-controlled application included the scoped application record and application-owned artifacts.

#### Additional recovery exports

XML backups were retained for supporting records that were not fully represented by scoped application source control, including:

- `sys_user`
- `sys_user_group`
- `sys_user_grmember`
- `sc_category`
- `sc_cat_item`
- `item_option_new`
- `sc_cat_item_category`

An exported update-set XML was also retained as a fallback recovery artifact.

### 7. Reset and wiped the original PDI

The PDI was reset while retaining the instance name.

The reset restarted/rebuilt the instance, but the instance returned on the same cluster node and Node ID. Performance remained poor.

A post-reset snapshot showed:

- Load average: approximately `682 / 681 / 603`
- User-initiated response time, 1 minute: approximately 15.8 seconds
- User-initiated response time, 5 minutes: approximately 6.1 seconds
- Database latency: 14 ms
- Database pool and scheduler still not saturated

Because the poor performance survived a wipe/reset, custom project configuration was further ruled out as the primary cause.

### 8. Released the PDI and requested a new Australia instance

The Developer Site also indicated that a newly provisioned Australia PDI was required to enable Build Agent.

Because the reset had not changed the underlying node and had not corrected the performance issue, the original PDI was released and a new Australia PDI was requested.

The replacement instance was provisioned as `dev200255` on a different host and Node ID.

### 9. Validated the replacement PDI before restoration

The first `stats.do` snapshot on `dev200255` showed dramatically improved behavior:

- Database latency: 1 ms
- User-initiated response time, 1 minute: approximately 590 ms
- User-initiated response time, 5 minutes: approximately 480 ms
- Default response time, 5 minutes: approximately 611 ms
- No meaningful semaphore, database, or scheduler backlog

The replacement PDI was also running Australia Patch 3, demonstrating that the patch level alone did not explain the original instance's severe slowness.

## Root-Cause Assessment

### Evidence-supported conclusion

The most likely cause was **host-level or shared-infrastructure resource contention affecting the original PDI's ServiceNow node**.

The exact underlying infrastructure fault cannot be determined from PDI-level telemetry alone, so the incident should not be documented as a proven CPU, database, or hardware failure.

The conclusion is based on the combined evidence that:

1. Slow transactions affected unrelated ServiceNow functionality.
2. Database and scheduler health did not show corresponding saturation.
3. Cache flushing provided only temporary relief.
4. A full PDI reset retained the same node and did not resolve the issue.
5. A replacement PDI on a different host performed normally.
6. Both the unhealthy and healthy PDIs used the same Australia Patch 3 build family.

### Causes not supported by the evidence

The incident did **not** provide evidence that the primary cause was:

- The Employee Access & Onboarding application.
- The catalog item or its variables.
- A project-specific Business Rule or flow.
- Browser choice.
- Database connection-pool exhaustion.
- Background scheduler backlog.
- Australia Patch 3 by itself.

## Recovery and Rebuild

After validating the replacement PDI, the project was restored in dependency order.

### Scoped application restore

The scoped application was imported from the dedicated GitHub repository and a new working branch was established:

`sn_instances/dev200255`

### Supporting-record restore

Supporting XML records were restored in the following sequence:

1. Project test users
2. Project groups
3. Group memberships
4. Employee Services catalog category
5. Employee Onboarding & Access Request catalog item
6. Catalog variables

The item/category relationship was verified after restoration and did not require an additional manual repair.

### Validation

Post-restore validation confirmed:

- All four EAO test users were present.
- EAO fulfillment/approval groups were present.
- Group memberships were restored.
- The Employee Services category was present.
- The Employee Onboarding & Access Request catalog item was active.
- All 10 catalog variables were restored in their intended order.
- The catalog item was associated with Employee Services.
- The requester-facing **Try It** view rendered successfully.

## Post-Restore Performance Baseline

After the application and supporting records were restored, `dev200255` remained responsive.

Representative post-restore values:

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

The replacement host later showed a higher Linux load average than its initial provisioning snapshot, but ServiceNow response times, database health, semaphores, and scheduler behavior remained normal. This reinforced the need to interpret Linux load average together with application-level performance measurements rather than as CPU utilization by itself.

## Resolution

**Resolved by migrating the project to a newly provisioned PDI on a different ServiceNow host.**

The original instance was not repaired in place. Resetting the PDI did not change its host placement and did not correct the performance issue.

## Reusable Troubleshooting Runbook

For future PDI performance problems:

1. Reproduce the issue using more than one ServiceNow function.
2. Review Transaction Log to determine whether delay is server-side.
3. Capture `stats.do` before making changes.
4. Check servlet memory and cache state.
5. Check Default semaphore queue depth and concurrency.
6. Check database latency, busy/available connections, retries, and resource usage.
7. Check background scheduler queue length and worker state.
8. Treat Linux load average as a contention signal, not CPU-utilization percentage.
9. If cache flushing helps, determine whether the improvement persists.
10. Back up scoped application metadata through source control and export supporting non-source-controlled records before destructive actions.
11. If a reset is performed, compare node, Node ID, build, and response-time metrics before and after the reset.
12. If the same infrastructure remains unhealthy after reset, consider replacement/reprovisioning rather than repeatedly rebuilding the same PDI.
13. Validate a replacement instance before restoring project data.
14. Restore records in dependency order and perform a post-restore functional and performance validation.

## Outcome

The incident became a useful part of the portfolio project because it required more than configuration work. The troubleshooting process demonstrated:

- ServiceNow transaction analysis
- `stats.do` interpretation
- Cache and servlet-memory troubleshooting
- Semaphore, database, and scheduler analysis
- Infrastructure-versus-application fault isolation
- Source-control-based recovery
- XML backup and restoration
- PDI migration
- Post-migration functional and performance validation
