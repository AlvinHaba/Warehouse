# Feature: Exception Reporting

**Feature ID:** 13  
**Branch pattern:** `feature/13-exception-reporting`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Report exceptions every time we are out of stock or quantities differ between ordered, received, picked, and delivered, to address accuracy and speed problems.  
**Depends on:** [Feature 8 — Supplier Order Receiving](feature-8-supplier-order-receiving.md), [Feature 10 — Order Picking](feature-10-order-picking.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md)  
**Related:** [Feature 7 — Supplier Order Creation](feature-7-supplier-order-creation.md)

## User Stories

### US-13.1: See stock-out exceptions
**As an** office employee  
**I want to** be told every time we are out of an item  
**So that** I can reorder before customers are affected  

**Priority:** P1  
**Independent test:** Reduce an item's quantity to 0 and see a stock-out exception listed.  
**Acceptance scenarios:** see ### US-13.1 under Acceptance Criteria

### US-13.2: See supplier-side and customer-side shortfalls
**As an** office employee  
**I want to** see quantity ordered minus quantity received (supplier) and quantity ordered minus quantity picked (customer)  
**So that** I can investigate whether to change suppliers or fix order calculations  

**Priority:** P1  
**Independent test:** Receive 90 on a line ordered at 100 and see a supplier shortfall of 10.  
**Acceptance scenarios:** see ### US-13.2 under Acceptance Criteria

### US-13.3: See delivery shortfalls
**As an** office employee  
**I want to** see quantity picked minus quantity delivered  
**So that** I can review the truck driver's deliveries for losses  

**Priority:** P1  
**Independent test:** Deliver 8 of 10 picked and see a delivery shortfall of 2.  
**Acceptance scenarios:** see ### US-13.3 under Acceptance Criteria

### US-13.4: Resolve an exception
**As an** office employee  
**I want to** mark an exception reviewed with a suspected cause  
**So that** open exceptions show only what still needs attention  

**Priority:** P2  
**Independent test:** Resolve an open exception with cause CALCULATION and see it leave the open list.  
**Acceptance scenarios:** see ### US-13.4 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST record an OUT_OF_STOCK exception every time an item's quantity on hand reaches zero.
- **FR-002**: System MUST record a SUPPLIER_SHORT exception with shortfall = quantity ordered − quantity received when receipt is less than ordered on a supplier order line.
- **FR-003**: System MUST record a CUSTOMER_SHORT exception with shortfall = quantity ordered − quantity picked when a customer order line is finalized short.
- **FR-004**: System MUST record a DELIVERY_SHORT exception with shortfall = quantity picked − quantity delivered when delivery is less than picked.
- **FR-005**: System MUST list exceptions filterable by type, item, supplier, driver, status (OPEN or RESOLVED), and date range, newest first.
- **FR-006**: System MUST let an office employee resolve an exception with a required cause: CHANGE_SUPPLIER, CALCULATION, TRUCK_DRIVER, or OTHER. [NEEDS CLARIFICATION: whiteboard poses these as questions ("Change suppliers?", "Calculations?", "Truck driver?"); confirm the cause list and which exception types each applies to]
- **FR-007**: System MUST NOT create an exception when quantities match.
- **FR-008**: System MUST NOT create duplicate exceptions for the same line and type.
- **FR-009**: System MUST show the supplier, order, and driver (where applicable) on each exception.

## Key Entities

- **Exception**: a recorded quantity discrepancy or stock-out; has a type, item, expected and actual quantities, shortfall, status, and optional resolution cause.
- **Supplier Order Line / Customer Order Line / Inventory Item**: sources of exceptions; see Features 7, 9, and 3.

## Initial Data Model

**exceptions**

| Column | Notes |
|---|---|
| id | PK |
| type | required, one of OUT_OF_STOCK, SUPPLIER_SHORT, CUSTOMER_SHORT, DELIVERY_SHORT |
| itemId | required, FK → items.id |
| supplierOrderLineId | nullable, FK → supplier_order_lines.id |
| customerOrderLineId | nullable, FK → customer_order_lines.id |
| driverEmployeeId | nullable, FK → employees.id (DELIVERY_SHORT) |
| quantityExpected | required, integer >= 0 |
| quantityActual | required, integer >= 0 |
| shortfall | required, integer > 0 for short types; 0 for OUT_OF_STOCK |
| status | required, one of OPEN, RESOLVED, default OPEN |
| resolutionCause | nullable, one of CHANGE_SUPPLIER, CALCULATION, TRUCK_DRIVER, OTHER |
| detectedAt | required, timestamp |
| resolvedAt | nullable, timestamp |
| resolvedByEmployeeId | nullable, FK → employees.id |

## Gherkin AC

### US-13.1 — See stock-out exceptions

#### Scenario: Stock reaching zero creates an exception
* **Given** an item has quantity on hand 3
* **When** a pick reduces quantity on hand to 0
* **Then** an OUT_OF_STOCK exception is created for that item with status OPEN

#### Scenario: Stock above zero creates no stock-out exception
* **Given** an item has quantity on hand 10
* **When** a pick reduces quantity on hand to 4
* **Then** no OUT_OF_STOCK exception is created

### US-13.2 — See supplier-side and customer-side shortfalls

#### Scenario: Short supplier receipt creates an exception
* **Given** a supplier order line has quantity ordered 100
* **When** 90 are received
* **Then** a SUPPLIER_SHORT exception is created with shortfall 10

#### Scenario: Short customer pick creates an exception
* **Given** a customer order line has quantity ordered 10
* **When** the line is finalized with 7 picked
* **Then** a CUSTOMER_SHORT exception is created with shortfall 3

#### Scenario: Matching quantities create no exception
* **Given** a supplier order line has quantity ordered 100
* **When** 100 are received
* **Then** no exception is created

#### Scenario: Duplicate exception is not created
* **Given** a SUPPLIER_SHORT exception already exists for a line
* **When** the same line is checked again with no change
* **Then** no second exception is created

### US-13.3 — See delivery shortfalls

#### Scenario: Short delivery creates an exception with the driver
* **Given** a customer order line has quantity picked 10
* **When** the driver confirms delivery of 8
* **Then** a DELIVERY_SHORT exception is created with shortfall 2
* **And** the exception shows the driver

### US-13.4 — Resolve an exception

#### Scenario: Office employee resolves an exception with a cause
* **Given** an OPEN exception exists
* **When** the employee resolves it with cause CALCULATION
* **Then** its status is RESOLVED with the cause, employee, and time

#### Scenario: Resolution without a cause is rejected
* **Given** an OPEN exception exists
* **When** the employee resolves it without a cause
* **Then** the system rejects the resolution with a validation message

#### Scenario: Open list excludes resolved exceptions
* **Given** one OPEN and one RESOLVED exception exist
* **When** the employee filters exceptions by status OPEN
* **Then** only the OPEN exception is listed
