# Indigo Airline Booking System - Open Source Backend Database

A comprehensive, production-ready SQLite database schema and synthetic dataset for building an airline booking system, specifically designed for **IndiGo Airlines (6E)** operations. Perfect for learning, development, demos, and open-source contributions.

## 📊 Database Overview

- **Total Records**: 574,790
- **Database Size**: ~72 MB
- **Tables**: 18 core tables
- **Flight Coverage**: 1,127 unique routes across India and international destinations
- **Booking Data**: 50,000 synthetic bookings with passengers and baggage
- **Time Span**: 20 years (2026-2046) for booking capabilities
- **Data Type**: 100% Synthetic using Faker library (no real PII)

## 🗄️ Database Schema

### Core Modules

#### 1. **Master Schedule Module** (Static)
- `FlightSchedule` - Flight templates with timing, aircraft type, capacity
- `DaysOfOperation` - Weekly schedule and effective date ranges
- `ConnectionRules` - Minimum connection times for domestic/international transfers

#### 2. **Customer Module** (Synthetic)
- `Customers` - 10,000 synthetic airline customers with profiles
  - Email, phone (unique identifiers)
  - Loyalty program membership
  - Customer segment (economy/premium/vip)

#### 3. **Booking & PNR Module** (Transactional)
- `Bookings` - 50,000 flight reservations with pricing
  - PNR linking (Passenger Name Record)
  - Booking status (confirmed/pending/cancelled)
  - Final fare with taxes and discounts
- `PNRs` - Unique 6-character PNR codes (e.g., A123456)
- `Itineraries` - Journey information (single/multi-leg)
- `ItineraryLegs` - Individual flight segments in a journey

#### 4. **Passenger Module** (Details)
- `Passengers` - 124,836+ passenger records
  - One-to-many per booking (1-4 passengers typical)
  - Passenger type (adult/child/infant)
  - Synthetic passport numbers (masked)

#### 5. **Baggage Module** (Ancillary)
- `PassengerBaggage` - Checked and cabin baggage tracking
  - Weight, dimensions, baggage tags
  - Status tracking (booked/checked-in/loaded/delivered)
- `SpecialBaggage` - Sports equipment, instruments, wheelchairs, pets

#### 6. **Flight Operations Module** (Real-time)
- `FlightInstances` - Specific flight on a specific date
  - 10,500 instances (2 years of weekly data)
  - Scheduled vs. actual times
  - Flight status tracking
- `FlightDelays` - 522 delay records
  - Delay categories: weather, mechanical, crew, air_traffic
  - Estimated revised times
- `FlightCancellations` - Cancellation tracking (extensible)
- `FlightReschedule` - Rebooking management (extensible)

#### 7. **Payment & Refunds Module** (Financial)
- `Payments` - 50,000 payment records
  - Multiple payment methods (credit/debit/UPI/netbanking)
  - Synthetic transaction IDs (no real gateway data)
  - Payment status tracking
- `Refunds` - Refund tracking (extensible)
  - Reasons: cancellation, delays, no-shows
  - Refund status and methods

#### 8. **Audit & Compliance**
- `AuditLog` - Change tracking for transparency
  - Entity type, action (INSERT/UPDATE/DELETE)
  - Old/new values in JSON
  - Timestamp and user tracking

## 🚀 Quick Start

### 1. Prerequisites
```bash
python 3.8+
pip install pandas numpy faker
sqlite3 (included with Python)
```

### 2. Run the Notebook
```bash
# Navigate to the workspace
cd /Users/vijendra/data-for-agents

# Open in VS Code
code airlines-data.ipynb

# Run all cells to:
# ✅ Extract 1,127 Indigo routes from airline_routes.json
# ✅ Create SQLite database schema
# ✅ Generate 10,000 synthetic customers
# ✅ Create 50,000 bookings with passengers & baggage
# ✅ Populate flight instances & delays for 2 years
# ✅ Display summary statistics and sample queries
```

### 3. Access the Database
```python
import sqlite3
conn = sqlite3.connect('indigo_airline.db')
cursor = conn.cursor()

# Example: Find all flights from Delhi to Mumbai
cursor.execute('''
    SELECT flight_id, departure_time, arrival_time, aircraft_type, seat_capacity
    FROM FlightSchedule
    WHERE origin_airport_code = 'DEL' AND destination_airport_code = 'BOM'
    ORDER BY departure_time
''')
```

