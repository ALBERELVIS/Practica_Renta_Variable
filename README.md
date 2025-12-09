# Arbitrage Study in BME

**Author:** Albert Martin  
**Institution:** Instituto BME  
**Module:** Módulo 2  
**Assignment:** High-Frequency Arbitrage in Fragmented Markets

## Overview

This project analyzes high-frequency arbitrage opportunities in fragmented European equity markets, specifically focusing on Spanish equities traded across multiple venues: BME (Bolsa de Madrid), CBOE Europe, Turquoise, and Aquis.

The study addresses three critical research questions:
1. **Do arbitrage opportunities still exist in Spanish equities?**
2. **What is the maximum theoretical profit** (assuming 0 latency)?
3. **The "Latency Decay" Curve:** How quickly does profit vanish as trading system latency increases (from 0µs to 100ms)?

## Project Structure

```
.
├── README.md
├── Arbitrage Study in BME Albert Martin .ipynb    # Main analysis notebook
├── Working with microsecond timeseries.ipynb      # Tutorial/helper notebook
├── DATA_BIG/                                      # Full dataset
│   ├── AQUIS_2025-11-07/
│   ├── BME_2025-11-07/
│   ├── CBOE_2025-11-07/
│   └── TURQUOISE_2025-11-07/
└── DATA_SMALL/                                    # Sample dataset for testing
    ├── AQUIS_2025-11-07/
    ├── BME_2025-11-07/
    ├── CBOE_2025-11-07/
    └── TURQUOISE_2025-11-07/
```

## Data Structure

### File Types

The dataset contains three types of compressed CSV files for each venue:

- **QTE (Quotes/Snapshots):** Order book snapshots with bid/ask prices and quantities (up to 10 levels)
- **STS (Trading Status):** Market phase updates (Open, Auction, Closed, etc.)
- **TRD (Trades):** Transaction records (not used in this arbitrage study)

### File Naming Convention

All files follow the pattern:
```
<type>_<session>_<isin>_<ticker>_<mic>_<part>.csv.gz
```

Example: `QTE_2025-11-07_ES0113900J37_SAN_XMAD_1.csv.gz`

### Order Book Identity

A single order book is identified by the tuple:
```
(session, isin, mic, ticker)
```

## Key Features

### 1. Data Cleaning & Validation

- **Magic Number Filtering:** Removes invalid price codes (e.g., 999999.999 for market orders, 666666.666 for unquoted)
- **Market Status Validation:** Only considers order books in continuous trading status
- **Microsecond Collision Handling:** Uses nanosecond offsets to handle multiple events at the same microsecond

### 2. Consolidated Tape Construction

- Combines order book snapshots from all venues into a unified timeline
- Forward-fills prices to maintain last known valid bid/ask across venues
- Enables real-time comparison of prices across exchanges

### 3. Arbitrage Detection

- Identifies price discrepancies between venues
- Calculates theoretical profit opportunities
- Filters for tradable conditions (valid prices, continuous trading)

### 4. Latency Analysis

- Simulates trading system latency from 0µs to 100ms
- Measures profit decay as latency increases
- Provides insights into the time-sensitive nature of arbitrage opportunities

## Requirements

### Python Libraries

```python
pandas
numpy
matplotlib
pathlib
glob
```

### Installation

```bash
pip install pandas numpy matplotlib
```

## Usage

### Quick Start (Using Sample Data)

1. Open the main notebook: `Arbitrage Study in BME Albert Martin .ipynb`
2. Ensure `DATA_SMALL` directory is in the project root
3. Run all cells sequentially

### Full Analysis (Using Complete Dataset)

1. Ensure `DATA_BIG` directory is in the project root
2. Modify the `DATA_DIR` variable in the notebook to `'DATA_BIG'`
3. Run all cells (note: this will take significantly longer)

## Data Specifications

### Invalid Price Codes (Magic Numbers)

The following values must be filtered out as they represent non-tradable states:

| Value | Meaning |
|-------|---------|
| 666666.666 | Unquoted/Unknown |
| 999999.999 | Market Order (At Best) |
| 999999.989 | At Open Order |
| 999999.988 | At Close Order |
| 999999.979 | Pegged Order |
| 999999.123 | Unquoted/Unknown |

### Continuous Trading Status Codes

Only order books with these status codes are considered tradable:

| Venue | Status Code(s) |
|-------|----------------|
| AQUIS | 5308427 |
| BME | 5832713, 5832756 |
| CBOE | 12255233 |
| TURQUOISE | 7608181 |

## Methodology

### Time Handling

- All timestamps use **microseconds since Unix epoch (UTC)**
- Multiple events at the same microsecond are handled using nanosecond offsets
- Ensures unique timestamps for proper time-series operations

### Arbitrage Logic

1. **Snapshot Alignment:** Align order book snapshots across venues by timestamp
2. **Price Comparison:** Compare best bid on one venue with best ask on another
3. **Profit Calculation:** Calculate spread minus transaction costs (if applicable)
4. **Latency Simulation:** Apply time delays to simulate real-world execution

## Notebooks

### Main Notebook: `Arbitrage Study in BME Albert Martin .ipynb`

Contains the complete arbitrage analysis including:
- Data ingestion and cleaning
- Order book consolidation
- Arbitrage opportunity detection
- Profit calculations
- Latency decay analysis
- Visualizations

### Tutorial Notebook: `Working with microsecond timeseries.ipynb`

Educational notebook covering:
- Handling microsecond-level timestamps
- Dealing with simultaneous events
- Building consolidated tapes
- Merging high-frequency and low-frequency data
- Vectorization best practices
- Market microstructure concepts

## Results & Visualizations

The analysis produces:
- Price comparison charts across venues
- Arbitrage opportunity timelines
- Profit distribution histograms
- Latency decay curves
- Statistical summaries of opportunities

## Notes

- The code is modularized for clarity and educational purposes
- Vectorization is preferred over loops for performance
- All timestamps are in UTC
- The analysis assumes perfect synchronization across venues (as per data vendor specifications)

## Contact

For questions about this assignment, contact: francisco.merlos@six-group.com

---

**Note:** This project is part of an academic assignment for Instituto BME. The analysis focuses on theoretical arbitrage opportunities and does not constitute financial advice.

