# 🚀 Indigo Airline Booking System - Quick Start Guide

## What You Have

✅ **indigo_airline.db** (72 MB)
- Production-ready SQLite database
- 574,790 records across 18 tables
- 50,000 synthetic bookings
- 10,000 synthetic customers
- 1,127 flight routes from airline_routes.json

✅ **airlines-data.ipynb**
- Python notebook that generated the database
- 9 cells with complete implementation
- Data generation, verification, and sample queries

✅ **README_INDIGO_DATABASE.md**
- Complete documentation for open-source release
- Use cases, data safety, extending features

✅ **SCHEMA_DOCUMENTATION.md**
- Detailed table definitions
- All fields with types and constraints
- Sample SQL queries
- Entity relationships

---

## 5-Minute Start

### 1. Open Database in Your Favorite Tool

**Python (Recommended)**
```python
import sqlite3
conn = sqlite3.connect('indigo_airline.db')
cursor = conn.cursor()

# Get basic stats
cursor.execute("SELECT COUNT(*) FROM Bookings")
print(f"Total bookings: {cursor.fetchone()[0]}")
```

**Command Line**
```bash
sqlite3 indigo_airline.db
sqlite> SELECT COUNT(*) FROM Customers;
sqlite> SELECT flight_id, departure_time FROM FlightSchedule LIMIT 5;
```

