# Feature: Customer Maintain

**Feature ID:** 4  
**Branch pattern:** `feature/4-customer-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Maintain (add, edit, delete) the customers the warehouse sells products to.  
**Related:** [Feature 6 — Route Maintenance](feature-6-route-maintenance.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md)

## User Stories

### US-4.1: Add a customer
**As an** office employee  
**I want to** add a customer with a delivery address  
**So that** I can take and deliver orders for them  

**Priority:** P1  
**Independent test:** Create a customer with name and address, then retrieve it by id.  
**Acceptance scenarios:** see ### US-4.1 under Acceptance Criteria

### US-4.2: Edit a customer
**As an** office employee  
**I want to** edit a customer's details  
**So that** deliveries go to the right place  

**Priority:** P1  
**Independent test:** Change a customer's address, then retrieve and confirm the new address.  
**Acceptance scenarios:** see ### US-4.2 under Acceptance Criteria

### US-4.3: Delete a customer
**As an** office employee  
**I want to** delete a customer we no longer serve  
**So that** the customer list stays accurate  

**Priority:** P2  
**Independent test:** Delete a customer with no orders and no route stops, then confirm it is gone.  
**Acceptance scenarios:** see ### US-4.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee add a customer with a name and delivery address; both are required.
- **FR-002**: System MUST let an office employee edit a customer's name, address, phone, and email.
- **FR-003**: System MUST NOT delete a customer that has customer orders or appears on a route.
- **FR-004**: System MUST list and search customers by name in a stable order.
- **FR-005**: System MUST reject a malformed email address when one is provided.

## Key Entities

- **Customer**: a business or person that buys from the warehouse; has name, delivery address, contact details; places many Customer Orders; appears on Routes.

## Initial Data Model

**customers**

| Column | Notes |
|---|---|
| id | PK |
| name | required |
| deliveryAddress | required |
| phone | optional |
| email | optional |

## Gherkin AC

### US-4.1 — Add a customer

#### Scenario: Office employee adds a customer
* **Given** an office employee is working in the system
* **When** the employee saves a customer with a name and delivery address
* **Then** the customer is stored
* **And** the customer appears in the customer list

#### Scenario: Customer without an address is rejected
* **Given** an office employee is working in the system
* **When** the employee saves a customer with an empty delivery address
* **Then** the system rejects the save with a validation message

### US-4.2 — Edit a customer

#### Scenario: Office employee updates a delivery address
* **Given** a customer exists
* **When** the employee changes the delivery address and saves
* **Then** the customer shows the new address

### US-4.3 — Delete a customer

#### Scenario: Customer with no orders or stops is deleted
* **Given** a customer exists with no orders and no route stops
* **When** the employee deletes the customer
* **Then** the customer no longer exists

#### Scenario: Customer with orders cannot be deleted
* **Given** a customer has at least one customer order
* **When** the employee deletes the customer
* **Then** the system rejects the deletion
