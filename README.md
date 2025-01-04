# SilentSpeak - IMU-Based Speech Command Classification Using Jaw Motion

### **Overview**  
This project introduces a **motion-based alternative** for speech command recognition by leveraging **Inertial Measurement Unit (IMU)** sensors to analyze jaw movements during speech. By capturing **accelerometer** and **gyroscope** data from an IMU sensor placed on the lower jaw, the system accurately classifies spoken commands without relying on audio. This approach ensures a **noise-resilient**, **privacy-conscious**, and **inclusive** solution for real-time speech command recognition.

---

### **Key Features**  
- **Noise Robustness**: Works effectively in loud environments where audio-based systems fail.  
- **Privacy-Focused**: Uses motion data instead of sensitive audio recordings.  
- **Accessibility**: Provides an alternative communication method for individuals with speech impairments.  
- **Real-Time Predictions**: Processes IMU data and triggers actions seamlessly on mobile devices.  
- **Wearable Integration**: Demonstrates potential for hands-free control of devices like smartphones, smart home systems, and assistive tools.  

---

## 📁 Project Structure

- **`data`**  
  Contains input data collected for real time gestures which is then picked up and for feature extraction before predicting

- **`main`**  
  Core logic for the ESP32, including:
  - `i2c_simple_main.c`: Main code for handling sensors and data collection.
  - `idf_component.yml`: ESP-IDF configuration file.

- **`espressif__mpu6050`**  
  Includes reusable libraries or components for the project.

- **`models`**  
  Stores machine learning artifacts:
  - `gesture_feature_columns.npy`: Stores Label Encoding Mapping.
  - `word_prediction_model.pkl`: Trained RandomForestClassifier model for gesture classification.
  - Train the model by running the notebook ML_notebook.ipynb to save the above two files before running

- **`notebook`**  
  - `ML_notebook.ipynb`: Notebook for RandomForestClassifier training, evaluation, and visualization.

- **`scripts`**  
  Utility scripts for automation:
  - `run.py`: Main script to run.

## Script: Real-Time Gesture Collection and Processing

### Overview
The Python script interfaces with an ESP32 device to collect sensor data, extract features, predict gestures, and execute actions based on the predicted gestures. Below is a detailed breakdown of its components:

### Key Libraries Used
- **`serial`**: For serial communication with the ESP32.
- **`csv`**: To save sensor data into CSV files.
- **`pandas`**: For data manipulation.
- **`numpy`**: For numerical computations.
- **`joblib`**: To load the trained model.
- **`subprocess`**: For executing Android Debug Bridge (ADB) commands.
- **`scipy.signal`**: For peak detection.

### Workflow

1. **Setup Serial Communication**:
   - Establishes a serial connection with the ESP32 device using `pyserial`.

2. **Data Collection**:
   - Collects data for 20 gestures.
   - Saves data in CSV format with sensor readings (`Accel_X`, `Accel_Y`, `Accel_Z`, `Gyro_X`, `Gyro_Y`, `Gyro_Z`).
   - Extracts features from collected data and predicts gestures using a pre-trained model.

3. **Feature Extraction**:
   - Computes statistical metrics (`mean`, `std`, `median`) for each sensor axis.
   - Estimates syllable count using the magnitude of accelerometer data and peak detection.

4. **Prediction**:
   - Loads the pre-trained Random Forest model.
   - Predicts gestures with a confidence threshold to avoid misclassification.

5. **Gesture Actions**:
   - Executes predefined actions (e.g., `CallManavUMass`, `end`, `PickUpCall`) using ADB commands based on the predicted gesture.

---

## Notebook: Model Training and Validation

### Overview
The Jupyter Notebook is used to train a Random Forest Classifier model for gesture prediction. It processes the combined gesture data (`combine.csv`), trains the model, and evaluates its performance.