## 📋 Example Queries

### Search Direct Flights
```sql
SELECT 
    f.flight_id,
    f.departure_time,
    f.arrival_time,
    f.aircraft_type,
    (f.seat_capacity - COUNT(b.booking_id)) as available_seats
FROM FlightSchedule f
LEFT JOIN Bookings b ON f.flight_id = b.flight_id AND b.flight_date = '2026-02-15'
WHERE f.origin_airport_code = 'DEL' AND f.destination_airport_code = 'BOM'
GROUP BY f.flight_id
ORDER BY f.departure_time;
```

### Get Booking Details with Passengers
```sql
SELECT 
    b.booking_id,
    b.pnr_code,
    c.first_name,
    c.last_name,
    COUNT(p.passenger_id) as passenger_count,
    b.final_amount
FROM Bookings b
JOIN Customers c ON b.customer_id = c.customer_id
LEFT JOIN Passengers p ON b.booking_id = p.booking_id
GROUP BY b.booking_id;
```

### Find Delayed Flights
```sql
SELECT 
    fi.flight_id,
    fi.flight_date,
    fi.scheduled_departure,
    fd.delay_reason,
    fd.delay_minutes
FROM FlightDelays fd
JOIN FlightInstances fi ON fd.flight_instance_id = fi.flight_instance_id
WHERE fi.flight_date >= date('now', '-7 days')
ORDER BY fi.flight_date DESC;
```

### Find Connecting Flight Combinations (Multi-leg)
```sql
SELECT 
    leg1.flight_id as outbound,
    leg2.flight_id as connecting,
    leg1.arrival_time as arrives_at_hub,
    leg2.departure_time as departs_from_hub,
    CAST(
        (strftime('%s', leg2.departure_time) - strftime('%s', leg1.arrival_time)) / 60
        AS INTEGER
    ) as connection_time_mins
FROM FlightSchedule leg1
JOIN FlightSchedule leg2 ON leg1.destination_airport_code = leg2.origin_airport_code
WHERE leg1.origin_airport_code = 'DEL'
    AND leg2.destination_airport_code = 'BOM'
    AND connection_time_mins >= 90
ORDER BY connection_time_mins;
```

## 🎯 Use Cases

### For Developers
- **Learn**: Build a real airline booking system from scratch
- **Practice**: SQL queries, database design, transaction management
- **Demo**: Show airlines/travel sites your booking platform capability
- **Test**: Load testing, performance optimization

### For Students
- **Portfolio**: Include in GitHub projects for internships/jobs
- **Coursework**: Database design assignments, backend development projects
- **Bootcamps**: Capstone projects for coding bootcamps

### For Open Source Contributors
- **No PII**: 100% synthetic data, safe for public repositories
- **Realistic**: Based on actual IndiGo operations and routes
- **Extensible**: Add payment gateways, loyalty programs, seat management
- **Collaborative**: MIT licensed for community contributions

## 📊 Data Generation Details

### Synthetic Customer Data
- **10,000 customers** generated using **Faker library** (en_IN locale)
- Unique emails: `customer{N}@indigo.com`
- Phone numbers: Randomized fake numbers
- Cities: Major Indian metros (Delhi, Mumbai, Bangalore, etc.)
- Loyalty IDs: `6EREWARD{random}`
- **NO real PII** - completely anonymized

### Flight Schedule
- **1,127 unique routes** extracted from airline_routes.json
- **6,762 flight instances** (6 departure times per route)
- Aircraft: A320 (180 seats), A321 (194 seats), ATR72 (70 seats)
- Times: Realistic departure times (06:00, 09:30, 12:00, 15:30, 18:00, 21:00)
- Durations: Calculated from distance (~1 hour per 800km)

### Bookings
- **50,000 bookings** distributed across:
  - Dates: Random across 20 years (2026-2046)
  - Passengers: 1-4 per booking (avg 2.5)
  - Fares: ₹2,000-₹15,000 per person
  - Status: 33% confirmed, 33% pending, 33% cancelled (realistic distribution)

