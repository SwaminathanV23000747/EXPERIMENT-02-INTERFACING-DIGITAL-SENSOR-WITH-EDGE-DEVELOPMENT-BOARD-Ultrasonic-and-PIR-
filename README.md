# EXPERIMENT-02-INTERFACTING-DIGITAL-SENSOR-WITH-EDGE-DEVELOPMENT-BOARD-ULTRASONIC-AND-PIR-SENSOR-(RASPBERRYPI-PI4)
### NAME : Swaminathan.V
### DEPARTMENT :CSE-IOT
### ROLL NO :212223110057
### DATE OF EXPERIMENT :28/04/2026

### AIM
To interface a digital sensor (Ultrasonic and PIR) with the Raspberry Pi 4 and control it using Python.

## APPARATUS REQUIRED
Raspberry Pi 4
LED (Light Emitting Diode)
330Ω Resistor
IR Sensor
Breadboard
Jumper Wires
USB Cable
 ## THEORY

![Raspberry Pi Pin](https://github.com/user-attachments/assets/19e5a1e7-cb46-4909-ba59-e4f4560cae03)





 
 
 
 ### FIGURE-01 RASPI PI 4 PINOUT DIAGRAM: 


The Raspberry Pi 4 Model B is built around a Broadcom BCM2711 system-on-chip that integrates a quad-core ARM Cortex-A72 (64-bit) CPU, VideoCore VI GPU, memory controller, and peripheral interfaces, forming a compact yet complete computer architecture where the SoC connects internally to RAM, USB 3.0 controller, Gigabit Ethernet, HDMI display, and wireless modules. Its 40-pin GPIO header provides a flexible pin configuration consisting of power pins (5 V and 3.3 V), multiple ground pins, and general-purpose input/output pins that operate at 3.3 V logic and can be programmed for digital I/O or alternate functions. Key alternate functions include I²C (SDA, SCL) for sensor communication, SPI (MOSI, MISO, SCLK, CS) for high-speed peripheral interfacing, UART (TX, RX) for serial communication, and PWM for control applications.  For communication, I2C (SDA, SCL), SPI (MOSI, MISO, SCK), and UART (TX, RX) interfaces are mapped across different GPIO pins, allowing seamless connectivity with sensors and peripherals. All GPIO pins support PWM (Pulse Width Modulation), making it useful for motor control, LED brightness adjustment, and sound applications. The BOOTSEL button enables USB mass storage mode for firmware flashing, while the DEBUG pins (SWD interface) provide debugging capabilities. With its low power consumption, flexible GPIO options, and rich interface support, the Raspberry Pi Pico is widely used for IoT, embedded systems, robotics, and automation projects.This architecture and pin multiplexing allow the Raspberry Pi 4 to act as both a general-purpose computing platform and an embedded controller, supporting rapid prototyping, hardware interfacing, and IoT applications.
## Ultrasonic Sensor:
An ultrasonic sensor is a distance-measuring device that uses high-frequency sound waves to detect objects and calculate how far away they are. It works by emitting ultrasonic pulses (typically around 40 kHz) and measuring the time taken for the echo to bounce back after hitting an object. Using the speed of sound in air, the sensor converts this time into distance. Ultrasonic sensors are widely used in robotics, obstacle detection, parking systems, and automation because they provide reliable, contactless measurement regardless of lighting conditions.
<img width="600" height="411" alt="image" src="https://github.com/user-attachments/assets/d879d982-347a-45ff-92c1-b78b57e9fdef" />
 ### FIGURE-02 Ultrasonic Sensor 

## PIR Sensor:
A Passive Infrared (PIR) sensor is a motion detection device that senses changes in infrared radiation emitted by warm objects such as humans or animals. Instead of emitting signals, it passively detects heat variations within its field of view. When a warm body moves across the sensor’s detection zones, it triggers an electrical signal indicating motion. PIR sensors are commonly used in security alarms, automatic lighting systems, and energy-saving smart devices due to their low power consumption and ability to detect human presence effectively.
<img width="428" height="494" alt="image" src="https://github.com/user-attachments/assets/bb6b0f22-33d7-4d63-b5c6-05e6d655e71d" />
 ### FIGURE-03 PIR Sensor 
## Working Principle:
Experiment 2A
The Ultrasonic sensor Trig pin is connected to one of the GPIO pins of the Raspberry Pi 4.
The Ultrasonic sensor Echo pin is connected to one of the GPIO pins of the Raspberry Pi 4.
The Python script sets the take the distance taken echo output and shown in Thingspeak cloud with current status and Console.
CIRCUIT DIAGRAM
Connect the Vcc of the Ultrasonic sensor is connected to +5V in Raspberrry Pi4.
Connect the Gnd of the Ultrasonic sensor is connected to Gnd in Raspberrry Pi4.
Connect the Trig pin to any one GPIO.
Connect the Echo pin to any one GPIO.


Experiment 2B
The IR sensor is connected one of the GPIO pins in Raspberry Pi 4.
The Python script sets the PIR sensor value based on the motion detected and shown in Thingspeak and console.
CIRCUIT DIAGRAM
Connect the PIR sensor Vcc to any +5V.
Connect the PIR sensor GND to any GND.
Connect the PIR sensor OUT to any one GPIO. 

Experiment 2A
## PROGRAM (Python)
```
import RPi.GPIO as GPIO
import time
import requests

# ThingSpeak settings
API_KEY = "8YIZX65I411MK0YZ"
THINGSPEAK_URL = "https://api.thingspeak.com/update"

# GPIO pins
TRIG = 23
ECHO = 24

GPIO.setmode(GPIO.BCM)
GPIO.setup(TRIG, GPIO.OUT)
GPIO.setup(ECHO, GPIO.IN)

def get_distance():
    GPIO.output(TRIG, False)
    time.sleep(0.5)

    # Trigger pulse
    GPIO.output(TRIG, True)
    time.sleep(0.00001)
    GPIO.output(TRIG, False)

    while GPIO.input(ECHO) == 0:
        pulse_start = time.time()

    while GPIO.input(ECHO) == 1:
        pulse_end = time.time()

    pulse_duration = pulse_end - pulse_start
    distance = pulse_duration * 17150
    distance = round(distance, 2)

    return distance

try:
    while True:
        distance = get_distance()

        # Console output
        print("distance =", distance, "cm")

        # Text message for ThingSpeak
        status_text = f"distance = {distance} cm"

        # Send data to ThingSpeak
        payload = {
            "api_key": API_KEY,
            "field1": distance,   # numeric for chart
            "status": status_text # text message
        }

        response = requests.get(THINGSPEAK_URL, params=payload)
        print("Sent to ThingSpeak")

        time.sleep(15)

except KeyboardInterrupt:
    GPIO.cleanup()


````

### OUPUT  
Experiment 2A

# FIGURE -04 Kit image
<img width="1600" height="1200" alt="image" src="https://github.com/user-attachments/assets/bc019d03-30ba-4918-81c1-6b228813a9e9" />


#  FIGURE -05 Console Output
<img width="592" height="682" alt="Screenshot 2026-04-28 141730" src="https://github.com/user-attachments/assets/85e437db-2530-4bdd-be0a-add9934df9a0" />


# FIGURE -06 ThingSpeak OutPut
<img width="1917" height="1028" alt="image" src="https://github.com/user-attachments/assets/505f5510-5e27-4e0a-aa55-3d02043ea464" />

Experiment 2B
## PROGRAM (Python)
```
import RPi.GPIO as GPIO
import time
import requests

WRITE_API_KEY = "8YIZX65I411MK0YZ"
URL = "https://api.thingspeak.com/update"

PIR_PIN = 17

GPIO.setmode(GPIO.BCM)
GPIO.setup(PIR_PIN, GPIO.IN)

print("PIR Monitoring Started...")
time.sleep(2)

last_state = -1   # store previous state

def update_thingspeak(state):
    data = {
        "api_key": WRITE_API_KEY,
        "field3": state
    }
    try:
        requests.get(URL, params=data)
        print("Uploaded to ThingSpeak:", state)
    except:
        print("Upload Failed")

while True:
    motion = GPIO.input(PIR_PIN)

    if motion != last_state:   # send only if changed
        if motion == 1:
            print("Motion Detected")
            update_thingspeak(1)
        else:
            print("No Motion")
            update_thingspeak(0)

        last_state = motion
        time.sleep(15)  # ThingSpeak delay

    time.sleep(1)

 



 
````

### OUPUT  
Experiment 2B

# FIGURE -07  07 KIT
<img width="1600" height="1200" alt="image" src="https://github.com/user-attachments/assets/a6801d49-9115-4440-b958-dd0f4860211e" />

#  FIGURE -08 OUTPUT TERMINAL 
<img width="1704" height="944" alt="Screenshot 2026-04-28 144027" src="https://github.com/user-attachments/assets/f881b22e-0012-4714-8d66-c0157e073742" />

# FIGURE -09 THINGSPEAK 
<img width="1916" height="1032" alt="Screenshot 2026-04-28 143908" src="https://github.com/user-attachments/assets/db98145a-ea29-44fd-8e10-228088f88e39" />

 <img width="1919" height="974" alt="Screenshot 2026-04-28 143940" src="https://github.com/user-attachments/assets/ae7b503b-f8e3-4670-b3c6-13f3c8f5f6a2" />

## RESULTS
The Ultrasonic sensor and PIR sensor is connected to the Raspberry Pi 4 successfully and the distance and the motion detection is visualised in thingspeak confirming the proper interfacing of a digital output.
