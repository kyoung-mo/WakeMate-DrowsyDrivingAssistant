# 🚗 WakeMate: HyBud - Interactive Driver Care System

## 🧠 Project Overview

**HyBud** is a combination of "Hyundai" and "Buddy", designed to act as a friendly interactive AI system that assists drowsy drivers by engaging in conversation and helping them stay alert.

- Detects driver drowsiness in real time using on-device AI and sensor data  
- Uses the **ChatGPT API** to naturally converse with the driver and boost attentiveness  
- Integrates **wearable devices** and both in-vehicle and environmental data (CO₂, temperature, camera, etc.) to offer a personalized driving experience

---

## 🎯 Objectives

- Prevent accidents by detecting drowsy driving and initiating **voice-based conversation**
- Construct a seamless AI pipeline: **Drowsiness Detection → STT → GPT Response → TTS**
- Provide an active safety system to support driver alertness and safety

---

## 🔎 Background

- Drowsy driving has a **higher fatality rate than drunk driving**  
- Police statistics (2019–2023): 10,790 drowsy driving accidents, 317 deaths  
- Highlights the need for **real-time detection and proactive intervention systems**
- Implements ChatGPT API and voice interfaces to deliver **personalized safety feedback**

---

## ⚙️ System Architecture

```
[Wearable Device / Camera] 
       ↓
[Raspberry Pi Inference Module] 
       ↓
[ChatGPT API ↔ Voice I/O]
       ↓
[Speaker and Driver Feedback]
```

- **WD (Wearable Device)**: Provides sleep duration, heart rate data  
- **Built-in Camera**: Detects drowsiness via facial recognition (EAR algorithm, dlib, opencv or AI + Hailo)  
- **Raspberry Pi**: Integrates sensor data → detects drowsiness → requests GPT response → converts to voice output  
- **STT**: Google STT API or Whisper (local model)

---

## 🛠️ Technologies & Devices

### ▪ Software (SW)

- Speech recognition: Google STT API, Whisper (Transformer-based)  
- Natural language processing: OpenAI ChatGPT API  
- Text-to-speech: gTTS or pyttsx3  
- Python Libraries: SpeechRecognition, dlib, opencv  
- Optimization: Caching, text embeddings

### ▪ Hardware (HW)

- Raspberry Pi 5 + Camera Module 3  
- CO₂ sensor, temperature/humidity sensor, microphone, speaker  
- Coral EdgeTPU (optional, for TFLite model acceleration)  
- Hailo-8 (optional, for fast real-time inference)

---

## 🚧 Development Strategy & Challenges

- Initial STT will be based on Google API; fallback to **Whisper local model** for improved speed/accuracy  
- Use **embedding + caching** to reduce GPT API costs and latency  
- Leverage **Hailo/Coral** hardware for real-time inference optimization

---

## 📌 Expected Outcomes

- Provide **personalized feedback** based on sleep, mood, and health tracking  
- Promote **safe driving and driver well-being** through interactive assistance  
- Potential for **real-world automotive applications** and smart mobility services
