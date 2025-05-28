
# Google Speech-to-Text 사용 가이드 (with Cloud Storage)

본 문서는 Google Cloud Speech-to-Text(STT) API를 활용하여 음성 파일을 텍스트로 변환하는 전체 과정을 정리한 문서입니다.  
녹음부터 변환, 업로드, 인식까지의 실습 절차를 단계별로 설명합니다.

---

## 📌 1. Google Cloud 준비

### ✅ 프로젝트 생성 및 API 활성화

1. [Google Cloud Console](https://console.cloud.google.com/) 접속
2. 새 프로젝트 생성
3. `Speech-to-Text API` 활성화
   - API 및 서비스 > 라이브러리 > Speech-to-Text 검색 후 활성화

---

## 📁 2. Cloud Storage(GCS) 버킷 구성

### ✅ 버킷 생성

1. Cloud Storage > 버킷 만들기
2. 이름: `bucket_hybud` (고유한 이름)
3. 리전: `asia-northeast3 (서울)` 추천
4. 스토리지 클래스: `Standard`
5. 계층적 네임스페이스 설정: ❌ 사용 안 함 (기본값 유지)

---

## 🎙️ 3. 음성 파일 준비 (.wav 권장)

### ✅ 녹음 방식

- **휴대폰 기본 녹음 앱** 사용
- 기본 포맷: `.m4a (AAC)`
- STT에서 정확도 향상을 위해 **`.wav (LINEAR16, 16000Hz, mono)` 변환 권장**

### ✅ 변환 명령어 (FFmpeg 사용)

```bash
ffmpeg -i input.m4a -acodec pcm_s16le -ac 1 -ar 16000 output.wav
```

- `-acodec pcm_s16le`: LINEAR16 형식
- `-ac 1`: 모노 채널
- `-ar 16000`: 16000Hz 샘플링

---

## ☁️ 4. 오디오 파일 Cloud Storage에 업로드

```bash
gsutil cp output.wav gs://bucket_hybud/audio-files/output.wav
```

---

## 🧠 5. Speech-to-Text 스크립트 작성

### ✅ 텍스트 변환 스크립트 생성 단계

1. Speech > 스크립트 작성 > 새 스크립트
2. **작업공간 선택**: `bucket_hybud`
3. **오디오 파일**: Cloud Storage 경로 지정
   - 예: `bucket_hybud/audio-files/output.wav`
4. **인코딩**: `LINEAR16`
5. **샘플링 레이트**: `16000`
6. **언어**: `Korean (ko-KR)`
7. **모델**: `default`
8. **리전**: `asia-northeast3` (서울)

제출 후 STT 변환이 자동 실행됩니다.

---

## 📄 6. 결과 확인 (.json)

1. Cloud Storage 내 `/transcripts/` 폴더에 `.json` 결과 생성됨
2. 결과 파일 다운로드 또는 Cloud Shell에서 확인:

```bash
gsutil cat "gs://bucket_hybud/transcripts/output.wav-[timestamp].json"
```

### 예시 결과

```json
{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "안녕하세요 마이크 테스트입니다",
          "confidence": 0.96
        }
      ]
    }
  ]
}
```

---

## 🛠️ 7. 오류 발생 시 체크리스트

- `.m4a` → `.wav` 변환 여부
- 인코딩 유형과 샘플링 레이트 정확히 입력했는지
- GCS 경로 오탈자 없는지
- 언어 설정이 `ko-KR`인지 확인

---

## 📌 기타 참고

- FFmpeg 설치: https://ffmpeg.org/
- Google STT 문서: https://cloud.google.com/speech-to-text/docs/

---

> 이 문서는 졸음운전 감지 기반 프로젝트에서 음성 입력을 텍스트로 처리하기 위한 절차로 작성되었습니다.


![image](https://github.com/user-attachments/assets/787801a1-f428-48bc-9b15-fc330cd86d98)

![image](https://github.com/user-attachments/assets/01e7e1dd-a7bb-4204-b26f-f422bcc4a1c4)

![image](https://github.com/user-attachments/assets/1cc13799-abc8-491e-b16c-2edae2e65ae4)

![image](https://github.com/user-attachments/assets/642762f4-1914-4006-a028-9ae9c478888e)

![image](https://github.com/user-attachments/assets/a918bb59-4e9b-4255-94b0-732ac4bdd0a9)

![image](https://github.com/user-attachments/assets/d97a637c-c89b-479c-a718-00c7390d674b)

![image](https://github.com/user-attachments/assets/8c84e8e3-26f4-4458-b6d5-9bdfe4203c1b)

![image](https://github.com/user-attachments/assets/4bdaa790-2e78-427e-88b0-f13f207d95e2)

![image](https://github.com/user-attachments/assets/9744af64-7df2-4b35-ba38-ebfbf2a82591)

![image](https://github.com/user-attachments/assets/7fbbbbad-2df6-4e3e-bfe0-e8a3491ba2f1)

![image](https://github.com/user-attachments/assets/30cb24a8-c6c4-4896-8b79-07a79cc87919)

![image](https://github.com/user-attachments/assets/385c3a50-e6b2-4751-ad78-6aa39259bf92)

일단 실패 ,,

+ )

![image](https://github.com/user-attachments/assets/b00a2e70-2724-4806-9099-344f5a5d3d62)

.m4a 형식은 텍스트 인식 불가. 

.m4a -> .wav 변환 후 인식 잘되는것 확인
