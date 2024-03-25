# Homework 1 
Classic ML with cats and MLOPS stuff.

__DEADLINE:  --.--.2024__

### "Time to Detect LLM Generated Texts"

You have to help Online Platform for english level test, where students write essays for time to pass the exam. After Large Language Model like ChatGPT released they found the problem, that students start generate essays by GPT.
Now they ask you to create the algorithm to detect LLM generated essays.


### Task
Implement and deploy a production-ready text classifier.
The current homework consists of two parts:
*  The Deep Learning Engineer part.
*  The MLOps features.
  
Firstly, you need to train a competitive  system, and secondly: wrap your code into a production-ready artifact, which may be deployed on any Linux server in one command.

--------------
### Requirements
We don't accept homework if any of the following requirements are not satisfied:
- The code should be situated in a public github (or gitlab) repository. Two branches: `dev` for development and `master` for the latest working version.
- You should build your project using [poetry](https://python-poetry.org/docs/) to freeze dependendecies of python packages you use. And attach your .whl file
- Readable and Understandable `README.md` file:
    - Your fullname & group
    - "**How To**" for your repo: train model, evaluate, deploy. With and without docker.
    - Resources you utilized
- Your code must be fully covered with logging to `./data/log_file.log`. The file should be viewable and downloadable
- Proper `.gitignore` file. You do not want rubbish in your repo.
- The major software artifact is `model.py`, containing the class `My_TextClassifier_Model` with following methods:
    - `train`. Recieves the dataset filename. Performes model training. Saves the artifacts to `./model/`. Logs the results.
    - `predict`. Recieves the dataset filename. Loads the model from `./data/model/`. Saves prediction to `./data/results.csv`.
- An integrated script for training and evaluation from CLI (check out `if __name__ == '__main__':`) so that:
```console
foo@bar:~$ python model.py train --dataset=/path/to/train/dataset
foo@bar:~$ python model.py predict --dataset=/path/to/evaluation/dataset
```
- `Dockerfile`
- `docker-compose.yaml`
- Submission on kaggle competition with [ROC AUC](https://developers.google.com/machine-learning/crash-course/classification/roc-and-auc) > 0.7.
Read more about the metric [here](https://alexanderdyakonov.wordpress.com/2017/07/28/auc-roc-%D0%BF%D0%BB%D0%BE%D1%89%D0%B0%D0%B4%D1%8C-%D0%BF%D0%BE%D0%B4-%D0%BA%D1%80%D0%B8%D0%B2%D0%BE%D0%B9-%D0%BE%D1%88%D0%B8%D0%B1%D0%BE%D0%BA/)
  
### Dataset
Here the competition on Kaggle with Dataset: [kaggle competition](https://www.kaggle.com/competitions/llm-detect-ai-generated-text/)

BUT we have a problem, we have only prompts which used in test set to generate Essays to check you generated text model quality and little train dataset. YOU expected collect (generate or find in open data sources) datasets with written and generated essays/texts by LLM and train your classifier.


### Project Milestones
##### 1. Data Science in Jupyter
Feel free to stick to Jupyter or Colab environment. Here we expect you to build two classifier models. 
 1) Baseline classifier. You can start from just rules with heuristics you have to find together. Also recommend try TF-IDF with you favourite classic classifer method.
 2) Neural Classifer. Model Backbone you can choose BERT, or try RNN, LSTM acrhitectures or give a chance  LLAMA/GPT/Mistral models

For both tasks, please, refer to target metrics at the end of README.
Pay attention, here you will create submissions on Kaggle platform using created your own notebook with your model.

Catch up the baseline.ipynb on rules with Kaggle submission format creation [colab](https://drive.google.com/file/d/1cNO3m8OQCueCOU4TQOP50pUlyc-JVjfq/view?usp=sharing)

##### 2. Pack into git repo
At this point we expect to see fully working CLI application in the `master` branch. 
For example read more about [Fire](https://google.github.io/python-fire/guide/)
  
##### 3. Pack to production ready solution
###### 3.1 Poetry
Create using poetry .whl file with your python packages dependencies. And command below works:
```console
foo@bar:~$ pip install your_file.whl 
```

##### 4. Grades
###### 4.1 Data Science part  
| Points         | ROC AUC     | Description |
|--------------|-----------|------------|
| 0       | < 0.5      |        |
| 10      | [0.7; 0.75)| Good Baseline.       |
| 20      | [0.75; 0.85] | Close to SOTA      |
| 25      | > 0.85  |  SOTA?       |  


__Total: 55 points__  
Please, note that cheating with metrics will lead you to the grade 0.

###### 4.2 MLOps part  
  
| Points         | Bulletpoint     | Description |
|--------------|-----------|------------|
| 10     | model.py      |    The model is properly packed into the class inside *.py file. CLI interface works well: train, predict.      |
| 10     |Code quality   | Clear OOP pattern. Well in-code comments. Build-in documentation for each function. No code duplicates. Meaningful variable names       |
| 15     |Poetry usage   | .whl file created useing poetry. And pip install your_package.whl works.     |
| 5 | Wandb Your Model Training Artifacts  | You log all your model train process using [Wandb](https://wandb.ai/site) (or local [MLFLOW](https://mlflow.org/)) |
| 5      | Logging       |Catch and log all possible errors. Singleton logging pattern (use logging module)      |
| 5      | git workflow  | Publicly available repo. dev and master branches. Regular Commits. No Commit Rush. Meaningful comment for each commit.    |
| 3      | docker        | working API in a docker container. Shared folder for all the artifacts (model files, logs, model predictions)      |
| 2      |docker-compose | working docker-compose file       |


__Total: 45 points__ 



###### Bonus Part
You can find Data Science article in the field you want to go deeper (e.x Natural Language Processing, Computer Vision, Reinforcement Learning). If you want, you can always ask me about help and we will find together interesting article for you.

After you read whole article you can prepare fast review and we can discuss it together, like it happens in [DS Talks Siberia seminars](https://t.me/+fQ07VSVJ2V8yZGYy), seminars recordings available on [Youtube](https://www.youtube.com/channel/UCKi44xqXU67E3dv5e0b_0Dg).

__Total: 20 points__  Over all points at this task.
