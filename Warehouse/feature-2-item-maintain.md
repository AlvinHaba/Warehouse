# Feature: Item Maintain

**Feature ID:** 2  
**Branch pattern:** `feature/2-item-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Maintain (add, edit, delete) the items the warehouse stocks, each with price, SKU, UPC, supplier, and description.  
**Depends on:** [Feature 1 — Supplier Maintenance](feature-1-supplier-maintenance.md)  
**Related:** [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md)

## User Stories

### US-2.1: Add an item
**As an** office employee  
**I want to** add an item with its price, SKU, UPC, supplier, and description  
**So that** the item can be stocked, ordered, and sold  

**Priority:** P1  
**Independent test:** Create an item for an existing supplier, then retrieve it by SKU.  
**Acceptance scenarios:** see ### US-2.1 under Acceptance Criteria

### US-2.2: Edit an item
**As an** office employee  
**I want to** edit an item's price, supplier, or description  
**So that** item data stays correct  

**Priority:** P1  
**Independent test:** Change an item's price, then retrieve it and confirm the new price.  
**Acceptance scenarios:** see ### US-2.2 under Acceptance Criteria

### US-2.3: Delete an item
**As an** office employee  
**I want to** delete an item we no longer carry  
**So that** the catalog contains only items we handle  

**Priority:** P2  
**Independent test:** Delete an item with no stock and no order lines, then confirm it is gone.  
**Acceptance scenarios:** see ### US-2.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee add an item with SKU, UPC, description, price, and supplier; all are required.
- **FR-002**: System MUST enforce unique SKU and unique UPC across items.
- **FR-003**: System MUST require the supplier to be an existing supplier.
- **FR-004**: System MUST require price to be zero or greater, stored with exact decimal precision.
- **FR-005**: System MUST let an office employee edit an item; edits to SKU or UPC MUST still satisfy uniqueness.
- **FR-006**: System MUST NOT delete an item that has stock on hand or appears on any supplier or customer order line.
- **FR-007**: System MUST list and search items by SKU, UPC, and description.

## Key Entities

- **Item**: a product the warehouse buys and sells; has price, SKU, UPC, description; supplied by one Supplier; has one Inventory Item record.
- **Supplier**: see Feature 1.

## Initial Data Model

**items**

| Column | Notes |
|---|---|
| id | PK |
| sku | required, unique |
| upc | required, unique |
| description | required |
| price | required, decimal, >= 0 |
| supplierId | required, FK → suppliers.id |

## Gherkin AC

### US-2.1 — Add an item

#### Scenario: Office employee adds an item with valid data
* **Given** a supplier exists
* **When** the employee saves an item with SKU, UPC, description, price, and that supplier
* **Then** the item is stored
* **And** the item can be found by its SKU

#### Scenario: Duplicate SKU is rejected
* **Given** an item with SKU "A-100" exists
* **When** the employee saves another item with SKU "A-100"
* **Then** the system rejects the save with a duplicate message

#### Scenario: Negative price is rejected
* **Given** an office employee is working in the system
* **When** the employee saves an item with price -1.00
* **Then** the system rejects the save with a validation message

#### Scenario: Unknown supplier is rejected
* **Given** no supplier exists with the chosen id
* **When** the employee saves an item with that supplier id
* **Then** the system rejects the save with a validation message

### US-2.2 — Edit an item

#### Scenario: Office employee changes an item's price
* **Given** an item exists
* **When** the employee changes the price and saves
* **Then** the item shows the new price

### US-2.3 — Delete an item

#### Scenario: Item with no stock or order lines is deleted
* **Given** an item exists with zero stock and no order lines
* **When** the employee deletes the item
* **Then** the item no longer exists

#### Scenario: Item on an order line cannot be deleted
* **Given** an item appears on a customer order line
* **When** the employee deletes the item
* **Then** the system rejects the deletion
