# The Ultimate ETA Predictor
### A Deep Dive into Geospatial Machine Learning for Swiggy Delivery

**Project Status: Completed & Deployed**

This repository contains the complete code, methodology, and findings for the "Ultimate ETA Predictor," an end-to-end Machine Learning Operations (MLOps) project focused on predicting food delivery times. 

The project culminates in a production-ready deployment, revealing powerful lessons about the physics of machine learning, the dangers of data leakage, and the delicate balance of the Bias-Variance tradeoff. The final conclusion demonstrates a rigorous, Kaggle-level approach to feature engineering and model optimization.

**Live Web App:** [Test the Live Gradio Dashboard Here!](https://huggingface.co/spaces/mahesh7777777/swiggy-eta-predictor )

### Table of Contents
1.  [Project Objective](#project-objective)
2.  [The Dataset: Challenges and Characteristics](#the-dataset-challenges-and-characteristics)
3.  [Visualizing the Data: Key Insights](#visualizing-the-data-key-insights)
4.  [Methodology: A Rigorous and Repeatable Workflow](#methodology-a-rigorous-and-repeatable-workflow)
5.  [The Gauntlet: Baseline Model Leaderboard](#the-gauntlet-baseline-model-leaderboard)
6.  [Grand Lessons & Key Findings](#grand-lessons--key-findings)
7.  [The Grand Finale: Optuna & Production Deployment](#the-grand-finale-optuna--production-deployment)
8.  [How to Use This Repository](#how-to-use-this-repository)
9.  [Future Improvements](#future-improvements)

---  
### Project Objective

The grand objective was to build a high-performance, production-ready Machine Learning system capable of predicting delivery ETAs in real-time. The project was divided into two main phases:

1.  **The Universal EDA Blueprint:** Systematically clean, profile, and mathematically transform raw delivery data into a "Gold" dataset using advanced geospatial and temporal engineering.
2.  **The ML & Optuna Blueprint:** Establish a robust cross-validation strategy, tune the champion model using Bayesian optimization, and deploy the final pipeline to a live Hugging Face web server.

---  
### The Dataset: Challenges and Characteristics
The project uses a real-world Swiggy delivery dataset containing thousands of delivery records. The dataset was chosen specifically for its complex, real-world imperfections.  

**Key Characteristics:**
*   **Geospatial Complexity:** Raw latitude and longitude coordinates are meaningless to standard linear models, requiring advanced mathematical transformations to extract distance and direction.
*   **Temporal Nuance:** Time is cyclical, not linear. The dataset required trigonometry to prevent the model from misunderstanding the boundary between 11:00 PM and Midnight.
*   **High Cardinality Categoricals:** Features like `City_Name` and `Weather` contained too many unique text values for standard One-Hot Encoding, risking massive matrix sparsity and memory crashes.

---
### Visualizing the Data: Key Insights
Exploratory Data Analysis (EDA) was crucial for understanding the physics of food delivery and guiding our feature engineering strategy.

**1. The "Heavy Lifters" (Strongest Predictors)**
Correlation analysis revealed the core physics of the dataset. `multiple_deliveries` (0.38) and `distance` (0.32) were the strongest positive drivers of delay. Fascinatingly, `age` (0.30) showed that older riders take longer, while `ratings` (-0.36) proved that faster riders receive higher scores.

**2. Categorical Impact (The Hidden Rules)**
By extracting the median target values for categories, we uncovered the hidden rules of the Swiggy universe:
*   **Festivals:** Add a massive 20-minute penalty to average delivery times (45 mins vs 25 mins).
*   **Traffic & Weather:** "Jam" traffic adds 11 minutes compared to "Low" traffic, while "Sunny" weather is consistently the fastest condition.

---

### Methodology: A Rigorous and Repeatable Workflow
The true hero of this project was the rigorous, Medallion-Architecture workflow. Every step was designed to prevent data leakage and ensure production readiness.

*   **Advanced Feature Engineering (The Physics Engine):**
    *   **Geospatial Bearing:** Used Arc-Tangent trigonometry to calculate the exact compass direction (0-360°) of the delivery, allowing the model to learn directional traffic patterns.
    *   **Cyclical Time Encoding:** Mapped the 24-hour clock onto a circle using Sine and Cosine transformations, curing the model's "Midnight Trap."
    *   **Domain Ratios:** Engineered custom metrics like `vehicle_strain` (Distance / Vehicle Condition) and `rider_efficiency` (Age * Ratings).

*   **Out-Of-Fold (OOF) Target Encoding:**
    *   Instead of naive target encoding (which causes massive data leakage), we utilized a 5-Fold Cross-Validation loop on the GPU (via RAPIDS `cuML`) to calculate the **Mean** and **Standard Deviation** of the target variable for each category. 

*   **Dynamic Feature Selection:**
    *   Deployed a "Probe" LightGBM model to rank all 83 engineered features, automatically dropping the bottom 1% of noise to prevent overfitting and speed up inference.

*   **Model Persistence (Joblib):** The final champion model, feature lists, and encoding dictionaries were serialized into `.pkl` files for seamless web deployment.

---

### The Gauntlet: Baseline Model Leaderboard
Before advanced tuning, we ran a baseline spot-check to prove our algorithm selection. The results definitively proved the superiority of Gradient Boosting for tabular data.

| Rank | Model | Baseline RMSE (Mins) | R² Score | Key Lesson Learned |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **LightGBM** | **4.0233** | **0.8160** | **THE ULTIMATE CHAMPION.** Blazing fast (0.88s) and highly accurate. |
| 2 | CatBoost | 4.0543 | 0.8131 | Excellent performance, confirming the power of modern boosting. |
| 3 | Random Forest | 4.1354 | 0.8056 | Strong accuracy, but computationally heavy and slow (64.7s). |
| 4 | XGBoost | 4.1442 | 0.8047 | A top-tier contender, but slightly edged out by LightGBM's speed. |
| 5 | Ridge Regression | 7.7494 | 0.3173 | **A key lesson:** Linear models cannot understand complex, non-linear physics like GPS coordinates or cyclical time. |

---

### Grand Lessons & Key Findings
*   **Data Leakage is a Silent Killer:** During EDA, we discovered that `pickup_time_minutes` (wait time at the restaurant) was highly predictive. However, we ruthlessly dropped it. Why? Because in the real world, we must predict the ETA *before* the rider arrives at the restaurant. Using future data is cheating.
*   **The Bias-Variance Tradeoff in Action:** By dropping the data leak and applying strict Optuna regularization, we intentionally increased our Bias slightly (RMSE went from 3.83 to 3.90). However, in exchange, we **completely crushed the Variance**. The gap between our training error and unseen test error shrank to just `0.0010`, guaranteeing the model will never overfit in production.
*   **Math Beats Raw Data:** Four of the top five most important features in the final model were custom mathematical interactions (e.g., `TE_STD_weather_traffic`, `vehicle_strain`). 

--- 
### The Grand Finale: Optuna & Production Deployment

**1. Bayesian Optimization (Optuna)**
We deployed Optuna to hunt for the perfect hyperparameters. It discovered that a low learning rate (`0.021`) combined with deep trees (`89 leaves`) and strict feature/row subsampling (`~0.79`) provided the ultimate shield against overfitting.

**2. The Final Verdict**
The champion LightGBM model was evaluated on the completely untouched **test set**.

| Metric | Final Score |
| :--- | :--- |
| **Overall OOF RMSE (Training)** | 3.9028 minutes |
| **Unseen Test Set RMSE** | **3.9038 minutes** |
| **Unseen Test Set R²** | **0.8267** |

**SUCCESS!** We successfully engineered a model that explains nearly 83% of the variance in delivery times, with an error margin of less than 4 minutes, completely free of data leakage.

**3. The Gradio Web Dashboard**
The final model was deployed to Hugging Face Spaces using Gradio. The UI is designed to take human-readable inputs, dynamically calculate the Trigonometry and Target Encoding in the background, and serve real-time predictions.

--- 
### How to Use This Repository
1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/Swiggy-ETA-Predictor.git
    cd Swiggy-ETA-Predictor
    ```
2.  **Explore the Notebooks:** The project is divided into two modular blueprints:
    *   `01_Universal_EDA_Blueprint.ipynb`: Data cleaning, geospatial math, and exporting the "Gold" dataset.
    *   `02_ML_Optuna_Blueprint.ipynb`: OOF Target Encoding, Optuna tuning, and model serialization.
3.  **Run the Deployed App Locally:** 
    ```bash
    pip install -r requirements.txt
    python app.py
    ```
    *   **Why Gradio?** It provides a sleek, modern UI that seamlessly handles the complex backend mathematics, allowing anyone to interact with the Kaggle-winning model in real-time.

---

### Future Improvements
While this project is complete and deployed, the methodology opens the door for further exploration:

*   **Live Traffic API Integration:** Replace the static "Traffic Density" dropdown with live calls to the Google Maps or Mapbox API for real-time congestion data.
*   **Lightweight Ensembling:** Blend the LightGBM champion with a tuned XGBoost model (e.g., 70/30 split ) to see if we can squeeze the RMSE below 3.80 without sacrificing web server latency.
*   **Deep Learning (TabNet):** Experiment with TabNet, a neural network architecture designed specifically for tabular data, to see if it can automatically learn the geospatial interactions without manual feature engineering.
