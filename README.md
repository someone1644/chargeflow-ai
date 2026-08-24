# ChargeFlow AI
<<<<<<< HEAD

**Predictive & Grid-Aware EV Charging Optimisation**

ChargeFlow AI is an intelligent EV charging coordination platform that combines demand forecasting, explainable station allocation, grid-aware charging optimisation, and dynamic incentive pricing to reduce charging congestion and distribute demand more effectively across a network.

> **PoC Disclaimer:** ChargeFlow AI is currently demonstrated using a deterministic synthetic five-station network in Chennai. Station telemetry, charging sessions, EV requests, and benchmark scenarios are simulated and are not intended to represent live charging infrastructure.

---

## 📌 Problem Statement

Public Electric Vehicle (EV) fast-charging infrastructure can experience significant spatial and temporal demand imbalance:

- **Spatial Imbalance:** Drivers may cluster at popular charging locations, creating long queues while nearby stations remain underutilised.
- **Temporal & Grid Stress:** Uncoordinated peak arrivals can push simulated station demand toward or beyond configured grid capacity limits.
- **Driver Friction:** Congested stations can increase waiting times and make charging less predictable.
- **Economic Inefficiency:** Static pricing provides limited incentive for drivers to consider less-congested alternatives.
- **Poor Coordination:** Current charging decisions can be made without jointly considering distance, queue pressure, available chargers, grid headroom, and predicted future demand.

### Core Question

> **Can we predict charging demand, intelligently allocate incoming EVs, schedule charging within grid constraints, and influence driver behaviour to reduce network congestion?**

---

## 💡 Solution Overview

**ChargeFlow AI** addresses the problem through five coordinated components:

1. **Demand & Congestion Forecasting**
   - LightGBM regression model
   - Multi-horizon prediction for 15, 30, 60, and 120 minutes
   - Uses temporal, lag, rolling-demand, and station-level features

2. **Explainable Multi-Factor Station Allocation**
   - Transparent five-factor scoring model
   - Considers distance, availability, queue, grid headroom, and future load
   - Produces an interpretable station recommendation

3. **Grid-Aware Charging Scheduling**
   - PuLP-based Mixed Integer Linear Programming (MILP)
   - Allocates charging power while respecting configured grid, charger, and EV charging-rate constraints
   - Distinguishes actively charging and deferred EVs

4. **Dynamic Incentive Pricing**
   - Adjusts station prices according to congestion, queue pressure, and predicted future congestion
   - Makes underutilised stations more attractive and congested stations less attractive

5. **Interactive Operator & Driver Interfaces**
   - React-based operations dashboard
   - Geospatial station monitoring
   - Peak-demand and grid-constraint simulations
   - Driver station recommendation portal
   - Before-versus-after benchmark comparison
=======
---

## Problem Statement
Public Electric Vehicle (EV) fast-charging infrastructure faces acute spatial and temporal demand imbalance:
- **Spatial Imbalance:** Drivers cluster at landmark stations (e.g., tech parks, highway exits), causing long waiting queues (60–90+ minutes), while stations just 2–4 km away sit underutilised (<30% load).
- **Temporal & Grid Stress:** Uncoordinated peak arrivals cause transformer overloads exceeding local distribution substation limits ($kW_{actual} > kW_{transformer\_limit}$), risking blackout events and equipment degradation.
- **Economic Inefficiency:** Flat pricing provides zero incentive for drivers to alter charging behaviors or alleviate grid congestion.

---

## Solution Overview
**ChargeFlow AI** is an intelligent, grid-aware coordination platform that balances network demand through:
1. **Demand & Congestion Forecasting:** LightGBM regression model predicting future station load across 15, 30, 60, and 120-minute horizons using cyclic time encodings and rolling lag features.
2. **Explainable Multi-Factor Allocation:** Transparent, normalized 5-factor scoring engine guiding incoming EVs to optimal stations.
3. **Grid-Constrained MILP Power Scheduling:** PuLP Mixed Integer Linear Programming ensuring total charging power never violates transformer limits.
4. **Behavioral Dynamic Incentive Pricing:** Real-time pricing model applying transparent surcharges to congested stations and discounts to underutilised ones.
5. **Interactive Operations Dashboard & Driver Portal:** High-performance React + Tailwind + Leaflet + Recharts application demonstrating live state transitions and before-vs-after delta metrics.
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d

