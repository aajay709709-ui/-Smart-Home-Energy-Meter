# Motion Detection Alert System

This project is a real-time home security system that detects motion using a PIR (Passive Infrared) sensor connected to an ESP8266 microcontroller. When motion is detected, the ESP8266 triggers a Python Flask server that activates a webcam, captures an image, performs face recognition using OpenCV, and sends an email alert if the face is not recognized (i.e., an intruder is detected). It is suitable for home security, office surveillance, or IoT-based intrusion detection applications.
---
##  Components Used

### Hardware:
- **ESP8266 NodeMCU** – Wi-Fi microcontroller to send alerts to the server.
- **PIR Motion Sensor** – Detects movement in its field of view.
- **USB Webcam** – Captures live image when motion is detected.
- **Breadboard, Jumper Wires**

### Software:
- **Arduino IDE** – To upload code to ESP8266.
- **Python 3** – Main server and AI logic.
- **Flask** – Lightweight web framework to receive motion triggers.
- **OpenCV** – Used for face detection and recognition.
- **smtplib** – Python’s SMTP library for sending email alerts.
- **EmailMessage** – To build and send alert messages with image attachments.

---
##  Working Mechanism

1. **Motion Detection (ESP8266)**:
   - The PIR sensor detects motion and sends a signal to the ESP8266.
   - ESP8266 makes an HTTP POST request to the Flask server endpoint `/motion`.

2. **Server Processing (Flask)**:
   - The Flask server receives the motion signal.
   - It triggers the `detect_and_alert()` function in a new thread.

3. **Face Detection & Recognition**:
   - A webcam captures an image.
   - OpenCV's Haar Cascade detects faces.
   - LBPHFaceRecognizer compares detected faces against a trained dataset.
   - If an unknown face is detected (confidence threshold > 70), an alert is triggered.

4. **Email Notification**:
   - An email is sent with the captured image attached using `smtplib`.

---
## Dataflow diagram
<img width="437" alt="image" src="https://github.com/user-attachments/assets/be62f4af-3efc-4be4-ba9c-f922ca4c3800" />

---
##  Hardware Setup

| ESP8266 Pin | PIR Sensor |
|-------------|------------|
| D2          | OUT        |
| 3V3         | VCC        |
| GND         | GND        |

Ensure the ESP8266 and webcam are connected to the same network as the server PC.

---
##  Project Structure

```
motion-detection-alert-system/
│
├── training_face.py                # Script to capture and train faces
├── security_system.py              # Core detection and email alert logic
├── app.py                          # Flask server
├── trained_model.yml               # Trained LBPH face recognition model
├── label_map.npy                   # Mapping of labels to names
├── known_faces/
│   └── person1_name.jpg             # known_person images to train the model 
│   └── person2_name.jpg
│   └── person3_name.jpg               
├── captured_images/
│   └── intruder.jpg                # Sample captured intruder image
├── README.md                       # Project documentation
```
----

##  Arduino Code (ESP8266)

Update `ssid`, `password`, and `serverUrl` in `motion_esp8266.ino`, then upload via Arduino IDE.

```cpp
const char* ssid = "Your_SSID";
const char* password = "Your_PASSWORD";
const char* serverUrl = "http://<your-server-ip>:5000/motion";
```

---

##  Flask Server Setup

###  Install Dependencies

```bash
pip install flask opencv-python numpy
```

###  Email Setup

Enable **App Passwords** for Gmail and update in `security_system.py`:

```python
email_user = "your_email@gmail.com"
email_pass = "your_app_password"
receiver_email = "recipient_email@gmail.com"
```

---

##  Running the Server

```bash
python app.py
```

This starts a Flask server on `http://0.0.0.0:5000/`

---

##  Face Recognition Model

Make sure you have:

- `trained_model.yml`: Your trained LBPH model
- `label_map.npy`: Mapping of label IDs to names
- `haarcascade_frontalface_default.xml`: Face detection model from OpenCV

---

##  Sample Email Alert

- **Subject**: 🚨 Alert: Unknown person detected!
- **Body**: Motion detected at your home. See attached image.
- **Attachment**: Intruder image captured by webcam.

---

##  Example Output

```
[SERVER] Motion detected! Starting alert process...
[INFO] Capturing image...
[INFO] Image saved to captured_images/intruder.jpg
[DEBUG] Detected: Unknown, Confidence: 82.56
[ALERT] Unknown person detected.
[INFO] Sending email alert...
[INFO] Email sent successfully.
```
---
## Acknowledgments

- [OpenCV](https://opencv.org/)
- [Flask](https://flask.palletsprojects.com/)
- [ESP8266 Arduino Core](https://github.com/esp8266/Arduino)
- Gmail for SMTP services

---
