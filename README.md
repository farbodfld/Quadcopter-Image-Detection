# Quadcopter Image Detection

A Webots-based autonomous quadcopter simulation that combines drone control, image processing, and a CNN classifier to identify target boxes from camera images and trigger landing when the correct destination is detected.

---

## Overview

This project demonstrates an autonomous quadcopter workflow using the **Webots robotics simulator** and a **Python-based computer vision pipeline**. A simulated Mavic 2 Pro drone follows a set of predefined waypoints, captures images using its onboard camera, processes the captured images, classifies the detected target using a trained CNN model, and lands when the predicted target matches the required destination.

The project connects three main areas:

- **Robotics simulation** using Webots
- **Image processing** using OpenCV
- **Machine learning classification** using TensorFlow/Keras

Rather than being only a static image classification project, this repository shows how image detection can be integrated into a drone navigation and decision-making loop.

---

## Key Features

- Simulated Mavic 2 Pro quadcopter in Webots
- Autonomous waypoint-based drone navigation
- Camera-based image capture from the simulated environment
- Image preprocessing pipeline using grayscale conversion, cropping, resizing, erosion, and Laplacian enhancement
- CNN model trained on 28×28 grayscale image data
- Saved Keras model for prediction
- Classification-based destination detection
- Automatic landing when the target destination is identified
- Jupyter notebooks for model training, image processing, and prediction testing
- Python controller for connecting Webots drone control with the trained model

---

## Project Workflow

The complete system works as follows:

1. The quadcopter starts inside the Webots simulation world.
2. The drone takes off and reaches a target altitude.
3. It moves through a sequence of predefined waypoints.
4. At each target area, the camera captures an image of the box or visual marker.
5. The captured image is converted to grayscale and cropped.
6. Image processing filters are applied to improve visual detail.
7. The processed image is resized to 28×28 pixels.
8. The trained CNN model predicts the class of the captured target.
9. The predicted class is mapped to a box/destination number.
10. If the predicted destination matches the required destination, the drone starts landing.
11. Once landing is complete, the controller stops the motors.

---

## Repository Structure

```text
Quadcopter Image Detection/
│
├── cnn/
│   ├── cnn.ipynb
│   ├── image_processing.ipynb
│   └── predict.ipynb
│
├── controllers/
│   ├── mavic2pro/
│   │   ├── Makefile
│   │   ├── mavic2pro.c
│   │   └── mavic2pro.exe
│   │
│   └── my_controller/
│       ├── my_controller.py
│       └── predict.py
│
├── datasets/
│   ├── train.csv
│   ├── test.csv
│   ├── box1/
│   ├── box2/
│   ├── box3/
│   ├── box4/
│   ├── box5/
│   └── image_processing/
│
├── images/
│   ├── 0_highres.jpg
│   ├── 1_highres.jpg
│   ├── 2_highres.jpg
│   ├── 5_highres.jpg
│   └── 8_highres.jpg
│
├── model/
│   └── location.keras
│
├── worlds/
│   └── mavic_2_pro.wbt
│
└── README.md
```

---

## Main Components

### 1. Webots Simulation

The `worlds/` folder contains the Webots world file:

```text
worlds/mavic_2_pro.wbt
```

This world defines the simulation environment where the quadcopter operates. The project uses a simulated Mavic 2 Pro drone with devices such as:

- Camera
- GPS
- Gyroscope
- Inertial measurement unit
- Propeller motors
- LEDs
- Camera pitch motor

---

### 2. Drone Controller

The main Python controller is located at:

```text
controllers/my_controller/my_controller.py
```

This file controls the drone movement and decision-making process. It handles:

- Drone takeoff
- Altitude control
- Roll, pitch, and yaw stabilization
- Waypoint navigation
- Camera image capture
- Calling the prediction pipeline
- Checking whether the detected target matches the desired destination
- Triggering landing when the correct target is detected

The controller uses proportional control constants for stabilizing movement and keeping the drone at the desired altitude.

---

### 3. Prediction Pipeline

The prediction helper file is located at:

```text
controllers/my_controller/predict.py
```

This script loads the trained Keras model from:

```text
model/location.keras
```

It processes captured images and predicts the target class. The prediction flow includes:

- Reading the captured box image
- Converting the image to grayscale
- Cropping the region of interest
- Resizing the cropped image to 28×28 pixels
- Passing the image into the CNN model
- Mapping the predicted label to a box/destination number

---

### 4. CNN Model Training

The CNN model is trained in:

```text
cnn/cnn.ipynb
```

The notebook loads the dataset from:

```text
datasets/train.csv
datasets/test.csv
```

The dataset contains labelled 28×28 grayscale images stored in CSV format. Each row contains a class label followed by 784 pixel values.

The CNN architecture includes:

- Convolutional layers
- Max pooling layers
- Flatten layer
- Dense hidden layer
- Softmax output layer

The trained model is saved as:

```text
model/location.keras
```

---

### 5. Image Processing Experiments

The notebook below applies image processing filters to cropped target images:

