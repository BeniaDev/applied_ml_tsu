# Homework 1

Cirrhosis Patient Survival Prediction & MLOps

**DEADLINE: 13.03.2026**

---

## Background

Liver cirrhosis is a chronic and progressive liver disease that can lead to severe complications or death if left untreated. Predicting patient survival outcomes provides valuable insights for clinical decision-making and helps design personalized treatment strategies.

## Goal

The main goal of this task is to predict the survival outcome (`Status`) of cirrhosis patients using demographic, biochemical, and clinical data. By developing accurate classification models, we aim to identify high-risk patients and support clinicians in prioritizing care.

## Task

You are challenged to predict the survival outcome of cirrhosis patients. The target variable `Status` has three possible outcomes: `C` (censored), `CL` (censored due to liver transplant), and `D` (death).

Implement and deploy a production-ready classifier. The current homework consists of two parts:

- The Data Science Part
- The MLOps features

Firstly, you need to train a competitive system, and secondly: wrap your code into a production-ready artifact, which may be deployed on any Linux server in one command.

---

## Before We Start

Data Scientist life is more than just experiments in Jupyter environment. About roles in Data Science industry, you can listen this video: [link](https://www.youtube.com/watch?v=lDkTNURDIaY). I hope you will find the job and role in DS you will love to do.

One of my ML teachers said (I hope you will meet him in the next years):

> Data science is an industry that is fully based on tons of open source tools, supported by a wide community. Whenever you, as a Data Scientist, train a model in the Jupyter environment, you have to understand that this moment is only a starting point in the lifecycle of the model, which is assumed to be deployed on the production server. On Linux Server. Hiring a Junior DS, I prefer one with at least basic Linux exposure and Command Line Interface (CLI) experience.

**Linux users:** 👍

**Windows users:** You can try to conquer WSL: https://canonical-ubuntu-wsl.readthedocs-hosted.com/en/latest/guides/install-ubuntu-wsl2/ or use Google Colab: https://colab.google/.

**Mac users:** You can do everything like Linux users in this task and feel comfortable.

---

## Dataset

Here is the competition on Kaggle with Dataset: [link](https://www.kaggle.com/competitions/playground-series-s3e26/overview)

### Key Features

#### Target Variable

- `Status` — patient's survival status:
    - `C` — Censored (alive at last follow-up)
    - `CL` — Censored due to liver transplant
    - `D` — Death

#### Demographic Attributes

| Feature  | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| `id`     | Unique patient identifier                                                   |
| `Age`    | Patient's age (in days) at registration                                     |
| `Sex`    | Biological sex (`M` or `F`)                                                 |
| `N_Days` | Number of days from registration to the earlier of death, transplant, or end of study |

#### Treatment Information

| Feature | Description                                          |
|---------|------------------------------------------------------|
| `Drug`  | Treatment type — either D-penicillamine or Placebo   |

#### Clinical Signs

| Feature        | Description                                                                                      |
|----------------|--------------------------------------------------------------------------------------------------|
| `Ascites`      | Presence of ascites fluid in the abdomen (`Y` / `N`)                                             |
| `Hepatomegaly` | Liver enlargement (`Y` / `N`)                                                                    |
| `Spiders`      | Spider angiomas visible on the skin (`Y` / `N`)                                                  |
| `Edema`        | Fluid retention status (`N` = none, `S` = controlled with diuretics, `Y` = uncontrolled)         |

#### Laboratory Measurements

| Feature        | Description                                    | Unit / Type |
|----------------|------------------------------------------------|-------------|
| `Bilirubin`    | Bilirubin level                                | mg/dL       |
| `Cholesterol`  | Serum cholesterol                              | mg/dL       |
| `Albumin`      | Albumin level                                  | g/dL        |
| `Copper`       | Urinary copper                                 | µg/day      |
| `Alk_Phos`     | Alkaline phosphatase                           | U/L         |
| `SGOT`         | Serum glutamic oxaloacetic transaminase        | U/mL        |
| `Tryglicerides`| Triglycerides                                  | mg/dL       |
| `Platelets`    | Platelet count                                 | 10³/µL      |
| `Prothrombin`  | Blood clotting time                            | seconds     |
| `Stage`        | Histologic stage of disease (1–4)              | ordinal     |

### Files Provided

| File                    | Description                              |
|-------------------------|------------------------------------------|
| `train.csv`             | Training dataset (with target `Status`)  |
| `test.csv`              | Test dataset (without target variable)   |
| `sample_submission.csv` | Template for submission                  |

## Metric

Submissions are evaluated using the **multi-class logarithmic loss**. Each `id` in the test set has a single true class label, `Status`. For each `id`, you must submit a set of predicted probabilities for each of the three possible outcomes: `Status_C`, `Status_CL`, and `Status_D`.

The metric is calculated as:

$$\text{log loss} = -\frac{1}{N}\sum_{i=1}^{N}\sum_{j=1}^{M} y_{ij}\log(p_{ij}),$$

where $N$ is the number of rows in the test set, $M$ is the number of outcomes (i.e., 3), $\log$ is the natural logarithm, $y_{ij}$ is 1 if row $i$ has the ground truth label $j$ and 0 otherwise, and $p_{ij}$ is the predicted probability that observation $i$ belongs to class $j$.

The submitted probabilities for a given row are not required to sum to one because they are rescaled prior to being scored (each row is divided by the row sum). In order to avoid the extremes of the log function, predicted probabilities are replaced with $\max(\min(p, 1 - 10^{-15}), 10^{-15})$.

## Submission File

For each `id` row in the test set, you must predict probabilities of the three outcomes `Status_C`, `Status_CL`, and `Status_D`. The file should contain a header and have the following format:

```
id,Status_C,Status_CL,Status_D
7905,0.628084,0.034788,0.337128
7906,0.628084,0.034788,0.337128
7907,0.628084,0.034788,0.337128
etc.
```

---

## Requirements

We don't accept homework if any of the following requirements are not satisfied:

- The code should be situated in a public GitHub (or GitLab) repository. Two branches: `develop` for development and `master` for the latest working version.
- You should build your project using [Poetry](https://python-poetry.org/docs/) to freeze dependencies of Python packages you use. And attach your `.whl` file. Or if you want you can add only `poetry.lock` file to your repo. Alternatively, you can use [UV](https://github.com/astral-sh/uv).
- Readable and understandable `README.md` file:
    - Your full name & group
    - "**How To**" for your repo: train model, evaluate, deploy. With and without Docker.
    - Resources you utilized
- Your code must be fully covered with logging to `./data/log_file.log`. The file should be viewable and downloadable.
- Proper `.gitignore` file. You do not want rubbish in your repo.
- The major software artifact is `model.py`, containing the class `My_Classifier_Model` with following methods:
    - `train` — receives the dataset filename, performs model training, saves the artifacts to `./model/`, logs the results.
    - `predict` — receives the dataset filename, loads the model from `./data/model/`, saves prediction to `./data/results.csv`.
- An integrated script for training and evaluation from CLI (check out `if __name__ == '__main__':`) so that:

```console
foo@bar:~$ python model.py train --dataset=/path/to/train/dataset
foo@bar:~$ python model.py predict --dataset=/path/to/evaluation/dataset
```

- `.ipynb` notebook in your repo folder `./notebooks/` with your final submission creation and [Optuna](https://optuna.org/) usage. You have to optimize your Gradient Boosting ([CatBoost](https://catboost.ai/)) classifier using Optuna.
- Submission on Kaggle competition with multi-class logarithmic loss (log loss) < 0.55.

---

## Project Milestones

### 1. Data Science in Jupyter

Feel free to stick to Jupyter or Colab environment. Here we expect you to build two classifier models:

1. **Baseline classifier** — here you can start with Logistic Regression or Random Forest Classifier or anything else you want.
2. **CatBoost Classifier** — or any other Gradient Boosting you want (LightGBM, XGBoost, TabNet).

For both tasks, please refer to target metrics in the **Grades** section below.

### 2. Pack into Git Repo

At this point we expect to see a fully working CLI application in the `master` branch. For example, read more about [Fire](https://google.github.io/python-fire/guide/).

### 3. Pack to Production-Ready Solution

#### 3.1 Poetry

Create a `.whl` file with your Python package dependencies using Poetry. The command below should work:

```console
foo@bar:~$ pip install your_file.whl
```

---

## Grades

### 4.1 Data Science Part

| Points | Log Loss       | Description      |
|--------|----------------|------------------|
| 0      | > 0.55         |                  |
| 20     | (0.45; 0.55]   | Good Baseline    |
| 5      | (0.40; 0.45]   | Close to SOTA    |
| 5      | ≤ 0.40         | SOTA?            |

> **P.S.** If you will have Log Loss ≈ 0.0 on closed test set — it will be interesting.
> Please note that cheating with metrics will lead you to the grade **0**.

**Total: 30 points**

### 4.2 MLOps Part

| Points | Bulletpoint                                | Description                                                                                                                                                                                                                                                                                         |
|--------|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 15     | `model.py`                                 | The model is properly packed into the class inside `*.py` file. CLI interface works well: train, predict.                                                                                                                                                                                           |
| 5      | Code quality                               | Clear OOP pattern. Well in-code comments. Built-in documentation for each function. No code duplicates. Meaningful variable names.                                                                                                                                                                  |
| 5      | Poetry or [UV](https://github.com/astral-sh/uv) usage | `.whl` file created using Poetry and `pip install your_package.whl` works. Or working `poetry.lock` file.                                                                                                                                                                                          |
| 5      | Logging                                    | Catch and log all possible errors. Singleton logging pattern (use `logging` module).                                                                                                                                                                                                                |
| 15     | ClearML usage                              | Screenshots from your local hosted ClearML. Show artifacts where you find best parameters using Optuna. [hint1](https://clear.ml/docs/latest/docs/clearml_serving/clearml_serving_setup/) · [hint2](https://clear.ml/docs/latest/docs/integrations/catboost) · [hint3](https://clear.ml/docs/latest/docs/guides/optimization/hyper-parameter-optimization/examples_hyperparam_opt) |
| 10     | Optuna usage                               | Final model hyperparameters found using Optuna. Optional: use wandb.ai to create great post-optimization artifacts ([hint](https://github.com/optuna/optuna-examples/blob/main/wandb/wandb_integration.py)).                                                                                        |
| 5      | Log Final Train / Test dataset to ClearML  | Upload your dataset to your ClearML Server.                                                                                                                                                                                                                                                         |
| 5      | Log Best Performance Model to ClearML      | Best model weights uploaded to ClearML Server.                                                                                                                                                                                                                                                      |
| 5      | Git workflow                               | Publicly available repo. `develop` and `master` branches. Regular commits. No commit rush. Meaningful comment for each commit.                                                                                                                                                                      |

**Total: 70 points**

---

## Bonus Part

You can find data science articles in the fields you want to explore (e.g., natural language processing, computer vision, reinforcement learning).

If you need help, you can ask me and we will look for interesting articles together. After reading the whole article, you can quickly prepare a review and we will discuss it, just like in [DS Talks Siberia seminars](https://t.me/+fQ07VSVJ2V8yZGYy).

Or you can simply present a paper you have written in any of our classes. [Here are some examples of papers.](https://docs.google.com/spreadsheets/d/1G0ShaZwP7rjpGKG6-ReO6i9Yr9H5o0zAVfQ-SFYaOrQ/edit?usp=sharing)

**Total: 20 bonus points** (over all points at this task)
