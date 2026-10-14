# Feature 15: Warehouse Dashboard

**Feature ID:** 15  
**Branch:** `feature/15-warehouse-dashboard`  
**Status:** Draft  
**Created:** 2026-10-12

**Purpose:** Give staff one starting screen showing important warehouse work and issues that need attention.

**Depends on:** [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 7 — Supplier Orders](feature-7-supplier-orders.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md), and [Feature 13 — Exception Reporting](feature-13-exception-reporting.md).

## User Stories

### US-15.1: View a Summary

**As a:** Manager  
**I want to:** See current stock and order status summaries  
**So that:** I can quickly understand the warehouse's workload.

### US-15.2: Find Work Needing Attention

**As an:** Employee  
**I want to:** See orders and exceptions that need action  
**So that:** I can respond without searching every page.

## Functional Requirements

- **FR-001:** The dashboard MUST show counts of supplier orders by status.
- **FR-002:** The dashboard MUST show counts of customer orders by status.
- **FR-003:** The dashboard MUST show the number of open exceptions.
- **FR-004:** The dashboard MUST show items at or below their reorder point.
- **FR-005:** Each summary MUST link to a filtered list of the underlying records.
- **FR-006:** The dashboard MUST respect the signed-in user's permissions.
- **FR-007:** The dashboard MUST show when its data was last refreshed.

## Key Entities

The dashboard reads existing records from the following entities:

- **Supplier Orders:** Supplier-order counts grouped by status.
- **Customer Orders:** Customer-order counts grouped by status.
- **Inventory Items:** Current stock quantities and reorder points.
- **Exceptions:** Open exceptions that need attention.

These records come from the related features. The dashboard does not create duplicate copies of the underlying records.

## Initial Data Model

The dashboard does not require a separate table for the summarized data. It reads existing records from the related features and calculates the counts and summaries when needed.

## Gherkin Acceptance Criteria

### Scenario 1: Dashboard Shows Open Exceptions

**Given** three exceptions have status `OPEN`  
**When** an authorized user opens the dashboard  
**Then** the dashboard shows an open-exception count of 3  
**And** the count links to the open-exception list.

### Scenario 2: Low-Stock Items Are Shown

**Given** an item is at or below its reorder point  
**When** a user opens the dashboard  
**Then** the item appears in the low-stock summary.

### Scenario 3: Supplier Orders Are Grouped by Status

**Given** supplier orders exist with different statuses  
**When** an authorized user opens the dashboard  
**Then** the dashboard displays the number of supplier orders in each status  
**And** each summary links to the corresponding filtered list.

### Scenario 4: Dashboard Displays the Last Refresh Time

**Given** the dashboard has loaded its data  
**When** a user views the dashboard  
**Then** the dashboard displays when its data was last refreshed.

### Scenario 5: Dashboard Respects User Permissions

**Given** a signed-in user has specific role permissions  
**When** the user opens the dashboard  
**Then** the dashboard displays only information the user is authorized to access.




