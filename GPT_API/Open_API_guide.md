# OpenAI API Key 발급 & 사용 가이드
> 졸음운전 방지 프로젝트 — ChatGPT 모듈 준비용  
> (v 2025 ‑ 05 ‑ 28)

---

## 1. API Key 발급 절차

| 단계 | 화면 경로 | 설명 |
|------|-----------|------|
| 1 | **platform.openai.com** 로그인 | 회원 가입 또는 기존 계정 로그인 |
| 2 | **Billing ▸ Payment methods** | 무료 크레딧 이후 사용하려면 카드 등록 |
| 3 | **右上 사용자 아이콘 ▸ “API keys”** | 키 관리 페이지 진입 |
| 4 | **Create new secret key** 버튼 | 별칭 입력 → **Create key** |
| 5 | **키 복사(sk‑…) & 보관** | 한 번만 노출됨 – 바로 `.env` 등에 저장 |

---

## 2. 안전 보관 수칙 🔐
| 항목 | 권장 방법 |
|------|-----------|
| **저장** | 프로젝트 루트에 `.env` → `OPENAI_API_KEY=sk-…` |
| **코드 공개** | `.gitignore`에 `.env` 추가 – GitHub에 노출 금지 |
| **키 분리** | 개발 / 운영 / 테스트 환경별로 개별 키 발급 |
| **회수(Revoke)** | 사용 안 하면 “⋯ ▸ Revoke” 로 즉시 폐기 |

---

## 3. 환경 변수 로드 예시 (Python)

```python
import os
from openai import OpenAI

client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

resp = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role":"user","content":"키 설정 완료?"}]
)
print(resp.choices[0].message.content)
```

---

## 4. VS Code 실행 팁

1. **Python 확장** 설치 후, `.venv` 가상환경 활성화  
2. **dotenv** 확장으로 `.env` 하이라이트  
3. `Ctrl+Shift+D` ▶ “Run & Debug” → 환경 변수가 자동 주입되면 정상

---


## 5. 가상환경(venv) 설정
```bash
# Windows (PowerShell)
python -m venv .venv
& .\.venv\Scripts\Activate

# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate
```
- 활성화되면 프롬프트 앞에 `(.venv)` 표시

## 가상환경이 켜지지 않는다면?
① VS Code PowerShell 터미널에서
Set-ExecutionPolicy Bypass -Scope Process -Force

② 가상환경 활성화
& .\.venv\Scripts\Activate

## 6. 의존성 설치
```bash
pip install openai python-dotenv
```
> 추가로 TTS 테스트용 `sounddevice`, `pydub`, `tiktoken` 설치 가능

## 7. 환경 변수 파일 생성 (`.env`)
프로젝트 루트에 `.env` 파일을 만들고 다음 내용 입력:
```env
OPENAI_API_KEY=sk-********************************
```
- `.gitignore`에 `.env` 추가하여 비공개 유지

## 8. 테스트 스크립트 작성 (`test.py`)
```python
import os
from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

load_dotenv()
api_key = os.getenv("OPENAI_API_KEY")
if not api_key:
    raise RuntimeError(".env에 OPENAI_API_KEY가 없습니다!")

client = OpenAI(api_key=api_key)

def main():
    try:
        resp = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role":"system","content":"You are a friendly assistant."},
                {"role":"user","content":"ChatGPT API 테스트입니다."}
            ],
            max_tokens=50,
            temperature=0.5
        )
        print("✅ 응답:", resp.choices[0].message.content.strip())
    except OpenAIError as e:
        print("❌ OpenAIError:", e)

if __name__ == "__main__":
    main()
```

## 9. 실행 및 결과 확인
```bash
python test.py
```
정상 실행 시:
```
✅ 응답: 테스트가 잘 되었습니다!
```
파이썬 코드 실행하면 이런식으로 답변이 뜸
---
![image](https://github.com/user-attachments/assets/ee92caa5-c88d-4b99-a1d9-8cee246ac145)
---
124토큰이 사용된 것을 확인가능
---
![image](https://github.com/user-attachments/assets/6192a875-02df-4b25-a667-e9c0f60546d9)


## 10. 주요 오류 및 해결
| 오류 코드 | 원인 | 해결 방법 |
|-----------|------|-----------|
| `401 Unauthorized` | 잘못된 API 키 | `.env` 키 확인 및 재발급 |
| `429 Insufficient quota` | 예산/한도 초과 | Dashboard → Usage → Budget 상향 |
| `Network Error` | 인터넷 연결 문제 | 연결 확인 / VPN/프록시 점검 |

