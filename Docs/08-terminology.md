# Project Terminology
| Term | Working meaning |
| --- | --- |
|Project Charter | High-level document establishing the purpose, objectives, scope, stakeholders, and success conditions of a project. |
| Business Problem | The undesirable current condition that creates a reason for the project. |
| Project Objective | The result the project is intended to produce. |
| Scope | The defined boundaries of work included in a project. |
| Out of Scope | Work deliberately excluded from the current project or release. | 
| Scope Creep | Uncontrolled expansion of project work beyond the agreed scope. | 
| Stakeholder | A person or group with an interest in or influence over the project’s outcome. |
| Persona | A representative type of user who interacts with the solution. |
| Requester | The person initiating a request. | 
| Fulfillment | Work performed to satisfy an approved request. | 
| Privileged Access | Access granting permissions beyond those of a normal user. | 
| Business Justification | The stated business reason explaining why something is required. |
| Success Criterion | An observable condition used to determine whether the project achieved its objective. | 
| Assumption | Something treated as true when planning or designing the project. | 
| Constraint | A limitation within which the project must operate. | 
| Validation | Checking that a configuration or solution produces the intended result. | 
| Requirement | A condition or capability the solution must satisfy. |
| Atomic Requirement | A requirement describing one independently understandable and testable behavior or condition. |
| Approval Gate | A control point that prevents subsequent work from proceeding until a required approval is granted. |
| Happy Path | The normal successful sequence through a process when expected conditions are met. |
| Requirements Refinement | The process of making broad requirements more precise, unambiguous, and testable. |
| Functional Requirement (FR) | A requirement defining a specific behavior or capability the system must provide. |
| Non-Functional Requirement (NFR) | A requirement defining a quality, constraint, or operating characteristic of a solution rather than a specific business behavior. |
| Requirements Traceability | The ability to connect a requirement to its implementation, validation, and supporting evidence. |
| Auditability | The ability to reconstruct or verify system activity using retained records or evidence. |
| Process Flow | A representation of the sequence of activities, decisions, and handoffs that make up a business process. |
| Decision Point | A point in a process where a defined condition determines which path the process follows. |
| Parallel Activity | Process activities that may proceed independently rather than requiring one to finish before another begins. |
| Alternate Path | A valid, expected variation from the primary or happy path of a process. |
| Exception Path | A path describing abnormal conditions or failures that interrupt normal process behavior. |
| Handoff | The transfer of responsibility or work from one actor, team, or process activity to another. |
| Architecture / Solution Architecture | The high-level technical structure of a solution, including its major components, responsibilities, interactions, and design decisions. |
| Architectural Goal | A high-level design principle or objective used to guide architectural decisions. |
| Logical Architecture | A technology-independent representation of the major responsibilities and interactions required within a solution. |
| Logical Component | A part of the solution defined by a distinct responsibility rather than by a specific application, table, or platform feature. |
| Component Responsibility | The behavior or capability that a particular architectural component is accountable for providing. |
| Persistent State | Information retained over time that represents the current condition or lifecycle position of a system object or record. |
| Process Orchestration | Coordination of conditions, decisions, activities, and dependencies that determines how a process advances. |
| Data Flow | The movement of information between solution components. |
| Control Flow | The sequence of decisions, events, or conditions that determines which activities occur and when they occur. |
| Implementation Mapping | The translation of logical architectural components into specific platform capabilities or technical implementations. |
| Requested Item (RITM) | A ServiceNow record representing an individual requested catalog item and its lifecycle through fulfillment. |
| Catalog Task | A ServiceNow fulfillment task associated with a Requested Item and assigned to the team responsible for completing specific work. |
| Architecture Decision Record (ADR) | A document recording an important architectural decision, its context, rationale, alternatives, and consequences. |
| Separation of Responsibilities | A design principle in which distinct responsibilities, such as approval and fulfillment, are assigned to separate solution components or actors. |
| Cross-Cutting Requirement | A requirement or constraint that applies across multiple components or stages of a solution rather than to one isolated function. |
| Personal Developer Instance (PDI) | A ServiceNow developer instance used for learning, development, and testing outside a production environment. |
| Scoped Application | A ServiceNow application whose configuration and application files are contained within a defined application scope. |
| Application Scope | The namespace and boundary that identifies which application owns a ServiceNow artifact and controls access between applications. |
| Service Catalog | The ServiceNow capability used to present requestable products and services to users. |
| Catalog Item | A requestable Service Catalog offering that defines the form and fulfillment process for a particular service or request. |
| Catalog Variable | A field on a Catalog Item used to collect information from the requester. |
| Catalog UI Policy | A ServiceNow configuration that dynamically controls Catalog Variable behavior such as visibility or mandatory state without requiring custom client scripting. |
| Workflow Studio | The ServiceNow development interface used to create and manage flows, subflows, actions, and process automation. |
| Flow | A ServiceNow automation composed of a trigger followed by actions and flow logic. |
| Flow Trigger | The event or condition that starts execution of a flow. |
| Data Pill | A selectable representation of data produced by a trigger or prior flow action that can be used as input elsewhere in a flow. |
| Ask for Approval | A Flow Designer / Workflow Studio action that creates and waits for approval decisions against a specified record. |
| Approval State | The recorded status of an approval process, such as requested, approved, or rejected. |
| Assignment Group | The team or group responsible for performing work on a ServiceNow task or record. |
| Assigned To | The individual user currently responsible for performing the work on a ServiceNow task or record. |
| Update Set | A ServiceNow mechanism for collecting configuration changes so they can be moved between instances. |
| Customer Update | An individual configuration-change record captured within an Update Set. |
| Source Control | Version-control integration used to store and track ServiceNow scoped-application source in an external Git repository. |
| Execution Plan | A legacy/catalog fulfillment mechanism that can generate fulfillment tasks for a Catalog Item independently of a Flow or Workflow. |
| Flow Reporting | ServiceNow execution-detail collection that records runtime information for flow troubleshooting and validation. |
| Flow Context | A runtime execution instance of a ServiceNow flow, including its current state and execution history. |
| Outbound Email (`sys_email`) | A ServiceNow email record representing a message generated or queued by the platform. |
