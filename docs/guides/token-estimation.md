# 토큰 수 예측하는 법

> 작성일: 2026-04-16
> 목적: 보내기 전에 토큰을 얼마나 쓸지 감 잡는 방법

---

## 방법 1. 글자 수로 어림 계산 (가장 빠름)

언어별로 글자당 토큰 비율이 다릅니다.

| 언어 | 대략적 비율 | 예시 |
|------|-----------|------|
| 영어 | 글자 4개 = 토큰 1개 | "Hello world" (11자) → ~3 토큰 |
| 한국어 | 글자 1~2개 = 토큰 1개 | "안녕하세요" (5자) → ~5 토큰 |
| 코드 | 글자 3~4개 = 토큰 1개 | `def add(a, b):` (15자) → ~5 토큰 |

**빠른 암산법:**
```
영어 텍스트 → 단어 수 × 1.3
한국어 텍스트 → 글자 수 × 0.8
파일 크기 → bytes ÷ 4 (영어 기준 rough estimate)
```

**실전 감각:**
```
짧은 질문 한 줄        → 10~30 토큰
문단 하나 (200자)      → 한국어 ~160 토큰
A4 한 장 분량          → ~800~1,200 토큰
코드 100줄             → ~500~800 토큰
이 문서 전체           → ~1,200 토큰
```

---

## 방법 2. Anthropic 토큰 카운팅 API (정확)

보내기 전에 정확한 input 토큰 수를 미리 확인할 수 있습니다.

```python
import anthropic

client = anthropic.Anthropic()

# 실제 전송 없이 토큰 수만 계산
response = client.messages.count_tokens(
    model="claude-sonnet-4-6",
    system="당신은 Python 전문가입니다.",
    messages=[
        {"role": "user", "content": "퀵소트 알고리즘을 구현해줘"}
    ]
)

print(f"예상 input 토큰: {response.input_tokens}")
# → 예상 input 토큰: 31
```

**output 토큰은 미리 알 수 없습니다.** 응답을 받아봐야 알 수 있어요.
대신 `max_tokens`로 최대치를 제한할 수 있습니다.

---

## 방법 3. 실제 사용 후 확인 (사후 측정)

### API 응답에서 확인

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "안녕하세요"}]
)

print(f"Input:  {response.usage.input_tokens}")
print(f"Output: {response.usage.output_tokens}")
# 캐시 사용 시
print(f"Cache 생성: {response.usage.cache_creation_input_tokens}")
print(f"Cache 읽기: {response.usage.cache_read_input_tokens}")
```

### Claude Code에서 확인

```
/cost       # 현재 세션 전체 토큰 + 비용
```

---

## 방법 4. 파일 크기로 빠르게 추정

```bash
# 파일 크기 확인
wc -c 파일명.md        # bytes
wc -w 파일명.md        # 단어 수 (영어 기준)
wc -m 파일명.md        # 글자 수
```

**추정 공식:**
```
한국어 md 파일: 글자 수 × 0.8 ≈ 토큰 수
영어 md 파일:  bytes ÷ 4      ≈ 토큰 수
```

---

## 방법 5. 감 익히기 — 실제 예시로 보정

아래 예시를 기준점으로 외워두면 빠르게 어림할 수 있습니다.

```
"안녕하세요"                     → ~5 토큰
"Python에서 리스트란 무엇인가요?" → ~20 토큰
트위터 한 글 (140자)              → ~110 토큰
이메일 한 통                      → ~200~400 토큰
이 파일(token-estimation.md)     → ~900 토큰
claude-guide.md                  → ~1,800 토큰
코드 파일 200줄                   → ~1,000~1,500 토큰
```

---

## 핵심 정리

| 상황 | 추천 방법 |
|------|---------|
| 보내기 전 대략 파악 | 방법 1 (글자 수 어림) |
| 보내기 전 정확히 알고 싶다 | 방법 2 (count_tokens API) |
| 보낸 후 정확한 값 확인 | 방법 3 (usage 확인) |
| Claude Code 세션 전체 확인 | `/cost` 명령 |

**output 토큰은 예측 불가** — 형식 지정(T1 규칙)으로 최대치를 제어하는 게 최선.
