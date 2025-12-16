# Step-by-Step Simulation Tutorial: DCASE 2025 Task 2 Baseline

This tutorial documents the exact process used to simulate the DCASE 2025 Task 2 baseline on a single machine type (`ToyCar`) with reduced training time. Follow these steps to replicate the results.

## 1. Prerequisites & Initial Setup

Before running anything, ensure you are in the project root and have the necessary permissions.

### Grant Execution Permissions
The shell scripts in the repository might not be executable by default. Run the following command to make all `.sh` files executable:

```bash
chmod +x *.sh
```
* **Explanation**: `chmod +x` adds "execute" permission to files. `*.sh` selects all shell scripts in the directory.

## 2. Data Preparation

Standard downloading takes a long time. We will modify the script to download only the `ToyCar` dataset.

### Modify `data_download_2025dev.sh`
Edit the file `data_download_2025dev.sh` to remove all machine types except `ToyCar`.

**Original Code (lines 5-13):**
```bash
for machine_type in \
    "ToyCar" \
    "ToyTrain" \
    "bearing" \
    ...
```

**Modified Code:**
```bash
for machine_type in \
    "ToyCar" \
; do
```
* **Explanation**: This loop controls which files are downloaded. By removing the other entries, we only fetch the `ToyCar` zip file (approx. 430MB), saving significant time and bandwidth.

### Run the Download Script
Execute the modified script to download the raw audio data:

```bash
./data_download_2025dev.sh
```
* **Explanation**: This script creates the directory structure `data/dcase2025t2/dev_data/raw/`, downloads the zip file from Zenodo, and unzips it.

## 3. Environment Setup

We need to install the required Python libraries.

### Fix `requirements.txt`
The original `requirements.txt` enforces specific CUDA versions for PyTorch that might conflict with your environment.

**Change lines 9-10 from:**
```text
torch==2.6.0+cu118
torchvision==0.21.0+cu118
```
**To:**
```text
torch
torchvision
```
* **Explanation**: Removing the version constraints allows `pip` to find the best compatible version of PyTorch for your specific system.

### Install Dependencies
Run the installation command:

```bash
pip3 install -r requirements.txt
```
* **Explanation**: This installs all libraries listed in the file.

### Fix `fasteners` Library
If you encounter an `AttributeError: module 'fasteners' has no attribute 'InterProcessReaderWriterLock'`, update the `fasteners` library:

```bash
pip3 install --upgrade fasteners
```
* **Explanation**: The code uses a class `InterProcessReaderWriterLock` which requires a newer version of the `fasteners` package (v0.20+).

## 4. Script Adjustments

Just like the download script, we need to tell the training and testing scripts to only look for `ToyCar`.

### Modify `01_train_2025t2.sh`
Edit the `dataset_list` variable (around line 31).

**Modified Code:**
```bash
if [ "${dev_eval}" = "-d" ] || [ "${dev_eval}" = "--dev" ]
then
    dataset_list="\
        DCASE2025T2ToyCar \
    "
```
* **Explanation**: This prevents the script from trying to train models for missing datasets (like `bearing`, `fan`, etc.), which would cause errors.

### Modify `02a_test_2025t2.sh` and `02b_test_2025t2.sh`
Apply the **exact same change** to both test scripts. Reduce the `dataset_list` to only include `DCASE2025T2ToyCar`.

## 5. Configuration (Faster Simulation)

Training 100 epochs (default) takes too long for a quick test.

### Modify `baseline.yaml`
Open `baseline.yaml` and change the number of epochs.

**Change line 28:**
```yaml
--epochs: 2
```
* **Explanation**: Reducing epochs from 100 to 2 makes the training finish in less than a minute. This is sufficient to check if the code runs, though the model performance will be poor.

## 6. Running the Pipeline

Now that everything is prepared, run the full pipeline.

### Step A: Train the Model
```bash
./01_train_2025t2.sh -d
```
* **Explanation**: `-d` specifies the "Development" dataset. This script generates the training data (spectrograms) and trains the Autoencoder.
* **Output**: Log files and model checkpoints are saved in `logs/` and `models/`.

### Step B: Run Inference (Testing)
```bash
./02a_test_2025t2.sh -d
```
* **Explanation**: This runs the "Simple Autoencoder" mode inference. It calculates anomaly scores for the test data using the trained model.
* **Output**: CSV files with scores are saved in `results/`.

### Step C: Summarize Results
```bash
./03_summarize_results.sh DCASE2025T2 -d
```
* **Explanation**: This script aggregates the CSV files from the previous step and calculates metrics like AUC and pAUC.
* **Output**: The final summary is saved in `results/dev_data/baseline/summarize/DCASE2025T2/`.

## 7. Verifying Results

You can view the final aggregated metrics to confirm the simulation worked:

```bash
cat results/dev_data/baseline/summarize/DCASE2025T2/DCASE2025T2_auc_pauc.csv
```
* **Explanation**: This CSV contains the AUC scores for the Source and Target domains. Since we only trained for 2 epochs, expect low scores (around 0.5-0.7), but the file's existence proves the pipeline ran successfully.
