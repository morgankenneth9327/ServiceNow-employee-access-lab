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

* Established the Employee Access & Onboarding scoped application for application-owned solution artifacts.
* Established a dedicated global Update Set for Service Catalog configuration.
* Separated scoped application artifacts from global catalog configuration in accordance with the documented architecture.
