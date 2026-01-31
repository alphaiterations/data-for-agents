# Data for Agents 🤖

An open-source library for creating **high-quality synthetic datasets** to train, test, and demo AI agents. Build realistic, production-ready datasets without worrying about real PII or compliance issues.

**Perfect for:**
- 🧠 Training AI/ML agents with realistic data
- 🧪 Building demos and prototypes
- 📚 Learning data structures and workflows  
- 🔬 Experimenting with agent behaviors
- 🏢 Enterprise testing and validation

## 📦 Available Datasets

### ✈️ Airlines Dataset (Indigo)
Complete flight booking system with 50,000 bookings, 10,000 customers, and 1,127 routes.

**Get Started:**
```bash
cd airlines-data
cat QUICK_START.md
```

**What's Included:**
- 🗄️ SQLite database (72 MB, 574,790 records)
- 📔 Jupyter notebook (data generation & modification)
- 📚 Complete documentation & schema
- 💾 1,127 real flight routes from airline_routes.json

---

### 🚧 More Datasets Coming Soon
Stay tuned! We're building datasets for:
- 🛒 E-commerce Platform
- 🏥 Hospital Management System
- 🚕 Ride-sharing App
- 💳 Banking & Financial Services
- 📱 Social Media Platform
- 🎓 Educational System

## 🎯 Quick Start

### 1. Explore Airlines Dataset
```bash
cd airlines-data
sqlite3 indigo_airline.db
```

### 2. Get Started in 5 Minutes
Read the quick start guide:
```bash
cd airlines-data
cat QUICK_START.md
```

### 3. Use in Your Code
```python
import sqlite3
import pandas as pd

# Connect to database
conn = sqlite3.connect('airlines-data/indigo_airline.db')

# Query bookings
df = pd.read_sql_query(
    "SELECT * FROM Bookings LIMIT 100", 
    conn
)
print(df.head())
```

## 📂 Project Structure

```
data-for-agents/
├── README.md                    # This file
├── airlines-data/               # Airlines booking dataset
│   ├── indigo_airline.db        # SQLite database (72 MB)
│   ├── airlines-data.ipynb      # Data generation notebook
│   ├── airline_routes.json      # Source flight routes
│   ├── QUICK_START.md           # Getting started guide
│   ├── README_INDIGO_DATABASE.md
│   ├── SCHEMA_DOCUMENTATION.md
│   ├── FILE_MANIFEST.txt
│   └── DATABASE_SUMMARY.txt
├── .venv/                       # Python virtual environment
└── .git/                        # Git repository
```

## ✨ Why Data for Agents?

### 🔒 Safe & Compliant
✅ **100% Synthetic Data** - Generated using Faker library  
✅ **No Real PII** - Completely anonymized  
✅ **GDPR Compliant** - Safe for any jurisdiction  
✅ **MIT Licensed** - Use freely in any project  

### 📊 Production-Ready
✅ **Realistic Scenarios** - Based on real-world workflows  
✅ **Complete Documentation** - Schema, queries, examples  
✅ **Extensible Design** - Easy to modify & expand  
✅ **Version Controlled** - Track changes over time  

### 🎓 Educational Value
✅ **Learn Real Systems** - Airline, e-commerce, healthcare  
✅ **Practice SQL** - Complex joins, aggregations, transactions  
✅ **Understand Databases** - Schema design, relationships, constraints  
✅ **Build Agents** - Use data to train intelligent systems  

## 📚 Documentation

Each dataset includes comprehensive documentation:

| File | Purpose |
|------|---------|
| **QUICK_START.md** | 5-minute getting started guide |
| **README_*.md** | Complete dataset documentation |
| **SCHEMA_DOCUMENTATION.md** | Detailed table and field definitions |
| **DATABASE_SUMMARY.txt** | Statistics and data overview |
| **FILE_MANIFEST.txt** | What each file contains |

## 🚀 Use Cases

### For AI/ML Engineers
- Train agents with realistic multi-step workflows
- Test agent behaviors in different scenarios
- Benchmark performance across datasets
- Create synthetic user behavior patterns

### For Developers
- Build portfolio projects
- Learn system design patterns
- Practice database queries
- Understand real-world workflows

### For Students
- Capstone & coursework projects
- Bootcamp final projects
- Interview preparation
- Learn data engineering

### For Enterprises
- Proof of concept demos
- Load testing & performance validation
- Training & testing workflows
- Safe sandbox for experimentation

## 🛠️ Technologies

- **SQLite** - Lightweight, portable databases
- **Faker** - Synthetic data generation (Python)
- **Pandas** - Data manipulation & analysis
- **Jupyter** - Interactive notebooks
- **Python** - Core language

## 🤝 Contributing

Want to add a new dataset? We'd love to have you!

1. **Fork** the repository
2. **Create** a new dataset folder
3. **Add** database schema & synthetic data
4. **Document** your dataset thoroughly
5. **Submit** a pull request

Guidelines:
- Use Faker for synthetic names, emails, phones
- Include complete schema documentation
- Provide Jupyter notebook for data generation
- Add example queries and use cases
- Ensure 100% synthetic (no real data)

## 📞 Support & Community

- **Issues & Questions**: GitHub Issues
- **Discussions**: GitHub Discussions
- **Contributing**: See CONTRIBUTING.md (coming soon)

## 📈 Project Statistics

| Metric | Value |
|--------|-------|
| Datasets | 1 (Airlines) |
| Total Records | 574,790 |
| Database Size | ~100 MB |
| Documentation | 5+ files |
| Code Examples | 20+ |
| License | MIT |

## 🗺️ Roadmap

### Q1 2026
- ✅ Airlines Dataset (Complete)
- 🔄 E-commerce Dataset (In Progress)

### Q2 2026
- 📅 Hospital Management System
- 📅 Ride-sharing Platform

### Q3 2026
- 📅 Banking & Financial Services
- 📅 Social Media Platform

### Q4 2026
- 📅 Educational System
- 📅 Dataset Generation Framework

## 📝 License

**MIT License** - Free for personal, educational, and commercial use.

See LICENSE file for details.

## 🙏 Credits & Inspiration

- **Faker Library** - Synthetic data generation
- **Real Datasets** - Inspired by actual systems
- **Open Source Community** - Making data accessible

---

## 📖 Get Started Now

```bash
# Navigate to airlines dataset
cd airlines-data

# Read the quick start guide
cat QUICK_START.md

# Or open the database
sqlite3 indigo_airline.db

# Or run the notebook
jupyter notebook airlines-data.ipynb
```

**Questions?** Check the documentation in the `airlines-data/` folder.

**Want to contribute?** We're building more datasets!

---

**Last Updated**: January 31, 2026  
**Status**: Active Development  
**License**: MIT  
**Maintainers**: Open Source Community
