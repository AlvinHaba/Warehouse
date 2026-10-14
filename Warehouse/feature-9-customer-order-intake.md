# Feature: Customer Order Intake

**Feature ID:** 9  
**Branch pattern:** `feature/9-customer-order-intake`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Receive orders from customers and record them on a customer order form for the warehouse to pick.  
**Depends on:** [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 4 — Customer Maintenance](feature-4-customer-maintenance.md)  
**Related:** [Feature 10 — Order Picking](feature-10-order-picking.md)

## User Stories

### US-9.1: Record a customer order
**As an** office employee  
**I want to** enter an order received from a customer  
**So that** the warehouse can pick and ship it  

**Priority:** P1  
**Independent test:** Create an order for an existing customer with two lines and retrieve it in status RECEIVED.  
**Acceptance scenarios:** see ### US-9.1 under Acceptance Criteria

### US-9.2: Edit or cancel an order before picking
**As an** office employee  
**I want to** change or cancel a customer order that has not started picking  
**So that** corrections do not reach the warehouse floor late  

**Priority:** P2  
**Independent test:** Change a line quantity on a RECEIVED order; confirm it is rejected once the order is PICKING.  
**Acceptance scenarios:** see ### US-9.2 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee create a customer order for an existing customer with one or more lines of item and quantity ordered.
- **FR-002**: System MUST require each quantity ordered to be a positive integer and each item to exist.
- **FR-003**: System MUST set a new order's status to RECEIVED and record the receiving employee and timestamp.
- **FR-004**: System MUST allow order creation even when stock on hand is below the quantity ordered, and MUST show current quantity on hand next to each line. [NEEDS CLARIFICATION: confirm orders may exceed stock, or must be rejected]
- **FR-005**: System MUST let an office employee edit or cancel an order only while its status is RECEIVED.
- **FR-006**: System MUST list customer orders by status and customer in a stable order.

## Key Entities

- **Customer Order**: a request from one Customer for items; has status and lines. Corresponds to the Customer Order Form.
- **Customer Order Line**: an Item with quantity ordered, quantity picked, and quantity delivered.
- **Customer / Item**: see Features 4 and 2.

## Initial Data Model

**customer_orders**

| Column | Notes |
|---|---|
| id | PK |
| customerId | required, FK → customers.id |
| receivedByEmployeeId | required, FK → employees.id |
| status | required, one of RECEIVED, CANCELLED, PICKING, PICKED, SHIPPED, DELIVERED |
| receivedAt | required, timestamp |

**customer_order_lines**

| Column | Notes |
|---|---|
| id | PK |
| customerOrderId | required, FK → customer_orders.id |
| itemId | required, FK → items.id |
| quantityOrdered | required, integer > 0 |
| quantityPicked | required, integer >= 0, default 0 (Feature 10) |
| quantityDelivered | required, integer >= 0, default 0 (Feature 12) |

## Gherkin AC

### US-9.1 — Record a customer order

#### Scenario: Office employee records a valid order
* **Given** customer "Mill Cafe" and two items exist
* **When** the employee saves an order for "Mill Cafe" with a line for each item
* **Then** the order is stored with status RECEIVED
* **And** the order records the receiving employee and time

#### Scenario: Order for an unknown customer is rejected
* **Given** no customer exists with the chosen id
* **When** the employee saves an order for that customer
* **Then** the system rejects the save with a validation message

#### Scenario: Order with no lines is rejected
* **Given** a customer exists
* **When** the employee saves an order with no lines
* **Then** the system rejects the save with a validation message

#### Scenario: Order exceeding stock is accepted and flagged
* **Given** an item has quantity on hand 5
* **When** the employee saves an order line for 10 of that item
* **Then** the order is stored
* **And** the line shows quantity on hand 5

### US-9.2 — Edit or cancel an order before picking

#### Scenario: Order is edited while RECEIVED
* **Given** an order has status RECEIVED
* **When** the employee changes a line quantity and saves
* **Then** the line shows the new quantity

#### Scenario: Order cannot be edited once picking starts
* **Given** an order has status PICKING
* **When** the employee changes a line quantity
* **Then** the system rejects the change
