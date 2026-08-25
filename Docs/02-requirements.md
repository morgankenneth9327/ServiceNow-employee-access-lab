# Solution Requirements

## Functional Requirements

| ID | Requirement |
| --- | --- |
| FR-001 | The system shall allow the Hiring Manager to submit an onboarding request. | 
| FR-002 | The system shall provide the Hiring Manager with a form to collect the employee’s identity/basic information, department, manager, job title, and start date. |
| FR-003 | The system shall allow the Hiring Manager to select required equipment and system access. | 
| FR-004 | The system shall require the Hiring Manager to provide a business justification if privileged access is required. |
| FR-005 | The system shall prevent privileged-access fulfillment from proceeding until the Security Approver grants approval. | 
| FR-006 | The system shall route any hardware/equipment request to IT Support for fulfillment. | 
| FR-007 | The system shall route any identity/access requests to the IAM Analyst for fulfillment. | 
| FR-008 | The system shall prevent a privileged-access request rejected by the Security Approver from proceeding to fulfillment. |
| FR-009 | The system shall notify the Hiring Manager of the completion of their request. |

## Non-Functional Requirements

| ID | Requirement |
| --- | --- |
| NFR-001 | Test data used in the solution shall be fictional and shall not contain real employee information. | 
| NFR-002 | The solution shall record who approved or denied a privileged access request, whether the request was approved or denied, and when the decision was made. |
| NFR-003 | The solution shall use native ServiceNow functionality rather than custom scripting when native functionality satisfies the requirement. |

## Notes