---

## Architecture & System Design

```text
+-------------------------------------------------------------------------------+
|                              ChargeFlow AI                                    |
+-------------------------------------------------------------------------------+

       [ EV Driver Requests ]                 [ Simulated Grid Telemetry ]
                 │                                      │
                 └──────────────────┬───────────────────┘
                                    ▼
              +---------------------------------------------+
              |              FastAPI Backend                |
              |                                             |
              |  /api/stations   /api/forecast              |
              |  /api/allocate   /api/schedule              |
              |  /api/simulate/* /api/metrics               |
              +-------------------------┬-------------------+
                                        │
                                        ▼
              +---------------------------------------------+
              |          Core Decision Engines              |
              |                                             |
              |  1. LightGBM Demand Forecasting             |
              |  2. Explainable Station Scoring             |
              |  3. PuLP / MILP Charging Scheduler          |
              |  4. Dynamic Incentive Pricing               |
              +-------------------------┬-------------------+
                                        │
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
             Station State       Charging Schedule    Price / Incentive
                    │                   │                   │
                    └───────────────────┼───────────────────┘
                                        ▼
              +---------------------------------------------+
              |          React Operator Dashboard           |
              |                                             |
              |  • Station Map                              |
              |  • KPI Cards                                |
              |  • Grid Load Charts                         |
              |  • Queue & Wait Indicators                  |
              |  • Demand Forecast Visualisation            |
              |  • Simulation Controls                      |
              |  • Before / After Benchmark                 |
              +---------------------------------------------+
                                        │
                                        ▼
              +---------------------------------------------+
              |              Driver Portal                  |
              |                                             |
              |  • Current Location                         |
              |  • Battery / Target SOC                     |
              |  • Recommended Station                      |
              |  • Score Breakdown                          |
              |  • Price & Incentive                        |
              |  • Alternative Stations                     |
              +---------------------------------------------+
```

---

<<<<<<< HEAD
## 📐 Core Decision Engines
=======
## Core Algorithms & Mathematical Formulations
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d

### 1. Explainable Multi-Factor Station Allocation

For an incoming EV request $e$ and candidate station $s$, ChargeFlow computes five normalised scores in the range $[0, 1]$, where $1.0$ represents the most favourable condition:

$$\text{Score}(s, e) = w_d \cdot S_{\text{dist}} + w_a \cdot S_{\text{avail}} + w_q \cdot S_{\text{queue}} + w_h \cdot S_{\text{headroom}} + w_f \cdot S_{\text{future}}$$

#### Default Weight Configuration

| Factor | Weight ($w$) |
|---|:---:|
| **Distance** | 25% ($0.25$) |
| **Availability** | 25% ($0.25$) |
| **Queue** | 20% ($0.20$) |
| **Grid Headroom** | 20% ($0.20$) |
| **Future Load** | 10% ($0.10$) |

#### Component Formulations

- **Distance Score:**
  $$S_{\text{dist}} = \max\left(0, 1 - \frac{\text{distance}_{\text{km}}}{20}\right)$$
  *A closer station receives a higher score, with the score clipped at zero beyond the 20 km threshold.*

- **Availability Score:**
  $$S_{\text{avail}} = \frac{\text{available\_chargers}}{\max(\text{total\_chargers}, 1)}$$
  *Stations with more available charging capacity receive a higher score.*

- **Queue Score:**
  $$S_{\text{queue}} = \max\left(0, 1 - \frac{\text{queue\_length}}{\max(2 \times \text{total\_chargers}, 1)}\right)$$
  *Stations with shorter queues receive a higher score.*

