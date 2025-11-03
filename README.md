# Smart Waste Sorting & Level Monitoring Project (Dual-Board)

![Edge Impulse](https://img.shields.io/badge/Machine%20Learning-Edge%20Impulse-blue.svg) ![ESP32](https://img.shields.io/badge/Hardware-ESP32%20|%20ESP32--CAM-purple.svg)

This project uses a **dual-microcontroller** architecture to create an efficient waste monitoring and sorting system:

1.  **Board 1: ESP32-CAM (Waste Sorter)**
    * Fully responsible for the *Computer Vision* (TinyML) task.
    * Runs an **Edge Impulse** model to identify waste (e.g., organic vs. inorganic).
    * Controls **Servo Motors** to operate the sorting actuator.

2.  **Board 2: ESP32 (Level Monitoring)**
    * Responsible for monitoring and IoT connectivity.
    * Reads **Ultrasonic Sensors** to measure the waste pile level.
    * Sends level data in *real-time* to a **Dashboard Application** (Blynk, Firebase, etc.) via Wi-Fi.

---

## 📂 Project Folder Structure

* **/trashbin/**
    * Contains the `.ino` code to be uploaded to the **ESP32-CAM**.
    * This code includes the Edge Impulse model inference and servo motor control logic.
* **/Monitoring/**
    * Contains the `.ino` code to be uploaded to the **standard ESP32**.
    * This code includes ultrasonic sensor readings and data transmission to the application (Blynk/Firebase/etc.).
* **/Collect_Images_for_EdgeImpulse/**
    * Contains a specific `.ino` code for the **ESP32-CAM** used only during the process of collecting a *datasheet* (training images) for Edge Impulse.
* **/library_edge_impulse_model/**
    * **EMPTY FOLDER (Placeholder):** create an empty folder; this is where you must place the model *library* `.zip` file that you download from Edge Impulse.

---

## ⚙️ Hardware & Software Requirements

### Hardware
* **Board 1:** 1x **ESP32-CAM** (with FTDI programmer module).
* **Board 2:** 1x **ESP32** (NodeMCU, WROOM, or similar).
* **Sensors:** 2x **Ultrasonic Sensors** (e.g., HC-SR04).
* **Actuators:** 2x **Servo Motors** (e.g., SG90).
* **Power Supply:** A stable 5V power supply (2A or more recommended, to power both boards and servos).
* Jumper Wires, LEDs, Resistors, etc.

### Software
* [Arduino IDE](https://www.arduino.cc/en/software)
* **ESP32** Board Driver for Arduino IDE.
* **Edge Impulse Model Library** (Created in Step 1).
* Sensor Libraries (e.g., `NewPing` for ultrasonic).
* Application Libraries (e.g., `BlynkSimpleEsp32.h`, `Firebase_ESP_Client.h`, etc. *Only installed for the ESP32 code*).

---

## 🔌 Schematics & Pinout

Here is the circuit schematic and pinout details used in this project.

![Circuit Schematic](/Skematik/Skematic.png)

### 1. Board 1: ESP32-CAM (Waste Sorter)
| Component | Control Pin |
| :--- | :--- |
| Servo 1 | GPIO 14 |
| Servo 2 | GPIO 15 |
| Power | VCC (+5V) & GND |

### 2. Board 2: ESP32 (Level Monitoring)
| Component | Trigger Pin | Echo Pin |
| :--- | :--- | :--- |
| Ultrasonic Sensor 1 | GPIO 21 | GPIO 19 |
| Ultrasonic Sensor 2 | GPIO 5 | GPIO 18 |
| Power | VCC (+5V) & GND |

**Important Note:** Ensure the **GND** from the 5V power supply, the **GND** of the ESP32-CAM, and the **GND** of the ESP32 are all connected to a single **Common Ground**.

---

## 🚀 Implementation Steps

This project is divided into two parallel workflows for each board.

### Part 1: (Required) Train the Model for ESP32-CAM

1.  **Collect Dataset:**
    * Open the `/Collect_Images_for_EdgeImpulse/` folder.
    * Upload the code to your **ESP32-CAM**.
    * Use this script to take many *datasheet* (training) images for each waste category.
2.  **Upload & Train on Edge Impulse:**
    * Create a new *Image Classification* project on [Edge Impulse](https://www.edgeimpulse.com/).
    * Upload all your images and assign the correct **Label**.
    * Create an *Impulse* (e.g., `96x96`, `Image`, `Classification`).
    * Train your model in the "Classifier" tab.
3.  **Deploy (Download Library):**
    * Go to the **Deployment** tab, select **Arduino**.
    * Click **Build** to download your model's `.zip` file.
    * Save this `.zip` file in the `/library_edge_impulse_model/` folder for reference.

---

### Part 2: Setup ESP32-CAM (Waste Sorter)

1.  **Install the Model Library:**
    * Open the Arduino IDE.
    * Select **Sketch** > **Include Library** > **Add .ZIP Library...**
    * Choose the `.zip` file you downloaded from Edge Impulse (Part 1).
2.  **Open the Sorter Project:**
    * Open the `.ino` file from the library folder (it will contain the training result code) or use the `/trashbin/` code and add the library.
3.  **Code Configuration:**
    * Adjust the **Servo Motor** pins (`SERVO_PIN`) to match your circuit (Pins 14 and 15 based on the schematic).
4.  **Upload the Code:**
    * Connect the **ESP32-CAM** (using an FTDI programmer).
    * Select the "AI Thinker ESP32-CAM" board and the correct port.
    * Upload the code.
    * **Note:** This board will now focus on running inference and controlling the servos.

---

### Part 3: Setup ESP32 (Level Monitoring)

1.  **Install Supporting Libraries:**
    * Open the Arduino IDE.
    * Use the Library Manager to install the `NewPing` sensor library and your application library (e.g., `Blynk`).
2.  **Open the Monitoring Project:**
    * Open the `.ino` file from the `/Monitoring/` folder.
3.  **Code Configuration:**
    * **Credentials:** Enter your Wi-Fi `SSID` and `PASSWORD`.
    * **Application Connection:** Enter the *Auth Token* or *API Key* for your application (e.g., Blynk, Firebase).
    * **Pinout:** Adjust the Ultrasonic Sensor pins (`TRIG_PIN`, `ECHO_PIN`) to match the schematic (Pins 21, 19, 5, 18).
4.  **Upload the Code:**
    * Connect your **standard ESP32**.
    * Select the appropriate board (e.g., "NodeMCU-32S" or "ESP32 Dev Module").
    * Upload the code.

### Part 4: Testing

1.  Power on both devices.
2.  The **ESP32** will connect to Wi-Fi and start sending level data to your application dashboard.
3.  The **ESP32-CAM** will start running the camera and the model.
4.  Point waste at the camera, and observe the **servos** move according to the classification (organic/inorganic).
5.  Check your *dashboard* to see the waste level data.

## 📄 License
MIT License.
