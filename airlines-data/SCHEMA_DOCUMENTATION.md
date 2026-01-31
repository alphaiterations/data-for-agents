# Indigo Airline Booking System - Schema Documentation

## 🗄️ Complete Database Schema

### Table: `Customers`
**Purpose**: Store all airline customers with demographic and loyalty info

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `customer_id` | VARCHAR(20) | PRIMARY KEY | Unique customer ID (CUST00000001) |
| `first_name` | VARCHAR(100) | | Synthetic first name (Faker) |
| `last_name` | VARCHAR(100) | | Synthetic last name (Faker) |
| `email` | VARCHAR(255) | UNIQUE | Unique email (customer{N}@indigo.com) |
| `phone_number` | VARCHAR(20) | | Synthetic phone number |
| `date_of_birth` | DATE | | DOB (adult: 18-75 years) |
| `gender` | VARCHAR(10) | | M/F/Other |
| `country_code` | CHAR(2) | | ISO 2-letter code (IN) |
| `city` | VARCHAR(100) | | Booking city (Delhi, Mumbai, etc.) |
| `loyalty_program_id` | VARCHAR(20) | | 6E Rewards ID |
| `customer_segment` | VARCHAR(50) | | economy/premium/vip |
| `created_at` | TIMESTAMP | | Record creation time |
| `updated_at` | TIMESTAMP | | Last update time |
| `is_synthetic` | BOOLEAN | | Flag for synthetic data |

**Indexes**: `email`, `phone_number`

---

### Table: `FlightSchedule`
**Purpose**: Master schedule template for all flights (reusable across 20 years)

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `flight_id` | VARCHAR(10) | PRIMARY KEY | Unique flight code (6E0001) |
| `origin_airport_code` | CHAR(3) | | IATA code (DEL, BOM, etc.) |
| `destination_airport_code` | CHAR(3) | | IATA code |
| `departure_time` | TIME | | Scheduled departure (HH:MM:SS) |
| `arrival_time` | TIME | | Scheduled arrival (HH:MM:SS) |
| `flight_duration_minutes` | INTEGER | | Flight duration in minutes |
| `aircraft_type` | VARCHAR(20) | | A320/A321/ATR72 |
| `seat_capacity` | INTEGER | | Total seats (70, 180, or 194) |
| `status` | VARCHAR(50) | | active/suspended |
| `created_at` | TIMESTAMP | | Record creation time |

**Total Records**: 6,762 flights  
**Coverage**: 1,127 unique routes with multiple daily frequencies

---

### Table: `DaysOfOperation`
**Purpose**: Define which days each flight operates (weekly schedule)

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `id` | INTEGER | PRIMARY KEY | Auto-increment |
| `flight_id` | VARCHAR(10) | FOREIGN KEY | Reference to FlightSchedule |
| `day_of_week` | INTEGER | | 0=Sun, 1=Mon, ..., 6=Sat |
| `effective_from` | DATE | | Schedule start date |
| `effective_to` | DATE | | Schedule end date (2046-12-31) |

**Total Records**: 47,334 (6,762 flights × 7 days)  
**Usage**: Validate flight availability for specific dates

---

### Table: `PNRs`
**Purpose**: Passenger Name Records - unique booking identifiers

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `pnr_code` | VARCHAR(6) | PRIMARY KEY | 6-char code (A123456) |
| `customer_id` | VARCHAR(20) | FOREIGN KEY | Reference to Customers |
| `pnr_status` | VARCHAR(50) | | issued/confirmed/cancelled |
| `issue_date` | DATETIME | | When PNR was created |
| `valid_until` | DATE | | Ticket expiry (typically 1 year) |
| `remarks` | VARCHAR(500) | | Special requests, notes |
| `created_at` | TIMESTAMP | | Record creation time |
| `updated_at` | TIMESTAMP | | Last update time |

**Total Records**: 50,000  
**Format**: Letter + 6-digit number (ensures uniqueness)

---

### Table: `Bookings`
**Purpose**: Flight reservations with pricing details

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `booking_id` | VARCHAR(20) | PRIMARY KEY | Unique booking ID (BK00000001) |
| `pnr_code` | VARCHAR(6) | FOREIGN KEY | Link to PNR |
| `customer_id` | VARCHAR(20) | FOREIGN KEY | Customer who booked |
| `flight_id` | VARCHAR(10) | FOREIGN KEY | Flight being booked |
| `flight_date` | DATE | | Travel date |
| `total_passengers` | INTEGER | | 1-4 typical |
| `booking_status` | VARCHAR(50) | | pending_payment/confirmed/cancelled |
| `booking_type` | VARCHAR(50) | | oneway/roundtrip/multicity |
| `total_fare` | DECIMAL(10,2) | | Base fare × passengers (INR) |
| `tax_charges` | DECIMAL(10,2) | | ~5% of fare |
| `discount` | DECIMAL(10,2) | | 0-15% typical |
| `final_amount` | DECIMAL(10,2) | | Total + tax - discount |
| `created_at` | TIMESTAMP | | Booking time |
| `updated_at` | TIMESTAMP | | Last modification |

