# AI-Based Rockfall Prediction & Alert System

![Smart India Hackathon 2025](https://img.shields.io/badge/Smart%20India%20Hackathon-2025-blue?style=for-the-badge)
![Theme](https://img.shields.io/badge/Theme-Disaster%20Management-red?style=for-the-badge)
![Category](https://img.shields.io/badge/Category-Software-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Prototype-orange?style=for-the-badge)

> **Problem Statement #25071** — AI-Based Rockfall Prediction and Alert System for Open-Pit Mines  
> **Team:** The Rolling Stones (ID: 86220) | **SIH 2025**

---

## Live Dashboard

**[→ View Live Dashboard](https://rockfall-temp.vercel.app/)**

The interactive dashboard provides real-time risk maps, zone-based color coding (Safe / Caution / High / Critical), sensor overlays, trend forecasts, and emergency controls.

---

## Problem Statement

Open-pit mines face a persistent and deadly challenge: unpredictable rockfalls triggered by slope geometry, fractured rock, rainfall, seismic activity, and pore pressure. Existing solutions are either late, unreliable, fragmented, or too costly:

| Current Approach | Limitation |
|---|---|
| Manual visual checks | Late and unreliable |
| LiDAR scans | High cost, periodic — not real-time |
| Manual instrument logs | Fragmented, no data integration |

Single ML models also fail — rockfall is too complex for any one approach, requiring hybrid physics + data-driven methods.

---

## Our Solution

A **hybrid AI-ML platform** that integrates drone imagery, IoT sensors, Digital Elevation Models (DEMs), and seismic signals to predict rockfall risk in real time — triggering automated alerts before disasters occur.

### Core Idea
- Combine **physics-based block models with ML** to predict rockfall probability, frequency, and magnitude
- **Zone-wise scoring**: derive base risk from terrain features, updated continuously with live weather and sensor inputs
- Output a **risk index (0–10)** classified as `Safe` | `Caution` | `High` | `Critical`

---

## System Architecture

```
IoT Sensors ──┐
UAV Imagery ──┤                              ┌──► Dashboard (GIS Maps)
DEM Data ─────┼──► Feature Engineering ──► ML Risk Model ──► Risk Index ──► Alerts (SMS/Email/App)
SAC Seismic ──┤         (Normalization,       (Ensemble:               └──► Evacuation Radii
Weather API ──┘          Vulnerability        RF + XGBoost + MLP)
                          Scoring)
```

### Data Sources

| Source | Role |
|---|---|
| **DEM** | Terrain geometry for long-term hazard mapping |
| **UAV Imagery** | Surface instability cues (cracks, vegetation stress) |
| **IoT Sensors** | Vibration, slope, gas, weather, piezometer data via MQTT/REST |
| **SAC Seismic Signals** | Underlying seismic activity detection |
| **Bhukosh / NLSM (GSI)** | Pre-classified risk zones, authoritative ground-truth layers |

---

## Model Architecture

### 1. Ensemble Risk Classifier
- **Models:** Random Forest + XGBoost + MLP (Keras API)
- **Input:** Statistical and temporal features with vulnerability scores; Mohr-Coulomb criterion to quantify shear strength exceedance; rainfall and thaw cycles as key predictive features
- **Output:** Risk score (0–10) → `Safe | Caution | High | Critical`

### 2. Image-to-DEM Pipeline (cGAN / pix2pix)
Converts drone/satellite RGB images into Digital Elevation Models.
- **Generator:** U-Net with skip connections — preserves global structure (edges, slopes, terrain alignment)
- **Discriminator:** PatchGAN — evaluates DEM realism patch-by-patch for locally consistent outputs
- **Training:** Generator creates realistic DEMs; discriminator spots fakes, pushing continuous improvement

### 3. UAV Segmentation (DeepLabV3+)
Pixel-wise classification of slope hazards from drone imagery.
- **Encoder:** Multi-scale feature extraction
- **Decoder:** Segmentation masks with skip connections
- **Detects:** Cracks, loose blocks, seepage zones, vegetation stress
- **Optimizations:** Lightweight backbone, removed heavy atrous convolutions, added shallow layers
- **Evaluation Metrics:** mIoU, MPA, FPS

---

## ⚙️ Tech Stack

| Component | Technology |
|---|---|
| UAV Segmentation | DeepLabV3+ (CNN encoder-decoder) |
| Image-to-DEM | cGAN (pix2pix) — U-Net + PatchGAN |
| Risk Model | Random Forest, XGBoost, MLP (Keras) |
| IoT Ingestion | MQTT / REST |
| Geospatial Visualization | GIS overlays, color-coded zone maps |
| Deployment | Containerized, Cloud/Edge flexible |

---

## Interactive Dashboard Features

| Feature | Description |
|---|---|
| **Real-Time Monitoring** | Ingest vibration, slope, gas, weather, and personnel data |
| **Risk Detection & Alerts** | ML-based zone scoring with automated alerts and escalation |
| **Geospatial Visualization** | GIS maps with color-coded zones, sensor overlays, safety radii |
| **Decision Support** | Emergency stop, handover notifications, admin-controlled access |

**[→ Open Dashboard](https://rockfall-temp.vercel.app/)**

---

## Alert System

- **Threshold breaches** trigger SMS, email, and app notifications automatically
- **30+ minute early warnings** enable safe evacuation before risk escalates
- **Safety holidays** can be declared on high-risk days using forecasting data
- **Temporal smoothing + multi-sensor confirmation** to filter noise and reduce false alerts

---

## Feasibility

### Technical
- Low-cost IoT sensors stream live via MQTT/REST
- Robust ensemble classifier (RF, XGBoost, MLP) built with Keras API
- Simple GIS overlays for intuitive decision-making
- Containerized, lightweight backend — replicable across mine sites

### Economic
- **Lower CapEx than LiDAR** — commodity sensors + open-source software
- Early detection prevents costly disruptions and equipment loss
- Automated monitoring reduces manual labor costs

### Operational
- Admin-only deployment with simple authentication
- Alerts and KPIs fit existing mine safety workflows
- Clear geospatial views minimize operator training requirements

---

## Impact & Benefits

| Feature | Impact | Benefit |
|---|---|---|
| Early Alerts + Fail-Safe Controls | Reduces injury/fatality risk | Safer workforce, stronger safety compliance |
| Zone-Based Scoring + Dashboards | Faster detection, better coordination | Reduced downtime, less manual effort |
| Hybrid AI (DEM + UAV + Sensors) | Accurate detection with real-time updates | Scalable across different mine types |
| Low-Cost UAVs + IoT + Edge Deploy | Affordable real-time monitoring | Lower CapEx than LiDAR, future-proof |
| Forecasting & Historical Scoring | Enables proactive safety shutdowns | Moves mines from reactive → predictive safety |

---

## Design Decisions — Why Not SAR or Stereo Imagery?

We evaluated two alternatives and ruled both out:

**Synthetic Aperture Radar (SAR):** Works through clouds and at night, but has limited vertical resolution for mine-scale terrain modeling and a lower update frequency — insufficient for our real-time requirements.

**Stereo Imagery (Sentinel/ASTER):** Lacks the precision needed for early-stage slope instability. Critical factors like pore pressure, rock type, and micro-movements remain unmonitored, limiting detection of fine-scale surface and subsurface changes.

**Our choice:** Hybrid UAV + AI (pix2pix) approach provides superior spatial resolution, real-time update frequency, and mine-scale accuracy that neither SAR nor stereo imagery can match.

---

## Repository Contents

```
this repo
├── README.md              ← You are here
├── presentation/          ← SIH 2025 slide deck (full PPT)
│   └── rolling-stones-sih2025.pptx
```

> This repository documents the system design, architecture, and research for SIH 2025.  
> Prototype code and notebooks are under active development.

---

## Hackathon Details

| Field | Details |
|---|---|
| Event | Smart India Hackathon 2025 |
| Problem ID | 25071 |
| Theme | Disaster Management |
| Category | Software |
| Team Name | The Rolling Stones |
| Team ID | 86220 |
