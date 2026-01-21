# Robo-Press & Fold: Automated Garment Care System

An integrated robotic assistant that combines **Computer Vision (YOLOv8)** with a **7-DoF Mechatronic System** to automate the steaming and folding of laundry. By leveraging a laptop's compute power and an Arduino-controlled gantry, this project provides a low-cost alternative to industrial garment automation.



## Key Features

* **Vision-Driven Path Planning:** Uses a custom-trained **YOLOv8** model to identify garment boundaries and plan optimal steaming paths.
* **7-DoF Actuation:** Consists of a 4-DoF steaming gantry (X, Y, Z, Tilt) and a 3-DoF automated FlipFold base (Left, Right, Bottom panels).
* **Dual-State End Effector:** A custom "Lift & Tilt" mechanism that toggles the steam iron's internal orientation sensor to manage power states programmatically.
* **Precision Folding:** High-torque servos execute a sequential folding pattern, replacing unreliable time-based DC motor control.

## System Architecture

### Hardware Logic
The system is decoupled into two primary subsystems to ensure low-latency motor control:
1. **Perception Layer (Laptop):** Handles the OpenCV/YOLOv8 pipeline, performing perspective correction (Homography) and Inverse Kinematics (IK) calculations.
2. **Actuation Layer (Arduino/ESP32):** Receives parsed G-code strings via Serial and executes real-time stepper/servo movements.



### Degrees of Freedom (DoF) Breakdown
| Subsystem | Components | DoF | Motion Type |
| :--- | :--- | :--- | :--- |
| **Steamer** | X-Y Gantry, Z-Lift, Pitch-Tilt | 4 | 3 Translation, 1 Rotation |
| **Folder** | Left, Right, & Bottom Panels | 3 | 3 Independent Rotations |
| **Total** | | **7** | |



## Technical Deep Dive

### Kinematic Implementation
The robot uses **Inverse Kinematics** to map 2D pixel coordinates $(P_x, P_y)$ from the camera feed into real-world motor steps.
* **Forward Kinematics:** $X = q_1 \cdot S$, $Y = q_2 \cdot S$ (where $q$ is steps and $S$ is the scale factor).
* **Perspective Correction:** Uses a 4-point **Homography Matrix** to warp the camera's tilted view into a measurable flat map of the folding board.



### "Lift & Tilt" Workflow
To safely toggle the iron's power state without snagging the fabric:
1. **Z-Lift:** Raises the end effector to clear the garment.
2. **Tilt ($\theta$):** Rotates the iron 90 degrees (Vertical = ON, Horizontal = OFF).
3. **Raster Scan:** Executes a "snake" pattern over the cloth coordinates.



## Installation & Setup

1. **Clone the Repo:** `git clone https://github.com/yourusername/robo-press-fold.git`
2. **Python Environment:** Install dependencies via `pip install opencv-python ultralytics pyserial`.
3. **Arduino Firmware:** Upload the `.ino` files found in `/firmware` to your microcontroller.
4. **Calibration:** Run `calibrate.py` to set your Homography anchor points and motor scaling factors.
