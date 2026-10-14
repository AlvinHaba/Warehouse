# Feature: Supplier Order Receiving

**Feature ID:** 8  
**Branch pattern:** `feature/8-supplier-order-receiving`  
**Status:** Draft  
**Input:** Record the quantities that arrive from suppliers and compare them with the quantities ordered.  
**Depends on:** Features 2, 3, and 7  
**Related:** Feature 13 — Exception Reporting

## Purpose
When a supplier delivery arrives, warehouse staff need to record the quantity received for each order line. The system must update inventory and identify short deliveries.

## User Stories
### US-8.1: Receive a supplier order
**As a** warehouse employee  
**I want to** record the quantity received for each item on a supplier order  
**So that** stock records match what physically arrived.

**Priority:** P1  
**Independent test:** Receive 10 units on a line ordered for 10 and verify that the received quantity and inventory are updated.

### US-8.2: Record a partial delivery
**As a** warehouse employee  
**I want to** record when fewer units arrive than were ordered  
**So that** the missing quantity can be investigated.

**Priority:** P1  
**Independent test:** Receive 8 units on a line ordered for 10 and verify a supplier-short exception for 2 units.

## Functional Requirements
- **FR-001:** The system MUST list supplier orders with status SENT or PARTIALLY_RECEIVED that still have units outstanding.
- **FR-002:** The system MUST let a warehouse employee record a received quantity for each order line.
- **FR-003:** Each receipt quantity MUST be an integer greater than or equal to zero.
- **FR-004:** The total quantity received for a line MUST NOT exceed the quantity ordered unless the instructor explicitly approves over-receipts.
- **FR-005:** The system MUST increase the item's quantity on hand by the quantity newly received.
- **FR-006:** The system MUST record the receiving employee and timestamp.
- **FR-007:** The order status MUST be PARTIALLY_RECEIVED if some, but not all, ordered units have been received; it MUST be RECEIVED when all ordered units have been received.
- **FR-008:** If a line is finalized with fewer units received than ordered, the system MUST create a SUPPLIER_SHORT exception as described in Feature 13.
- **FR-009:** The system MUST prevent the same receipt from being applied twice.
- **FR-010:** Supplier payment is out of scope.

## Key Entities
- **Supplier Order:** the order sent to a supplier.
- **Supplier Order Line:** the item, ordered quantity, and received quantity.
- **Receipt:** a record of a receiving action, employee, time, and quantities received.
- **Inventory Item:** the stock record updated when goods arrive.
- **Exception:** a record of a shortage that needs review.

## Initial Data Model
**supplier_receipts**

| Column | Notes |
|---|---|
| id | Primary key |
| supplierOrderId | Required foreign key to supplier_orders.id |
| receivedByEmployeeId | Required foreign key to employees.id |
| receivedAt | Required timestamp |
| finalized | Required boolean, default false |

**supplier_receipt_lines**

| Column | Notes |
|---|---|
| id | Primary key |
| receiptId | Required foreign key to supplier_receipts.id |
| supplierOrderLineId | Required foreign key to supplier_order_lines.id |
| quantityReceived | Required integer, >= 0 |

The existing `supplier_order_lines.quantityReceived` field stores the cumulative quantity received for each line. Each receipt must update that cumulative value and inventory exactly once.

## Gherkin Acceptance Criteria

### Scenario: Receive all units ordered
- **Given** a supplier order line has 10 units ordered and none received
- **When** a warehouse employee records receipt of 10 units and finalizes the receipt
- **Then** the received quantity becomes 10
- **And** inventory increases by 10
- **And** the supplier order status becomes RECEIVED

### Scenario: Receive only part of an order
- **Given** a supplier order line has 10 units ordered
- **When** a warehouse employee records receipt of 8 units and finalizes the line as short
- **Then** the received quantity becomes 8
- **And** inventory increases by 8
- **And** a SUPPLIER_SHORT exception records a shortfall of 2

### Scenario: Negative receipt is rejected
- **Given** a supplier order exists
- **When** the employee enters -1 as the received quantity
- **Then** the system rejects the entry with a validation message

### Scenario: Duplicate receipt is prevented
- **Given** a receipt has already been applied to inventory
- **When** the same receipt is submitted again
- **Then** the system does not increase inventory a second time
