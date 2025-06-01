# HyBud System Flow Overview

## Hardware Setup
| Board | Components | Interfaces |
|---|---|---|
| **Rasp1** (Sensing & Decision) | Raspberry Pi 5, Camera Module 3, CO₂ sensor, Galaxy Watch (Samsung Health), MCP2515 CAN transceiver | CAN (send) |
| **Rasp2** (Dialogue & Actuation) | Raspberry Pi 5, MCP2515 CAN transceiver, Speaker & Mic, Vehicle actuators (LED, motor, buzzer, etc.) | CAN (receive) |

## Continuous Drowsiness Detection (Rasp1)
1. **YOLOv8**: Detect closed eyes/head‑nodding in real time.  
2. **Samsung Health SDK**: Fetch previous night’s sleep metrics & live heart rate.  
3. **CO₂ sensor**: Monitor in‑cabin air quality.  
4. Fuse the three signals → determine *drowsy* / *normal* status → broadcast result via CAN.

## Branch 1 — Drowsy Detected
| Step | Rasp1 | Rasp2 |
|---|---|---|
| 1 | Send “DROWSY” CAN frame | Receive frame → enter **dialogue mode** |
| 2 | – | Send prompt to **GPT API** (“I’m tired, talk to me…”) |
| 3 | – | Convert GPT reply to speech with **TTS** → play via speaker |
| 4 | – | Loop: driver reply → **STT** → GPT → TTS until drowsiness relieved |

## Branch 2 — Normal Driving (Voice Actuator Control)
| Step | Rasp1 | Rasp2 |
|---|---|---|
| 1 | Capture driver voice → **STT** → text | – |
| 2 | Send text to **GPT API** | – |
| 3 | Map GPT text to CAN command (ID, DATA) → transmit | Receive CAN → drive actuators (e.g., turn on AC, open window) |
| 4 | – | Announce action via **TTS** (“Air‑con turned on.”) |

## Software Stack
- **Computer Vision**: YOLOv8 (Ultralytics) + Picamera2 / OpenCV  
- **Health Data**: Samsung Health REST API  
- **CAN I/O**: python‑can + MCP2515  
- **Conversation**: OpenAI GPT, Google Cloud Speech‑to‑Text & Text‑to‑Speech  

## Key Points
- Clear two‑board architecture: *Rasp1 = sensors & judgement,* *Rasp2 = interaction & control*  
- Two CAN message types only: **(1) Drowsiness alert**, **(2) Actuator command** → simple protocol.  
- Mode‑dependent behavior ensures focus on drowsiness mitigation while still allowing normal voice controls.
