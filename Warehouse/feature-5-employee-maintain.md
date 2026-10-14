# Feature: Employee Maintain

**Feature ID:** 5  
**Branch pattern:** `feature/5-employee-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Maintain (add, edit, delete) employees in three types: office people, warehouse people, and truck drivers.  
**Related:** [Feature 6 — Route Maintenance](feature-6-route-maintenance.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md)

## User Stories

### US-5.1: Add an employee
**As an** office employee  
**I want to** add an employee with a role  
**So that** work can be attributed to the right person and type  

**Priority:** P1  
**Independent test:** Create an employee with role TRUCK_DRIVER, then retrieve and confirm the role.  
**Acceptance scenarios:** see ### US-5.1 under Acceptance Criteria

### US-5.2: Edit an employee
**As an** office employee  
**I want to** change an employee's details or role  
**So that** records match job changes  

**Priority:** P1  
**Independent test:** Change an employee's role from WAREHOUSE to OFFICE and confirm it.  
**Acceptance scenarios:** see ### US-5.2 under Acceptance Criteria

### US-5.3: Delete an employee
**As an** office employee  
**I want to** remove an employee who has left  
**So that** only current staff can be assigned work  

**Priority:** P2  
**Independent test:** Delete an employee with no recorded activity and confirm it is gone.  
**Acceptance scenarios:** see ### US-5.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee add an employee with a name and a role of OFFICE, WAREHOUSE, or TRUCK_DRIVER; both are required.
- **FR-002**: System MUST let an office employee edit an employee's name and role.
- **FR-003**: System MUST NOT delete an employee referenced by any order, adjustment, shipment, or delivery record.
- **FR-004**: System MUST list and filter employees by role in a stable order.
- **FR-005**: System MUST only allow TRUCK_DRIVER employees to be assigned to shipments and deliveries.
- **FR-006**: [NEEDS CLARIFICATION: Do employees log in? Whiteboard defines roles but no authentication or permissions.]

## Key Entities

- **Employee**: a person working for the warehouse; has a name and exactly one role (office, warehouse, truck driver).

## Initial Data Model

**employees**

| Column | Notes |
|---|---|
| id | PK |
| name | required |
| role | required, one of OFFICE, WAREHOUSE, TRUCK_DRIVER |
| active | required, boolean, default true |

## Gherkin AC

### US-5.1 — Add an employee

#### Scenario: Office employee adds a truck driver
* **Given** an office employee is working in the system
* **When** the employee saves a new employee with a name and role TRUCK_DRIVER
* **Then** the employee is stored with role TRUCK_DRIVER

#### Scenario: Invalid role is rejected
* **Given** an office employee is working in the system
* **When** the employee saves a new employee with role "MANAGER"
* **Then** the system rejects the save with a validation message

### US-5.2 — Edit an employee

#### Scenario: Office employee changes a role
* **Given** an employee exists with role WAREHOUSE
* **When** the office employee changes the role to OFFICE and saves
* **Then** the employee shows role OFFICE

### US-5.3 — Delete an employee

#### Scenario: Employee with no activity is deleted
* **Given** an employee exists with no recorded activity
* **When** the office employee deletes the employee
* **Then** the employee no longer exists

#### Scenario: Employee with recorded activity cannot be deleted
* **Given** an employee has picked a customer order
* **When** the office employee deletes the employee
* **Then** the system rejects the deletion
