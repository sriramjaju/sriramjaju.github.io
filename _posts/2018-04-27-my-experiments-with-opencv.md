---
layout: post
published: true
title: My Experiments with OpenCV - Part1
date: '2018-04-27'
---


### Face and Eye Recognizer using Haar cascades


Haar cascades for OpenCV can be downloaded from [here](https://github.com/opencv/opencv/tree/master/data/haarcascades).


```python
# Import required libraries for this section

%matplotlib inline

import numpy as np
import matplotlib.pyplot as plt
import cv2                     # OpenCV library for computer vision
```

```python
# Load in color image for face detection
image = cv2.imread('images/sriram.png')

# Convert the image to RGB colorspace
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

# Plot the RGB image
fig = plt.figure(figsize = (6,6))
ax1 = fig.add_subplot(111)
ax1.set_xticks([])
ax1.set_yticks([])

ax1.set_title('Original Image')
ax1.imshow(image)
```

![Original Image]({{site.baseurl}}/img/output_1_1.png)

```python
# Convert the RGB  image to grayscale
gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)

# Extract the pre-trained face detector from an xml file
face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')

# Detect the faces in image
faces = face_cascade.detectMultiScale(gray, 1.25, 6)

# Print the number of faces detected in the image
print('Number of faces detected:', len(faces))

# Make a copy of the orginal image to draw face detections on
image_with_detections = np.copy(image)

# Get the bounding box for each detected face
for (x,y,w,h) in faces:
    # Add a red bounding box to the detections image
    cv2.rectangle(image_with_detections, (x,y), (x+w,y+h), (255,0,0), 3)
    

eye_cascade = cv2.CascadeClassifier('haarcascade_eye.xml')

for (x,y,w,h) in faces:
    #cv.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    roi_gray = gray[y:y+h, x:x+w]
    roi_color = image_with_detections[y:y+h, x:x+w]
    eyes = eye_cascade.detectMultiScale(roi_gray)
    for (ex,ey,ew,eh) in eyes:
        cv2.rectangle(roi_color,(ex,ey),(ex+ew,ey+eh),(0,255,0),2)



# Print the number of faces detected in the image
print('Number of eyes detected:', len(eyes))


# Plot the image with both faces and eyes detected
fig = plt.figure(figsize = (6,6))
ax1 = fig.add_subplot(111)
ax1.set_xticks([])
ax1.set_yticks([])

ax1.set_title('Image with Face and Eye Detection')
ax1.imshow(image_with_detections)
```

    Number of faces detected: 1
    Number of eyes detected: 2

![Image with Face and Eye Detection]({{site.baseurl}}/img/output_2_2.png)


### Face and Eye Detection for web-cam feed

```python
import cv2
import time

face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
eye_cascade = cv2.CascadeClassifier('haarcascade_eye.xml')


# wrapper function for face/eye detection with your laptop camera
def laptop_camera_go():
    # Create instance of video capturer
    cv2.namedWindow("face detection activated")
    vc = cv2.VideoCapture(0)

    # Try to get the first frame
    if vc.isOpened():
        rval, frame = vc.read()
    else:
        rval = False

    # Keep the video stream open
    while rval:
        # Plot the image from camera with all the face and eye detections marked
        gray = cv2.cvtColor(frame, cv2.COLOR_RGB2GRAY)
        faces = face_cascade.detectMultiScale(gray, 1.3, 5)
        for (x, y, w, h) in faces:
            cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 0, 255), 2)
            roi_gray = gray[y:y + h, x:x + w]
            roi_color = frame[y:y + h, x:x + w]
            eyes = eye_cascade.detectMultiScale(roi_gray)
            for (ex, ey, ew, eh) in eyes:
                cv2.rectangle(roi_color, (ex, ey), (ex + ew, ey + eh), (0, 255, 0), 2)

        cv2.imshow("face detection activated", frame)

        # Exit functionality - press Esc key to exit laptop video
        key = cv2.waitKey(20)
        if key != 255:  # Exit by pressing Esc key
            # Destroy windows
            cv2.destroyAllWindows()

            # Make sure window closes on OSx
            for i in range(1, 5):
                cv2.waitKey(1)
            return

        # Read next frame
        time.sleep(0.05)  # control framerate for computation - default 20 frames per sec
        rval, frame = vc.read()


laptop_camera_go()
```