**Total Records**: 50,000  
**Status Distribution**: 33% confirmed, 33% pending, 33% cancelled

---

### Table: `Passengers`
**Purpose**: Individual passenger details in a booking

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `passenger_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (PASS00000001) |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `first_name` | VARCHAR(100) | | Synthetic name |
| `last_name` | VARCHAR(100) | | Synthetic name |
| `date_of_birth` | DATE | | Age 1-75 |
| `gender` | VARCHAR(10) | | M/F/Other |
| `passport_number` | VARCHAR(20) | | Masked (XXX####) |
| `nationality` | CHAR(2) | | ISO code (IN) |
| `passenger_type` | VARCHAR(50) | | adult/child/infant |
| `created_at` | TIMESTAMP | | Record creation |

**Total Records**: 124,836+  
**Average**: 2.5 passengers per booking  
**Indexes**: `booking_id`

---

### Table: `Itineraries`
**Purpose**: Journey information (single or multi-leg)

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `itinerary_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (ITN00000001) |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `total_legs` | INTEGER | | 1=direct, 2+=connecting |
| `journey_type` | VARCHAR(50) | | direct/connecting |
| `total_duration_minutes` | INTEGER | | Total flight time |
| `created_at` | TIMESTAMP | | Creation time |

**Total Records**: 50,000  
**Journey Types**: All currently direct (1 leg); can be extended for multi-city

---

### Table: `ItineraryLegs`
**Purpose**: Individual flight segments in a journey

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `leg_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (LEG00000001) |
| `itinerary_id` | VARCHAR(20) | FOREIGN KEY | Parent itinerary |
| `leg_number` | INTEGER | | 1, 2, 3... for multi-leg |
| `flight_id` | VARCHAR(10) | FOREIGN KEY | Which flight |
| `flight_date` | DATE | | Travel date |
| `origin_airport` | CHAR(3) | | Departure IATA code |
| `destination_airport` | CHAR(3) | | Arrival IATA code |
| `departure_time` | TIME | | Scheduled departure |
| `arrival_time` | TIME | | Scheduled arrival |
| `seat_number` | VARCHAR(10) | | Seat assignment (12A) |
| `seat_class` | VARCHAR(50) | | economy/business/premium_economy |
| `leg_status` | VARCHAR(50) | | confirmed/cancelled/rescheduled |
| `created_at` | TIMESTAMP | | Creation time |

**Total Records**: 50,000  
**Indexes**: `itinerary_id, leg_number`

---

### Table: `PassengerBaggage`
**Purpose**: Track checked and cabin baggage

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `passenger_baggage_id` | VARCHAR(20) | PRIMARY KEY | BAG{passenger_id} |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `passenger_id` | VARCHAR(20) | FOREIGN KEY | Passenger |
| `baggage_type` | VARCHAR(50) | | checked/cabin/special |
| `bag_weight_kg` | DECIMAL(5,2) | | 10-25 kg typical |
| `bag_dimensions_cm` | VARCHAR(50) | | e.g., "55x40x20" |
| `baggage_status` | VARCHAR(50) | | booked/checked_in/loaded/delivered |
| `baggage_tag_number` | VARCHAR(20) | | Tracking ID |
| `created_at` | TIMESTAMP | | Creation time |

**Total Records**: 124,836+  
**Indexes**: `booking_id, passenger_id`

---

### Table: `SpecialBaggage`
**Purpose**: Track special items (sports, instruments, pets, etc.)

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `special_baggage_id` | VARCHAR(20) | PRIMARY KEY | Unique ID |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `baggage_type` | VARCHAR(50) | | sports_equipment/musical_instrument/wheelchair/pet/fragile |
| `item_description` | VARCHAR(500) | | Item details |
| `declared_value` | DECIMAL(10,2) | | Insurance value |
| `handling_instructions` | VARCHAR(500) | | Special handling notes |
| `created_at` | TIMESTAMP | | Creation time |

**Extensible**: Add more bookings with special baggage as needed

---

### Table: `FlightInstances`
**Purpose**: Track specific flights on specific dates (actual flight execution)

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `flight_instance_id` | VARCHAR(30) | PRIMARY KEY | flight_id + YYYYMMDD |
| `flight_id` | VARCHAR(10) | FOREIGN KEY | Base flight schedule |
| `flight_date` | DATE | | Date of flight |
| `scheduled_departure` | DATETIME | | Planned departure |
| `scheduled_arrival` | DATETIME | | Planned arrival |
| `actual_departure` | DATETIME | | Actual departure (NULL if future) |
| `actual_arrival` | DATETIME | | Actual arrival (NULL if future) |
| `flight_status` | VARCHAR(50) | | scheduled/boarding/departed/in_air/landed/cancelled |
| `created_at` | TIMESTAMP | | Creation time |

**Total Records**: 10,500  
**Time Period**: Weekly data for 2 years (2026-2027)  
**Indexes**: `flight_id, flight_date`

---

### Table: `FlightDelays`
**Purpose**: Track and document flight delays

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `delay_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (DLY00000001) |
| `flight_instance_id` | VARCHAR(30) | FOREIGN KEY | Which flight instance |
| `delay_category` | VARCHAR(50) | | weather/mechanical/crew/air_traffic |
| `delay_reason` | VARCHAR(500) | | Human-readable reason |
| `delay_minutes` | INTEGER | | 15-120 minutes |
| `estimated_departure` | DATETIME | | Revised departure |
| `estimated_arrival` | DATETIME | | Revised arrival |
| `delay_status` | VARCHAR(50) | | ongoing/resolved/cancelled |
| `delay_announced_at` | TIMESTAMP | | When announced |
| `created_at` | TIMESTAMP | | Record creation |

