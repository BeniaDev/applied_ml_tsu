# Homework 2
 
The use of computer vision in applied projects
 
**DEADLINE: --.--.2026**
 

## You can explore information about tools for working with computer vision.
 
**Learn more about CV:**
 

*   [https://docs.ultralytics.com/](https://docs.ultralytics.com/) 
     
*   [https://habr.com/ru/articles/821971/](https://habr.com/ru/articles/821971/) 
     
*   [https://timeweb.cloud/blog/yolo-neyroset-obnaruzhenie-obektov](https://timeweb.cloud/blog/yolo-neyroset-obnaruzhenie-obektov) 
     
*   [https://datasecrets.ru/articles/20](https://datasecrets.ru/articles/20) 
     
---
     
To work with the main model, you will need a powerful graphics card. If there is no such thing, then we will use Google collaborate.
     
    
## Intro
     
Intelligent transport monitoring systems, including automated parking monitoring, recording of traffic violations and keyless entry systems, rely on license plate recognition technology (ALPR/ANPR). The key step in this process is to accurately locate the number on the image in real time. One of the main problems when developing such systems for different regions is the variability of operating conditions: rooms can be polluted, at an acute angle, have a specific design (size, font, color scheme) or be poorly lit. Traditional methods of color segmentation or contour detection often turn out to be insufficiently reliable, requiring manual adjustment of many parameters for each specific scenario. Using the YOLO (You Only Look Once) architecture allows us to solve this problem by training a model on a specific data set, combining high speed with resistance to visual distortion, which is critically important for systems operating "in a stream".
     
    
## The purpose of the work
     
Implement and implement a ready-to-use license plate detection system based on the YOLO architecture. Your decision should be divided into two stages corresponding to the roles in the actual development:
     
1.  Deep Learning Engineer Part
         
    Data collection and markup: Independently select video materials with road traffic. Perform the bounding boxes for license plates using tools like CVAT or labelImg.
         
    Training: Refine (fine-tune) the YOLO model on the assembled dataset. You need to achieve a high mAP metric while maintaining a low output latency.
         
    Analysis: Evaluate the quality of the model in different scenarios (day/night, different distance).
         
2.  Software Engineer Part
         
    Operating modes: Implement a CLI application or service that supports two modes:
         
    1.  Video Processing: Processing an existing video file while saving the result.
             
    2.  Live Stream: Processing the webcam stream in real time.
             
            
## Technical requirements
             
We do not accept homework assignments unless at least one of the following conditions is met:
             
*   Git policy: The code must be in a public repository `GitHub/GitLab`. There must be two branches: `dev` (for development) and `main` (stable version).
                 
*   Dependency Management: The project must be assembled using Poetry. There must be a file in the repository.the whl of your package.
                 
*   Documentation `README.md`:
                 
    *   Your full name and group number.
                     
    *   "How-to" instructions: how to start training, how to start evaluation, and how to deploy the system via Docker.
                     
    *   A list of resources used (links to datasets, pre-trained weights, articles).
                     
*   Logging: The entire process (initialization, frame processing, errors) should be logged in the file `./data/log_file.log`. The file must be available inside the container for viewing.
                     
*   The purity of the repository: The presence of a correct one .gitignore. The repository should not contain temporary files, cache, or heavy raw data.
                     
*   Software artifact: The main file is `model_impl.py`  containing the `My_LicensePlate_Model` class with the following method: `def detect_plates(self, frame: np.ndarray) -> list[dict]` (The method takes a frame and returns a list of dictionaries with frame coordinates and probability).
                     
*   Deployment: Availability of `Dockerfile` and `docker-compose.yaml` for launching the application in the "Video" or "Camera" modes.

## Dataset
 
As a dataset, you can take any video recorded on the road (which contains license plates) and mark it up on a convenient platform (for example: [roboflow](https://roboflow.com/)  or [Label Studio](https://labelstud.io/) ).
 
If you have any problems with the markup, you can take the [ready-made data](https://app.roboflow.com/etbx/license_plates-fepuu/1)  (which I have prepared especially for you).
 

## Project Milestones
 

1.  IDE
     
    You can freely use any development environment, but if your computer is not powerful enough, then it is recommended to use google collaborate.
     
2.  Drivers
     
    Model training is recommended on NVIDIA graphics cards. To do this, it is recommended to install the latest driver version, CUDA, and the corresponding library version for PyTorch.:
     
    `pip install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu128`
     
3.  Pack into git repo
     
    At this point we expect to see fully working application in the `main` branch.
     

## **Grades**
 
**Data Science part**
 

| **Points** | **Token Accuracy** | **Description** |
| ---------- | ------------------ | --------------- |
| 0 | &lt; 0.5 |  |
| 10 | \[0.9; 0.93) | Good Baseline. |
| 15 | \[0.93; 0.97\] | Close to SOTA |
| 15 | \> 0.97 | SOTA? |

**Total: 40 points**<br>Please, note that cheating with metrics will lead you to the grade 0.
 
**Software Engineer part**
 <table><tbody><tr><th >Points</th><th >Bulletpoint</th><th >Description</th></tr><tr><td >15</td><td >Your dataset</td><td >You didn't use a ready-made dataset, but created and used your own</td></tr><tr><td >15</td><td >Use cases</td><td >Your model is capable of working with both video files and stream analysis (for example, webcams)</td></tr><tr><td >5</td><td >README demos</td><td >In your README file, gif files have been added showing how your model works</td></tr><tr><td >5</td><td >Code quality</td><td >Clear OOP pattern. Well in-code comments. Build-in documentation for each function. No code duplicates. Meaningful variable names</td></tr><tr><td >5</td><td >model.py</td><td >The model is properly packed into the class inside \*.py file.</td></tr><tr><td >5</td><td >Wandb Your Model Training Artifacts</td><td >You log all your model train process using Wandb or ClearML</td></tr><tr><td >2</td><td >Logging</td><td >Catch and log all possible errors. Singleton logging pattern (use logging module)</td></tr><tr><td >3</td><td >git workflow</td><td >Publicly available repo. dev and master branches. Regular Commits. No Commit Rush. Meaningful comment for each commit.</td></tr></tbody></table>
