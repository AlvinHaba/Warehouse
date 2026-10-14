# Feature 18: Customer Returns

**Feature ID:** 18  
**Branch pattern:** `feature/18-customer-returns`  
**Status:** Final  
**Created:** 2026-10-12  
**Input:** Record products returned by customers and make the stock impact clear. Return policies and whether returned items can be restocked need instructor/business approval.

**Depends on:** [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 4 — Customer Maintenance](feature-4-customer-maintenance.md), [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md), and [Feature 17 — Activity and Audit History](feature-17-activity-and-audit-history.md).

---

## User Stories

### US-18.1: Record a Return

**As an:** Office employee  
**I want to:** Record items returned against a delivered order  
**So that:** The warehouse knows what came back and why.

### US-18.2: Inspect Returned Items

**As a:** Warehouse employee  
**I want to:** Record whether returned items can be restocked  
**So that:** Damaged items are not accidentally treated as sellable stock.

---

## Functional Requirements

- **FR-001:** The system MUST link a return to an existing customer order that has been shipped or delivered.
- **FR-002:** The system MUST record the item, quantity returned, date, reason, and employee.
- **FR-003:** The returned quantity MUST be greater than zero and MUST NOT exceed the quantity delivered minus previous returns for that order line.
- **FR-004:** The system MUST record an inspection result of `RESTOCK`, `DAMAGED`, or `REVIEW_REQUIRED`.
- **FR-005:** Only items marked `RESTOCK` may increase sellable quantity on hand.
- **FR-006:** The system MUST preserve the original order and delivery records.
- **FR-007:** The system MUST make returns searchable by customer, order, and date.

---

## Key Entities

- **Return:** A return record linked to a customer order.
- **Return Line:** Contains the item, quantity returned, reason, and inspection result.

---

## Initial Data Model

### `customer_returns`


| Field                  | Description                                                       |
| ---------------------- | ----------------------------------------------------------------- |
| `id`                   | Primary key (PK).                                                 |
| `customerOrderId`      | Foreign key (FK) referencing the customer order.                  |
| `recordedByEmployeeId` | Foreign key (FK) referencing the employee who records the return. |
| `reason`               | Required reason for the return.                                   |
| `status`               | Required return status.                                           |
| `createdAt`            | Timestamp when the return was created.                            |


### `customer_return_lines`


| Field                 | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| `id`                  | Primary key (PK).                                              |
| `returnId`            | Foreign key (FK) referencing `customer_returns.id`.            |
| `customerOrderLineId` | Foreign key (FK) referencing the original customer order line. |
| `quantityReturned`    | Integer greater than zero.                                     |
| `inspectionResult`    | Required value: `RESTOCK`, `DAMAGED`, or `REVIEW_REQUIRED`.    |


---

## Gherkin Acceptance Criteria

### Scenario 1: Restock an Approved Return

- **Given** a delivered order contains 10 units of an item.
- **When** 2 units are returned and inspected as `RESTOCK`.
- **Then** the return is recorded.
- **And** sellable inventory increases by 2 units.

### Scenario 2: Reject a Return Quantity Above the Delivered Quantity

- **Given** only 3 units were delivered and no previous returns exist.
- **When** an employee enters 4 units as the returned quantity.
- **Then** the system rejects the return.

### Scenario 3: Do Not Restock Damaged Items

- **Given** a returned item is inspected and marked `DAMAGED`.
- **When** the return is saved.
- **Then** the return is recorded.
- **And** sellable inventory does not increase.

---