**Total Records**: 522 (~5% delay rate)  
**Distribution**: 26% weather, 26% crew, 25% air_traffic, 23% mechanical

---

### Table: `Payments`
**Purpose**: Process and track payments for bookings

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `payment_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (PAY00000001) |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `payment_amount` | DECIMAL(10,2) | | Final amount paid |
| `payment_method` | VARCHAR(50) | | credit_card/debit_card/upi/netbanking/wallet/cash |
| `payment_status` | VARCHAR(50) | | pending/success/failed/refunded |
| `transaction_id` | VARCHAR(50) | | Synthetic TXN ID (no real gateway) |
| `payment_gateway` | VARCHAR(50) | | Razorpay/PayU/CCAvenue (mock) |
| `created_at` | TIMESTAMP | | Payment time |

**Total Records**: 50,000  
**Indexes**: `booking_id`

---

### Table: `Refunds`
**Purpose**: Track refunds and reimbursements

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `refund_id` | VARCHAR(20) | PRIMARY KEY | Unique ID (REF00000001) |
| `booking_id` | VARCHAR(20) | FOREIGN KEY | Parent booking |
| `refund_reason` | VARCHAR(50) | | customer_request/flight_cancelled/flight_delayed/no_show |
| `refund_amount` | DECIMAL(10,2) | | Amount refunded |
| `refund_percentage` | DECIMAL(5,2) | | 50%, 75%, 100% etc. |
| `refund_status` | VARCHAR(50) | | initiated/approved/processed/rejected |
| `refund_method` | VARCHAR(50) | | original_payment/airline_credit/bank_transfer |
| `refund_date` | DATETIME | | When refund was processed |
| `created_at` | TIMESTAMP | | Record creation |

**Extensible**: Add actual refund records as bookings are cancelled

---

### Table: `ConnectionRules`
**Purpose**: Define connection requirements for multi-leg journeys

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `connection_id` | INTEGER | PRIMARY KEY | Auto-increment |
| `origin_airport` | CHAR(3) | | Starting point |
| `destination_airport` | CHAR(3) | | Final destination |
| `min_connection_time_domestic` | INTEGER | | 90 minutes |
| `min_connection_time_international` | INTEGER | | 120 minutes |
| `turnaround_time_mins` | INTEGER | | Buffer for crew/aircraft |
| `created_at` | TIMESTAMP | | Creation time |

**Usage**: Validate if connecting flights have sufficient layover time

---

### Table: `AuditLog`
**Purpose**: Track all changes for transparency and compliance

| Field | Type | Constraints | Description |
|-------|------|-----------|-------------|
| `log_id` | BIGINT | PRIMARY KEY | Auto-increment |
| `entity_type` | VARCHAR(50) | | Bookings/Customers/Payments/etc. |
| `entity_id` | VARCHAR(50) | | ID of changed entity |
| `action` | VARCHAR(50) | | INSERT/UPDATE/DELETE |
| `old_values` | TEXT (JSON) | | Previous state (JSON) |
| `new_values` | TEXT (JSON) | | New state (JSON) |
| `changed_by` | VARCHAR(100) | | User/system who made change |
| `timestamp` | DATETIME | | When change occurred |

**Indexes**: `entity_type, entity_id, timestamp`

---

## 📊 Entity Relationship Diagram (Text)

```
┌─────────────┐
│  Customers  │
└──────┬──────┘
       │
       │ customer_id
       │
       ├──────────┬──────────┬─────────────┐
       │          │          │             │
    PNRs    BookingS    (via FK)       AuditLog
       │       │
       │       │flight_id + customer_id
       │       │
       │    ┌──┴────────────────┐
       │    │                   │
     booking_id          FlightSchedule
       │                   │
       ├──────────┬────────┤
       │          │        └─────────DaysOfOperation
    Itineraries  │
       │      Baggage─────┬
       │      │           │
       │  PassengerBaggage PassengerBaggage
       │      │           (checked/cabin)
       │  Passengers
       │
       ├──Payments
       ├──Refunds
       │
   ItineraryLegs
       │
       ├──FlightInstances
       │    │
       │    └──FlightDelays
       │
       └──Bookings (cycle check)