- **Grid Headroom Score:**
  $$S_{\text{headroom}} = \text{clip}\left(\frac{\text{grid\_limit} - \text{current\_load}}{\max(\text{grid\_limit}, 1)}, 0, 1\right)$$
  *Stations with more unused transformer/grid capacity receive a higher score.*

- **Future Load Score:**
  $$S_{\text{future}} = 1 - \text{predicted\_utilisation}$$
  *A station predicted to remain lightly utilised receives a higher score. If a prediction is unavailable, a neutral default of 0.5 is used.*

#### Final Recommendation
The final station score is a weighted combination of these five factors. The Driver Portal exposes the score breakdown transparently so that recommendations can be explained rather than presented as a black-box decision.

---

<<<<<<< HEAD
### 2. Grid-Aware Charging Scheduling — PuLP MILP
=======
## Measured Benchmark Results: Uncoordinated Baseline vs. ChargeFlow AI
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d

For each assigned EV $i$, the optimiser determines:
- $P_i \ge 0$: Charging power in kW
- $z_i \in \{0, 1\}$: Binary status ($1$ = active charging, $0$ = deferred/queued)

#### Objective Function
$$\min \left[ \sum_{i} P_i \cdot C_s + \lambda \sum_{i} (1 - z_i) \right]$$

where:
- $C_s$ is the station charging price (₹/kWh)
- $\lambda = 50$ is the penalty weight for deferring an EV
- $z_i = 1$ indicates active charging; $z_i = 0$ indicates deferred charging

*The objective balances charging power cost against the penalty of deferring EVs.*

#### Constraints

1. **Grid Headroom Constraint:**
   $$\sum_{i} P_i \le P_{\text{grid\_limit}} - P_{\text{current\_load}}$$
   *Total charging power assigned to EVs must remain strictly within available grid headroom.*

2. **Charger Capacity Constraint:**
   $$\sum_{i} z_i \le N_{\text{chargers}}$$
   *The number of simultaneously active EVs cannot exceed available charger slots.*

3. **EV Maximum Charging Rate:**
   $$P_i \le P_{i, \max} \cdot z_i$$
   *An EV cannot receive more power than its onboard maximum charging rate.*

4. **Minimum Active Charging Power:**
   $$P_i \ge 10 \cdot z_i$$
   *An EV marked as active must receive at least 10 kW.*

#### Optimisation Output
The scheduler returns per-EV charging power, active/deferred status, estimated duration, cost, and delay. Active EVs consume scheduled power, while deferred EVs contribute to the queue without consuming power.

> **PoC Note:** Target SOC is used to estimate energy requirement, duration, and cost. Deadline-aware energy fulfilment is not currently enforced as a hard MILP constraint and is planned as a future extension.

---

### 3. Dynamic Incentive Pricing

ChargeFlow adjusts station prices according to current and predicted network conditions:

$$\text{Price}_s = \text{BasePrice} + \Delta_{\text{congestion}} + \Delta_{\text{queue}} + \Delta_{\text{forecast}}$$

#### Pricing Parameters
- **Base Price:** ₹16.0 / kWh
- **Congestion Adjustment ($\Delta_{\text{congestion}}$):**
  - $-₹4.0$ / kWh at 0% utilisation
  - $₹0.0$ adjustment at 50% utilisation
  - $+₹5.0$ / kWh at 100% utilisation
- **Queue Adjustment ($\Delta_{\text{queue}}$):**
  - $+₹0.5$ / kWh per queued EV (capped at $+₹3.0$ / kWh)
- **Forecast Adjustment ($\Delta_{\text{forecast}}$):**
  - $+₹0.5$ / kWh for moderate predicted congestion ($>40\%$)
  - $+₹1.5$ / kWh for high predicted congestion ($>70\%$)
- **Price Bounds:** Minimum ₹10.0 / kWh, Maximum ₹25.0 / kWh

*The pricing mechanism encourages drivers to choose underutilised stations and disincentivises joining overcrowded hubs.*

---

## 📈 Demand Forecasting

ChargeFlow uses a LightGBM regression model to estimate future station demand across multiple time horizons:

