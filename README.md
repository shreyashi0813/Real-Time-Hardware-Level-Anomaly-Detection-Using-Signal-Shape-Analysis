# Real-Time Hardware-Level Anomaly Detection Using Signal Shape Analysis

A low-cost embedded anomaly detection system built around an Arduino Uno. The system monitors three different sensor channels, extracts statistical features from fixed-size sampling windows, assigns weighted anomaly scores, and classifies results into four alert levels. Scores are sent over USB serial to a Python/Matplotlib visualization client.

## Project Overview

Conventional threshold-only monitoring can confuse transient spikes with meaningful events and usually provides only a binary alert. This project explores a lightweight feature-based approach using the mean, root mean square (RMS), and variance of sampled sensor signals.

## Hardware

- **Arduino Uno** — ATmega328P microcontroller with 10-bit ADC
- **Microphone sensor module** — acoustic signal monitoring
- **MQ-3 alcohol sensor** — alcohol/gas response monitoring
- **KY-024 Hall-effect sensor** — magnetic-field detection
- **Active 5 V buzzer** — audible alert output
- Breadboard, jumper wires, and USB cable

## System Architecture

```text
Microphone sensor ─┐
MQ-3 sensor ───────┼──> Arduino Uno ──> Feature extraction ──> Weighted score
KY-024 Hall sensor ┘        │                                     │
                            │                                     v
                            │                             Four-level alert
                            │                                     │
                            v                                     v
                       USB serial ──> Python + Matplotlib       Buzzer
```

## Signal Processing

Samples are collected in fixed-size windows. For each sensor channel, the firmware calculates:

- **Mean** — average signal level.
- **Root Mean Square (RMS)** — effective magnitude of the signal.
- **Variance** — spread of the samples around their mean.

The report describes the weighted scoring approach as:

`Score = w1 × RMS + w2 × Variance + w3 × Mean`

Weights are selected according to the sensor's behaviour. The report suggests, for example, assigning more weight to variance for alcohol-sensor fluctuations and to RMS for acoustic bursts.

## Alert Classification

| Alert level | Score range in the report | Buzzer pattern described |
|---|---:|---|
| NORMAL | Below 150 | No alert specified |
| WARNING | 150–300 | Single beep |
| HIGH ALERT | 300–500 | Double beep |
| CRITICAL | Above 500 | Rapid multiple beeps |

Confirm the exact comparison operators in the final firmware, especially at scores of 300 and 500, before treating the table as an exact software specification.

## Real-Time Visualization

The report describes a Python client that:

1. Reads serial data from the Arduino at 9600 baud.
2. Parses Sound, Hall, and Alcohol anomaly scores.
3. Stores recent values in rolling buffers.
4. Plots the score histories using Matplotlib.
5. Displays reference lines at 150, 300, and 500.

Update the serial port in the Python script for your computer; `COM5` is the example port shown in the report.

## Software and Tools

| Tool | Purpose |
|---|---|
| Arduino IDE 2.x | Firmware development and upload |
| Python 3.x | Serial data parsing and visualization |
| PySerial | USB serial communication |
| Matplotlib | Live score plotting |
| MATLAB (optional) | Alternative visualization / analysis mentioned in the report |

## Repository Layout

```text
.
├── README.md
├── .gitignore
├── firmware/
│   └── arduino/
├── python/
│   ├── requirements.txt
│   └── realtime_visualization.py
└── docs/
    └── images/
```

These are proposed locations for the source files. They may not all exist until firmware, Python code, and approved images are added.

## Setup Guide

### Arduino firmware

1. Open the Arduino sketch in Arduino IDE.
2. Confirm sensor pin assignments and buzzer wiring against the actual prototype.
3. Select **Arduino Uno** and the correct serial port.
4. Upload the sketch and open Serial Monitor to verify the output format.
5. Use 9600 baud if following the settings described in the report.

### Python visualization

Install the dependencies:

```bash
python -m pip install pyserial matplotlib
```

After the Python script is added, update its serial port (for example, `COM5` on Windows) and run it with Python.

## Testing Described in the Report

The report describes controlled tests with:
- Ambient sound followed by claps, loud speech, and tapping near the microphone.
- Ambient air followed by proximity of alcohol swabs or hand-sanitizer vapours to the MQ-3 sensor.
- Baseline magnetic conditions followed by permanent magnets placed at varying distances from the Hall sensor.

It reports that stable conditions remained below the 150 threshold and that introduced stimuli could elevate the score into higher alert levels. These are reported observations, not an independent replication or a guarantee of performance in other environments.

## Limitations and Safety

- This is an educational prototype, not a certified gas detector, breath-alcohol tester, industrial safety system, or medical device.
- MQ-3 response depends on calibration, warm-up, environment, and other conditions; do not interpret raw scores as a precise alcohol concentration.
- Test in a controlled, ventilated setting and follow the sensor manufacturer's safety guidance.
- The report does not fully specify the exact sample-window length, all weights, calibration procedure, or complete firmware. Verify and document these details before presenting the project as a reproducible build guide.
- Avoid claiming measured accuracy or false-positive rate unless supported by repeatable and documented test data.

## Credits

Based on the project report titled *Hardware-Level Anomaly Detection Using Signal Shape Analysis*.

This repository is a project portfolio entry and is not an official publication of any institution.

## License

No license has been selected yet. Until a license is added, reuse rights are not granted by this repository. Add a license only after confirming the rights to all included source code and project materials.
