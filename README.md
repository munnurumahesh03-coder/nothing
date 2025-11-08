# Titanic Survival Prediction: An End-to-End MLOps Project

**Project Status: Completed**

This repository documents a complete, structured workflow for the Kaggle "Titanic: Machine Learning from Disaster" competition. It follows a rigorous, version-controlled methodology, moving from raw data to a fully trained, explained, and deployed interactive model.

The project's core philosophy is to demonstrate a professional machine learning lifecycle, emphasizing reproducibility, automation, and maintainability. It culminates in a champion model built with CatBoost, explained with SHAP, and deployed with Gradio.

---

### 🏆 Final Competition Score

*   **Official Kaggle Score:** **0.77033**
*   **Leaderboard Rank:** Achieved a score better than ~34% of all participants on the first submission, demonstrating a solid and robust baseline model.

This result highlights a common and important machine learning concept: the **Generalization Gap**. The model's performance on the hidden Kaggle test set (77.0%) was slightly lower than on our own validation set (~83.7%), which is an expected and normal outcome when generalizing to new, unseen data.

---

### Table of Contents
1.  [Key Technologies Used](#-key-technologies-used)
2.  [Project Objective](#-project-objective)
3.  [About the Dataset](#-about-the-dataset)
4.  [The Project Workflow: A Structured Approach](#-the-project-workflow-a-structured-approach)
5.  [Feature Engineering](#-feature-engineering)
6.  [The Core Pipeline: A Single Source of Truth](#-the-core-pipeline-a-single-source-of-truth)
7.  [Model Training & Tuning](#-model-training--tuning)
8.  [Final Model Performance](#-final-model-performance)
9.  [Model Explainability: Unlocking the Black Box](#-model-explainability-unlocking-the-black-box)
10. [Interactive Demo with Gradio](#-interactive-demo-with-gradio)
11. [How to Use This Repository](#-how-to-use-this-repository)
12. [Future Work: The Path to a Higher Score](#-future-work-the-path-to-a-higher-score)

---

### 🛠️ Key Technologies Used
- **Data Analysis & Manipulation:** `Pandas`, `NumPy`
- **Modeling:** `Scikit-learn`, `CatBoost`
- **Hyperparameter Tuning:** `Optuna`
- **Model Explainability:** `SHAP`
- **Interactive Demo:** `Gradio`
- **Version Control:** `Git` & `GitHub`

---

### 🚩 Project Objective

The primary objective was to build a reliable machine learning system to predict passenger survival on the Titanic. This involved two parallel goals:

1.  **Competition Goal:** To accurately predict survival and submit the results to the Kaggle competition.
2.  **Technical Goal:** To implement a professional, end-to-end MLOps workflow, demonstrating best practices for version control, feature engineering, pipelining, model evaluation, interpretability, and deployment.

---

### 💾 About the Dataset

This project uses the classic **Titanic** dataset from Kaggle. It contains information about 891 passengers in the training set and 418 in the test set.

- **Features:** The data includes passenger details like `Age`, `Sex`, `Pclass` (Passenger Class), and `Fare`.
- **Target Variable:** The target variable is `Survived`, a binary indicator (0 = No, 1 = Yes).
- **Challenge:** The dataset contains missing values and a mix of numerical and categorical data, requiring a robust preprocessing pipeline.

---

### ⚙️ The Project Workflow: A Structured Approach

This project was built on a structured, iterative lifecycle. Every step was version-controlled with Git using Conventional Commits to ensure a clean and understandable project history.

1.  **Data Splitting:** A 3-way stratified split (Train/Validation/Test) was used to prevent data leakage and ensure honest model evaluation.
2.  **Feature Engineering:** A `scikit-learn` pipeline was built to automate transformations.
3.  **Model Training:** A `CatBoostClassifier` was trained to establish a strong baseline.
4.  **Hyperparameter Tuning:** `Optuna` was used to search for better parameters. The experiment confirmed the default model was already highly effective.
5.  **Final Evaluation:** The model was evaluated on the held-out test set.
6.  **Interpretability:** `SHAP` was used to explain the model's predictions.
7.  **Deployment:** A `Gradio` web app was built for live, interactive predictions.
8.  **Submission:** The final predictions were formatted and submitted to Kaggle.

---

### 🔬 Feature Engineering

A series of custom transformers were built to engineer new features, capturing deeper insights from the raw data. The most impactful of these were:
*   **`Title`:** Extracted from the `Name` column (e.g., 'Mr', 'Mrs', 'Miss'), which proved to be the most powerful predictive feature.
*   **`FamilySize` & `IsAlone`:** Created by combining `SibSp` and `Parch` to quantify a passenger's family connections.
*   **`Age*Class` & `FarePerPerson`:** Interaction features created to capture combined effects that are more predictive than individual features.

---

### ⛓️ The Core Pipeline: A Single Source of Truth

The heart of this project is a single, robust `scikit-learn` pipeline (`final_pipeline`). This object encapsulates the entire workflow—from raw data to final prediction—preventing data leakage and ensuring perfect reproducibility. When saved to a file (`final_titanic_model.pkl`), it becomes a portable, production-ready artifact.

---

### 🎯 Final Model Performance

The final model was evaluated on the internal, held-out test set before submission.

| Metric         | Test Set Score |
| :------------- | :------------- |
| **AUC Score**  | **0.8622**     |
| Accuracy       | 0.7989         |
| F1-Score       | 0.7231         |

The strong AUC score demonstrates the model's excellent ability to distinguish between surviving and non-surviving passengers.

---

### 🧠 Model Explainability: Unlocking the Black Box

A model is only useful if we can trust it. **SHAP (SHapley Additive exPlanations)** was used to explain the final model's predictions. The plot below confirms that the model learned historically accurate patterns.

![SHAP Feature Importance](assets/shap_summary.png)
*(Note: You will need to add your SHAP plot image to an `assets` folder in your GitHub repo for this to display)*

**Key Drivers of Survival:**
1.  **Title:** The passenger's title ('Mrs', 'Miss' vs. 'Mr') was the single biggest predictor.
2.  **Sex:** Being female dramatically increased the predicted chance of survival.
3.  **Pclass:** Passengers in 1st Class had a much higher survival probability than those in 3rd Class.

---

### 🚀 Interactive Demo with Gradio

To make the model accessible, a simple web application was built using Gradio. This allows anyone to input a passenger's details and receive an instant survival prediction.

![Gradio Demo Screenshot](assets/gradio_demo.png)
*(Note: You will need to add a screenshot of your Gradio app to an `assets` folder for this to display)*

---

### 📖 How to Use This Repository

1.  **Clone the repository:**
    ```
    git clone https://github.com/munnurumahesh03-coder/Your-Repo-Name.git
    cd Your-Repo-Name
    ```
2.  **Explore the Notebook:**
    The `Titanic.ipynb` file contains the complete code for this project, from data loading to the Gradio demo.

---

### 🔮 Future Work: The Path to a Higher Score

While this project provides a robust baseline, the next steps to climb the Kaggle leaderboard would involve:

*   **Advanced Ensembling:** Combine the predictions of several different models (e.g., CatBoost, LightGBM, XGBoost ) to create a more powerful "wisdom of the crowd" prediction.
*   **Deeper Feature Engineering:** Investigate more complex features from the `Ticket` and `Cabin` columns.
*   **Cross-Validation Strategy:** Implement a more complex cross-validation strategy (e.g., Stratified K-Fold) during the final training phase to make the model even more robust.
