# Feature: Order Delivery

**Feature ID:** 12  
**Branch pattern:** `feature/12-order-delivery`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Truck drivers deliver shipped orders and record a check and confirmation of the quantities delivered.  
**Depends on:** [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 11 — Order Shipping](feature-11-order-shipping.md)  
**Related:** [Feature 13 — Exception Reporting](feature-13-exception-reporting.md)

## User Stories

### US-12.1: Confirm a delivery
**As a** truck driver  
**I want to** record the quantity delivered per line and a customer confirmation  
**So that** there is proof the customer received the order  

**Priority:** P1  
**Independent test:** Confirm a SHIPPED order with full quantities and see status DELIVERED with a confirmation name.  
**Acceptance scenarios:** see ### US-12.1 under Acceptance Criteria

### US-12.2: Record a short delivery
**As a** truck driver  
**I want to** record when less was delivered than was picked  
**So that** the difference is reported and investigated  

**Priority:** P1  
**Independent test:** Deliver 8 of 10 picked and confirm an exception is created.  
**Acceptance scenarios:** see ### US-12.2 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST only allow delivery confirmation for orders with status SHIPPED.
- **FR-002**: System MUST let the assigned truck driver record quantity delivered per line, from 0 up to quantity picked.
- **FR-003**: System MUST require a confirmation of receipt by the customer (receiver name) to complete a delivery. [NEEDS CLARIFICATION: whiteboard says "Check/Confirmation"; confirm whether a signature or only a name is required]
- **FR-004**: System MUST set the order status to DELIVERED when the delivery is confirmed.
- **FR-005**: System MUST create a delivery exception (see Feature 13) for each line where quantity delivered is less than quantity picked.
- **FR-006**: System MUST record the driver and delivery timestamp.
- **FR-007**: System MUST NOT allow a driver other than the one on the bill of lading to confirm the delivery.

## Key Entities

- **Delivery Confirmation**: a record that a shipped order reached the customer; has driver, receiver name, and time.
- **Bill of Lading / Customer Order Line**: see Features 11 and 9; quantity delivered is stored on the line.

## Initial Data Model

**delivery_confirmations**

| Column | Notes |
|---|---|
| id | PK |
| billOfLadingId | required, unique, FK → bills_of_lading.id |
| driverEmployeeId | required, FK → employees.id |
| receiverName | required |
| deliveredAt | required, timestamp |

`customer_order_lines.quantityDelivered` stores the delivered quantity per line.

## Gherkin AC

### US-12.1 — Confirm a delivery

#### Scenario: Driver confirms a full delivery
* **Given** an order has status SHIPPED and the driver is assigned on its bill of lading
* **When** the driver records full quantities delivered and receiver name "J. Smith"
* **Then** the delivery confirmation is stored
* **And** the order status is DELIVERED

#### Scenario: Delivery without a receiver name is rejected
* **Given** an order has status SHIPPED
* **When** the driver confirms delivery without a receiver name
* **Then** the system rejects the confirmation with a validation message

#### Scenario: Unassigned driver cannot confirm
* **Given** an order is SHIPPED with driver A on its bill of lading
* **When** driver B tries to confirm the delivery
* **Then** the system rejects the confirmation

#### Scenario: Unshipped order cannot be delivered
* **Given** an order has status PICKED
* **When** a driver tries to confirm delivery
* **Then** the system rejects the confirmation

### US-12.2 — Record a short delivery

#### Scenario: Short delivery creates an exception
* **Given** an order line has quantity picked 10
* **When** the driver records 8 delivered and confirms
* **Then** a delivery exception is created with picked 10 and delivered 8

#### Scenario: Delivering more than picked is rejected
* **Given** an order line has quantity picked 10
* **When** the driver records 11 delivered
* **Then** the system rejects the entry with a validation message
