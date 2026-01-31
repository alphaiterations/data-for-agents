# Data for Agents - Datasets Overview

A collection of open-source, production-ready synthetic datasets for training, testing, and demonstrating AI agents.

## 📦 Available Datasets

### 1. ✈️ Airlines (Indigo) - Complete
**Location**: `airlines-data/`

A comprehensive airline booking system with realistic data spanning 20 years.

**Dataset Size**:
- 📊 574,790 total records
- 💾 ~100 MB
- 🛫 1,127 flight routes
- 📕 50,000 bookings
- 👥 10,000 customers
- ✈️ 6,762 flight schedules

**Files**:
- `indigo_airline.db` - SQLite database
- `airlines-data.ipynb` - Data generation notebook
- `airline_routes.json` - Flight route data
- Complete documentation

**Start Here**: `airlines-data/QUICK_START.md`

---

### 2. 🛒 E-commerce - Coming Q2 2026
**Location**: `ecommerce-data/` (planned)

Online shopping platform with products, customers, orders, and inventory.

**Expected Dataset**:
- Product catalog (50,000+ items)
- Customer database (10,000+ users)
- Order history (100,000+ orders)
- Inventory management
- Review & rating system

---

### 3. 🏥 Hospital Management - Coming Q2 2026
**Location**: `hospital-data/` (planned)

Healthcare system with patients, doctors, appointments, and medical records.

**Expected Dataset**:
- Patient records (10,000+)
- Doctor profiles (500+)
- Appointments (50,000+)
- Medical history
- Billing & insurance

---

### 4. 🚕 Ride-sharing - Coming Q3 2026
**Location**: `rideshare-data/` (planned)

Ride-sharing platform like Uber/Lyft.

**Expected Dataset**:
- Driver profiles (5,000+)
- Passenger data (20,000+)
- Trip history (100,000+)
- Ratings & reviews
- Payment records

---

### 5. 💳 Banking & Finance - Coming Q3 2026
**Location**: `banking-data/` (planned)

Financial services with accounts, transactions, and lending.

**Expected Dataset**:
- Customer accounts (10,000+)
- Transactions (500,000+)
- Loans & credit
- Account balances
- Fraud detection patterns

---

### 6. 📱 Social Media - Coming Q3 2026
**Location**: `social-media-data/` (planned)

Social network with users, posts, and interactions.

**Expected Dataset**:
- User profiles (50,000+)
- Posts (500,000+)
- Comments & likes (1M+)
- Follower network
- Trending topics

---

### 7. 🎓 Educational System - Coming Q4 2026
**Location**: `education-data/` (planned)

Learning management system with students, courses, and grades.

**Expected Dataset**:
- Student profiles (10,000+)
- Courses (1,000+)
- Enrollments (50,000+)
- Grades & performance
- Learning analytics

---

## 🏗️ Dataset Generation Framework - Coming Q4 2026

A framework for easily creating new synthetic datasets:

```python
from data_for_agents import DatasetGenerator

# Define your schema
schema = {
    'users': {
        'id': 'integer',
        'name': 'person_name',
        'email': 'email',
        'phone': 'phone_number'
    },
    'orders': {
        'id': 'integer',
        'user_id': 'foreign_key(users.id)',
        'amount': 'decimal',
        'created_at': 'datetime'
    }
}

# Generate dataset
generator = DatasetGenerator(schema)
generator.generate(num_records=10000)
generator.export_to_sqlite('my_dataset.db')
```

---

## 🎯 How to Use

### 1. Pick a Dataset
Choose an available dataset from the list above.

### 2. Navigate to Dataset Folder
```bash
cd dataset-name-data/
```

### 3. Read Getting Started Guide
```bash
cat QUICK_START.md
```

### 4. Use the Database
```python
import sqlite3
conn = sqlite3.connect('database.db')
df = pd.read_sql_query("SELECT * FROM table_name", conn)
```

---

## 🔒 Data Privacy & Compliance

All datasets follow strict privacy standards:

✅ **100% Synthetic Data** - Generated using Faker  
✅ **No Real Information** - Completely anonymized  
✅ **GDPR Compliant** - Safe for any jurisdiction  
✅ **MIT Licensed** - Free for any use  

---

## 🚀 Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/alphaiterations/data-for-agents.git
   cd data-for-agents
   ```

2. **Choose a dataset**
   ```bash
   cd airlines-data
   ```

3. **Read documentation**
   ```bash
   cat QUICK_START.md
   ```

4. **Start using**
   ```python
   import sqlite3
   conn = sqlite3.connect('indigo_airline.db')
   ```

---

## 🤝 Contributing

Want to add a new dataset? Contributions welcome!

1. Fork the repository
2. Create dataset in new folder: `dataset-name-data/`
3. Include:
   - SQLite database file
   - Jupyter notebook for generation
   - QUICK_START.md
   - Schema documentation
   - Example queries
4. Submit pull request

---

## 📚 Documentation Structure

Each dataset includes:

```
dataset-name-data/
├── database.db              # SQLite file
├── dataset-name.ipynb       # Generation notebook
├── QUICK_START.md           # Getting started (5 min)
├── README_*.md              # Full documentation
├── SCHEMA_DOCUMENTATION.md  # Technical reference
└── FILE_MANIFEST.txt        # File descriptions
```

---

## 📞 Support

- **Questions?** Check dataset documentation
- **Issues?** GitHub Issues
- **Contributions?** GitHub Pull Requests

---

## 📊 Project Statistics

| Metric | Current | Target |
|--------|---------|--------|
| Datasets | 1 | 7+ |
| Total Records | 574K | 10M+ |
| Database Size | 100 MB | 5+ GB |
| Documentation | 5 docs | 20+ docs |

---

## 🗓️ Roadmap

- **Q1 2026**: Airlines ✅
- **Q2 2026**: E-commerce, Hospital
- **Q3 2026**: Rideshare, Banking, Social Media
- **Q4 2026**: Education, Framework

---

## 📝 License

MIT License - Free for all uses

---

**Happy Data Exploring!** 🚀

For the latest updates, visit: [GitHub Repository](https://github.com/alphaiterations/data-for-agents)
