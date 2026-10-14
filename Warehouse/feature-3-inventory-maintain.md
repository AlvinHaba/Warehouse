# Feature: Inventory Maintain

**Feature ID:** 3  
**Branch pattern:** `feature/3-inventory-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Keep track of the quantity of each item in the warehouse and where it is stored, and allow manual maintenance of those records.  
**Depends on:** [Feature 2 — Item Maintenance](feature-2-item-maintenance.md)  
**Related:** [Feature 8 — Supplier Order Receiving](feature-8-supplier-order-receiving.md), [Feature 10 — Order Picking](feature-10-order-picking.md)

## User Stories

### US-3.1: Track quantity on hand
**As a** warehouse employee  
**I want to** see how many of each item are in the warehouse  
**So that** I know what we can ship  

**Priority:** P1  
**Independent test:** Create an inventory record with quantity 50, then retrieve it and confirm 50.  
**Acceptance scenarios:** see ### US-3.1 under Acceptance Criteria

### US-3.2: Adjust inventory manually
**As a** warehouse employee  
**I want to** correct a quantity or storage location  
**So that** the system matches what is physically on the shelf  

**Priority:** P1  
**Independent test:** Adjust quantity from 50 to 48 with a reason, then confirm 48 and an adjustment record.  
**Acceptance scenarios:** see ### US-3.2 under Acceptance Criteria

### US-3.3: Set reorder levels
**As an** office employee  
**I want to** set a reorder point and target level per item  
**So that** supplier orders can be calculated from stock levels  

**Priority:** P2  
**Independent test:** Set reorder point 10 and target 100, then retrieve and confirm both.  
**Acceptance scenarios:** see ### US-3.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST keep exactly one inventory record per item with quantity on hand and storage location.
- **FR-002**: System MUST NOT allow quantity on hand to be negative.
- **FR-003**: System MUST let a warehouse employee adjust quantity and location; every adjustment MUST record who, when, old value, new value, and a required reason.
- **FR-004**: System MUST let an office employee set reorder point and target level per item; target MUST be greater than or equal to reorder point.
- **FR-005**: System MUST list inventory by item with current quantity in a stable order.
- **FR-006**: System MUST create an inventory record with quantity 0 when an item is added. [NEEDS CLARIFICATION: confirm auto-create vs. created on first receipt]

## Key Entities

- **Inventory Item**: the stock record for an Item; has quantity on hand, storage location, reorder point, target level.
- **Inventory Adjustment**: an audit record of a manual change to an Inventory Item.
- **Item**: see Feature 2.

## Initial Data Model

**inventory_items**

| Column | Notes |
|---|---|
| id | PK |
| itemId | required, unique, FK → items.id |
| quantityOnHand | required, integer, >= 0 |
| location | optional, e.g. aisle/bin |
| reorderPoint | optional, integer, >= 0 |
| targetLevel | optional, integer, >= reorderPoint |

**inventory_adjustments**

| Column | Notes |
|---|---|
| id | PK |
| inventoryItemId | required, FK → inventory_items.id |
| employeeId | required, FK → employees.id |
| oldQuantity | required |
| newQuantity | required |
| reason | required |
| adjustedAt | required, timestamp |

## Gherkin AC

### US-3.1 — Track quantity on hand

#### Scenario: Warehouse employee views quantity on hand
* **Given** an inventory record exists for an item with quantity 50
* **When** the employee opens the inventory list
* **Then** the item is shown with quantity 50

### US-3.2 — Adjust inventory manually

#### Scenario: Warehouse employee corrects a quantity
* **Given** an inventory record exists with quantity 50
* **When** the employee sets quantity to 48 with the reason "damaged"
* **Then** quantity on hand is 48
* **And** an adjustment record stores the old value, new value, reason, and employee

#### Scenario: Negative quantity is rejected
* **Given** an inventory record exists with quantity 5
* **When** the employee sets quantity to -1
* **Then** the system rejects the change with a validation message

#### Scenario: Adjustment without a reason is rejected
* **Given** an inventory record exists
* **When** the employee changes quantity without a reason
* **Then** the system rejects the change with a validation message

### US-3.3 — Set reorder levels

#### Scenario: Office employee sets reorder levels
* **Given** an inventory record exists
* **When** the employee sets reorder point 10 and target level 100
* **Then** both values are stored

#### Scenario: Target below reorder point is rejected
* **Given** an inventory record exists
* **When** the employee sets reorder point 50 and target level 10
* **Then** the system rejects the change with a validation message
