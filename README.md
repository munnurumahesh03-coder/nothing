# AI-Firewall-IDS
### A Deep Dive into Deep Learning and Cybersecurity for Network Traffic Classification

**Project Status: Completed & Deployed**

This repository contains the complete code, methodology, and findings for the "AI Firewall," an end-to-end Deep Learning project focused on Network Intrusion Detection Systems (NIDS). 

The project culminates in a production-ready deployment, revealing powerful lessons about data leakage in cybersecurity datasets, the necessity of rigorous neural network regularization, and the engineering required to bridge the gap between a complex research model and a lightning-fast web application.

**Live Web App:** [Test the Live Gradio Dashboard Here!](https://huggingface.co/spaces/mahesh7777777/AI-Firewall-IDS )

### Table of Contents
1.  [Project Objective](#project-objective)
2.  [The Dataset: Challenges and Characteristics](#the-dataset-challenges-and-characteristics)
3.  [Visualizing the Data: Key Insights](#visualizing-the-data-key-insights)
4.  [Methodology: A Rigorous and Repeatable Workflow](#methodology-a-rigorous-and-repeatable-workflow)
5.  [The Gauntlet: Architecture Evolution](#the-gauntlet-architecture-evolution)
6.  [Grand Lessons & Key Findings](#grand-lessons--key-findings)
7.  [The Grand Finale: Dynamic Callbacks & Production Deployment](#the-grand-finale-dynamic-callbacks--production-deployment)
8.  [How to Use This Repository](#how-to-use-this-repository)
9.  [Future Improvements](#future-improvements)

---  
### Project Objective

The grand objective was to build a high-performance, production-ready Deep Learning system capable of classifying network traffic as "Normal" or "Cyberattack" in real-time. The project was divided into two main phases:

1.  **The Universal Preprocessing Blueprint:** Systematically clean, scale, and encode raw network packets into a mathematically stable dataset using dynamic Scikit-Learn pipelines.
2.  **The Deep Learning & Edge Blueprint:** Establish a highly regularized Artificial Neural Network (ANN), extract the most critical features using LightGBM, and deploy a lightweight "Edge Model" to a live Hugging Face web server.

---  
### The Dataset: Challenges and Characteristics
The project uses the benchmark **NSL-KDD** dataset, a refined and highly respected dataset in the cybersecurity domain. It was chosen specifically for its complex, real-world imperfections.  

**Key Characteristics:**
*   **High Cardinality Categoricals:** Features like `service` (e.g., http, ftp, private ) and `flag` contain dozens of unique text values, requiring bulletproof One-Hot Encoding to prevent matrix sparsity and deployment crashes on unseen data.
*   **Extreme Scale Variance:** Numerical features range from single-digit connection counts to massive byte transfers (`src_bytes`), requiring strict standardization so the neural network can converge.
*   **Hidden Data Leaks:** The dataset contains meta-features (like `difficulty_level`) that do not exist in real-time network traffic, posing a massive risk for artificial model inflation.

---
### Visualizing the Data: Key Insights
Exploratory Data Analysis (EDA) and algorithmic feature selection were crucial for understanding the physics of network intrusions and guiding our UI strategy.

**1. The Target Distribution**
Initial profiling revealed a relatively balanced dataset (approx. 53% Normal / 47% Attack), allowing us to rely on standard Binary Crossentropy without needing synthetic oversampling (SMOTE) for the baseline.

**2. The "Heavy Lifters" (LightGBM Feature Selection)**
To optimize the model for a human-readable UI, we deployed a LightGBM Classifier to rank all 41 features based on tree-split importance. This revealed the core physics of an attack:
*   **Connection Metrics:** `src_bytes` and `dst_bytes` are massive indicators of data exfiltration or buffer overflow attempts.
*   **Host Server Rates:** Features like `dst_host_same_srv_rate` and `dst_host_diff_srv_rate` are the strongest predictors of automated probing and port-scanning attacks.

---

### Methodology: A Rigorous and Repeatable Workflow
The true hero of this project was the rigorous, leak-proof workflow. Every step was designed to ensure production readiness.

*   **Dynamic Feature Engineering (The Pipeline):**
    *   **ColumnTransformer:** Built a dynamic Scikit-Learn pipeline that automatically routes numerical data through a `StandardScaler` and categorical data through a `OneHotEncoder` (configured with `handle_unknown='ignore'` to prevent production crashes).
    *   **Target Engineering:** Converted the multi-class attack labels (DoS, Probe, R2L, U2R) into a strict Binary Classification problem (0 = Normal, 1 = Attack) for a robust baseline firewall.

*   **Deep Learning Architecture (The Funnel):**
    *   Designed a Multi-Layer Perceptron (MLP) that forces data compression. By stepping down the neurons (128 ➡️ 64 ➡️ 1), the network is forced to learn underlying attack patterns rather than memorizing the training data.

*   **Model Persistence (Joblib & Keras):** 
    *   The final champion model was saved in the modern `.keras` format, while the exact fitted `ColumnTransformer` and feature lists were serialized into `.pkl` files for seamless web deployment.

---

### The Gauntlet: Architecture Evolution
Before deploying to the web, we had to solve a major UX problem: asking a user to input 41 different network parameters is terrible design. We evolved our architecture to solve this.

| Model Version | Input Features | Architecture | Key Lesson Learned |
| :--- | :--- | :--- | :--- |
| **1. The Research Model** | 41 (Expands to 122) | 128 ➡️ 64 ➡️ 1 | Highly accurate, but too heavy and complex for a user-friendly web dashboard. |
| **2. The Edge Model** | **10 (Top LightGBM)** | **64 ➡️ 32 ➡️ 1** | **THE ULTIMATE CHAMPION.** By slicing the data to the Top 10 features and shrinking the network, we achieved millisecond inference times with near-identical accuracy. |

---

### Grand Lessons & Key Findings
*   **Data Leakage is a Silent Killer:** During EDA, we identified the `difficulty_level` column. We ruthlessly dropped it. Why? Because in the real world, a live network packet doesn't come with a "difficulty" score attached to it. Using future/meta data is cheating.
*   **The Bias-Variance Tradeoff in Action:** Neural networks are prone to massive overfitting on tabular data. By injecting **Batch Normalization** (for mathematical stability) and **30% Dropout** (randomly turning off neurons), we intentionally increased our Bias slightly to completely crush the Variance, ensuring the model generalizes to unseen test data.
*   **UX Dictates ML Architecture:** The best model isn't always the biggest. Using LightGBM to extract the Top 10 features allowed us to build a smaller, faster "Edge Model" that powers a clean, 10-input Gradio dashboard without sacrificing security.

--- 
### The Grand Finale: Dynamic Callbacks & Production Deployment

**1. Dynamic Training (Callbacks)**
Instead of blindly training for 50 epochs, we implemented strict Keras callbacks:
*   `ReduceLROnPlateau`: Dynamically cut the learning rate in half when the model stopped learning, allowing it to take finer steps toward the optimal weights.
*   `EarlyStopping`: Monitored validation loss and halted training the moment the model began to overfit, automatically restoring the absolute best weights.

**2. The Final Verdict**
The champion Edge Model was evaluated on the completely untouched **KDDTest+.txt** set.

| Metric | Final Score |
| :--- | :--- |
| **Test Accuracy** | ~ 85-90% (Highly generalized) |
| **Precision (Attack)** | Optimized to minimize False Alarms |
| **Recall (Attack)** | Optimized to catch maximum Intrusions |

**SUCCESS!** We successfully engineered a deep learning firewall that accurately classifies unseen network traffic, completely free of data leakage.

**3. The Gradio Web Dashboard**
The final Edge Model was deployed to Hugging Face Spaces using Gradio. The UI is designed to take human-readable inputs, dynamically format them into a Pandas DataFrame, push them through the serialized `ColumnTransformer`, and serve real-time AI predictions. Furthermore, TensorFlow was configured to run in CPU-only mode (`CUDA_VISIBLE_DEVICES="-1"`) to guarantee zero memory-allocation crashes on the cloud server.

--- 
### How to Use This Repository
1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/AI-Firewall-IDS.git
    cd AI-Firewall-IDS
    ```
2.  **Explore the Notebook:** 
    *   `NIDS_Deep_Learning.ipynb`: Contains the full pipeline—Data cleaning, LightGBM feature selection, ANN architecture, and model serialization.
3.  **Run the Deployed App Locally:** 
    ```bash
    pip install -r requirements.txt
    python app.py
    ```
    *   **Why Gradio?** It provides a sleek, modern UI that seamlessly handles the complex backend preprocessing, allowing anyone to interact with the deep learning model in real-time.

---

### Future Improvements
While this project is complete and deployed, the methodology opens the door for further exploration:

*   **Sequential Packet Analysis:** Experiment with 1D Convolutional Neural Networks (CNNs ) or Long Short-Term Memory (LSTM) networks to analyze the *sequence* of network packets over time, rather than isolated events.
*   **Live Packet Sniffing:** Replace the manual Gradio inputs with a Python script (using `Scapy`) that intercepts live Wi-Fi traffic and feeds it directly into the Edge Model.
*   **FastAPI Microservice:** Wrap the `.keras` model in a FastAPI REST endpoint, allowing other applications or routers to query the AI Firewall programmatically.
