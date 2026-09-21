# Mental Health Lifestyle Model — In Progress

An educational binary-classification project comparing a manually trained single sigmoid neuron, scikit-learn logistic regression, a small hidden-layer neural network, and a constant baseline.

The goal is to predict whether the dataset records a mental health condition. This is a training experiment, not a validated diagnostic tool.

## Project status

This project is in progress. At the current 0.5 threshold, the models predict that **every person has a condition**, including people whose actual label is `None`. The training bug has been corrected, but this prediction problem is still unresolved. I am working on fixing it and improving the model’s ability to distinguish both classes. The experiments listed below are planned next steps, not completed fixes.

## Code

[MentalHealthModeling.ipynb](MentalHealthModeling.ipynb) contains the corrected preprocessing, training, evaluation, and plots. Execution outputs are omitted. A copy of the dataset is included in this repository: [Mental_Health_Lifestyle_Dataset.csv](Mental_Health_Lifestyle_Dataset.csv).

## Run in Google Colab

1. Download the notebook and open it in Google Colab using File > Upload notebook, or open this repository through Colab’s GitHub tab.
2. Download `Mental_Health_Lifestyle_Dataset.csv` from this repository and put it in Google Drive at `My Drive/Collab Docs/Mental_Health_Lifestyle_Dataset.csv`.
3. Run the code cell and authorize the Drive mount when prompted. For a different location, update the path in the mount check and in `pd.read_csv(...)`.
4. Review the model comparison, confusion matrices, classification reports, and training-loss plot.

Dependencies: Python 3, NumPy, pandas, matplotlib, and scikit-learn (normally included in Colab). In a fresh environment:

```bash
pip install numpy pandas matplotlib scikit-learn jupyter
```

For local Jupyter use, remove the Google Drive mounting block and change the CSV path to a local file.

## Dataset and features

The supplied dataset has 3,000 rows. This version retains the original three inputs:

- Happiness Score: standardized using training rows only.
- Gender: one-hot encoded, with Other as the reference.
- Diet Type: one-hot encoded, with Keto as the reference.

Target: `Mental Health Condition`. The literal `None` maps to 0 (595 rows). Anxiety, Depression, Bipolar, and PTSD map to 1 (2,405 rows). Missing or unexpected labels raise an error instead of being treated as healthy examples.

The CSV is read with `keep_default_na=False` to preserve the literal `None`. The split is stratified: 80% training, 20% test, random seed 42.

## What was fixed

- Removed the extra division by training-set size in the manual gradient.
- Used stable binary cross-entropy to match the logistic-regression objective.
- Replaced ineffective adaptive learning-rate conditions with a rate of 0.1 and 10,000 epochs.
- Standardized happiness using training data only.
- Made label encoding and category references explicit.
- Used effectively unregularized logistic regression for comparison.
- Added a baseline, balanced accuracy, ROC AUC, log loss, and reports for both classes.
- Added a neural network with one hidden layer of eight tanh units.

A single sigmoid neuron without a hidden layer is mathematically logistic regression. The hidden-layer network is a separate comparison model.

## Observed results

The corrected notebook ran successfully in Colab on the supplied dataset:

| Model | Accuracy | Balanced accuracy | ROC AUC | Log loss |
| --- | ---: | ---: | ---: | ---: |
| Training-prevalence baseline | 0.8017 | 0.5000 | 0.5000 | 0.4981 |
| Corrected single neuron | 0.8017 | 0.5000 | 0.5260 | 0.4975 |
| Scikit-learn logistic regression | 0.8017 | 0.5000 | 0.5257 | 0.4975 |
| Hidden-layer network | 0.8017 | 0.5000 | 0.5079 | 0.4980 |

At the 0.5 threshold, all four models predict a recorded condition for every test row. Each confusion matrix is:

```text
                     Predicted 0  Predicted 1
Actual 0 (None)              0          119
Actual 1 (condition)         0          481
```

The manual model’s probabilities match scikit-learn within approximately 0.00017, providing a check on training. Near-chance AUC and zero recall for the no-condition class show that fixing training did not produce useful discrimination with these three inputs. Accuracy alone is misleading because about 80% of rows are positive.

Results may vary slightly with library versions. Dataset origin, collection methods, and clinical validity have not been independently established.

## Possible next experiments

These are proposed improvements, not implemented features:

- Evaluate other lifestyle inputs, such as sleep, stress, exercise, work hours, and social interaction.
- Compare class-weighted training and thresholds chosen on validation data.
- Use a fresh held-out test set for future final evaluation, since the current test results have already been inspected.

Changing the threshold can produce both classes without improving discrimination. Coefficients describe fitted associations, not causal effects or clinically validated risk.