- **Forecast Horizons:** 15 min, 30 min, 60 min, 120 min
- **Engineered Feature Groups:**
  - Hour of day, Day of week, Weekend indicator
  - Station identifier encoding
  - Rolling demand averages (3h, 6h, 12h)
  - Demand lag features (1h, 24h)
  - Historical energy and power statistics
  - Morning/evening peak indicators
  - Cyclical sine/cosine temporal encodings

---

## 🧪 Simulation & Benchmarking

ChargeFlow includes a deterministic simulation environment for reproducible evaluation across three scenarios:

1. **Normal Scenario:** Balanced baseline demand across the network.
2. **Peak Demand Scenario:** A burst of incoming EV requests concentrated near ST01 (Central Chennai).
3. **Grid Constraint Scenario:** Derated transformer capacities at ST01 and ST03 to evaluate response under physical grid limits.

### Optimisation Flow

```text
EV Requests ──► Demand Forecast ──► Station Scoring ──► Station Assignment
                                                              │
                                                              ▼
Metrics ◄── Station State Update ◄── Per-EV Power ◄── PuLP MILP Scheduling
```

### Benchmark Results: Uncoordinated Baseline vs. ChargeFlow AI

> **Benchmark Context:** Results are generated deterministically using a synthetic 12-EV peak-demand scenario. They demonstrate the behaviour of the PoC under controlled conditions and are not real-world performance measurements.

| Metric | Uncoordinated Baseline | ChargeFlow AI Optimisation | Impact Delta |
|---|:---:|:---:|:---:|
| **Average Waiting Time** | `97.5 min` | `0.0 min` | **-100.0%** ⚡ |
| **Average Station Queue** | `14.5 EVs` | `5.4 EVs` | **-62.8%** 📉 |
| **Peak Grid Utilisation** | `235.0%` *(Overload)* | `95.0%` *(Safe Headroom)* | **-59.6%** 🛡️ |
| **Grid Overload Incidents** | `12 violations` | `0 violations` | **-100% (12 prevented)** |
| **Station Load Imbalance** | `207.3%` | `67.3%` | **-67.5%** ⚖️ |

---

## Repository Structure

