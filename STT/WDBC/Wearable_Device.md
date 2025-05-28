# Wearable Device에서 API를 받아와서 사용하는 방법을 정리함.

## 논점1. 갤럭시 워치만으로 API를 사용? vs 스마트폰에 어플까지 설치를 해야 API를 사용?

### 📱 스마트폰 사용 여부에 따른 기능 비교

| 기능 | 스마트폰 사용 시 | 스마트폰 없이 워치 단독 |
|------|------------------|-------------------------|
| 실시간 심박수 측정 | 가능 (Samsung Health 또는 Health Connect API) | 가능 (워치 앱에서 센서 직접 사용) |
| 수면 감지 및 분석 | 가능 (삼성 헬스 앱 연동 필요) | 기본적인 수면 기록은 가능하나 API 접근은 제한적 |
| 장기 데이터 저장/분석 | 가능 (삼성 헬스 앱에 기록됨) | 불가 (워치에 저장되나 외부 추출 어려움) |
| API를 통한 외부 활용 | 가능 (Health Connect API 등) | 불가 (단, 워치 앱에서 직접 서버 전송 구현 시 가능) |
| 외부 서버 전송 | 가능 (스마트폰 앱에서 처리) | 가능 (워치 앱에서 직접 HTTP 전송 구현 시) |
| 앱 설치 필요 여부 | 필요 (Samsung Health, Health Connect) | 필요 (워치 전용 자체 앱 개발 필요) |


- 갤럭시워치(Wear OS)에서 health connect 사용 시 : 기본적인 심박수나 수면 데이터를 가져올 수 있음

- 스마트폰(Android)에서 Samsung Health 어플 설치 시 : 그 외에 

    - 수면 단계 분석 (얕은 수면, 깊은 수면, 렘 수면 등), 심박수 변화 추이, 수면 중 코골이 감지, 수면 중 산소 포화도 측정  같은 내용들을 알 수 있음 -> 있으면 정확도가 높아지고 좋지만 추가적으로 어플을 깔아야하는 귀찮음이 있음 -> 워치에 있는 데이터만 가져와서 졸음 운전 판단에 사용하는 것이 가장 간단하고 합리적인 방법으로 예상됌.

*이제 갤럭시워치에서 health connect만 사용하여 API로 정보를 가져오는 방법을 찾아야함.*

---

## 논점2. 갤럭시 워치에서 API를 통해 정보를 가져오는 방법


### ⌚ 워치 단독으로 센서 데이터를 가져오는 방법 요약

| 항목 | 방법 | API 예시 | 비고 |
|------|------|----------|------|
| 심박수 | `SensorManager`로 실시간 수집 | `Sensor.TYPE_HEART_RATE` | 정확하고 실시간 가능 |
| 수면 추적 | 공식 API 미제공. 시간 기반 수면 추정 필요 | 직접 수면 시간 계산 로직 구현 필요 | 정확한 수면 단계는 어려움 |
| 서버 전송 | HTTP 라이브러리 사용 (`HttpURLConnection`, `OkHttp` 등) | `POST` 요청 구현 | 워치에 Wi-Fi/LTE 연결 필수 |
| BLE 송신 | GATT Server 구현 후 광고 | Android BLE API | 수신 측 장치 구현 필요 |
| 데이터 저장 | 내부 저장소 또는 `SharedPreferences` 사용 | `openFileOutput()` 등 | 임시 저장용도 (장기 저장 어려움) |



https://demat.tistory.com/77
갤럭시워치 api 가져오는거 관련해서 정리된 블로그. 여기 내용에서 참고하자면 


1. 삼성에서 공식 지원하는 SDK
    - https://developer.samsung.com/codelab
    - 대신 이 codelab이라는 공간 안에서는 모의 라이브러리만 사용할 수 있고(모의데이터를 통한 코드 실행), 진짜로 데이터를 사용하려면 삼성이랑 파트너쉽을 맺어야함 -> 좀 어려운 부분이라 다른 방법을 찾아야할 것 같음.

 
