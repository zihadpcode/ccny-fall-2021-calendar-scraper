# CCNY Fall 2021 Academic Calendar Scraper

A guided Python notebook built incrementally with requests, Beautiful Soup, and pandas.

## Current stage

Completed calendar scraper setup and Python date introduction. The scraper will be added in later commits during the walkthrough.

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter notebook
```

Open `fall_2021_calendar.ipynb`. Run cells from top to bottom with Shift + Enter.

## Required result

A DataFrame with a Python datetime.date index named date and columns dow and text.

## Planned commits

1. Create starter notebook and project setup.
2. Download calendar HTML using requests.
3. Extract calendar rows with Beautiful Soup.
4. Parse dates and construct the DataFrame.
5. Validate requirements and document results.

Source: https://www.ccny.cuny.edu/registrar/fall