### Flight Delays
- **522 delay instances** (~5% of flights)
- Categories: Weather (26%), Crew (26%), Air Traffic (25%), Mechanical (23%)
- Duration: 15-120 minutes
- Status: Marked as 'resolved' for historical data

## 🔐 Safety & Compliance

### PII Protection
✅ **All data is synthetic** - No real customer information  
✅ **Faker library** - Industry standard for test data  
✅ **Email anonymized** - `customer{N}@indigo.com` pattern  
✅ **Phone anonymized** - Random Faker numbers (not real)  
✅ **Passport masked** - Format: `XXX{4-digits}`  

### Open Source Ready
✅ **MIT Licensed** - Free for commercial/personal use  
✅ **No dependencies** on proprietary tools  
✅ **No real payment data** - Synthetic transaction IDs only  
✅ **Audit trail** - AuditLog table for transparency  

## 🛠️ Extending the Database

### Add Real-Time Availability
```sql
ALTER TABLE FlightInstances ADD COLUMN available_seats INTEGER;
UPDATE FlightInstances SET available_seats = (
    SELECT seat_capacity FROM FlightSchedule 
    WHERE flight_id = FlightInstances.flight_id
) - (
    SELECT COUNT(*) FROM Bookings 
    WHERE flight_id = FlightInstances.flight_id
);
```

### Add Seat Maps
```sql
CREATE TABLE SeatMaps (
    seat_map_id INTEGER PRIMARY KEY,
    aircraft_type VARCHAR(20),
    seat_number VARCHAR(10),
    seat_class VARCHAR(50),
    seat_available BOOLEAN DEFAULT 1
);
```

### Add Loyalty Program
```sql
CREATE TABLE LoyaltyTransactions (
    transaction_id INTEGER PRIMARY KEY,
    customer_id VARCHAR(20),
    booking_id VARCHAR(20),
    points_earned INTEGER,
    points_redeemed INTEGER,
    created_at TIMESTAMP
);
```

## 📈 Database Statistics

| Metric | Value |
|--------|-------|
| Total Tables | 18 |
| Total Records | 574,790 |
| Database Size | 72 MB |
| Unique Routes | 1,127 |
| Flight Schedules | 6,762 |
| Customer Records | 10,000 |
| Bookings | 50,000 |
| Passengers | 124,836+ |
| Baggage Records | 124,836+ |
| Flight Instances | 10,500 |
| Delay Records | 522 |
| Payment Records | 50,000 |
| Coverage | 20 years (2026-2046) |

## 📦 Files in This Package

```
/Users/vijendra/data-for-agents/
├── indigo_airline.db                    # SQLite database (72 MB)
├── airline_routes.json                  # Source route data
├── airlines-data.ipynb                  # Data generation notebook
├── README_INDIGO_DATABASE.md            # This file
└── Schema documentation (auto-generated via notebook)
```

## 🤝 Contributing

This is an open-source project! Ways to contribute:

1. **Add Features**: Seat inventory, dynamic pricing, loyalty programs
2. **Improve Queries**: Optimize for performance, add more examples
3. **Documentation**: Add API docs, deployment guides
4. **Testing**: Unit tests for booking logic, payment validation
5. **Internationalization**: Extend to other airlines/countries

## 📝 License

MIT License - Free for personal, educational, and commercial use.

## 🙏 Credits

- **Data Source**: airline_routes.json (flight network data)
- **Synthetic Data**: Faker library (Python)
- **Real Inspiration**: IndiGo Airlines actual operations
- **Designed for**: Open-source community

## 📞 Support

- Questions? Check the notebook cells for detailed implementation
- Want to modify? SQLite is portable—use any SQL tool
- Issues? Fork and contribute improvements!

## 🎓 Learning Resources

- [SQLite Docs](https://www.sqlite.org/docs.html)
- [Faker Documentation](https://faker.readthedocs.io/)
- [Database Design Best Practices](https://en.wikipedia.org/wiki/Database_design)
- [Airline Systems Architecture](https://en.wikipedia.org/wiki/Airline_booking_system)

---

**Ready to build the next generation of airline booking systems?** 🚀

Start with the database, build your backend, and share your innovation!