```text
chargeflow-ai/
├── README.md                           # Documentation
├── .gitignore                          # Git ignore rules
├── requirements.txt                    # Python dependencies
│
├── data/
│   ├── stations.csv                    # 5 synthetic Chennai demonstration stations
│   ├── charging_sessions.csv           # Historical synthetic charging sessions
│   ├── ev_requests.csv                 # Incoming synthetic EV requests
│   └── features.csv                    # Engineered ML feature dataset
│
├── backend/
│   └── app/
│       ├── main.py                     # FastAPI entry point & CORS
│       ├── api/
│       │   ├── stations.py             # GET /api/stations, GET /api/stations/{id}
│       │   ├── forecasting.py          # POST /api/forecast
│       │   ├── allocation.py           # POST /api/allocate
│       │   ├── scheduling.py           # POST /api/schedule
│       │   └── simulation.py           # POST /api/simulate/* & GET /api/metrics
│       └── core/
│           ├── optimizer.py            # PuLP MILP grid-constrained power scheduler
│           ├── scoring.py              # Explainable 5-factor scoring engine
│           └── pricing.py              # Dynamic behavioral incentive pricing
│
├── ml/
│   ├── generate_features.py            # Feature engineering pipeline
│   ├── train.py                        # LightGBM 3-fold time-series CV trainer
│   ├── predict.py                      # Multi-horizon inference engine
│   └── models/
│       ├── demand_model.pkl            # Trained LightGBM model weights
│       └── metrics.json                # Model validation metrics
│
├── simulation/
│   ├── generate_data.py                # Deterministic synthetic data generator
│   ├── simulator.py                    # State manager & baseline simulation engine
│   ├── baseline.py                     # Standalone baseline benchmark
│   ├── benchmark.py                    # Side-by-side comparative benchmark runner
│   └── scenarios/
│       ├── normal.json                 # Balanced baseline scenario
│       ├── peak.json                   # 12-EV surge scenario
│       └── grid_constraint.json        # Transformer capacity reduction scenario
│
├── frontend/
│   ├── package.json                    # React 19, Tailwind v4, Recharts, Leaflet
│   ├── vite.config.js                  # Vite bundler configuration
│   ├── index.html                      # HTML entry with dark theme
│   └── src/
│       ├── main.jsx                    # React root entry
│       ├── App.jsx                     # Top-level shell with navigation
│       ├── index.css                   # Tailwind v4 theme & custom utilities
│       ├── components/
│       │   ├── MapView.jsx             # Leaflet dark-theme map with status pins
│       │   ├── StationPanel.jsx        # Station utilisation & capacity cards
│       │   ├── DemandChart.jsx         # Multi-horizon demand forecast chart
│       │   ├── GridLoadChart.jsx       # Load vs. grid capacity bar chart
│       │   ├── QueuePanel.jsx          # Station queue length & wait badges
│       │   ├── Recommendation.jsx      # Transparent recommendation breakdown card
│       │   └── SimulationControls.jsx  # Interactive simulation scenario buttons
│       ├── pages/
│       │   ├── Dashboard.jsx           # Operator operations dashboard
│       │   └── DriverPortal.jsx        # Driver station finder & preset form
│       └── services/
│           └── api.js                  # Axios/Fetch REST API client
│
└── demo/
    └── demo_script.md                  # Step-by-step presentation script
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 19, Vite, Tailwind CSS v4, Recharts, React Leaflet, Lucide React |
| **Backend** | Python 3.12, FastAPI, Pydantic, Uvicorn |
| **Machine Learning** | LightGBM, Scikit-learn, Pandas, NumPy, Joblib |
| **Optimisation** | PuLP with CBC Solver (Mixed Integer Linear Programming) |
| **Geospatial** | Leaflet with CartoDB Dark Matter tiles, Haversine Distance |
| **Data & Simulation** | Deterministic synthetic generator & scenario engine |

---

## Quick Start & Installation

### 1. Prerequisites
- Python 3.10+ (Python 3.12 recommended)
- Node.js 18+ & npm

### 2. Backend Setup
```bash
# Navigate to project root
cd chargeflow-ai

# Create and activate virtual environment
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# (Optional) Regenerate synthetic data & retrain ML model
python simulation/generate_data.py
python ml/generate_features.py
python ml/train.py

# Launch FastAPI backend server
python -m uvicorn backend.app.main:app --host 0.0.0.0 --port 8000 --reload
```
*Backend runs on `http://localhost:8000` (Interactive Swagger docs at `http://localhost:8000/docs`).*

### 3. Frontend Setup
```bash
# Open a new terminal
cd chargeflow-ai/frontend

# Install dependencies
npm install

# Start Vite development server
npm run dev
```
*Frontend runs on `http://localhost:5173`.*

---

<<<<<<< HEAD
## 🔌 API Endpoints

| Method & Endpoint | Description |
|---|---|
| `GET /api/stations` | Retrieve live states for all stations |
| `GET /api/stations/{id}` | Retrieve individual station state & telemetry |
| `POST /api/forecast` | Generate multi-horizon demand forecasts |
| `POST /api/allocate` | Recommend optimal station with score breakdown |
| `POST /api/schedule` | Generate grid-constrained charging schedule via PuLP |
| `POST /api/simulate/peak` | Inject peak-demand surge scenario |
| `POST /api/simulate/grid-constraint` | Inject transformer constraint scenario |
| `POST /api/simulate/optimize` | Execute ChargeFlow AI two-stage optimisation |
| `POST /api/simulate/reset` | Reset simulation state to normal baseline |
| `GET /api/metrics` | Retrieve live network KPIs and comparison metrics |

---

## 🧪 Running the Benchmark

The comparative benchmark can be executed directly from the terminal without the frontend:

