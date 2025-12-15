# Smart Dustbin — Arduino Automatic Lid

A simple Arduino project that opens a dustbin lid automatically when an object (hand, trash) is detected close to the ultrasonic sensor.

This README explains the hardware, wiring, how the provided code works, how to configure/tune it, and troubleshooting tips.

---

## Table of contents
- Project overview
- Hardware required
- Wiring / Pin mapping
- Software / Libraries
- How the code works
- Configuration & tuning
- Troubleshooting

---

## Project overview
The project uses an ultrasonic distance sensor to detect objects within a configurable distance and a hobby servo to open and close the dustbin lid. The code reads the distance several times, averages the readings to reduce noise, and triggers the servo when an object is detected within the threshold.

Provided code file: `smart-dustbin-code.txt`

Commit reference (source file): https://github.com/mirthun-d/arduino-dustbin-project/blob/fee85c7d3c34a889a56f7bcfc9f6f4f8ca522d86/smart-dustbin-code.txt

---

## Hardware required
- Arduino UNO (or compatible)
- Ultrasonic sensor (HC-SR04 or similar)
- Servo motor (e.g., SG90 or similar hobby servo)
- Jumper wires
- Breadboard (optional)
- Small mounting setup for servo and sensor to the dustbin lid

---

## Wiring / Pin mapping
The code expects the following pin configuration (modify in code if you wish to change pins):

- Ultrasonic trigger (TRIG): Arduino digital pin 5
- Ultrasonic echo (ECHO): Arduino digital pin 6
- Servo signal pin: Arduino digital PWM pin 9
- Servo VCC: 5V (or external 5V supply if servo draws significant current)
- Servo GND: Arduino GND (ensure common ground with Arduino)

Wiring summary (HC-SR04):
- VCC -> 5V
- GND -> GND
- TRIG -> D5
- ECHO -> D6

Servo:
- Signal -> D9
- VCC -> 5V (or external 5V)
- GND -> GND (common with Arduino)

Important: If you use an external power supply for the servo, connect grounds together.

---

## Software / Libraries
- Arduino IDE
- Servo library (built-in to Arduino IDE)

Install no additional libraries; just open the code in the Arduino IDE and upload.

---

## How the code works (summary)
Important functions and behavior in the provided sketch:
- setup()
  - Initializes serial, attaches servo, sets pin modes.
  - Writes servo position to `100` (interpreted as the "closed" position in this sketch) then detaches the servo.
- measure()
  - Triggers the ultrasonic sensor and measures round-trip pulse time using `pulseIn(echo, HIGH)`.
  - Converts time to distance in cm: Distance = (Duration / 2) / 29.1.
- loop()
  - Takes three rapid distance readings, stores in an array, averages them to reduce noise.
  - If averaged distance <= 20 cm, the servo is attached and moved to `300` (intended "open" position), waits, then moved back to `100` (closed) and detached.

Notes about the provided code:
- The code uses averaging of three samples to smooth the ultrasonic readings.
- The sketch attaches the servo only when moving to save power and potentially reduce jitter while idle.

---

## Configuration & tuning
- Distance threshold:
  - Change the detection range by editing the value in the conditional:
    - `if (Distance <= 20)` — set to the number of centimeters appropriate for your setup.
- Servo angles:
  - The sketch uses `servo.write(100)` and `servo.write(300)`. Typical hobby servos accept values in the 0–180 range. If your servo behaves incorrectly with `300`, replace `300` with an appropriate angle (e.g., `0`, `90`, or `180`) that fully opens the lid on your mounting.
  - Test and choose two values for closed and open positions (for example `servo.write(90)` closed and `servo.write(0)` open).
- Timeouts/delays:
  - The code uses `delay(1500)` after moving the servo (1,500 ms = 1.5 s). Adjust this to keep the lid open longer or shorter. Note: the comment in the original file mentions 3.5 seconds but the code uses 1.5 seconds; update as needed.
- Averaging:
  - More samples reduce noise but increase latency. The current code uses 3 samples with a 10 ms gap between them.


---

## Troubleshooting
- No response from ultrasonic sensor:
  - Check wiring (TRIG/ECHO pins), ensure VCC and GND connected.
  - Use Serial Monitor to print `Duration` and `Distance` for debugging.
- Servo jittering or resets:
  - Servo drawing too much current from Arduino 5V — use an external 5V supply and common ground.
  - Try removing `servo.detach()` during testing; some setups behave better when the servo remains attached.
- Strange servo angles (e.g., trying to write 300):
  - Most servos accept 0–180. Change `300` to a valid angle and recalibrate.

---

Author: mirthun-d
Repository: https://github.com/mirthun-d/arduino-dustbin-project
