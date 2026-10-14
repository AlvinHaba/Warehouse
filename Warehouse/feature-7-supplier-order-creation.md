# Feature: Supplier Order Creation

**Feature ID:** 7  
**Branch pattern:** `feature/7-supplier-order-creation`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Create orders to suppliers, with order quantities calculated by a formula from stock levels, and send them electronically.  
**Depends on:** [Feature 1 — Supplier Maintenance](feature-1-supplier-maintenance.md), [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md)  
**Related:** [Feature 8 — Supplier Order Receiving](feature-8-supplier-order-receiving.md)

## User Stories

### US-7.1: Calculate a suggested order
**As an** office employee  
**I want to** have the system calculate how much of each item to order  
**So that** we order accurately without manual math  

**Priority:** P1  
**Independent test:** With quantity 20, reorder point 30, target 100, the calculation suggests 80.  
**Acceptance scenarios:** see ### US-7.1 under Acceptance Criteria

### US-7.2: Create a supplier order
**As an** office employee  
**I want to** create a supplier order with lines for items from that supplier  
**So that** the warehouse is restocked  

**Priority:** P1  
**Independent test:** Create an order with two lines for one supplier and retrieve it in status DRAFT.  
**Acceptance scenarios:** see ### US-7.2 under Acceptance Criteria

### US-7.3: Send a supplier order electronically
**As an** office employee  
**I want to** send the order to the supplier electronically  
**So that** the supplier receives it immediately  

**Priority:** P1  
**Independent test:** Send a DRAFT order; the stub sender receives it and the order becomes SENT.  
**Acceptance scenarios:** see ### US-7.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST calculate a suggested quantity per item as target level minus quantity on hand for each item whose quantity on hand is at or below its reorder point. [NEEDS CLARIFICATION: whiteboard says "Calculation/Formula" without defining it; confirm this formula and whether open (unreceived) order quantities are subtracted]
- **FR-002**: System MUST let an office employee create a supplier order for exactly one supplier, with one or more lines of item and quantity ordered.
- **FR-003**: System MUST require every line's item to be supplied by the order's supplier.
- **FR-004**: System MUST require each quantity ordered to be a positive integer.
- **FR-005**: System MUST let an office employee edit or discard an order while it is DRAFT.
- **FR-006**: System MUST send a DRAFT order electronically to the supplier's order email and then set status to SENT with a sent timestamp.
- **FR-007**: System MUST NOT modify an order after it is SENT.
- **FR-008**: System MUST leave the order in DRAFT and report an error when electronic sending fails.
- **FR-009**: Payment to suppliers is out of scope. [per whiteboard: "Don't worry about pay"]

## Key Entities

- **Supplier Order**: a purchase request to one Supplier; has status and lines. Corresponds to the Supplier Order Form.
- **Supplier Order Line**: an Item and the quantity ordered on a Supplier Order.
- **Supplier / Item / Inventory Item**: see Features 1–3.

## Initial Data Model

**supplier_orders**

| Column | Notes |
|---|---|
| id | PK |
| supplierId | required, FK → suppliers.id |
| createdByEmployeeId | required, FK → employees.id |
| status | required, one of DRAFT, SENT, PARTIALLY_RECEIVED, RECEIVED |
| createdAt | required, timestamp |
| sentAt | nullable, timestamp |

**supplier_order_lines**

| Column | Notes |
|---|---|
| id | PK |
| supplierOrderId | required, FK → supplier_orders.id |
| itemId | required, FK → items.id |
| quantityOrdered | required, integer > 0 |
| quantityReceived | required, integer >= 0, default 0 (set in Feature 8) |

## Gherkin AC

### US-7.1 — Calculate a suggested order

#### Scenario: Suggestion for an item below its reorder point
* **Given** an item has quantity 20, reorder point 30, and target level 100
* **When** the employee requests a suggested order
* **Then** the suggestion includes that item with quantity 80

#### Scenario: Item above its reorder point is not suggested
* **Given** an item has quantity 60, reorder point 30, and target level 100
* **When** the employee requests a suggested order
* **Then** the suggestion does not include that item

### US-7.2 — Create a supplier order

#### Scenario: Office employee creates a draft order
* **Given** items exist that are supplied by "Acme Foods"
* **When** the employee creates an order for "Acme Foods" with two valid lines
* **Then** the order is stored with status DRAFT and two lines

#### Scenario: Line with an item from another supplier is rejected
* **Given** an item is supplied by "Other Co"
* **When** the employee adds it to an order for "Acme Foods"
* **Then** the system rejects the line with a validation message

#### Scenario: Zero quantity is rejected
* **Given** a draft order exists
* **When** the employee adds a line with quantity 0
* **Then** the system rejects the line with a validation message

### US-7.3 — Send a supplier order electronically

#### Scenario: Draft order is sent
* **Given** a DRAFT order exists for a supplier with an order email
* **When** the employee sends the order
* **Then** the order is delivered to the supplier's email
* **And** the order status is SENT with a sent timestamp

#### Scenario: Sent order cannot be edited
* **Given** an order has status SENT
* **When** the employee tries to change a line quantity
* **Then** the system rejects the change

#### Scenario: Failed send leaves the order as draft
* **Given** a DRAFT order exists and electronic sending is unavailable
* **When** the employee sends the order
* **Then** the system reports an error
* **And** the order status remains DRAFT
