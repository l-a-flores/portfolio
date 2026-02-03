
# International Logistics Management System

## Overview
This system is a production logistics platform used for managing Balik-Bayan box shipments from Hong Kong to the Philippines.
It supports day-to-day operational workflows including shipment intake, warehouse processing, batching, and delivery tracking.

The system was designed around real logistics operations rather than idealized flows.

---

## Core Responsibilities
- Client and recipient management
- Shipment and box registration
- Warehouse processing workflows
- Shipment batching and manifest generation
- Status tracking and operational reporting

---

## System Flow

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/2697fb77-fee4-4df3-bb55-205af1f5dc70" />


---

## Key Engineering Focus

### 1. Shipment State Integrity
Shipments follow strict state transitions to prevent invalid operational states.

```php
class Shipment {
    public function transitionTo(string $nextState): void {
        if (!$this->canTransitionTo($nextState)) {
            throw new InvalidShipmentState();
        }
        $this->status = $nextState;
    }
}
```

### 2. Operational Safety Checks
```php
if (empty($payload['tracking_number'])) {
    throw new ValidationException('Tracking number required');
}
```
Prevents incomplete or invalid shipment data from entering the system.

---

## Refactoring Example
Before
```php
saveShipment($_POST);
```

After
```php
$request = ShipmentRequest::fromHttp($_POST);
$this->shipmentService->create($request);
```

---

## Production Considerations
  - Handles inconsistent real-world data
  - Supports incremental refactoring
  - Optimized for operational clarity over abstraction

    
