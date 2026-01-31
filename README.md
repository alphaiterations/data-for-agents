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
# Data for Agents

This repository is an index of datasets for building, testing, and experimenting with agents. It is intentionally minimal and structured so you can add new datasets quickly.

## How this README works

- Each dataset should have its own top-level folder. Add a short entry below using the template.
- Use expandable sections to keep the index readable as it grows.

## Datasets (expandable)

<details>
<summary>airlines-data — Indigo (example)</summary>

- Location: [airlines-data](airlines-data)
- Short description: Synthetic airline bookings, routes, and supporting docs.
- Quick start: see [airlines-data/QUICK_START.md](airlines-data/QUICK_START.md)

More details live in the dataset folder.

</details>

<details>
<summary>Template — Add new dataset</summary>

Use this template when adding a dataset:

- Folder: `your-dataset-name/`
- Required files:
    - `QUICK_START.md` — short usage guide
    - `README.md` — dataset-specific documentation
    - `SCHEMA_DOCUMENTATION.md` — tables/fields description (if applicable)
    - data files or database (e.g., `.db`, `.csv`, `.json`)
- Example entry for this README:

```
<details>
<summary>Your Dataset — One-line description</summary>

- Location: [your-dataset-name](your-dataset-name)
- Short description: Brief summary of what this dataset contains.
- Quick start: see your-dataset-name/QUICK_START.md

</details>
```

Guidelines:
- Keep datasets fully synthetic (no real PII).
- Document schema and usage examples.
- Add a quick start with example queries or code snippets.

</details>

## How to add a dataset

1. Create a new folder at the repo root: `your-dataset-name/`
2. Add dataset files and documentation (see template).
3. Update this README with a new expandable entry (copy the template section).
4. Open a pull request.

## License

Datasets in this repository follow the repo license (see LICENSE).

---

Last updated: January 31, 2026
✅ **No Real PII** - Completely anonymized  
