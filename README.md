# Smart Home Automation Tool using Dask, PySpark & Spark Streaming

This repository contains the code for our **Smart Home Automation System**, a big-data pipeline that:

- Ingests and explores smart-home + weather data at scale with **Dask** and **PySpark**  
- Trains:
  - **Random Forest models** to predict device on/off states  
  - A **LightGBM regressor** to predict household energy consumption  
- Simulates **real-time streaming** predictions using **Spark Structured Streaming**

The code is organized as a set of Jupyter notebooks designed to run in Google Colab or a local Python environment.
Since the file is too large to upload to GitHub, here is a link to the zip file of the model weights that are used below. Download it and upload it to the directory your notebooks will be connected to:
https://drive.google.com/file/d/18XOCGbz8lMQJw7MQ4wMwqQn-YHIjzbzy/view?usp=sharing

---

## 1. Project Structure

- `matplotlib_visualisations_bigdata.ipynb`
  - Loads the combined smart-home + weather dataset  
  - Writes it to **partitioned Parquet** using Dask
  - Generates the visualizations used in the presentation and more 

- `bigData_device_prediction (1).ipynb`  
  - Feature engineering for device-level behavior  
  - Trains **Random Forest models** for appliance usage (TV, Dryer, Oven, Refrigerator, Microwave)  
  - Saves a `scaler.pkl` and an `appliance_models.pkl` used by the streaming demo

- `BigData_energyusage.ipynb`  
  - Feature engineering for energy forecasting  
  - Trains a **LightGBM** regressor  
  - Saves a `lightgbm_energy_model.txt`

- `sparkstreaming_demo_v2.ipynb`  
  - Spark Structured Streaming demo for **device usage predictions**  
  - Loads `appliance_models.pkl` and `scaler.pkl`  
  - Reads mini-batches generated from the main dataset and outputs live appliance predictions

- `sparkstreaming_energy_prediction_demo_v2.ipynb`  
  - Spark Structured Streaming demo for **energy usage predictions**  
  - Loads `lightgbm_energy_model.txt`  
  - Streams mini-batches from the main dataset and outputs live kWh predictions

---

## 2. Datasets

The notebooks assume a single combined CSV in the repository root:

> `smart_home_dataset_with_weather.csv`

This file is an augmented, merged dataset built from:

1. **Smart Home Dataset (Mendeley)**  
   - Original IoT sensor + activity data (lights, doors, appliances, etc.)

2. **NOAA Weather Data**  
   - Historical weather information: temperature, conditions, etc.

- **Option A – Use our preprocessed CSV**  
  - Place the file in the project root with the exact name:  
    `smart_home_dataset_with_weather.csv`  

- **Option B – Rebuild the CSV yourself**  
  - Download the original smart-home dataset and NOAA data  
  - Merge and augment them following the same schema as used in the notebooks  
  - Ensure the columns at least include:

    ```text
    Unix Timestamp
    Weather_Type
    Outside_Temperature_C
    Line Voltage
    Voltage
    Apparent Power
    Energy Consumption (kWh)
    Television, Dryer, Oven, Refrigerator, Microwave
    ```

  - Save the final file as `smart_home_dataset_with_weather.csv` in the repo root.

---

## 3. Environment Setup

You can either use **Google Colab** (recommended for grading) or a **local environment**.

### 3.1. Using Google Colab

1. Open the notebook from GitHub in Colab (File → Open Notebook → GitHub).
2. In Colab, upload:
   - `smart_home_dataset_with_weather.csv`
   - Any pre-trained model files (`appliance_models.pkl`, `scaler.pkl`, `lightgbm_energy_model.txt`) if you don’t want to retrain.
3. Each notebook installs its own dependencies via:

   ```python
   !pip install dask[complete] pyspark findspark lightgbm numpy pandas matplotlib seaborn -q