2. health platform api(https://developer.android.com/health-and-fitness/guides/health-services/health-platform?hl=ko)
    - 이 api는 현재 새로운 health connect라는게 생겨서 유지보수 중단되었다고한다
    - <img width="851" alt="image" src="https://github.com/user-attachments/assets/0b36f0b0-b194-42bd-9b9f-d71646890175" />


3. health connect(https://charlie-dev.tistory.com/14, https://developer.android.com/health-and-fitness/guides/health-connect?hl=ko)

    헬스커넥트는 health services api를 사용하는 wear os 에서 사용 불가하다

    헬스커넥트는 android 9 (api28)이상부터 적용가능하다
    
    구글 play 서비스가 설치되어야 하는 등 특정 호환성 요구사항이 있다(애플은 안된다~)
    
    헬스커넥트sdk 는 오직 코틀린으로만 사용가능하다
    
    첫 권한 요청 전의 최대 30일 전까지의 데이터만 읽는것이 가능하다
    
     
    
    Health Connect SDK 자체는 android 8 부터 지원되는데 얘를 쓸라면 꼭 있어야되는 헬스커넥트 앱이 android9 부터 지원되므로 사실상 health connect 는 android 9 부터 쓸수있다고 보면된다

4. Health Services on Wear OS (https://stackoverflow.com/questions/76710253/confusions-on-reading-health-data-from-wear-os-watch-on-android)

안드로이드 wear OS에서 health 데이터를 받아오는 방법을 물어보는 질문에 이렇게 답변이 달림

    Health Connect will allow you to read data from the Samsung Health app only if the Samsung Health app writes that data type to Health Connect. This guide can help you understand which situations Health Connect is best suited for.
    
    If you want to read data directly from the Galaxy Watch (and other Wear OS devices) while the exercise is in progress, you should consider using the Health Services for Wear OS API and create a Wear OS app, then connect that app to your mobile app. Samples are in the same repository.
    
    Now to get data from watch to mobile you can use:
    
    Wearable Data Layer API or
    Send data using the network.
    This link has details on both options: developer.android.com/training/wearables/data/data-layer

    (한글로)
    Health Connect를 사용하면 Samsung Health 앱에서 해당 데이터 유형을 Health Connect에 기록하는 경우에만 Samsung Health 앱에서 데이터를 읽을 수 있습니다. 이 가이드는 Health Connect가 어떤 상황에 가장 적합한지 이해하는 데 도움이 될 수 있습니다.

    운동 중에 Galaxy Watch(및 다른 Wear OS 기기)에서 직접 데이터를 읽으려면 Wear OS용 Health Services API를 사용하여 Wear OS 앱을 만든 후, 해당 앱을 모바일 앱에 연결하는 것을 고려해 보세요. 샘플은 동일한 저장소 에 있습니다 .
    
    이제 시계에서 모바일로 데이터를 가져오려면 다음을 사용할 수 있습니다.
    
    웨어러블 데이터 계층 API 또는
    네트워크를 사용하여 데이터를 전송합니다.
    이 링크에는 두 옵션에 대한 세부 정보가 있습니다: developer.android.com/training/wearables/data/data-layer

---

## 가능성이 있는 기술은 4번 : Health Services on Wear OS
다른 방법은 제휴(파트너쉽 체결), 서비스 중단, 별도의 스마트폰 어플리케이션 설치와 같은 문제점을 갖고 있음
이 중에서 워치에서 바로 데이터를 전송할 수 있는 방법인 4번을 채택하게 되었음
<img width="877" alt="스크린샷 2025-05-28 오후 9 20 05" src="https://github.com/user-attachments/assets/dc4f97fb-d4f8-4888-a8e6-d1654b18bb79" />


https://developer.android.com/health-and-fitness/guides/health-services?hl=ko

Google이 제공하는 `Health Services API`는 Wear OS 기반 스마트워치에서 **스마트폰 없이 건강 데이터를 직접 수집**할 수 있도록 설계된 공식 API입니다.

---

#### ✅ 기본 개요

- **지원 플랫폼**: Wear OS 3 이상
- **스마트폰 필요 여부**: ❌ 필요 없음
- **주요 목적**: 스마트워치 단독으로 센서 데이터를 수집하고 활용

---

#### 🔧 제공 주요 기능

| 기능 | 설명 |
|------|------|
| 실시간 심박수 측정 | 센서를 통해 실시간 스트리밍 가능 |
| 걸음 수, 이동 거리 | 자동 수집 가능 |
| 칼로리, 활동 시간 | 운동 기반 메트릭 제공 |
| 운동 감지/기록 | 운동 시작-중지 제어, 운동 유형 지정 가능 |
| 수면 데이터 | 수면 감지는 가능하지만 수면 단계 세부 정보는 제한됨 |
| Passive Monitoring | 백그라운드에서 자동 데이터 수집 가능 |
| Foreground Exercise | 운동 앱에서 실시간 데이터 수집 가능 |

---

#### 📦 주요 클라이언트 구성

- `HealthServices.getClient(context)` → Health Services 진입점
- `PassiveMonitoringClient` → 워치가 자동 감지한 건강 데이터를 지속 수신
- `ExerciseClient` → 사용자가 운동 시작 시 실시간으로 메트릭 수집

---

#### 🧩 예시 코드 스니펫

```kotlin
val healthClient = HealthServices.getClient(context)
val passiveClient = healthClient.passiveMonitoringClient

val config = PassiveMonitoringConfig(
    dataTypes = setOf(DataType.HEART_RATE_BPM)
)
passiveClient.setPassiveMonitoringConfig(config)
```


---



https://developer.android.com/training/building-wearables.html
각종 API 소개 및 UI 구성하는 법이 소개된 사이트

Wear OS by Google에서는 사용자가 연결 상태를 유지하고, 건강/피트니스 목표를 추적하고, 작업을 실행하며, 자신을 표현하는 데 도움이 되는 앱을 작성할 수 있음.

https://demat.tistory.com/77
갤럭시워치 api 가져오는거 관련해서 정리된 블로그


## 📌 참고 요약

- 워치 단독으로도 **심박수 측정 및 서버 전송**은 가능
- 수면 데이터는 **기초적인 시간 기록 수준까지만** 가능
- Health Connect 연동을 통해 스마트폰에서 **공식 API로 정밀 데이터 활용** 가능
- **워치 앱 개발 시 Wear OS SDK + Android Studio** 필요



