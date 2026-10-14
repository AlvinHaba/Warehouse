# Feature 14: User Accounts and Role Permissions

**Feature ID:** 14  
**Branch:** `feature/14-user-accounts-and-role-permissions`  
**Status:** Draft  
**Created:** 2026-10-12

**Purpose:** Control who can access the warehouse system and which actions they may perform. This feature formalizes the open question in Feature 5 about employee sign-in and permissions.

**Depends on:** [Feature 1 — Item Management](feature-1-item-management.md), [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 4 — Customer Maintenance](feature-4-customer-maintenance.md), [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 6 — Supplier Maintenance](feature-6-supplier-maintenance.md), [Feature 7 — Supplier Orders](feature-7-supplier-orders.md), [Feature 8 — Receiving](feature-8-receiving.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md), [Feature 10 — Order Processing](feature-10-order-processing.md), [Feature 11 — Order Fulfillment](feature-11-order-fulfillment.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md), and [Feature 13 — Exception Reporting](feature-13-exception-reporting.md).

## User Stories



### US-14.1: Sign In

**As an:** Authorized employee  
**I want to:** Sign in securely  
**So that:** Only approved staff can use the system.

### US-14.2: Apply Role Permissions

**As an:** Owner or system administrator  
**I want to:** Assign permitted actions to each role  
**So that:** Employees can access only the functions needed for their work.

## Functional Requirements

- **FR-001:** The system MUST allow an authorized user to sign in and sign out.
- **FR-002:** The system MUST store passwords securely using a suitable password-hashing method. It MUST NOT store plain-text passwords.
- **FR-003:** The system MUST support the roles Owner, Manager, Office Employee, Warehouse Employee, and Truck Driver.
- **FR-004:** The system MUST check permissions before protected actions, including deleting records, editing orders, resolving exceptions, and confirming deliveries.
- **FR-005:** The system MUST reject unauthenticated access to protected functions.
- **FR-006:** The system MUST allow an authorized Owner or Manager to deactivate a user without deleting historical work records.
- **FR-007:** The system MUST display a clear message when access is denied.



## Key Entities



### User Account

A user account stores the information needed to identify a user, authenticate them, and determine their access permissions.

### Role

A role represents a named set of permissions that determines which functions a user can access.

## Initial Data Model



### `user_accounts` Table


| Field          | Description                                                                   |
| -------------- | ----------------------------------------------------------------------------- |
| `id`           | Primary key identifying the user account                                      |
| `username`     | Required, unique login identifier                                             |
| `passwordHash` | Required securely hashed password                                             |
| `role`         | Required role assigned to the user                                            |
| `employeeId`   | Nullable foreign key linking to an employee record                            |
| `active`       | Required Boolean indicating whether the account is active; defaults to `true` |




## Gherkin Acceptance Criteria



### Scenario 1: Active User Signs In

**Given** an active user account exists  
**When** the user provides valid sign-in details  
**Then** the system grants access permitted for that role.

### Scenario 2: Unauthenticated Access Is Rejected

**Given** a user is not authenticated  
**When** the user tries to open a protected function  
**Then** the system denies access.

### Scenario 3: Truck Driver Cannot Edit Supplier Records

**Given** a user has the Truck Driver role  
**When** the user tries to edit a supplier  
**Then** the system denies the action  
**And** displays a clear access-denied message.

### Scenario 4: Deactivated User Cannot Sign In

**Given** a user account has been deactivated  
**When** the user attempts to sign in  
**Then** the system denies access  
**And** preserves the user's historical work records.

### Scenario 5: User Signs Out

**Given** an authenticated user is signed in  
**When** the user selects the sign-out option  
**Then** the system ends the user's authenticated session  
**And** requires authentication before the user can access protected functions again.