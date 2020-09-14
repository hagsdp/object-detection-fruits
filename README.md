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

#### 3.1 - Network Architecture:
Yolo v3 uses a network architecture called Darknet-53. The architecture defines the number of layers that the network is comprised of. Darknet-53 is comprised of a total of 53 convolutional layers, and another 53 stacked up for object detection purposes, which brings it to a total of 106. This architecture is an improvement from its previous version, Darknet-19 with only 19 layers. This increase in the number of layers has provided it with additional capacity to fix several drawbacks compared with Darknet-19, like the detection of small objects.
#### 3.2 - Weights
In order to use a neural network for inference it is required to train it on a image dataset to define the set of weights for each layer. The ideal scenario is to train the NN from the beginning for our particular use case so that all the weights are customised for our application, however, this would take a long time and computer power. To solve this, it is widely accepted that weights trained from one object detection application is a good starting point and and can be used for other object detection tasks. 
#### 3.2 - Customization of weights for our problem 
In our case it has ben taken an intermediate approach that is accepted as a good compromise for problem customization. This involves to use pre-trained weights, as discussed above, but unfreeze the last few layers of the architecture and re-obtain these weights for for our particular application, with our data. 

### 4 - INFERENCE: RESULTS 
The gif at the start of the post shows some instances where the algorithm is able to provide results that correspond to the ground truth or are very close.  However, there were many other instances where the results were not as accurate. Below they are shown a few of these examples where the algorithm did not perform as as well as it was expected.

- PROBLEM 1: Objects being misclassified.
<p align="center">
<img width="955" alt="Screenshot 2020-09-14 at 08 20 56" src="https://user-images.githubusercontent.com/35865504/93056528-734fe600-f664-11ea-80ec-9c6cfe3c77db.png">
</p>

- PROBLEM 2: Objects not being detected at all
<p align="center">
<img width="955" alt="Screenshot 2020-09-14 at 07 58 36" src="https://user-images.githubusercontent.com/35865504/93055439-d5a7e700-f662-11ea-93da-fe4c85ad2b45.png">
</p>

- PROBLEM 3: Objects being classified as multiple classes
<p align="center">
<img width="953" alt="Screenshot 2020-09-14 at 08 33 07" src="https://user-images.githubusercontent.com/35865504/93056873-f2ddb500-f664-11ea-8ecf-1567ef48c710.png">
</p>

- PROBLEM 4: Multiple boxes for classifiying the same object (left grapefruit)
<p align="center">
<img width="953" alt="Screenshot 2020-09-14 at 08 36 28" src="https://user-images.githubusercontent.com/35865504/93057215-74354780-f665-11ea-8c40-c74bf213e2e4.png">
</p>

Based on these observations there are proposed additional actions to improve the performance of the algorithm.

### 5 - NEXT STEPS: WHAT CAN BE IMPROVED (AND HOW) (TO DO - expand)
As shown above after the first iteration there are a few problems to solve. In general, I would expect that by feeding more data into the algorithm the overall results will improve. The current model was trained with 180 images with a 75-25% train-dev split, and test in brand new video feed. In the next iteration I will do a train-dev-test split of 70-20-10 to learn if feeding it with a video can cause or worsen any of the problems mentioned above (maybe different scale or colours from the photo camera?)

(TO DO - expand)
- Increment number of training images to improve overall performance.
- Potentially monitor and allow for more epochs if both the loss and val_loss keep getting lower and closer (heading towards convergence).
- Implement mAP to have a representative metric for model accuracy
- Revisit Non max suppresion to fix several bounding boxes detecting the same object as per gif above.
(TO DO - expand)


## References
<a id="1">[1]</a> 
https://towardsdatascience.com/training-a-yolov3-object-detection-model-with-a-custom-dataset-4981fa480af0
