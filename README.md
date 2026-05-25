# AI-Based Rockfall Prediction and Alert System for Open-Pit Mines

> Smart India Hackathon 2025 | Problem Statement #25071 | Theme: Disaster Management
> Team: The Rolling Stones (Team ID: 86220)
> 
> 

---

## Overview

Open-pit mines face a persistent and deadly challenge: unpredictable rockfalls triggered by a combination of slope geometry, fractured rock, rainfall, seismic activity, and pore pressure. Existing solutions like manual visual checks, LiDAR scans, and manual instrument logs are often late, unreliable, fragmented, or too costly to be effective in real time.

Our solution is a hybrid AI-ML platform that integrates drone imagery, IoT sensors, Digital Elevation Models (DEMs), and seismic signals to predict rockfall risk in real time. By combining physics-based block models with machine learning, the system predicts rockfall probability, frequency, and magnitude, triggering automated alerts before disasters occur.

---

## Core Features

* **Real-Time Risk Scoring:** Outputs a risk index (0–10) classified into Safe, Caution, High, or Critical zones. Base risks are derived from terrain features and updated continuously with live weather and sensor inputs.


* **UAV Segmentation:** Utilizes a DeepLabV3+ CNN to detect surface instability cues such as cracks, loose blocks, seepage zones, and vegetation stress from drone imagery.


* **Image-to-DEM Pipeline:** Converts drone or satellite RGB images into Digital Elevation Models using a Conditional GAN (pix2pix architecture), reconstructing slope geometry including angle, roughness, and elevation.


* **Hybrid AI Prediction:** An ensemble classifier (Random Forest, XGBoost, and a Keras-based MLP) trained on historical logs identifies complex patterns for accurate probability estimation.


* **Interactive Dashboard:** Features GIS-based real-time risk maps with color-coded zones, evacuation radii visualized on rockfall kinematics, trend forecasts, and KPI tiles.


* **Automated Alerts:** Threshold breaches trigger SMS, email, and app notifications. Early warnings (30+ minutes lead time) enable safe evacuation and facilitate "safety holidays" on high-risk days.


* **Fail-Safe Controls:** Includes emergency stops, handover notifications, and admin-controlled access to keep operators in charge at all times.



---

## Technical Architecture & Stack

### Data Sources

* **DEM:** Provides terrain geometry for long-term hazard mapping.


* **UAV Imagery:** Captures fixed and dynamic surface instability cues.


* **IoT Sensors:** Measures vibration, slope, gas, weather, and pore water pressure via piezometers. Data streams live via MQTT/REST.


* **SAC Seismic Signals:** Detects underlying seismic activity cues.


* **Reference Geodata:** Utilizes Bhukosh (Geological Survey of India) and National Landslide Susceptibility Mapping (NLSM) to pre-classify risk zones and provide ground-truth layers.



### Tech Stack

| Component | Technology |
| --- | --- |
| **UAV Segmentation** | DeepLabV3+ (CNN encoder-decoder)

 |
| **Image-to-DEM** | cGAN (pix2pix) — U-Net Generator + PatchGAN Discriminator

 |
| **Risk Model** | Random Forest, XGBoost, MLP (Keras API)

 |
| **IoT Data Ingestion** | MQTT / REST

 |
| **Geospatial Visualization** | GIS overlays with color-coded zone maps

 |
| **Deployment** | Lightweight, containerized backend (Cloud/Edge flexible)

 |

---

## Model Workflow

1. **Data Ingestion:** DEM, UAV Imagery, IoT/Weather Data, and SAC Seismic Signals are continuously collected.


2. **Feature Engineering:** Inputs are normalized and transformed into statistical and temporal features with vulnerability scores. The Mohr-Coulomb criterion helps quantify when shear strength is exceeded, and rainfall/thaw cycles act as significant predictive features.


3. **Ensemble Classifier:** The engineered predictive features are fed into the hybrid ML risk model.


4. **Risk Index Generation:** The model outputs a score (0–10) categorized as Safe, Caution, High, or Critical.


5. **Actionable Output:** The risk score drives live dashboard monitoring, automated alerts, and decision support protocols.



---

## Deep Dive: Model Architectures

### UAV Segmentation

* **Model:** DeepLabV3+ encoder-decoder for pixel-wise classification of slope hazards.


* **Encoder:** Multi-scale feature extraction.


* **Decoder:** Segmentation masks with skip connections.


* **Speed Optimizations:** Removed heavy atrous convolutions, added shallow layers, and implemented a lightweight backbone.


* **Training & Evaluation:** Uses image augmentation (flips, rotations, scaling) and cross-entropy + IoU-based loss. Evaluated via mIoU, MPA, and FPS.



### Image-to-DEM Pipeline

* **Method:** Conditional Generative Adversarial Network (cGAN) using pix2pix architecture.


* **Generator (U-Net):** Utilizes an encoder-decoder with skip connections to preserve global structures like edges, slopes, and terrain alignment.


* **Discriminator (PatchGAN):** Evaluates DEM realism patch-by-patch for locally consistent outputs.


* **Training Process:** The generator creates realistic DEMs while the discriminator spots fakes, pushing the generator to continuously improve.



---

## Design Choices: Why Not SAR or Stereo Imagery?

We evaluated Synthetic Aperture Radar (SAR) and Sentinel/ASTER stereo imagery but ruled them out. SAR has limited vertical resolution for mine-scale terrain modeling and a lower update frequency. Stereo imagery lacks the precision required to detect early-stage slope instability, leaving critical factors like pore pressure, rock type, and micro-movements unmonitored. We chose a hybrid UAV + AI (pix2pix) approach for superior spatial resolution and real-time update frequency.

---

## Impact & Viability

* **Proactive Safety:** Moves mines from reactive monitoring to predictive safety, reducing the risk of injury and fatalities.


* **Economic Feasibility:** Offers lower CapEx than LiDAR by utilizing commodity IoT sensors and open-source software, reducing initial investments.


* **Operational Scalability:** Containerized deployment allows seamless scaling across multiple mine sites without over-provisioning.


* **Reduced Downtime:** Early detection prevents costly disruptions, equipment loss, and manual monitoring labor. Temporal smoothing and multi-sensor confirmation are used to filter noise and reduce false alerts.



---

## License

This project was built for a national-level hackathon. Please contact the team before reusing or building upon this work.