**GUI Tools** (Download one)
- [DB Browser for SQLite](https://sqlitebrowser.org/)
- [DataGrip](https://www.jetbrains.com/datagrip/)
- [DBeaver Community](https://dbeaver.io/)

---

## 2. Explore the Data

### Find Flights from Delhi to Mumbai
```sql
SELECT flight_id, departure_time, arrival_time, aircraft_type, seat_capacity
FROM FlightSchedule
WHERE origin_airport_code = 'DEL' AND destination_airport_code = 'BOM'
ORDER BY departure_time;
```

### Get a Customer's Bookings
```sql
SELECT 
    c.first_name, c.last_name,
    b.booking_id, b.pnr_code, b.flight_date,
    COUNT(p.passenger_id) as passengers,
    b.final_amount
FROM Customers c
JOIN Bookings b ON c.customer_id = b.customer_id
LEFT JOIN Passengers p ON b.booking_id = p.booking_id
WHERE c.customer_id = 'CUST00000001'
GROUP BY b.booking_id;
```

### Check Flight Delays
```sql
SELECT 
    fi.flight_id, fi.flight_date,
    fd.delay_reason, fd.delay_minutes,
    fd.estimated_departure
FROM FlightDelays fd
JOIN FlightInstances fi ON fd.flight_instance_id = fi.flight_instance_id
LIMIT 10;
```

---

## 3. Database Overview

| Entity | Count | Details |
|--------|-------|---------|
| 🛫 Flights | 6,762 | 1,127 unique routes, 6 daily frequencies |
| ✈️ Routes | 1,127 | DEL-BOM, DEL-HYD, BOM-BLR, etc. |
| 👥 Customers | 10,000 | Synthetic with Faker library |
| 📕 Bookings | 50,000 | Distributed across 20 years |
| 👤 Passengers | 124,836 | 2-3 passengers per booking |
| 🎫 PNRs | 50,000 | A123456 format |
| 💺 Baggage | 124,836 | Checked & cabin baggage tracking |
| ⏱️ Delays | 522 | ~5% delay rate (weather, crew, etc.) |
| 💳 Payments | 50,000 | Credit card, UPI, netbanking, etc. |

---

## 4. Key Tables

### FlightSchedule (Master Data)
```
6E0001: DEL → BOM, 06:00-08:30, A320 (180 seats)
6E0002: DEL → BOM, 09:30-12:00, A321 (194 seats)
6E0003: DEL → BOM, 12:00-14:30, A320 (180 seats)
... (6,762 total)
```

### Customers
```
CUST00000001: Arin Bhattacharyya, arin.xx@indigo.com, Delhi, VIP
CUST00000002: Siddharth Ganesan, siddharth.xx@indigo.com, Mumbai, Economy
... (10,000 total)
```

### Bookings
```
BK00000001: PNR A123456, Customer 1, Flight 6E0001, 2026-02-15
  Status: confirmed | Amount: ₹45,000 | Passengers: 3
BK00000002: PNR B234567, Customer 2, Flight 6E0005, 2026-03-20
  Status: pending_payment | Amount: ₹28,500 | Passengers: 2
```

---

## 5. Common Tasks

### Add a New Booking
```python
import sqlite3
from datetime import datetime

conn = sqlite3.connect('indigo_airline.db')
cursor = conn.cursor()

# Insert booking
cursor.execute('''
    INSERT INTO Bookings 
    (booking_id, pnr_code, customer_id, flight_id, flight_date,
     total_passengers, booking_status, booking_type, total_fare,
     tax_charges, discount, final_amount, created_at, updated_at)
    VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
''', (
    'BK00050001', 'Z999999', 'CUST00000100', '6E0001', '2026-06-15',
    2, 'pending_payment', 'oneway', 8000, 400, 500, 7900,
    datetime.now(), datetime.now()
))

conn.commit()
print("Booking created!")
```

### Search Available Flights
```python
import pandas as pd
import sqlite3

conn = sqlite3.connect('indigo_airline.db')

flights = pd.read_sql_query('''
    SELECT 
        f.flight_id,
        f.departure_time,
        f.arrival_time,
        f.aircraft_type,
        f.seat_capacity
    FROM FlightSchedule f
    WHERE f.origin_airport_code = 'DEL' 
      AND f.destination_airport_code = 'BOM'
    ORDER BY f.departure_time
''', conn)

print(flights)
```

### Get Booking Confirmation
```sql
SELECT 
    p.pnr_code,
    c.first_name || ' ' || c.last_name as customer_name,
    p.issue_date,
    p.valid_until,
    b.final_amount,
    (SELECT COUNT(*) FROM Passengers WHERE booking_id = b.booking_id) as passenger_count
FROM PNRs p
JOIN Bookings b ON p.pnr_code = b.pnr_code
JOIN Customers c ON p.customer_id = c.customer_id
WHERE p.pnr_code = 'A123456';
```

---

## 6. Next Steps

### For Developers
1. **Fork & Extend**
   - Add seat inventory management
   - Implement dynamic pricing
   - Build loyalty program integration

2. **Build an API**
   ```python
   # FastAPI example
   from fastapi import FastAPI
   from sqlalchemy import create_engine
   
   app = FastAPI()
   engine = create_engine('sqlite:///indigo_airline.db')
   
   @app.get("/flights/{origin}/{destination}")
   def search_flights(origin: str, destination: str):
       # Query FlightSchedule table
       pass
   ```

3. **Create a Frontend**
   - React/Vue UI for booking
   - Real-time seat selection
   - Payment gateway integration

### For Data Analysts
1. **Route Performance Analysis**
   ```sql
   SELECT 
       origin_airport_code, destination_airport_code,
       COUNT(*) as bookings, SUM(final_amount) as revenue,
       AVG(final_amount) as avg_fare
   FROM Bookings b
   JOIN FlightSchedule f ON b.flight_id = f.flight_id
   GROUP BY origin_airport_code, destination_airport_code
   ORDER BY revenue DESC;
   ```

2. **Customer Segmentation**
   - VIP customers (high spending)
   - Frequent travelers
   - Route preferences

### For Learning
1. **Database Design**: Study the 18-table schema
2. **SQL Practice**: Run the provided queries
3. **Data Generation**: Understand Faker-based synthetic data
4. **Airline Operations**: See how real booking systems work

---

## 7. File Organization

```
/Users/vijendra/data-for-agents/
├── indigo_airline.db                 # ⭐ Main database (72 MB)
├── airlines-data.ipynb               # ⭐ Data generation code
├── airline_routes.json               # Source data (1,127 routes)
├── README_INDIGO_DATABASE.md         # Full documentation
├── SCHEMA_DOCUMENTATION.md           # Table definitions
└── QUICK_START.md                    # This file!
```

---

## 8. Troubleshooting

### "Database is locked"
- Close other connections to the database
- Use proper connection handling:
```python
conn = sqlite3.connect('indigo_airline.db')
try:
    # Do stuff
finally:
    conn.close()
```

### "Table doesn't exist"
- Verify database path is correct
- Run all notebook cells to populate schema

### "Slow queries"
- Add indexes for frequent queries
- Use EXPLAIN QUERY PLAN to analyze

### "Want to reset data"
- Delete `indigo_airline.db` and re-run notebook

---

## 9. Real-World Use Cases

### ✈️ Airline Use Case
- **Seat Inventory**: Add real availability tracking
- **Pricing**: Integrate dynamic pricing algorithms
- **Loyalty**: Track RedFX rewards points
- **Operations**: Monitor cancellations and delays

### 🏢 Travel Agency Use Case
- **Multi-booking**: Book multiple flights for packages
- **Bulk Operations**: Handle group bookings
- **Commission Tracking**: Add travel agency markup
- **Report Generation**: Dashboard for sales

### 📱 Mobile App Use Case
- **Push Notifications**: Delay alerts, booking confirmations
- **Offline Support**: Cache flight data
- **Payment Gateway**: Integrate Razorpay/PayU
- **In-App Wallet**: Handle refunds and credits

### 📊 Analytics Platform
- **Revenue Analysis**: Route-wise, time-wise
- **Customer Behavior**: Booking patterns, preferences
- **Forecasting**: Demand prediction
- **Reporting**: Daily/weekly/monthly dashboards

---

## 10. Open Source Contribution

Want to contribute? Great! 🎉

1. **Fork the repository**
2. **Create a feature branch** (seat management, pricing, etc.)
3. **Add your feature** with documentation
4. **Submit a pull request**
5. **We'll review and merge!**

---

## 📞 Quick Help

| Task | Command |
|------|---------|
| Open database | `sqlite3 indigo_airline.db` |
| Count bookings | `SELECT COUNT(*) FROM Bookings;` |
| Export to CSV | `pandas.read_sql_query(...).to_csv()` |
| Backup database | `cp indigo_airline.db indigo_airline.backup.db` |
| View schema | `PRAGMA table_info(Bookings);` |

---

## 🎓 Learning Path

### Beginner (1-2 weeks)
1. Explore tables with SELECT queries
2. Understand relationships between tables
3. Run sample queries from documentation

### Intermediate (2-4 weeks)
1. Write complex JOINs across 3+ tables
2. Understand booking workflow
3. Add simple features (new booking type, etc.)

### Advanced (4+ weeks)
1. Build complete booking API
2. Implement business logic (delays, refunds)
3. Optimize queries, add performance indexes
4. Deploy to cloud (AWS RDS, Heroku, etc.)

---

## 🚀 Ready to Build?

You have:
✅ Database with 50,000 real-looking bookings  
✅ 1,127 flight routes from real data  
✅ Complete schema documentation  
✅ Working Python notebook  
✅ No real PII (100% synthetic)  

**Start building your airline booking system now!**

Questions? Check the detailed documentation files or explore the notebook cells! 

Happy coding! ✈️

---

**Version**: 1.0  
**Created**: January 31, 2026  
**License**: MIT (Open Source)
