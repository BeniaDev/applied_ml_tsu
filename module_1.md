# Homework 1 
Classic ML with cats and MLOPS stuff.

__DEADLINE:  --.--.2025__

### "A long time ago in a galaxy far, far away...."
Welcome to the "long time ago in a galaxy far, far away", where your data science skills are needed to solve a cosmic mystery. We've received a transmission from four lightyears away and things aren't looking good.


### Task
You need to help rescue crews and retrieve the lost passengers, you are challenged to predict which passengers were transported by the anomaly using records recovered from the spaceship’s damaged computer system.

Implement and deploy a production-ready classifier.
The current homework consists of two parts:
*  The Data Science Part
*  The MLOps features.
  
Firstly, you need to train a competitive  system, and secondly: wrap your code into a production-ready artifact, which may be deployed on any Linux server in one command.

### Before we start 
Data Scientist life is more than just experiments in Jupyter environment. About roles in Data Science industry, you can listen this video: [link](https://www.youtube.com/watch?v=lDkTNURDIaY)
I hope you will find the job and role in DS you will love do.

One of my ML teachers said (I hope you will meet him in the next years):
```
Data science is an industry that is fully based on tons of open source tools, supported by a wide community. Whenever you, as a Data Scientist, train a model in the Jupyter environment, you have to understand that this moment is only a starting point in the lifecycle of the model, which is assumed to be deployed on the production server. On Linux Server. Hiring a Junior DS, I prefer one with at least basic Linux exposure and Command Line Interface (CLI) experience.
```
###### Linux users:
👍
###### Windows users:
You can try to conquer WSL: https://canonical-ubuntu-wsl.readthedocs-hosted.com/en/latest/guides/install-ubuntu-wsl2/
or use google colab: https://colab.google/.

###### Mac users:
You can do everything like Linux users in this task and feel yourself comfortable.

--------------
### Requirements
We don't accept homework if any of the following requirements are not satisfied:
- The code should be situated in a public github (or gitlab) repository. Two branches: `dev` for development and `master` for the latest working version.
- You should build your project using [poetry](https://python-poetry.org/docs/) to freeze dependendecies of python packages you use. And attach your .whl file. Or if you want you can add only poetry.lock file to your repo.
- Readable and Understandable `README.md` file:
    - Your fullname & group
    - "**How To**" for your repo: train model, evaluate, deploy. With and without docker.
    - Resources you utilized
- Your code must be fully covered with logging to `./data/log_file.log`. The file should be viewable and downloadable
- Proper `.gitignore` file. You do not want rubbish in your repo.
- The major software artifact is `model.py`, containing the class `My_Classifier_Model` with following methods:
    - `train`. Recieves the dataset filename. Performes model training. Saves the artifacts to `./model/`. Logs the results.
    - `predict`. Recieves the dataset filename. Loads the model from `./data/model/`. Saves prediction to `./data/results.csv`.
- An integrated script for training and evaluation from CLI (check out `if __name__ == '__main__':`) so that:
```console
foo@bar:~$ python model.py train --dataset=/path/to/train/dataset
foo@bar:~$ python model.py predict --dataset=/path/to/evaluation/dataset
```
- `.ipynb notebook`  in your repo folder `./notebooks/` with your final submission creation and [Optuna framework](https://optuna.org/) usage. You have to optimize your Gradient Boosting [Catboost](https://catboost.ai/) Classifer using Optuna.
- Flask Application (feel free to use FastAPI or other frameworks) flask_app.py. Runs REST API service on port 5000.
- Submission on kaggle competition with [Classification Accuracy](https://developers.google.com/machine-learning/crash-course/classification/accuracy) > 0.7
  
### Dataset
Here the competition on Kaggle with Dataset: [link](https://www.kaggle.com/t/20c5bb889f2a46c78c11aa38a7b051fd)



### Project Milestones
##### 1. Data Science in Jupyter
Feel free to stick to Jupyter or Colab environment. Here we expect you to build two classifier models. 
 1) A baseline classifier, here you can start with Logistic Regression or Random Forest Classifier or anything else you want.
 2) Catboost Classifier. Or any other Gradient Boosting you want. (LightGBM, XGBoost, TabNet)

For both tasks, please, refer to target metrics at the end of README.

##### 2. Pack into git repo
At this point we expect to see fully working CLI application in the `master` branch. 
For example read more about [Fire](https://google.github.io/python-fire/guide/)
  
##### 3. Pack to production ready solution
###### 3.1 Poetry
Create using poetry .whl file with your python packages dependencies. And command below works:
```console
foo@bar:~$ pip install your_filre.whl 
```

Which builds docker image and spins up container with the necessary parameters.

##### 4. Grades
###### 4.1 Data Science part  
| Points         | Accuracy     | Description |
|--------------|-----------|------------|
| 0       | < 0.5      |        |
| 20      | [0.7; 0.75)| Good Baseline.       |
| 5      | [0.75; 0.82] | Close to SOTA      |
| 5      | > 0.82  |  SOTA?       |  


P.S if you will have Accuracy == 1.0 on closed test set it will be interesting.
Please, note that cheating with metrics will lead you to the grade 0.

__Total: 30 points__  


###### 4.2 MLOps part  
  
| Points         | Bulletpoint     | Description |
|--------------|-----------|------------|
| 15     | model.py      |    The model is properly packed into the class inside *.py file. CLI interface works well: train, predict.      |
| 5     | Code quality   | Clear OOP pattern. Well in-code comments. Build-in documentation for each function. No code duplicates. Meaningful variable names|
| 5     | Poetry or [UV](https://github.com/astral-sh/uv) usage   | .whl file created useing poetry. And pip install your_package.whl works. Or working poetry.lock file     |
| 15 | ClearML usage| Screenshots from your local hosted ClearML. You have to show artifacts from ClearML where you find best parameters using optuna. [hint1](https://clear.ml/docs/latest/docs/clearml_serving/clearml_serving_setup/) [hint2](https://clear.ml/docs/latest/docs/integrations/catboost)  [hint3](https://clear.ml/docs/latest/docs/guides/optimization/hyper-parameter-optimization/examples_hyperparam_opt) |
| 10 | Optuna usage | Final models hyperparameters found using Optuna. Optional: use wandb.ai to create great post optimization process artifacts [hint](https://github.com/optuna/optuna-examples/blob/main/wandb/wandb_integration.py) |
| 5      | Logging       | Catch and log all possible errors. Singleton logging pattern (use logging module).    |
| 5      | git workflow  | Publicly available repo. dev and master branches. Regular Commits. No Commit Rush. Meaningful comment for each commit.    |
| 5      | Log Final Train / Test dataset to ClearML      | Upload your dataset to your ClearML Server.    |
| 5      | Log Best Performance Model to ClearML | Best model weights uploaded to ClearMl Server.      |


__Total: 60 points__ 



###### Bonus Part
You can find Data Science article in the field you want to go deeper (e.x Natural Language Processing, Computer Vision, Reinforcement Learning). If you want, you can always ask me about help and we will find together interesting article for you.

After you read whole article you can prepare fast review and we can discuss it together, like it happens in [DS Talks Siberia seminars](https://t.me/+fQ07VSVJ2V8yZGYy).

__Total: 20 points__  Over all points at this task.