=======
## Running Benchmarks & Verification
To execute the automated benchmark script without running the frontend:
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d
```bash
python simulation/benchmark.py
```

This runs the deterministic peak-demand scenario through both the Uncoordinated Baseline and ChargeFlow AI, printing the comparison metrics and percentage improvements.

---

<<<<<<< HEAD
## 📍 Demonstration Geography
=======
## Demonstration Geography Disclaimer
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d

ChargeFlow AI uses a synthetic five-station network distributed across Chennai for demonstration purposes:

| Station ID | Demonstration Station | Latitude | Longitude |
|:---:|---|:---:|:---:|
| **ST01** | Central Chennai | 13.0827 | 80.2707 |
| **ST02** | Anna Nagar | 13.0850 | 80.2101 |
| **ST03** | T. Nagar | 13.0418 | 80.2341 |
| **ST04** | Adyar | 13.0067 | 80.2570 |
| **ST05** | OMR / Perungudi | 12.9650 | 80.2460 |

> **Disclaimer:** These are synthetic demonstration stations. They do not represent actual charging locations or live infrastructure. The underlying algorithms are geography-agnostic and can be configured for any regional charging network.

---

<<<<<<< HEAD
## 🔬 Limitations & Future Scope

### Current PoC Scope & Limitations
- **Simulated Telemetry:** Station telemetry, charging sessions, and EV requests are generated synthetically for reproducible evaluation.
- **External Factors:** Live traffic congestion, weather events, and utility SCADA signals are not currently ingested.
- **MILP Formulation:** Target SOC is used to estimate energy needs; strict departure deadlines are not currently enforced as hard linear constraints.
- **Simulated Pricing:** Pricing adjustments are demonstration incentives and do not process live financial transactions.

### Future Scope
1. **OCPP Gateway Integration:** Connect with physical chargers via OCPP 2.0.1 / ISO 15118 protocol for live telemetry and automated power modulation.
2. **Utility Demand Response:** Ingest OpenADR 2.0b / smart grid signals for automated transformer protection.
3. **Enriched Forecasting:** Integrate live traffic APIs, weather feeds, and event calendars into the LightGBM pipeline.
4. **Deadline-Aware MILP:** Formulate explicit time-indexed energy delivery constraints ensuring departure readiness.
5. **Network Scaling:** Expand the optimisation engine from 5 stations to large-scale multi-CPO regional networks.

---

## 🎬 Presentation Demo Flow

A complete presentation guide is available in [`demo/demo_script.md`](file:///c:/Users/advai/OneDrive/Documents/chargeflow-ai/demo/demo_script.md).

**Recommended Sequence:**
1. **Normal State:** Review balanced initial baseline (green markers, low wait).
2. **Simulate Peak:** Trigger 12-EV surge at ST01 (red alert, 84 min queue wait, ₹23.5/kWh price).
3. **Demand Forecast:** Inspect LightGBM multi-horizon congestion prediction.
4. **Run Optimisation:** Execute ChargeFlow AI (load redistributed, 0 overloads).
5. **Review Comparison:** Examine Before vs. After delta table.
6. **Driver Portal:** Submit driver location to inspect transparent 5-factor scoring breakdown and incentive discounts.

---

## 📄 License

This project is developed as an academic / hackathon proof of concept.
=======
## Limitations & Future Scope
- **Current Scope (MVP PoC):** Evaluated on a deterministic synthetic regional cluster of 5 stations in Chennai and ~8,000 charging sessions with fixed seed for reproducible jury evaluation.
- **Future Scope (Production):**
  1. Real-time OCPP 2.0.1 / ISO 15118 protocol gateway for hardware bi-directional smart charging.
  2. Integration with Utility Smart Grid SCADA / OpenADR 2.0b signals for automated demand response.
  3. Dynamic traffic congestion and weather API ingestion for precise arrival time predictions.
  4. Multi-Agent Reinforcement Learning (MARL) for competitive multi-CPO micro-market bidding.
>>>>>>> 7fc073041401046bd077b8543a3180e740bd126d
