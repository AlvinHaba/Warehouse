# Feature: Supplier Maintain

**Feature ID:** 1  
**Branch pattern:** `feature/1-supplier-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Maintain (add, edit, delete) the suppliers the warehouse buys products from in bulk.  
**Related:** [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 7 — Supplier Order Creation](feature-7-supplier-order-creation.md)

## User Stories

### US-1.1: Add a supplier
**As an** office employee  
**I want to** add a supplier  
**So that** I can order products from them  

**Priority:** P1  
**Independent test:** Create a supplier with a name and order email, then retrieve it by id.  
**Acceptance scenarios:** see ### US-1.1 under Acceptance Criteria

### US-1.2: Edit a supplier
**As an** office employee  
**I want to** edit a supplier's details  
**So that** orders go to current contact information  

**Priority:** P1  
**Independent test:** Update a supplier's email, then retrieve it and confirm the new value.  
**Acceptance scenarios:** see ### US-1.2 under Acceptance Criteria

### US-1.3: Delete a supplier
**As an** office employee  
**I want to** delete a supplier we no longer use  
**So that** the supplier list stays accurate  

**Priority:** P2  
**Independent test:** Delete a supplier with no items or orders, then confirm it can no longer be retrieved.  
**Acceptance scenarios:** see ### US-1.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee add a supplier with a name and an order email address; both are required.
- **FR-002**: System MUST reject a supplier whose name matches an existing supplier (case-insensitive).
- **FR-003**: System MUST let an office employee edit a supplier's name, email, phone, and address.
- **FR-004**: System MUST NOT delete a supplier that is referenced by any item or supplier order.
- **FR-005**: System MUST list and search suppliers by name in a stable order.
- **FR-006**: System MUST reject a malformed order email address.

## Key Entities

- **Supplier**: a company the warehouse buys products from; has a name and contact details; supplies many Items; receives many Supplier Orders.

## Initial Data Model

**suppliers**

| Column | Notes |
|---|---|
| id | PK |
| name | required, unique (case-insensitive) |
| orderEmail | required, used to send orders electronically |
| phone | optional |
| address | optional |

## Gherkin AC

### US-1.1 — Add a supplier

#### Scenario: Office employee adds a supplier with valid details
* **Given** an office employee is working in the system
* **When** the employee saves a supplier with a name and a valid order email
* **Then** the supplier is stored
* **And** the supplier appears in the supplier list

#### Scenario: Supplier is rejected without a name
* **Given** an office employee is working in the system
* **When** the employee saves a supplier with an empty name
* **Then** the system rejects the save with a validation message

#### Scenario: Duplicate supplier name is rejected
* **Given** a supplier named "Acme Foods" exists
* **When** the employee saves another supplier named "acme foods"
* **Then** the system rejects the save with a duplicate message

### US-1.2 — Edit a supplier

#### Scenario: Office employee updates a supplier's email
* **Given** a supplier exists
* **When** the employee changes the order email and saves
* **Then** the supplier shows the new email

### US-1.3 — Delete a supplier

#### Scenario: Supplier with no references is deleted
* **Given** a supplier exists with no items and no orders
* **When** the employee deletes the supplier
* **Then** the supplier no longer exists

#### Scenario: Supplier with items cannot be deleted
* **Given** a supplier exists that is referenced by at least one item
* **When** the employee deletes the supplier
* **Then** the system rejects the deletion
* **And** the supplier still exists
