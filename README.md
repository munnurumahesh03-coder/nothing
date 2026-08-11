# 🌪️ Aerial Disaster Response AI (Building Damage Assessment)

![Python](https://img.shields.io/badge/Python-3.12-blue )
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-yellow )
![SAHI](https://img.shields.io/badge/SAHI-Inference-orange )
![Streamlit](https://img.shields.io/badge/Streamlit-Deployed-red )

### 🔴 [Live Web App: Try the AI Dashboard Here](https://gnvdgb5u9xxgyrxj9sqptd.streamlit.app/ )

## 📌 The Business Problem
Following a natural disaster (hurricane, tornado, earthquake), first responders and insurance adjusters face a massive bottleneck: manually reviewing thousands of drone and satellite images to locate destroyed infrastructure. 
* **For Rescue Teams (FEMA):** Delays in identifying collapsed buildings cost lives.
* **For Insurance Companies:** Manual roof inspections cost millions in labor and delay payouts to victims.

## 💡 The Solution
An automated Computer Vision pipeline that ingests high-resolution aerial imagery and instantly classifies buildings as **Damaged** or **Safe**. This allows rescue commanders to triage helicopter deployments and automates insurance claim verifications.

---

## 🛠️ Data Engineering & Architecture
This project utilizes a highly optimized **YOLOv8s** architecture, but the true performance comes from rigorous data engineering and advanced inference techniques:

### 1. Programmatic Data Pruning (Handling Extreme Imbalance)
The initial dataset contained extreme class imbalance (600,000+ safe buildings vs. 25,000 destroyed). Training on this would cause the model to blindly predict "Safe" to achieve 89% accuracy. I engineered a Python script to merge overlapping classes (`minor-damage`, `major-damage`, `destroyed`) into a strict binary classification (`Damaged` vs `Safe`), drastically improving model convergence and recall.

### 2. Compute Optimization
Configured the YOLOv8s training pipeline for a T4 GPU using `batch=32`, `patience=5` (Early Stopping), and `cache=True` to optimize cloud compute costs and prevent CUDA Out-Of-Memory (OOM) bottlenecks during training.

### 3. SAHI (Slicing Aided Hyper Inference)
Standard object detection models fail on satellite imagery because shrinking a 4K image to 640p destroys small objects. I integrated SAHI during the inference phase to mathematically slice the images into 320x320 grids, run predictions on each slice, and stitch them back together. This resulted in flawless small-object detection without requiring a massive, computationally expensive model.

---

## 🧠 Real-World MLOps & Lessons Learned
Building the model was only half the battle. Deploying a heavy Computer Vision pipeline to a free-tier cloud server required significant DevOps troubleshooting:

* **Overcoming Cloud RAM Limits (502 Bad Gateway):** 
  Running SAHI on 4K images causes massive RAM spikes. I implemented dynamic image resizing (capping at 1200px) and forced Python Garbage Collection (`gc.collect()`) to instantly clear memory arrays after prediction, stabilizing the Streamlit server.
* **PyTorch CPU Optimization:** 
  Configured `requirements.txt` to pull the lightweight CPU-only version of PyTorch (`--extra-index-url https://download.pytorch.org/whl/cpu` ), preventing the server from crashing while trying to load 4GB GPU binaries.
* **Linux Dependency Hell (`libGL.so.1`):** 
  Headless cloud servers lack the graphics drivers required by OpenCV. I bypassed OS-level version conflicts (Debian Bullseye vs. Trixie) by injecting a `packages.txt` file to install `python3-opencv` directly via `apt-get`, satisfying the C++ dependencies without breaking the server.

---

## 🚀 How to Run Locally
1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/Aerial-Damage-AI.git
