# object-detection-fruits

## PROJECT SUMMARY: Object Detection as an alternative for self checkouts 
With the spread of the Coronavirus, there has been a general push for increased hygene measures and an aim to remove physical contact as much as possible. Since this started I noticed how self checkouts in grocery stores still use quite manual approaches to entering a good amount of items. In particular, for fruits and vegetables, interaction with the screen is needed to browse throught the items. This can also take a reasonable amount of time if you have several different products. 
With this project, I wanted to study the feasibility of an alternative system that would use a neural network for automatic object detection and classification of different fruits. An extract of the first results is shown below

![ezgif com-gif-maker](https://user-images.githubusercontent.com/35865504/92520772-0c6d9f80-f214-11ea-9805-ec534ffac027.gif)

Even being still quite far from a ready-to-deploy system, I believe it already shows promising results at this early stage and with limited resources. The steps that I have gone through, the drawbacks (and potential ways to improve them) and future work is covered below (NOTE SOME OF THE LAST SECTIONS STILL NEED UPDATED).

## PROJECT BREAKDOWN 

### 1 - PROBLEM STATEMENT 

The aim of the project is to train a neural network that is able to detect and classify a number of fruit and vegetable categories. This would form an alternative system to replace manually entering these products at self checkouts. For implementation of the algorithm I have modified the code provided for a similar application in this post [1]

### 2 - DATA ACQUISITION

In order to start the project it is necessary to get data that will be used to train the neural network. There are fruit datasets available in platforms like Kaggle, however I decided to get my own data for a number of reasons:

1. This object detection problem is quite particular as the position of the camera on the deployment environment will be fixed. This means that the area of interest (the surface of the self checkout) and therefore the distances to the fruits always the same. This should (hopefully) simplify the learning process. By taking my own pictures I can mirror this situation.
2. I can also recreate the environment as much as possible, for instance by taking the pictures with a stainless steel sheet on the background, to mirror the surface of the self checkouts.
3. Existing fruit datasets are comprised of individual fruits, whereas in the real scenario, there will be a combined number of fruits which the algorithm will need to detect and recognise.

In this case I chose a total of 10 different fruits/vegetables (classes): beef tomato, salad tomato, lemon, lime, orange, grapfruit, red pepper, yellow pepper, mango and avocado. In order to get the data I took pictures at home from different combinations of the classes above that will be fed to the neural network. For the first iteration of training I took a total of 180 pictures of different combination of the 10 fruits.

### 3 - DATA LABELLING AND PRE-PROCESSING

For data labelling purposes I have used LabelImg, choosing the 'Yolo' set up for annotation, which produces one .txt file for each picture annotated. 

For image processing Roboflow is an online system that allows you to take your annotated images to a file ready to train your algorithm. As shown on their website, this process involves several steps: 

1. Analyse the images to make sure there are no duplicates or to assess annotation quality to make sure that quality of the model will not be compromised.
1. Add image augmentation techniques. In my case I applied rotation to my dataset. This is becuase of the nature of the fruits, where it is normal to find them in many different orientation (they can rotate 360 degress basically). If it had been a car dataset, this should have been done with more carefully to avoid, for instance, having a car upside down. 
1. Finally, Roboflow processes the images and provides a link to a zip file that I can implement in my code (see .py attached)

### 3 - THE ALGORITHM: YOLO - TRANING PROCESS

In order to solve the problem I have used YOLOv3 Object Detector algorithm. When it comes to object detection there are a few options available. For this particular application I have decided to use YOLO as it is the one that gives best performance on (almost) real time applications which would be a key characteristic for this use case. Along the process there were a few parameters needed to be tweaked including number of epochs, batch size and mainly the learning rate. As we will see, it will also be required at a later date to improve non max suppression, as in some instances more than one box mistakenly surrounds a particular object. This is discussed more in detail in the next sections.

### 4 - INFERENCE: RESULTS (TO DO - expand)

- Add examples of successfully classified classes
- Add examples of unsuccessfully classified classes. Explain potential reasons why this is happening. 


### 5 - NEXT STEPS: WHAT CAN BE IMPROVED (AND HOW) (TO DO - expand)

- Increment number of training images to improve overall performance.
- Potentially monitor and allow for more epochs if both the loss and val_loss keep getting lower and closer (heading towards convergence).
- Implement mAP to have a representative metric for model accuracy
- Revisit Non max suppresion to fix several bounding boxes detecting the same object as per gif above.


## References
<a id="1">[1]</a> 
https://towardsdatascience.com/training-a-yolov3-object-detection-model-with-a-custom-dataset-4981fa480af0