### Key Libraries Used
- **`numpy`**: For numerical operations.
- **`pandas`**: For data manipulation.
- **`seaborn` & `matplotlib`**: For data visualization.
- **`sklearn`**: For model training, encoding labels, and evaluation.

### Workflow

1. **Data Preparation**:
   - Reads the combined dataset (`combine.csv`).
   - Drops missing values and shuffles the data.
   - Prioritizes the `syllable_count` feature by amplifying its impact.

2. **Feature and Label Encoding**:
   - Splits data into features (`X`) and labels (`y`).
   - Encodes labels using `LabelEncoder`.

3. **Model Training**:
   - Splits data into training and testing sets (80:20 ratio).
   - Trains a Random Forest Classifier.

4. **Evaluation**:
   - Calculates accuracy on test data.
   - Generates a confusion matrix to visualize model performance.

5. **Model Persistence**:
   - Saves the trained model (`word_prediction_model.pkl`) and label encodings (`gesture_feature_columns.npy`) for real-time predictions.

---

## 🔧 Setup Instructions

### 🖥️ Hardware Setup
1. **Connect the ESP32S3 Microcontroller**:
   - To run this example, you should have one ESP32, ESP32-S, ESP32-C or ESP32-H based development board as well as a MPU6050. MPU6050 is a inertial measurement unit, which contains a accelerometer, gyroscope as well as a magnetometer.
   - Connect your ESP32S3 and IMU sensor to your computer using a USB cable.
   - Ensure that the ESP32 is programmed to send IMU (Inertial Measurement Unit) data in the required format.
   
#### Pin Assignment:
|                  | SDA             | SCL           |
| ---------------- | -------------- | -------------- |
| ESP I2C Master   | I2C_MASTER_SDA | I2C_MASTER_SCL |
| MPU9250 Sensor   | SDA            | SCL            |

### Install ESP-IDF framework 

Refer ESP-IDF documentation for installation [ESP-IDF Documentation](https://docs.espressif.com/projects/esp-idf)

### Install Required Libraries

To install the necessary libraries for the project, run the following command:

```bash
pip install numpy pandas joblib pyserial scikit-learn
```

### ⚙️ Update Serial Port
1. Locate the `esp32_port` variable in the relevant script.  
2. Update the variable to match your ESP32's serial port:  
   - For **Windows**, use `COMx` (e.g., `COM10`). This can be found in device manager under ports  
   - For **Linux/Mac**, use `/dev/ttyUSBx` or `/dev/ttyACMx` (e.g., `/dev/ttyUSB0`).
3. To establish a serial connection with the ESP32 for collecting sensor data:

```python
#### Update the ESP32 serial port
esp32_port = 'COM10'  # Replace 'COM10' with your ESP32's port
baud_rate = 115200

#### Initialize serial connection
ser = serial.Serial(esp32_port, baud_rate, timeout=1)
```

### Build and Flash

Enter `idf.py -p PORT flash monitor` to build, flash and monitor the project.

(To exit the serial monitor, type ``Ctrl-]``.)

See the [Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/index.html) for full steps to configure and use ESP-IDF to build projects.

### **Project Report**  
To find the project report click:  
https://docs.google.com/document/d/1vLwOhwVL9Af6kikUltheWme07E2eAEAzWyzDgtBfKgU/edit?usp=drive_link

---

### **Real-Time Demo**  
Watch the end-to-end demonstration of the project here:  
https://drive.google.com/file/d/1g4QbBxXHoueTJju_Ub60Bt_T03qUPyH6/view?usp=share_link

---

### **Future Scope**  
- Integrating the system into wearable devices for hands-free control.  
- Expanding the command set for broader applications.  
- Improving model robustness with larger datasets and advanced algorithms like deep learning.  

---

### **Contributors**  
- Khushi Gandhi
- Shivani Anilkumar
- Manav Manoj Dhelia 
---

### **Acknowledgments**  
Special thanks to the University of Massachusetts, Amherst for providing resources and support for this project.  
