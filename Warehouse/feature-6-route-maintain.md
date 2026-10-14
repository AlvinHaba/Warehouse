# Feature: Route Maintenance

**Feature ID:** 6  
**Branch pattern:** `feature/6-route-maintenance`  
**Status:** Draft  
**Created:** 2026-10-12  
**Input:** Maintain (add, edit, delete) delivery routes, each being an ordered list of customers.  
**Depends on:** [Feature 4 — Customer Maintenance](feature-4-customer-maintenance.md)  
**Related:** [Feature 11 — Order Shipping](feature-11-order-shipping.md), [Feature 12 — Order Delivery](feature-12-order-delivery.md)

## User Stories

### US-6.1: Create a route
**As an** office employee  
**I want to** create a route with a list of customers  
**So that** drivers know which customers to deliver to  

**Priority:** P1  
**Independent test:** Create a route with two customers and retrieve the stops in order.  
**Acceptance scenarios:** see ### US-6.1 under Acceptance Criteria

### US-6.2: Edit a route
**As an** office employee  
**I want to** add, remove, or reorder customers on a route  
**So that** routes reflect where we actually deliver  

**Priority:** P1  
**Independent test:** Remove a stop and confirm remaining stops are renumbered.  
**Acceptance scenarios:** see ### US-6.2 under Acceptance Criteria

### US-6.3: Delete a route
**As an** office employee  
**I want to** delete a route we no longer run  
**So that** the route list stays current  

**Priority:** P2  
**Independent test:** Delete a route with no open shipments and confirm it is gone.  
**Acceptance scenarios:** see ### US-6.3 under Acceptance Criteria

## Functional Requirements

- **FR-001**: System MUST let an office employee create a route with a unique name and at least one customer.
- **FR-002**: System MUST store route stops in a defined sequence and keep the sequence contiguous after edits.
- **FR-003**: System MUST NOT allow the same customer twice on one route. [NEEDS CLARIFICATION: may a customer appear on more than one route?]
- **FR-004**: System MUST let an office employee add, remove, and reorder stops.
- **FR-005**: System MUST NOT delete a route referenced by an unfinished shipment.
- **FR-006**: System MUST show, for a route, each stop's customer name and delivery address.

## Key Entities

- **Route**: a named delivery path; has an ordered list of Route Stops.
- **Route Stop**: one Customer at a position on a Route.
- **Customer**: see Feature 4.

## Initial Data Model

**routes**

| Column | Notes |
|---|---|
| id | PK |
| name | required, unique |

**route_stops**

| Column | Notes |
|---|---|
| id | PK |
| routeId | required, FK → routes.id |
| customerId | required, FK → customers.id |
| stopSequence | required, integer >= 1, unique per route |

## Gherkin AC

### US-6.1 — Create a route

#### Scenario: Office employee creates a route with customers
* **Given** customers "Mill Cafe" and "Oak Diner" exist
* **When** the employee creates route "North" with those customers in that order
* **Then** the route is stored with stop 1 "Mill Cafe" and stop 2 "Oak Diner"

#### Scenario: Route without customers is rejected
* **Given** an office employee is working in the system
* **When** the employee saves a route with no customers
* **Then** the system rejects the save with a validation message

### US-6.2 — Edit a route

#### Scenario: Removing a stop renumbers the rest
* **Given** route "North" has three stops
* **When** the employee removes stop 2
* **Then** the remaining stops are numbered 1 and 2 in their original relative order

#### Scenario: Duplicate customer on a route is rejected
* **Given** route "North" already includes "Mill Cafe"
* **When** the employee adds "Mill Cafe" again
* **Then** the system rejects the change with a validation message

### US-6.3 — Delete a route

#### Scenario: Route with no open shipments is deleted
* **Given** a route exists with no unfinished shipments
* **When** the employee deletes the route
* **Then** the route and its stops no longer exist

#### Scenario: Route with an unfinished shipment cannot be deleted
* **Given** a route is used by a shipment that has not been delivered
* **When** the employee deletes the route
* **Then** the system rejects the deletion
