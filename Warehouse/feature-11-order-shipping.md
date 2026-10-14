# Feature: Order Shipping

**Feature ID:** 11  
**Branch pattern:** `feature/11-order-shipping`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Ship picked orders by verifying contents, recording packaging, and producing a bill of lading that contains the order form.  
**Depends on:** [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 6 — Route Maintenance](feature-6-route-maintenance.md), [Feature 10 — Order Picking](feature-10-order-picking.md)  
**Related:** [Feature 12 — Order Delivery](feature-12-order-delivery.md)

## User Stories

### US-11.1: Verify a picked order
**As a** warehouse employee  
**I want to** verify picked quantities against the order before shipping  
**So that** errors are caught before the truck leaves  

**Priority:** P1  
**Independent test:** Verify a PICKED order whose picks match and see it marked verified.  
**Acceptance scenarios:** see ### US-11.1 under Acceptance Criteria

### US-11.2: Package and create a bill of lading
**As a** warehouse employee  
**I want to** record packaging and generate a bill of lading  
**So that** the driver carries a complete shipping document  

**Priority:** P1  
**Independent test:** Package a verified order and retrieve a bill of lading linked to the order and a driver.  
**Acceptance scenarios:** see ### US-11.2 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST only allow shipping of orders with status PICKED.
- **FR-002**: System MUST let a warehouse employee verify an order by confirming quantity picked per line against quantity ordered, recording who verified and when.
- **FR-003**: System MUST let a warehouse employee record the package count for the shipment.
- **FR-004**: System MUST create a bill of lading that contains the customer order form details (customer, delivery address, lines, quantities picked), package count, assigned truck driver, and optional route. [NEEDS CLARIFICATION: whiteboard shows "inside" arrows from Bill of Lading to both order forms; confirm the bill of lading embeds the order form and also applies to inbound supplier orders]
- **FR-005**: System MUST require the assigned driver to be an employee with role TRUCK_DRIVER.
- **FR-006**: System MUST set the order status to SHIPPED when the bill of lading is issued.
- **FR-007**: System MUST NOT issue a second bill of lading for the same order.
- **FR-008**: System MUST allow a route to be attached only if the order's customer is a stop on that route.

## Key Entities

- **Bill of Lading**: shipping document for one Customer Order; has driver, package count, optional Route, verification details, and ship time.
- **Customer Order / Line**: see Feature 9.
- **Employee (Truck Driver) / Route**: see Features 5 and 6.

## Initial Data Model

**bills_of_lading**

| Column | Notes |
|---|---|
| id | PK |
| customerOrderId | required, unique, FK → customer_orders.id |
| driverEmployeeId | required, FK → employees.id (role TRUCK_DRIVER) |
| routeId | nullable, FK → routes.id |
| packageCount | required, integer > 0 |
| verifiedByEmployeeId | required, FK → employees.id |
| verifiedAt | required, timestamp |
| shippedAt | required, timestamp |

## Gherkin AC

### US-11.1 — Verify a picked order

#### Scenario: Matching order is verified
* **Given** an order has status PICKED and every line's picked quantity matches what is packed
* **When** the warehouse employee verifies the order
* **Then** the verification is recorded with the employee and time

#### Scenario: Unpicked order cannot be shipped
* **Given** an order has status RECEIVED
* **When** the employee attempts to verify it for shipping
* **Then** the system rejects the action

### US-11.2 — Package and create a bill of lading

#### Scenario: Bill of lading is issued for a verified order
* **Given** a verified PICKED order and a truck driver exist
* **When** the employee records 4 packages and issues the bill of lading for that driver
* **Then** the bill of lading is stored with the order details, 4 packages, and the driver
* **And** the order status is SHIPPED

#### Scenario: Non-driver cannot be assigned
* **Given** a verified order and an employee with role WAREHOUSE
* **When** the employee issues a bill of lading assigned to the WAREHOUSE employee
* **Then** the system rejects it with a validation message

#### Scenario: Duplicate bill of lading is rejected
* **Given** an order already has a bill of lading
* **When** the employee issues another for the same order
* **Then** the system rejects it

#### Scenario: Route must include the customer
* **Given** an order's customer is not a stop on route "North"
* **When** the employee attaches route "North" to the bill of lading
* **Then** the system rejects it with a validation message
