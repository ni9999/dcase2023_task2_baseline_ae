# DCASE 2025 Task 2 Baseline Simulation Walkthrough

This document summarizes the simulation of the DCASE 2025 Task 2 baseline autoencoder system.

## Summary of Changes and Actions

1.  **Data Download**: Downloaded the `ToyCar` development dataset using a modified `data_download_2025dev.sh`.
2.  **Configuration**: Reduced training epochs from 100 to 2 in `baseline.yaml` to speed up the simulation.
3.  **Training**: Trained the autoencoder model on the `ToyCar` dataset using `01_train_2025t2.sh -d` with `MSE` loss.
4.  **Inference**: Ran anomaly detection on the test set using `02a_test_2025t2.sh -d`.
5.  **Results**: Summarized the results using `03_summarize_results.sh`.

## Verification Results

### Training
The model was successfully trained for 2 epochs.
- **Log File**: `logs/baseline/DCASE2023T2-AE_DCASE2025T2ToyCar_id(0_)_seed13711/log.csv`
- **Model Checkpoint**: `models/checkpoint/baseline/DCASE2023T2-AE_DCASE2025T2ToyCar_id(0_)_seed13711/model_epoch_02.pth`

### Inference & Anomaly Scores
Anomaly scores were calculated for the test set.
- **Score File**: `results/dev_data/baseline_MSE/anomaly_score_DCASE2025T2ToyCar_section_00_test_seed13711_id(0_).csv`
- **Decision Result**: `results/dev_data/baseline_MSE/decision_result_DCASE2025T2ToyCar_section_00_test_seed13711_id(0_).csv`

### Performance Metrics
The overall performance for the `ToyCar` section 00 (Source+Target) is summarized below:

| Metric | Score |
| :--- | :--- |
| **AUC (Source)** | 0.7386 |
| **AUC (Target)** | 0.3520 |
| **pAUC** | 0.5026 |

*Note: These results are from a model trained for only 2 epochs and are for demonstration purposes only. They do not reflect the full potential required for the challenge.*

## Detailed Result File
The aggregated results can be found in:
`results/dev_data/baseline/summarize/DCASE2025T2/DCASE2025T2_auc_pauc.csv`
