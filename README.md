# 🧠 EEG Seizure Detection Pipeline

An automated machine learning pipeline that translates raw biological electricity into predictive algorithms. This project detects epileptic seizures from continuous EEG data by mapping clinical neurological signatures (flatlines, fast-spiking, and wave-spike complexes) to mathematical features.

## 🔬 The Biological Premise & Feature Engineering

Epilepsy is not a single waveform; it manifests through dynamic, evolving electrical phases. To capture this, the raw EEG signal is segmented into 4-second epochs. For each of the 23 electrodes, 8 distinct features are extracted (184 total features per window) to capture the exact cellular chemistry of a seizure:

*   **Variance (Electrodecremental Onset):** Captures the pre-seizure "flatline." When the normal background networks momentarily shut down before a seizure, the voltage stops swinging, and variance drops to near-zero.
*   **Power Spectral Density / Welch's Method (Tonic & Clonic Phases):** 
    *   **Gamma (30–50 Hz):** Detects cellular hyper-excitability. High Gamma power flags the rhythmic fast-spiking of the Tonic phase.
    *   **Delta (0.5–4 Hz) & Theta (4–8 Hz):** Detects massive, slow, rolling swells. High Delta power flags the rhythmic muscle jerking of the Clonic recovery phase.
*   **Line Length & Hjorth Mobility (Interictal Discharges):** Welch's method averages frequencies over time, completely missing violent 30-millisecond spikes. Line Length measures the absolute vertical distance traveled by the wave, perfectly detecting rapid wave-spike complexes and jaggedness.

## ⚙️ Architecture & Pipeline

1.  **Ingestion:** Raw `.edf` files ingested via the `mne` library (23 channels, 256 Hz sampling rate).
2.  **Transformation (ETL):** Continuous data is chunked into 4-second 3D tensors (Windows × Channels × Time).
3.  **Feature Extraction:** `scipy.signal` flattens the 3D tensor into a 2D tabular matrix containing the 184 engineered features.
4.  **Class Balancing:** Synthetic Minority Over-sampling Technique (SMOTE) is applied exclusively to the training set to resolve the massive class imbalance between Normal and Seizure windows.
5.  **Inference:** A stateless Random Forest Classifier evaluates the feature matrix to flag pathological epochs.

## 🚀 Local Setup Instructions

Due to GitHub's strict 100 MB file size limit, the raw biological data (`.edf` files) are ignored via `.gitignore` and not included in this repository. To run this pipeline locally:

1. Clone the repository:
   ```bash
   git clone [https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git)
   cd YOUR_REPO_NAME
