# Assignment 1: Assessing Bicycle Lane Quality Using Smartphone Sensors

## Team 1

- Raina Allkoçi — 1922785
- Xinran Zhi — 2565145
- Rahma El Sayed — 2551403

## Project overview

This project investigates whether smartphone sensor measurements can distinguish smooth from bumpy cycling surfaces. The workflow includes data loading, exploratory analysis, preprocessing, window-based feature extraction, feature selection, supervised learning, unsupervised learning, model comparison, and deployment on an independent external recording.

Four supervised models are evaluated: K-Nearest Neighbours, Decision Tree, Random Forest, and Logistic Regression. Four unsupervised methods are implemented: Self-Organising Map, DBSCAN, K-Means, and Gaussian Mixture Model.

All project implementation code is contained in `group1.ipynb`; no additional project `.py` files are required.

## Submission structure

```text
group1_HAR_submission.zip
├── group1.ipynb
├── group1_Report.pdf
├── README.md
├── data/
│   ├── participant_1/
│   │   ├── smooth/
│   │   │   └── *.zip
│   │   └── bumpy/
│   │       └── *.zip
│   ├── participant_2/
│   │   ├── smooth/
│   │   │   └── *.zip
│   │   └── bumpy/
│   │       └── *.zip
│   └── participant_3/
│       ├── smooth/
│       │   └── *.zip
│       └── bumpy/
│           └── *.zip
└── DeploymentData/
    ├── Accelerometer.csv
    ├── Gyroscope.csv
    └── Gravity.csv
```

Each ZIP file under `data/` represents one original recording. Keep these recording ZIP files intact: the notebook reads their CSV contents directly.

Extract the outer submission archive before running the notebook. Preserve the folder names and hierarchy shown above.

## Software environment

The saved notebook outputs report the following environment:

| Software | Version |
|---|---|
| Python | 3.12.14 |
| NumPy | 2.5.3 |
| pandas | 3.0.6 |
| Matplotlib | 3.11.2 |
| scikit-learn | 1.9.1 |
| MiniSom | 2.3.6 |

A Jupyter-compatible environment is required to open and execute the notebook.

To create a separate environment with Python 3.12:

```bash
python3.12 -m venv .venv
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the analysis packages and JupyterLab:

```bash
python -m pip install numpy==2.5.3 pandas==3.0.5 matplotlib==3.11.1 scikit-learn==1.9.0 minisom==2.3.6
python -m pip install jupyterlab
```

Internet access is required for initial package installation. The analysis reads the supplied local data files and does not require downloading additional datasets.

## Running the notebook

1. Extract the submission ZIP.
2. Open a terminal in the extracted folder containing `group1.ipynb`.
3. Activate the Python environment described above.
4. Start JupyterLab:

   ```bash
   python -m jupyterlab
   ```

5. Open `group1.ipynb` and select the corresponding Python environment.
6. Restart the kernel and run all cells in order.
7. Wait for all model searches and plots to finish, then save the executed notebook.

The notebook prints its Python and package versions at the beginning. Hyperparameter searches may take several minutes or longer, depending on the computer. Some searches use all available CPU cores.

The submitted notebook includes saved outputs for inspection. Re-running it regenerates the analysis from the supplied source data.

## Collected dataset

The dataset contains 30 recordings from three participants:

| Participant | Smooth recordings | Bumpy recordings |
|---|---:|---:|
| participant_1 | 3 | 7 |
| participant_2 | 3 | 7 |
| participant_3 | 4 | 6 |
| Total | 10 | 20 |

The original recordings contain 420,506 aligned sensor measurements.

Each recording ZIP contains the following sensor files at its top level:

- `Accelerometer.csv`
- `Gyroscope.csv`
- `Gravity.csv`

The notebook reads the columns `time`, `seconds_elapsed`, `x`, `y`, and `z`. It checks that the three sensors have matching `time` values before combining them.

Sensor Logger was configured to record the three sensors at a nominal sampling rate of 100 Hz. The standardisation option was enabled. Phones were placed in the right rear trouser pocket using a consistent intended orientation.

Participant identifiers are pseudonymous. Original recording files are preserved; derived data are generated in memory during execution.

## Labels and metadata

Road-condition labels are obtained from the parent folder of each recording ZIP:

- `smooth`: encoded as `0`
- `bumpy`: encoded as `1`

Bumpy is the positive class for binary precision, recall, and F1-score.

Labels describe the overall condition of each recording. They were not independently verified for every two-second window. A recording labelled bumpy may therefore contain locally smooth sections.

Participant IDs, recording IDs, window IDs, and timestamps are retained as metadata but excluded from model predictors.

## Preprocessing and features

The notebook:

1. Loads and combines the three sensor streams for each recording.
2. Removes the first four seconds and final six seconds to reduce phone-handling artefacts.
3. Divides each trimmed recording into non-overlapping two-second windows.
4. Retains windows containing at least 180 sensor measurements.
5. Extracts 26 numerical features using sensor-axis and magnitude statistics.

For supervised learning, mutual-information feature selection retains ten features within the training pipeline. KNN and Logistic Regression additionally use standardisation fitted within that pipeline.

The unsupervised models use all 26 standardised features without using road-condition labels for fitting.

## Evaluation

Supervised models use three outer leave-one-participant-out folds. Each participant is held out once. Hyperparameters are selected using participant-grouped cross-validation within the remaining training participants.

Feature selection and scaling are fitted within the training process. Balanced accuracy is the primary comparison metric. Accuracy, precision, recall, and F1-score are also reported.

Summary tables report means and standard deviations across participants. Confusion matrices pool the outer-test predictions, so their values may differ from participant-averaged metrics.

Unsupervised evaluation reports silhouette score, road-label Adjusted Rand Index (ARI), Normalised Mutual Information (NMI), and participant ARI. DBSCAN noise windows are excluded from these clustering metrics, and their proportion is reported separately.

SOM is evaluated using its occupied map nodes; these are not predefined road-condition classes.

## External deployment data

`DeploymentData/` contains the independent recording supplied for the assignment. Its three sensor CSV files must be extracted directly into that folder.

The external recording is not used for training, hyperparameter tuning, or model selection. After selecting the model using the labelled participant dataset, the final model is fitted on that labelled dataset and applied to the external recording.

Deployment uses the same trimming, windowing, and feature-extraction rules as model development. The fitted feature selector is applied without refitting on the external data.

Predicted smooth and bumpy intervals are displayed along the recording timeline. The external data have no road-condition ground-truth labels, so deployment predictions cannot establish classification accuracy.

## Outputs and limitations

The notebook displays dataset summaries, exploratory plots, feature-selection results, supervised and unsupervised evaluations, and external deployment visualisations. The PDF report summarises the methodology, findings, and limitations using the CRISP-DM framework.

Important limitations include the three-participant sample, recording-level labels, differences between participants and routes, unmeasured cycling speed, and possible sensitivity to phone orientation. Deployment predictions should be interpreted as model estimates rather than verified road conditions.

## Troubleshooting

- **Raw data folder not found:** confirm that `data/` is beside `group1.ipynb` and start JupyterLab from the extracted submission folder.
- **No recording ZIP files found:** retain the original recording ZIP files under the participant and condition folders.
- **External sensor file not found:** confirm that the three external CSV files are directly inside `DeploymentData/`, without an additional nested folder.
- **Missing package:** install the listed packages in the Python environment used by the notebook kernel.
- **Sensor timestamps do not match:** confirm that the CSV files belong to the same original recording. Do not bypass the alignment check.
- **Unexpected or stale results:** restart the kernel and run all cells from the beginning.