```

---

## 🔗 Foreign Key Relationships

```sql
-- Core relationships
Bookings.customer_id → Customers.customer_id
Bookings.flight_id → FlightSchedule.flight_id
Bookings.pnr_code → PNRs.pnr_code

-- Passengers
Passengers.booking_id → Bookings.booking_id
PassengerBaggage.booking_id → Bookings.booking_id
PassengerBaggage.passenger_id → Passengers.passenger_id
SpecialBaggage.booking_id → Bookings.booking_id

-- Itinerary
Itineraries.booking_id → Bookings.booking_id
ItineraryLegs.itinerary_id → Itineraries.itinerary_id
ItineraryLegs.flight_id → FlightSchedule.flight_id

-- Operations
FlightInstances.flight_id → FlightSchedule.flight_id
FlightDelays.flight_instance_id → FlightInstances.flight_instance_id

-- Financial
Payments.booking_id → Bookings.booking_id
Refunds.booking_id → Bookings.booking_id

-- Scheduling
DaysOfOperation.flight_id → FlightSchedule.flight_id
ConnectionRules (no FK, reference data)

-- Compliance
AuditLog (no FK, tracks entity IDs as strings)
```

---

## 💡 Query Patterns

### Direct Flight Search
```sql
SELECT * FROM FlightSchedule
WHERE origin_airport_code = ? AND destination_airport_code = ?
ORDER BY departure_time;
```

### Multi-leg Journey Construction
```sql
SELECT leg1.flight_id, leg2.flight_id
FROM FlightSchedule leg1
JOIN FlightSchedule leg2 
  ON leg1.destination_airport_code = leg2.origin_airport_code
WHERE leg1.origin_airport_code = ? AND leg2.destination_airport_code = ?
  AND CAST((strftime('%s', leg2.departure_time) - strftime('%s', leg1.arrival_time)) / 60 AS INTEGER) >= 90;
```

### Booking Availability
```sql
SELECT 
    f.flight_id,
    f.seat_capacity - COUNT(DISTINCT b.booking_id) as available_seats
FROM FlightSchedule f
LEFT JOIN Bookings b ON f.flight_id = b.flight_id AND b.flight_date = ?
GROUP BY f.flight_id;
```

### Customer Booking History
```sql
SELECT b.booking_id, b.pnr_code, b.flight_date, b.final_amount
FROM Bookings b
WHERE b.customer_id = ?
ORDER BY b.flight_date DESC;
```

### Flight Delay Analysis
```sql
SELECT 
    delay_category,
    COUNT(*) as frequency,
    AVG(delay_minutes) as avg_delay,
    MAX(delay_minutes) as max_delay
FROM FlightDelays
GROUP BY delay_category
ORDER BY frequency DESC;
```

---

## 📋 Summary Statistics

| Metric | Count |
|--------|-------|
| **Total Tables** | 18 |
| **Total Records** | 574,790 |
| **Customers** | 10,000 |
| **Flight Schedules** | 6,762 |
| **Unique Routes** | 1,127 |
| **DaysOfOperation** | 47,334 |
| **Bookings** | 50,000 |
| **PNRs** | 50,000 |
| **Passengers** | 124,836+ |
| **Itineraries** | 50,000 |
| **ItineraryLegs** | 50,000 |
| **PassengerBaggage** | 124,836+ |
| **FlightInstances** | 10,500 |
| **FlightDelays** | 522 |
| **Payments** | 50,000 |
| **Database Size** | 72 MB |

---

**This schema is designed for:**
✅ Real bookings (50,000 synthetic bookings)  
✅ 20-year coverage (2026-2046)  
✅ Multi-leg journeys (extensible)  
✅ Complex operations (delays, cancellations, rebooking)  
✅ Payment processing (multiple methods)  
✅ Compliance & auditing (full change tracking)  

Ready for open-source release with **NO REAL PII**! 🚀
