# Homework 3
ML captcha solver.

__DEADLINE:  --.--.2024__

### "Recognition of encrypted messages by strangers"

We are trying to access the site using a bot, but we have encountered an insurmountable obstacle - a captcha. Our customer is very serious, so we need to get the maximum accuracy.

### Task
Implement and deploy a ready-to-use captcha recognizer.
The current homework consists of two parts:
* Computer Vision Enginner task.
* Classic developer for inference.
  
Firstly, you need to train a competitive  system, and secondly: wrap your code into a production-ready artifact, which may be deployed on any Linux server in one command. This time you have no need to wrap it to docker:)

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
- The major software artifact is `model.py`, containing the class `My_CaptchaSolver_Model` with following methods:
    - `train`. Recieves the dataset folder. Performes model training. Saves the artifacts to `./model/`. Logs the results.
    - `predict`. Recieves the picture. Return predicted text from captcha, logs to file predicition results: `./data/results.csv`.
- An integrated script for training and evaluation from CLI (check out `if __name__ == '__main__':`) so that:
```console
foo@bar:~$ python model.py predict --dataset=/path/to/evaluation/dataset
```
  
### Dataset
You can generate dataset by this script: [Captcha generator](https://github.com/IvanArsenev/Captcha-solver). You are free to use any open source dataset you will find.

We will measure the **Final Accuracy** using the test set. The download link for the test set will be provided by Ivan Arsenev.

### Project Milestones
##### 1. Data Science in Jupyter
Feel free to stick to Jupyter or Colab environment. Here we expect you to build two classifier models. 
 1) Baseline classifier.
 2) Neural Classifer.

Hope one of this link will help you:
https://deepayan137.github.io/blog/markdown/2020/08/29/building-ocr.html
https://paperswithcode.com/task/optical-character-recognition
https://github.com/GitYCC/crnn-pytorch

For both tasks, please, refer to target metrics at the end of README.

**Note!**: your model train process can stay in clean jupyter notebook file, you should pack to app **only** the model inference.

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
| Points         | Accuracy    | Description |
|--------------|-----------|------------|
| 0       | < 0.5      |        |
| 20      | [0.7; 0.75)| Good Baseline.       |
| 30      | [0.75; 0.85] | Close to SOTA      |
| 30      | > 0.85  |  SOTA?       |  


__Total: 80 points__  
Please, note that cheating with metrics will lead you to the grade 0.

###### 4.2 MLOps part  
  
| Points         | Bulletpoint     | Description |
|--------------|-----------|------------|
| 5     |Code quality   | Clear OOP pattern. Well in-code comments. Build-in documentation for each function. No code duplicates. Meaningful variable names       |
| 5     | model.py      |    The model is properly packed into the class inside *.py file. CLI interface works well: predict.      |
| 5      | Logging       |Catch and log all possible errors. Singleton logging pattern (use logging module)      |
| 3      | git workflow  | Publicly available repo. dev and master branches. Regular Commits. No Commit Rush. Meaningful comment for each commit.    |
| 2      | Poetry usage   | .whl file created useing poetry. And pip install your_package.whl works.     |


__Total: 20 points__ 



###### Bonus Part
You can find Data Science article in the field you want to go deeper (e.x Natural Language Processing, Computer Vision, Reinforcement Learning). If you want, you can always ask me about help and we will find together interesting article for you.

After you read whole article you can prepare fast review and we can discuss it together, like it happens in [DS Talks Siberia seminars](https://t.me/+fQ07VSVJ2V8yZGYy), seminars recordings available on [Youtube](https://www.youtube.com/channel/UCKi44xqXU67E3dv5e0b_0Dg).

__Total: 20 points__  Over all points at this task.


###### Acknowledgements
Want to say thank you everyone who help with creating this task and who involved to solve it, especially [Ivan Arsenev](https://github.com/IvanArsenev/).
Also I would like to express my sincere gratitude to Sensei and the author of the original Advanced ML Course: [Dmitriy Menshikov](https://github.com/MenshikovDmitry), hope you will meet him in the next semesters.