```text
cnn/image_processing.ipynb
```

The image processing steps include:

- Grayscale image loading
- Minimum filtering using erosion
- Maximum filtering using dilation
- Laplacian filtering for detail enhancement
- Saving processed outputs for comparison

Processed images are stored under:

```text
datasets/image_processing/
```

---

### 6. Prediction Notebook

The prediction notebook is located at:

```text
cnn/predict.ipynb
```

It is used for testing the trained model on processed images from the box folders. It follows a similar workflow to the controller prediction logic and helps validate the image preprocessing and model prediction process before integrating it into Webots.

---

## Dataset

The project includes two CSV datasets:

```text
datasets/train.csv
datasets/test.csv
```

Each dataset row follows this structure:

```text
label, pixel1, pixel2, pixel3, ..., pixel784
```

This means each image is represented as a 28×28 grayscale image flattened into 784 pixel values.

The project also includes box image folders:

```text
datasets/box1/
datasets/box2/
datasets/box3/
datasets/box4/
datasets/box5/
```

These folders store captured and processed images for different target boxes. The saved versions include original images, grayscale images, cropped images, filtered images, Laplacian-enhanced images, and resized images.

---

## Model Classes

The CNN classifier uses five output classes:

```python
class_dict = {
    0: "T-shirt",
    1: "pants",
    2: "pullover",
    3: "shoes",
    4: "Bag"
}
```

These predicted classes are then mapped to destination boxes:

```python
box_dict = {
    "T-shirt": 1,
    "pants": 5,
    "pullover": 2,
    "shoes": 4,
    "Bag": 3
}
```

This mapping allows the controller to convert CNN predictions into physical target destinations inside the Webots simulation.

---

## Technologies Used

- Python
- Webots
- TensorFlow / Keras
- OpenCV
- NumPy
- Pandas
- Matplotlib
- Pillow
- Jupyter Notebook
- C controller files for Webots reference/control support

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/farbodfld/Quadcopter-Image-Detection.git
cd Quadcopter-Image-Detection
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it:

Windows:

```bash
venv\Scripts\activate
```

macOS/Linux:

```bash
source venv/bin/activate
```

### 3. Install Python dependencies

```bash
pip install numpy pandas opencv-python matplotlib pillow tensorflow keras jupyter
```

### 4. Install Webots

Download and install Webots from the official Cyberbotics website. After installation, make sure Python controllers are correctly configured in Webots.

---

## How to Run the Project

### Run the Webots Simulation

1. Open Webots.
2. Open the world file:

```text
worlds/mavic_2_pro.wbt
```

3. Make sure the controller is set to:

```text
my_controller
```

4. Start the simulation.

The drone should take off, move between waypoints, capture images, classify the detected target, and land when the desired destination is found.

---

## How to Train the CNN Model

Open the notebook:

```text
cnn/cnn.ipynb
```

Run the cells in order. The notebook performs the following steps:

1. Loads `train.csv` and `test.csv`
2. Separates labels and pixel values
3. Reshapes images into 28×28×1 format
4. Normalizes pixel values
5. Converts labels to categorical format
6. Builds the CNN model
7. Trains the model
8. Evaluates test accuracy
9. Saves the model to `model/location.keras`

---

## Image Processing Steps

The image preprocessing pipeline used before prediction includes:

1. Read image from a box folder
2. Convert image to grayscale
3. Crop the relevant region
4. Apply erosion/minimum filtering
5. Apply Laplacian enhancement
6. Resize image to 28×28 pixels
7. Send the processed image to the CNN model

This preprocessing is important because the trained model expects small grayscale images with the same format as the training dataset.

---

## Example Prediction Flow

A simplified version of the prediction logic is:

```python
image = cv2.imread("box_image.jpg", cv2.IMREAD_GRAYSCALE)
cropped = image[row_start:row_end, col_start:col_end]
filtered = cv2.erode(cropped, None)
enhanced = apply_laplacian_filter(filtered)
resized = resize_image(enhanced, (28, 28))
prediction = model.predict(np.array([resized]))
```

The final predicted class is mapped to a destination box. If the destination matches the target, the drone begins landing.

---

## Evaluation

The model is evaluated in `cnn/cnn.ipynb` using test accuracy after training. For future improvement, the following metrics could also be added:

- Confusion matrix
- Precision
- Recall
- F1-score
- Per-class accuracy
- Prediction confidence visualization
- Comparison of preprocessing filters

---

## Future Improvements

- Replace manual crop coordinates with automatic object detection
- Train the model on custom target images from the Webots environment
- Add real-time camera frame prediction instead of saved-image prediction
- Improve dataset consistency between training classes and simulated target boxes
- Add confusion matrix and detailed evaluation report
- Add requirements.txt for easier setup
- Add screenshots or GIFs of the Webots simulation
- Add command-line arguments for selecting destination target
- Improve path handling to make the project portable across operating systems
- Deploy the prediction model as a small API or dashboard

---

## Author

Developed by [farbodfld](https://github.com/farbodfld)

---
