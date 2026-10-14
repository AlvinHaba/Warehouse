# Feature: Order Picking

**Feature ID:** 10  
**Branch pattern:** `feature/10-order-picking`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Warehouse employees pick customer orders from stock and record the quantity picked for each line.  
**Depends on:** [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md)  
**Related:** [Feature 13 — Exception Reporting](feature-13-exception-reporting.md)

## User Stories

### US-10.1: Pick an order
**As a** warehouse employee  
**I want to** see a pick list and record the quantity picked per line  
**So that** the order is assembled accurately and quickly  

**Priority:** P1  
**Independent test:** Pick 8 on a line ordered at 10 and see quantity picked 8 and stock reduced by 8.  
**Acceptance scenarios:** see ### US-10.1 under Acceptance Criteria

### US-10.2: Flag out-of-stock during picking
**As a** warehouse employee  
**I want** the system to flag when I cannot pick the full quantity  
**So that** the shortage is reported instead of silently shipped short  

**Priority:** P1  
**Independent test:** Pick less than ordered and confirm an exception is created.  
**Acceptance scenarios:** see ### US-10.2 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST present a pick list for an order with item, description, location, and quantity ordered per line.
- **FR-002**: System MUST let a warehouse employee record quantity picked per line, from 0 up to quantity ordered.
- **FR-003**: System MUST set the order status to PICKING when the first pick is recorded and PICKED when all lines are finalized.
- **FR-004**: System MUST reduce quantity on hand by the quantity picked.
- **FR-005**: System MUST NOT allow picking more than quantity on hand.
- **FR-006**: System MUST create a shortage exception (see Feature 13) for each line where quantity picked is less than quantity ordered when the line is finalized.
- **FR-007**: System MUST record the picking employee and timestamp per pick.
- **FR-008**: System MUST NOT allow picking an order that is CANCELLED or not yet RECEIVED.

## Key Entities

- **Customer Order / Customer Order Line**: see Feature 9; this feature updates quantity picked and status.
- **Pick**: a record of a quantity picked for one order line by one employee.
- **Inventory Item**: see Feature 3; quantity on hand decreases on pick.

## Initial Data Model

**picks**

| Column | Notes |
|---|---|
| id | PK |
| customerOrderLineId | required, FK → customer_order_lines.id |
| pickedByEmployeeId | required, FK → employees.id |
| quantityPicked | required, integer > 0 |
| pickedAt | required, timestamp |

**customer_order_lines** adds `pickFinalized` (boolean, default false). `quantityPicked` is the running total of picks.

## Gherkin AC

### US-10.1 — Pick an order

#### Scenario: Warehouse employee picks a full line
* **Given** an order line has quantity ordered 10 and the item has quantity on hand 50
* **When** the employee records 10 picked
* **Then** quantity picked is 10
* **And** quantity on hand is 40

#### Scenario: Picking more than ordered is rejected
* **Given** an order line has quantity ordered 10
* **When** the employee records 11 picked
* **Then** the system rejects the pick with a validation message

#### Scenario: Picking more than on hand is rejected
* **Given** an item has quantity on hand 3 and a line orders 10
* **When** the employee records 5 picked
* **Then** the system rejects the pick with a validation message

#### Scenario: Order becomes PICKED when all lines are finalized
* **Given** an order has two lines
* **When** the employee finalizes both lines
* **Then** the order status is PICKED

### US-10.2 — Flag out-of-stock during picking

#### Scenario: Short pick creates an exception
* **Given** an order line has quantity ordered 10 and only 8 are available
* **When** the employee picks 8 and finalizes the line
* **Then** a shortage exception is created with ordered 10 and picked 8

#### Scenario: Cancelled order cannot be picked
* **Given** an order has status CANCELLED
* **When** the employee records a pick
* **Then** the system rejects the pick
