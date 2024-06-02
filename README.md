([https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe/blob/main/README_EN.md](https://github.com/navwil/hand-gesture-recognition-using-mediapipe))]


# hand-gesture-recognition-using-mediapipe
This sample program uses MediaPipe (Python version) to estimate hand posture and uses the detected key points to recognize hand signs and finger gestures using a simple MLP.
![mqlrf-s6x16](https://user-images.githubusercontent.com/37477845/102222442-c452cd00-3f26-11eb-93ec-c387c98231b

This repository contains the following contents:
*Sample program
* Hand sign recognition model (TFLite)
* Finger gesture recognition model (TFLite)
* Learning data for hand sign recognition and learning notebook
* Learning data for finger gesture recognition and learning notebook

# Requirements
* mediapipe 0.8.4
* OpenCV 4.6.0.66 or Later
* Tensorflow 2.9.0 or Later
* protobuf <3.20,>=3.9.2
* scikit-learn 1.0.2 or Later 
* matplotlib 3.5.1 or Later 

# Demo
Below is how to run the demo using a web camera.
```bash
python app.py
```


Here's how to run the demo using Docker and a webcam:。
```bash
docker build -t hand_gesture .

xhost +local: && \
docker run --rm -it \
--device /dev/video0:/dev/video0 \
-v `pwd`:/home/user/workdir \
-v /tmp/.X11-unix/:/tmp/.X11-unix:rw \
-e DISPLAY=$DISPLAY \
hand_gesture:latest

python app.py
```

The following options can be specified when running the demo.
* --device<br>Specification of camera device number (default: 0)
* --width<br>Width at camera capture (default: 960)
* --height<br>Height when capturing with camera (default: 540)
* --use_static_image_mode<br>Whether to use static_image_mode for MediaPipe inference (default: unspecified)
* --min_detection_confidence<br>
Detection confidence threshold (default: 0.5)
* --min_tracking_confidence<br>
Tracking confidence threshold (default: 0.5)

# Directory
<pre>
│ app.py
│ keypoint_classification.ipynb
│ point_history_classification.ipynb
│
├─model
│ ├─keypoint_classifier
│ │ │ keypoint.csv
│ │ │ keypoint_classifier.hdf5
│ │ │ keypoint_classifier.py
│ │ │ keypoint_classifier.tflite
│ │ └─ keypoint_classifier_label.csv
│ │
│ └─point_history_classifier
│ │ point_history.csv
│ │ point_history_classifier.hdf5
│ │ point_history_classifier.py
│ │ point_history_classifier.tflite
│ └─ point_history_classifier_label.csv
│
└─utils
    └─cvfpscalc.py
</pre>
### app.py
This is a sample program for inference. <br>Also, learning data (key points) for hand sign recognition, <br>
You can also collect learning data (index finger coordinate history) for finger gesture recognition.

### keypoint_classification.ipynb
This is a model training script for hand sign recognition.

### point_history_classification.ipynb
This is a model training script for finger gesture recognition.

### model/keypoint_classifier
This is a directory that stores files related to hand sign recognition. <br>
The following files are stored.
* Learning data (keypoint.csv)
* Trained model (keypoint_classifier.tflite)
* Label data (keypoint_classifier_label.csv)
* Inference class (keypoint_classifier.py)

### model/point_history_classifier
This is a directory that stores files related to finger gesture recognition. <br>
The following files are stored.
* Learning data (point_history.csv)
* Trained model (point_history_classifier.tflite)
* Label data (point_history_classifier_label.csv)
* Inference class (point_history_classifier.py)

### utils/cvfpscalc.py
This is a module for FPS measurement.

# Training
For hand sign recognition and finger gesture recognition, you can add and change learning data, and retrain the model.

### Hand sign recognition training method
#### 1. Learning data collection
Press "k" to enter the key point saving mode ("MODE:Logging Key Point" will be displayed)<br>
<img src="https://user-images.githubusercontent.com/37477845/102235423-aa6cb680-3f35-11eb-8ebd-5d823e211447.jpg" width="60%"><br><br>
If you press "0" to "9", key points will be added to "model/keypoint_classifier/keypoint.csv" as shown below. <br>
1st column: Pressed number (used as class ID), 2nd and subsequent columns: Key point coordinates<br>
<img src="https://user-images.githubusercontent.com/37477845/102345725-28d26280-3fe1-11eb-9eeb-8c938e3f625b.png" width="80%"><br><br>
The key point coordinates are saved after the following preprocessing has been performed up to ④. <br>
<img src="https://user-images.githubusercontent.com/37477845/102242918-ed328c80-3f3d-11eb-907c-61ba05678d54.png" width="80%">
<img src="https://user-images.githubusercontent.com/37477845/102244114-418a3c00-3f3f-11eb-8eef-f658e5aa2d0d.png" width="80%"><br><br>
In the initial state, three types of learning data are included: par (class ID: 0), goo (class ID: 1), and pointing (class ID: 2). <br>
Please prepare training data by adding 3 or later or deleting existing data in csv as necessary. <br>
<img src="https://user-images.githubusercontent.com/37477845/102348846-d0519400-3fe5-11eb-8789-2e7daec65751.jpg" width="25%">　<img src="https://user -images.githubusercontent.com/37477845/102348855-d2b3ee00-3fe5-11eb-9c6d-b8924092a6d8.jpg" width="25%">　<img src="https://user-images.githubusercontent.com/37477845/102348861 -d3e51b00-3fe5-11eb-8b07-adc08a48a760.jpg" width="25%">
#### 2. Model training
Open "[keypoint_classification.ipynb](keypoint_classification.ipynb)" in Jupyter Notebook and run it from top to bottom. <br>
If you want to change the number of classes in the training data, change the value of "NUM_CLASSES = 3" and <br>edit the labels in "model/keypoint_classifier/keypoint_classifier_label.csv" as appropriate. <br><br>

#### X. Model structure
The image of the model prepared in "[keypoint_classification.ipynb](keypoint_classification.ipynb)" is as follows.
<img src="https://user-images.githubusercontent.com/37477845/102246723-69c76a00-3f42-11eb-8a4b-7c6b032b7e71.png" width="50%"><br><br>

### Finger gesture recognition training method
#### 1. Learning data collection
Press "h" to enter the mode for saving fingertip coordinate history (displays "MODE: Logging Point History")<br>
<img src="https://user-images.githubusercontent.com/37477845/102249074-4d78fc80-3f45-11eb-9c1b-3eb975798871.jpg" width="60%"><br><br>
When you press "0" to "9", keypoints will be added to "model/point_history_classifier/point_history.csv" as follows. <br>
1st column: Number pressed (used as class ID), 2nd column onwards: Coordinate history<br>
<img src="https://user-images.githubusercontent.com/37477845/102345850-54ede380-3fe1-11eb-8d04-88e351445898.png" width="80%"><br><br>
Keypoint coordinates are saved after the following preprocessing up to ④. <br>
<img src="https://user-images.githubusercontent.com/37477845/102244148-49e27700-3f3f-11eb-82e2-fc7de42b30fc.png" width="80%"><br><br>
Initially, four types of training data are included: stationary (class ID: 0), clockwise (class ID: 1), counterclockwise (class ID: 2), and moving (class ID: 4). <br>
Add 5 and onwards as necessary, or delete existing data in the CSV to prepare training data. <br>
<img src="https://user-images.githubusercontent.com/37477845/102350939-02b0c080-3fe9-11eb-94d8-54a3decdeebc.jpg" width="20%">　<img src="https://user-images.githubusercontent.com/37477845/102350945-05131a80-3fe9-11eb-904c-a1ec573a5c7d.jpg" width="20%">　<img src="https://user-images.githubusercontent.com/37477845/102350951-06444780-3fe9-11eb-98cc-91e352edc23c.jpg" width="20%">　<img src="https://user-images.githubusercontent.com/37477845/102350942-047a8400-3fe9-11eb-9103-dbf383e67bf5.jpg" width="20%">

#### 2. Model training
Open "point_history_classification.ipynb" (point_history_classification.ipynb)" in Jupyter Notebook and run it from top to bottom. <br>
If you want to change the number of classes in the training data, change the value of "NUM_CLASSES = 4" and <br> modify the labels in "model/point_history_classifier/point_history_classifier_label.csv" as appropriate. <br><br>

#### X. Model structure
The image of the model provided in "[point_history_classification.ipynb](point_history_classification.ipynb)" is as follows.
<img src="https://user-images.githubusercontent.com/37477845/102246771-7481ff00-3f42-11eb-8ddf-9e3cc30c5816.png" width="50%"><br>
The model using "LSTM" is as follows. <br>When using, change "use_lstm = False" to "True" (requires tf-nightly (as of December 16, 2020)) <br>
<img src="https://user-images.githubusercontent.com/37477845/102246817-8368b180-3f42-11eb-9851-23a7b12467aa.png" width="60%">

# Application example
Below is an example of an application.
* [Control DJI Tello drone with Hand gestures](https://towardsdatascience.com/control-dji-tello-drone-with-hand-gestures-b76bd1d4644f)
* [Classifying American Sign Language Alphabets on the OAK-D](https://www.cortic.ca/post/classifying-american-sign-language-alphabets-on-the-oak-d)

# Reference
* [MediaPipe](https://mediapipe.dev/)
* [Kazuhito00/mediapipe-python-sample](https://github.com/Kazuhito00/mediapipe-python-sample)

# Author
Kazuhito Takahashi(https://twitter.com/KzhtTkhs)

# License
hand-gesture-recognition-using-mediapipe is under [Apache v2 license](LICENSE).
