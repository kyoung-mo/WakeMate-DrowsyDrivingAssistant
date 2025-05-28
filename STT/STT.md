# 🧠 STT 관련 정리 문서

## 1. 🎯 프로젝트 배경

WakeMate 프로젝트에서 졸음운전 감지 후 음성 기반 상호작용을 위해 STT(Speech-to-Text)는 필수 요소입니다. 운전자의 음성을 인식하고 이를 텍스트로 변환하여 ChatGPT에 전달하는 과정을 구성해야 합니다.

---

## 2. 📌 주요 STT 처리 구조

```text
[카메라/센서] → [STT] → [ChatGPT API] → [TTS 출력] → [운전자 응답]
```

- STT는 명령어 혹은 자유로운 자연어 문장을 음성에서 추출해야 하며,
- 임베디드 환경에서는 속도, 정확도, 메모리 사용량이 중요합니다.

---

## 3. ✅ 주요 STT 방법 및 API

| 분류 | 방법 | 장점 | 단점 |
|------|------|------|------|
| **Google STT API** | 클라우드 기반 | 높은 정확도, 다양한 언어 지원 | 인터넷 필수, 개인정보 이슈 |
| **Naver Clova STT** | 클라우드 기반 (한국어 최적화) | 한국어 인식 우수, TTS 연동 용이 | 사용권 제한, 비용 공개 없음 |
| **Whisper (OpenAI)** | 로컬 or 서버 실행 가능 | 오픈소스, 언어 인식 뛰어남 | GPU 필요, 느린 속도 |
| **ESP BOX KWS** | 온디바이스 명령어 인식 | 초저전력, 빠름, 로컬 인식 | 단순 명령어(300~400개 제한) |
| **Coral TPU + KWS** | 최대 140개 명령어 인식 | Edge AI, 빠른 추론 | 모델 크기 6MB 제한, STT는 사실상 불가능 |

---

## 4. ❌ Coral TPU의 한계

- Coral TPU는 최대 **6MB TFLite 모델**만 실행 가능
- 일반 STT 모델(크기):
  - Whisper Tiny: 75MB
  - Whisper Base: 142MB
  - Whisper Large: ~1.5GB
- 결과적으로 **자유 발화 기반 STT는 불가능**
- **명령어 인식 수준의 KWS(Keyword Spotting)** 만 가능

---

## 5. ⚙️ 구조 설계 추천

### 옵션 A: 클라우드 기반
```text
[라즈베리파이 5 + USB 마이크] 
→ Google STT or Clova STT API 
→ ChatGPT (gpt-3.5/4)
→ gTTS/pyttsx3로 음성 출력
```

### 옵션 B: 로컬 + 서버 연동
```text
[라즈베리파이 5] → Whisper (서버/GPU 실행)
→ ChatGPT → pyttsx3 음성 출력
```

### 옵션 C: 완전 로컬 명령어 인식
```text
ESP32-S3 or Coral TPU → Keyword Spotting (e.g., “졸려”, “뉴스 알려줘”)
→ Raspberry Pi에 명령 전달
```

---

## 6. 🧩 기타 고려사항

- Whisper 같은 모델은 GPU 서버 필요 (ex: Jetson, GTX 1060 이상)
- STT 후 **명령 분류 → ChatGPT API 호출** 구조도 가능
- **네트워크 연결이 불안정한 차량 환경** 고려 시 **로컬 KWS + 클라우드 STT 하이브리드** 구성이 적절

---

## 7. 🔚 결론

- **Coral TPU는 STT 용도로 부적합** (KWS 수준만 가능)
- 실제 프로젝트에는 Google STT 또는 Whisper on GPU 추천
- 자율성 + 프라이버시 중시할 경우 Whisper 기반 서버 구성 검토
