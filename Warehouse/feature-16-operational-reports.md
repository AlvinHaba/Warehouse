# Feature 16: Operational Reports

**Feature ID:** 16  
**Branch:** `feature/16-operational-reports`  
**Status:** Draft  
**Created:** 2026-10-12

**Purpose:** Provide printable or downloadable summaries for daily warehouse operations.

**Depends on:** [Feature 1 — Item Management](feature-1-item-management.md), [Feature 2 — Item Maintenance](feature-2-item-maintenance.md), [Feature 3 — Inventory Maintenance](feature-3-inventory-maintenance.md), [Feature 4 — Customer Maintenance](feature-4-customer-maintenance.md), [Feature 5 — Employee Maintenance](feature-5-employee-maintenance.md), [Feature 6 — Supplier Maintenance](feature-6-supplier-maintenance.md), [Feature 7 — Supplier Orders](feature-7-supplier-orders.md), [Feature 8 — Receiving](feature-8-receiving.md), [Feature 9 — Customer Order Intake](feature-9-customer-order-intake.md), [Feature 10 — Order Processing](feature-10-order-processing.md), [Feature 11 — Order Fulfillment](feature-11-order-fulfillment.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md), and [Feature 13 — Exception Reporting](feature-13-exception-reporting.md).

## User Stories

### US-16.1: Review Order Activity

**As a:** Manager  
**I want to:** Report on orders over a selected date range  
**So that:** I can monitor order volume and completion.

### US-16.2: Review Stock Levels

**As an:** Office employee  
**I want to:** Produce a stock report  
**So that:** I can plan replenishment.

## Functional Requirements

- **FR-001:** The system MUST provide a stock-on-hand report with item SKU, description, location, quantity, reorder point, and target level where available.
- **FR-002:** The system MUST provide supplier-order and customer-order reports filtered by date range and status.
- **FR-003:** The system MUST provide an exception report filtered by type, status, and date range.
- **FR-004:** Reports MUST show the time period covered and the time they were generated.
- **FR-005:** Reports MUST use the underlying system records and MUST NOT change operational data.
- **FR-006:** The system MUST allow users to print a report. Export to CSV/PDF is optional and requires instructor approval.
- **FR-007:** Reports MUST respect role permissions and avoid exposing information the user is not authorized to view.

## Key Entities

Reports read data from the following entities:

- **Items:** Item identification, SKU, and description.
- **Inventory Items:** Current quantities, locations, reorder points, and target levels where available.
- **Supplier Orders:** Supplier-order activity, dates, and statuses.
- **Customer Orders:** Customer-order activity, dates, and statuses.
- **Deliveries:** Delivery information and completion details.
- **Exceptions:** Reported operational issues, types, statuses, and dates.

## Gherkin Acceptance Criteria

### Scenario 1: Generate Stock Report

**Given** inventory records exist for several items  
**When** an authorized employee opens the stock report  
**Then** each row shows the item's current quantity and location.

### Scenario 2: Filter Order Report

**Given** orders exist with different statuses and dates  
**When** a user selects a date range and status  
**Then** only matching orders appear.

### Scenario 3: Print an Operational Report

**Given** an authorized user has generated a report  
**When** the user selects the print option  
**Then** the system provides a printable version of the report  
**And** the report displays its selected time period and generation time.

### Scenario 4: Respect User Permissions

**Given** a user does not have permission to view certain information  
**When** the user generates an operational report  
**Then** the system does not display unauthorized information.

