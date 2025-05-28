# ✅ 8.4 vLLM 기반 LLM 서빙 실습
> **지금까지 배운 최적화 이론들이 실제로 어떻게 적용되는지**를
**vLLM이라는 서빙 엔진을 사용해 실습**
---

## 🧠 vLLM이란?

> **빠르고 효율적인 LLM 추론을 위한 오픈소스 서빙 프레임워크**

vLLM은 다음의 주요 기술들을 내장하고 있습니다:

* ✅ **PagedAttention** (효율적인 KV 캐시 관리)
* ✅ **Continuous Batching** (실시간 배치 최적화)
* ✅ **CUDA 커널 최적화** (FlashAttention 등 사용)
* ✅ OpenAI API와 호환 가능

---

## 💻 실습 흐름 요약

### 1️⃣ 모델 다운로드

```bash
huggingface-cli download shangrila/yi-ko-6b-text2sql --local-dir ./model --local-dir-use-symlinks False
```

* Hugging Face 모델을 미리 다운로드 받아 로컬에 저장

---

### 2️⃣ vLLM 서버 실행

```bash
python -m vllm.entrypoints.openai.api_server \
  --model ./model \
  --tokenizer ./model \
  --port 8888 \
  --host 0.0.0.0
```

* OpenAI API 포맷으로 REST API 서버 실행
* 원하는 모델로 vLLM을 돌릴 수 있음

✔️ **nohup**을 사용하면 백그라운드 실행도 가능

---

### 3️⃣ API 상태 확인

```bash
curl http://localhost:8888/v1/models
```

* 모델 로딩 상태 확인
* 출력 예:

  ```json
  {
    "data": [{"id": "shangrila/yi-ko-6b-text2sql", ...}]
  }
  ```

---

### 4️⃣ API 요청 (OpenAI 형식과 동일)

```bash
curl http://localhost:8888/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
        "prompt": "SELECT name FROM students WHERE",
        "max_tokens": 64,
        "temperature": 0.7
      }'
```

* `prompt`, `max_tokens`, `temperature` 등 설정 가능
* 응답은 생성된 텍스트와 함께 돌아옴

---

### 5️⃣ Python 코드로도 요청 가능 (openai 패키지 활용)

```python
from openai import OpenAI
client = OpenAI(api_key="not-needed", base_url="http://localhost:8888/v1")

res = client.completions.create(
    prompt="SELECT name FROM students WHERE",
    model="shangrila/yi-ko-6b-text2sql",
    max_tokens=64,
    temperature=0.7
)

print(res.choices[0].text)
```

* `openai` 라이브러리 그대로 사용 가능
* **vLLM은 OpenAI API 포맷을 그대로 지원**하기 때문

---

## 🧩 요약 정리

| 항목    | 설명                                 |
| ----- | ---------------------------------- |
| 목적    | 이론으로 배운 서빙 최적화 기법을 실제로 테스트         |
| 사용 도구 | vLLM + Hugging Face 모델             |
| 장점    | 빠르고 유연한 LLM 서빙 환경 구축 가능            |
| 특징    | OpenAI API 포맷 그대로 사용 가능 (실전 투입 용이) |

