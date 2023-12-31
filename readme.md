## Real-Time Face Recognition: An End-to-End Project

# 3 Phases

To create a complete project on Face Recognition, we must work on 3 very distinct phases:

- Face Detection and Data Gathering
- Train the Recognizer
- Face Recognition
  
The below block diagram resumes those phases:
![e](https://github.com/saqib772/Real-Time-Face-Recognition/assets/121972215/5a02b078-fbe5-48ac-a553-9cd14dab1fee)

# Step 1: BoM - Bill of Material
Main parts:

- Raspberry Pi V3 - 
- 5 Megapixels 1080p Sensor OV5647 Mini Camera Video Module

  # Step 2: Installing OpenCV 3 Package

I am using a Raspberry Pi V3 updated to the last version of Raspbian (Stretch), so the best way to have OpenCV installed, is to follow the excellent tutorial developed by Adrian Rosebrock: ![Raspbian Stretch: Install OpenCV 3 + Python on your Raspberry Pi.](https://pyimagesearch.com/2017/09/04/raspbian-stretch-install-opencv-3-python-on-your-raspberry-pi/)

I tried several different guides to install OpenCV on my Pi. Adrian's tutorial is the best. I advise you to do the same, following his guideline step-by-step.

Once you finished Adrian's tutorial, you should have an OpenCV virtual environment ready to run our experiments on your Pi.

Let's go to our virtual environment and confirm that OpenCV 3 is correctly installed.

Let's go to our virtual environment and confirm that OpenCV 3 is correctly installed.

Adrian recommends run the command "source" each time you open up a new terminal to ensure your system variables have been set up correctly.
```
source ~/.profile
```
Next, let's enter on our virtual environment:

```
workon cv
```
If you see the text (cv) preceding your prompt, then you are in the cv virtual environment:

```
(cv) pi@raspberry:~$
```
Now, enter in your Python interpreter:

```
python
```
and confirm that you are running the 3.5 (or above) version.

Inside the interpreter (the ">>>" will appear), import the OpenCV library:

```
import cv2
```
# Step 3: Testing Your Camera
Once you have OpenCV installed in your RPi let's test to confirm that your camera is working properly.

I am assuming that you have a PiCam already installed on your Raspberry Pi.

In case you get an errar like: OpenCV Error: Assertion failed , you can try solve the issue, using the command:

```
sudo modprobe bcm2835-v4l2 

```
Once you have all drivers correctly installed, enter the main.py Python code on your IDE.
That code will capture the video stream that will be generated by your PiCam, displaying both, in BGR color and Gray mode.

To execute, enter the command:

```
python main.py
```
To finish the program, you must press the key [ESC] on your keyboard. Click your mouse on the video window, before pressing [ESC].

Some makers found issues when trying to open the camera ( "Assertion failed" error messages). That could happen if the camera was not enabled during OpenCv installation and so, camera drivers did not install correctly. To correct, use the command:

```
sudo modprobe bcm2835-v4l2 

```
# Step 4: Face Detection

Object Detection using Haar feature-based cascade classifiers is an effective object detection method proposed by Paul Viola and Michael Jones in their paper, "Rapid Object Detection using a Boosted Cascade of Simple Features" in 2001. It is a machine learning based approach where a cascade function is trained from a lot of positive and negative images. It is then used to detect objects in other images.

Here we will work with face detection. Initially, the algorithm needs a lot of positive images (images of faces) and negative images (images without faces) to train the classifier. Then we need to extract features from it. The good news is that OpenCV comes with a trainer as well as a detector. If you want to train your own classifier for any object like car, planes etc. you can use OpenCV to create one. Its full details are given here: ![Cascade Classifier Training.](https://docs.opencv.org/3.3.0/dc/d88/tutorial_traincascade.html)

If you do not want to create your own classifier, OpenCV already contains many pre-trained classifiers for face, eyes, smile, etc. Those XML files can be download from ![haarcascades](https://github.com/opencv/opencv/tree/master/data/haarcascades) directory.



Copy The Code of face.py now.

When you compare with the last code used to test the camera, you will realize that few parts were added to it. 
Note the line below:
```
faceCascade = cv2.CascadeClassifier('Cascades/haarcascade_frontalface_default.xml')
```
This is the line that loads the "classifier" (that must be in a directory named "Cascades/", under your project directory).

Then, we will set our camera and inside the loop, load our input video in grayscale mode (same we saw before).

Now we must call our classifier function, passing it some very important parameters, as scale factor, number of neighbors and minimum size of the detected face.

```
faces = faceCascade.detectMultiScale(
        gray,     
        scaleFactor=1.2,
        minNeighbors=5,     
        minSize=(20, 20)
    )
```
The function will detect faces on the image. Next, we must "mark" the faces in the image, using, for example, a blue rectangle. This is done with this portion of the code:

```
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    roi_gray = gray[y:y+h, x:x+w]
    roi_color = img[y:y+h, x:x+w]
```
run the file using:
```
python faceDetection.py
```

# Step 5: Data Gathering

You can Look the Following Tutorials:

![FACE RECOGNITION USING OPENCV AND PYTHON: A BEGINNER’S GUIDE](https://www.superdatascience.com/opencv-face-recognition/)

![FACE RECOGNITION - 3 parts](https://thecodacus.com/category/opencv/face-recognition/)


 What we will do here, is starting from last step (Face Detecting), we will simply create a dataset, where we will store for each id, a group of photos in gray with the portion that was used for face detecting.

First, create a directory where you develop your project, for example, FacialRecognitionProject:

```
mkdir FacialRecognitionProject
```
In this directory, besides the 3 python scripts that we will create for our project, we must have saved on it the Facial Classifier. You can download it from 
![haarcascade_frontalface_default.xml](https://github.com/Mjrovai/OpenCV-Face-Recognition/blob/master/FacialRecognition/haarcascade_frontalface_default.xml)

Next, create a subdirectory where we will store our facial samples and name it "dataset":

```
mkdir dataset
```
Copy The Code now of face_data.py now.

The code is very similar to the code that we saw for face detection. What we added, was an "input command" to capture a user id, that should be an integer number (1, 2, 3, etc)

```
face_id = input('\n enter user id end press  ==>  ')
```
And for each one of the captured frames, we should save it as a file on a "dataset" directory:
```
cv2.imwrite("dataset/User." + str(face_id) + '.' + str(count) + ".jpg", gray[y:y+h,x:x+w])
```
Note that for saving the above file, you must have imported the library "os". Each file's name will follow the structure:

```
User.face_id.count.jpg
```
For example, for a user with a face_id = 1, the 4th sample file on dataset/ directory will be something like:

```
User.1.4.jpg
```
# Step 6: Trainer

On this second phase, we must take all user data from our dataset and "trainer" the OpenCV Recognizer. This is done directly by a specific OpenCV function. The result will be a .yml file that will be saved on a "trainer/" directory.

So, let's start creating a subdirectory where we will store the trained data:

```
mkdir trainer

```
Now Copy the code of face_train.py.
Confirm if you have the PIL library installed on your Rpi. If not, run the below command in Terminal:

```
pip install pillow
```
We will use as a recognizer, the LBPH (LOCAL BINARY PATTERNS HISTOGRAMS) Face Recognizer, included on OpenCV package. We do this in the following line:
```
recognizer = cv2.face.LBPHFaceRecognizer_create()
```
The function "getImagesAndLabels (path)", will take all photos on directory: "dataset/", returning 2 arrays: "Ids" and "faces". With those arrays as input, we will "train our recognizer":

```
recognizer.train(faces, ids)
```
As a result, a file named "trainer.yml" will be saved in the trainer directory that was previously created by us.

# Step 7: Recognizer
we will capture a fresh face on our camera and if this person had his face captured and trained before, our recognizer will make a "prediction" returning its id and an index, shown how confident the recognizer is with this match.

Hey Folks, CONGRATULATIONS You have make it so far now few things More left.
Copy the face_recognize.py code now.

We are including here a new array, so we will display "names", instead of numbered ids:

```
names = ['None', 'Marcelo', 'Paula', 'Ilza', 'Z', 'W']
```
Next, we will detect a face, same we did before with the haasCascade classifier. Having a detected face we can call the most important function in the above code:

```
id, confidence = recognizer.predict(gray portion of the face)
```
The recognizer.predict (), will take as a parameter a captured portion of the face to be analyzed and will return its probable owner, indicating its id and how much confidence the recognizer is in relation with this match.

**Note that the confidence index will return "zero" if it will be cosidered a perfect match**

Special Thanks To 🔥MJRoBot (Marcelo Rovai).🔥

