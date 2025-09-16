
# Quality Control — COVID-19, Weather & Air Quality (Ann Arbor)

This project explores the relationship between **COVID-19 cases** and **environmental factors** (weather and air quality) in the Ann Arbor area. We collect data from public APIs, store it in SQLite, compute **5-day averages**, and generate graphs and a JSON summary for analysis. The study window begins **March 22, 2020** and spans **100 days**.  
*Team: Londyn Jefferson, Grace Strom, Ansileen Washington.*
## Table of Contents
- [Quickstart](#quickstart)
- [Features](#features)
- [Architecture](#architecture)
- [Usage Examples](#usage-examples)
- [FAQ](#faq)
- [Dependencies](#dependencies)
- [Acknowledgements](#acknowledgements)
- [License](#license)

---
## Quickstart
**Prerequisites**
- Python 3.9+ (macOS, Linux, or Windows)

**1) (Optional) Create and activate a virtual environment**
```bash
python -m venv .venv
# macOS/Linux:
source .venv/bin/activate
# Windows:
# .venv\Scripts\activate
pip install -U pip


#2) Set the SQLite database name in the scripts

In airQuality.py, covid.py, and weather.py (inside main()), set:

set_up_database('project3.db')  # change 'project3.db' if you want a different DB name


In dataAndGraphs.py (inside main()), set:

conn = sqlite3.connect(path + "/" + 'project3.db')


#3) Ingest data into SQLite (each script is run four times)

python airQuality.py
python airQuality.py
python airQuality.py
python airQuality.py

python covid.py
python covid.py
python covid.py
python covid.py

python weather.py
python weather.py
python weather.py
python weather.py


#4) Generate charts and the calculations file

python dataAndGraphs.py


#Outputs:

#Charts saved in a graphs/ folder. A JSON file (e.g., calculations.json) with the calculated results
```
---
## Features
- Multi-source ingest of COVID-19, weather, and air-quality metrics for Ann Arbor
- SQLite data store with tables for COVID cases, weather measurements, and air quality (O₃, PM2.5)

- 5-day interval aggregation for COVID totals/new cases and environmental metrics

- Visualizations (line charts, pie chart) plus a JSON summary of calculated values

- Simple, reproducible scripts: covid.py, weather.py, airQuality.py, dataAndGraphs.py



## Architecture
flowchart LR
  subgraph Sources
  ```bash
    COV[COVID-19 API]
    WX[Weather API]
    AQ[Air Quality API]
  end

  subgraph Ingest Scripts
    A[covid.py]
    B[weather.py]
    C[airQuality.py]
  end

  subgraph Storage
    DB[(SQLite: project3.db)]
  end

  subgraph Analysis & Viz
    D[dataAndGraphs.py]
    JSON[calculations.json]
    PNG[graphs/*.png]
  end

  COV --> A --> DB
  WX  --> B --> DB
  AQ  --> C --> DB
  DB  --> D --> JSON
  DB  --> D --> PNG

```
## Usage Examples
Example — run the full pipeline

```python
# assuming you've adjusted the DB name in each file (see Quickstart)

python airQuality.py && python airQuality.py && python airQuality.py && python airQuality.py

python covid.py && python covid.py && python covid.py && python covid.py

python weather.py && python weather.py && python weather.py && python weather.py
```
```python

python dataAndGraphs.py

# => writes charts into graphs/ and a calculations JSON file
```
```python
#Example — helper calls used in analysis (inside dataAndGraphs.py)
#Count air-quality rows in a value range:

from dataAndGraphs import fetch
count = fetch(cur, col='pm2_5', min=12.1, max=35.4)
print(count)
```
```python
#Export calculated values to JSON:

from dataAndGraphs import write_calc_to_file
write_calc_to_file(cur)   # writes calculations.json
```
## FAQ
**Why do I run each ingest script four times?**
The tables are filled incrementally by date buckets; repeated runs ensure the full 100-day window is populated before aggregation.

**What exactly is aggregated?**
COVID totals/new cases are averaged over 5-day windows; weather and air-quality metrics are grouped the same way for comparison.

**Where do the outputs go?**
Graphs are written to graphs/. The calculated results are saved as a JSON file.

**Can I change the DB file name or the date window?**
Yes. Change the DB name in each script (see Quickstart). You can also adjust date logic in the ingest scripts, then re-run the pipeline.

**Do I need API keys?**
Depending on which endpoints you use, some APIs may require keys. Keep secrets out of version control (use a .env that is git-ignored).

## Dependencies
```shell
requests>=2.31
numpy>=1.26
matplotlib>=3.8
pandas>=2.2           # optional but useful
python-dateutil>=2.9  # if doing flexible date math
```
## Acknowledgements

**Team Quality Control**: Londyn Jefferson, Grace Strom, Ansileen Washington

Based on an academic analysis of COVID-19, weather, and air quality in Ann Arbor (starting March 22, 2020, over a 100-day span)

## License

MIT and course policy for SI206
