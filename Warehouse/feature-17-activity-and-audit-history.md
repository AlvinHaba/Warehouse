# Feature 17: Activity and Audit History

**Feature ID:** 17  
**Branch:** `feature/17-activity-and-audit-history`  
**Status:** Draft  
**Created:** 2026-10-12

**Purpose:** Keep a history of important changes so the warehouse can investigate mistakes and understand who changed a record.

**Depends on:** [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), and [Feature 14 — User Accounts and Role Permissions](feature-14-user-accounts-and-role-permissions.md).

## User Stories

### US-17.1: Review Record Changes

**As a:** Manager  
**I want to:** See who changed an important record and when  
**So that:** I can investigate incorrect information.

## Functional Requirements

- **FR-001:** The system MUST record the actor, action, record type, record ID, and timestamp for important changes.
- **FR-002:** Audit entries MUST be created for inventory adjustments, order status changes, receipt finalization, shipping, delivery confirmation, and exception resolution.
- **FR-003:** Each audit entry SHOULD include a brief summary of the changed fields without storing passwords or other secrets.
- **FR-004:** Ordinary users MUST NOT edit or delete audit entries.
- **FR-005:** Authorized users MUST be able to search audit history by date, actor, action, and record ID.
- **FR-006:** Audit history MUST respect access permissions.

## Key Entities

### Audit Entry

An audit entry records who performed an action, what action was performed, which record was affected, when the action occurred, and a safe summary of the change.

## Initial Data Model

### `audit_entries` Table


| Field         | Description                                               |
| ------------- | --------------------------------------------------------- |
| `id`          | Primary key identifying the audit entry                   |
| `actorUserId` | Foreign key referencing `user_accounts.id` when available |
| `action`      | Required action performed                                 |
| `recordType`  | Required type of record affected                          |
| `recordId`    | Required ID of the affected record                        |
| `summary`     | Optional summary of the change                            |
| `createdAt`   | Required timestamp recording when the action occurred     |


## Gherkin Acceptance Criteria

### Scenario 1: Inventory Adjustment Is Recorded

**Given** a warehouse employee adjusts inventory  
**When** the adjustment is saved  
**Then** an audit entry records who made the change and when.

### Scenario 2: Audit History Cannot Be Edited

**Given** an audit entry exists  
**When** an ordinary user tries to change it  
**Then** the system denies the action.

### Scenario 3: Authorized User Searches Audit History

**Given** audit entries exist in the system  
**When** an authorized user searches by date, actor, action, or record ID  
**Then** the system displays matching audit entries  
**And** the results respect the user's access permissions.



