# 고수들이 쓰는 Claude 고급 기법

> 출처: Anthropic 공식 cookbook, 해커톤 수상작 발표, Claude docs
> 일반 사용자와 고수를 가르는 기법들

---

## 1. Extended Thinking (가장 강력한 기법)

Claude가 응답하기 전에 **내부적으로 깊이 사고하는 시간**을 예산으로 할당하는 기능.
복잡한 추론, 수학, 전략 문제에서 품질이 극적으로 올라감.

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000  # 사고에 쓸 최대 토큰
    },
    messages=[{"role": "user", "content": "이 비즈니스 전략의 리스크를 분석해줘"}]
)

# 사고 과정과 최종 답변이 분리되어 반환됨
for block in response.content:
    if block.type == "thinking":
        print("사고 과정:", block.thinking)
    elif block.type == "text":
        print("최종 답변:", block.text)
```

**핵심:** budget_tokens를 높일수록 더 깊이 생각하지만 토큰 소모도 증가.
단순 작업엔 쓰지 말 것.

---

## 2. Assistant Turn 프리필링 (출력 형식 완전 제어)

Claude의 응답 시작 부분을 미리 채워서 **원하는 형식을 강제**하는 기법.
해커톤에서 JSON 파싱 오류를 없애는 데 자주 사용됨.

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "사용자 정보를 JSON으로 추출해줘: 홍길동, 30세, 개발자"},
        {"role": "assistant", "content": "{"}  # ← 응답 시작을 강제
    ]
)
# Claude는 반드시 { 로 시작하는 JSON을 완성함
# 응답: "name": "홍길동", "age": 30, "job": "개발자"}
```

**활용 패턴:**
```python
# XML 태그 강제
{"role": "assistant", "content": "<result>"}

# 특정 형식 강제
{"role": "assistant", "content": "## 분석 결과\n\n**문제:**"}

# 코드만 받기
{"role": "assistant", "content": "```python\n"}
```

---

## 3. XML 태그로 프롬프트 구조화

Claude는 XML 태그로 구조화된 프롬프트에 가장 잘 반응함.
긴 컨텍스트에서 특히 효과적.

```python
prompt = """
<role>당신은 시니어 코드 리뷰어입니다</role>

<context>
Python FastAPI 프로젝트, 팀 규모 5명, 프로덕션 코드
</context>

<code_to_review>
def get_user(id):
    return db.query(f"SELECT * FROM users WHERE id={id}")
</code_to_review>

<instructions>
- 보안 취약점만 지적
- 심각도: CRITICAL/HIGH/MED/LOW
- 수정 코드 포함
</instructions>
"""
```

**왜 효과적인가:**
- Claude 학습 데이터에 XML 구조가 많음
- 긴 프롬프트에서 각 섹션을 명확히 구분
- 지시사항이 섞이지 않아 할루시네이션 감소

---

## 4. 멀티 에이전트 오케스트레이션 패턴

해커톤 우승작의 공통점: Claude 하나가 아니라 **역할이 다른 여러 Claude**를 조율.

```
[오케스트레이터 Claude]
    ├── 계획 수립
    ├── 작업 분배
    └── 결과 통합
         ├── [전문가 Claude A] 코드 작성
         ├── [전문가 Claude B] 코드 리뷰
         └── [전문가 Claude C] 테스트 작성
```

```python
# 오케스트레이터 패턴
orchestrator_prompt = """
당신은 소프트웨어 팀 리드입니다.
아래 요구사항을 받아 다음 형식으로 작업을 분배하세요:

<output_format>
{
  "tasks": [
    {"agent": "coder", "instruction": "..."},
    {"agent": "reviewer", "instruction": "..."},
    {"agent": "tester", "instruction": "..."}
  ]
}
</output_format>
"""
```

**핵심 원칙:**
- 오케스트레이터는 계획만, 실행은 전문가 에이전트에게
- 각 에이전트의 시스템 프롬프트는 단일 역할에 집중
- 에이전트 간 결과물은 구조화된 형식(JSON/XML)으로 전달

---

## 5. 자기 비평 루프 (Self-Critique)

Claude에게 자신의 답변을 스스로 검토하고 개선하게 하는 패턴.
품질이 중요한 작업에서 단일 응답보다 훨씬 좋은 결과.

```python
# 1단계: 초안 생성
draft = client.messages.create(
    messages=[{"role": "user", "content": f"다음을 작성해줘: {task}"}]
)

# 2단계: 자기 비평
critique = client.messages.create(
    messages=[
        {"role": "user", "content": f"다음 글의 문제점을 찾아줘:\n\n{draft.content[0].text}"}
    ]
)

# 3단계: 개선
final = client.messages.create(
    messages=[
        {"role": "user", "content": f"""
원본: {draft.content[0].text}
문제점: {critique.content[0].text}

위 문제점을 반영해서 개선된 버전을 작성해줘.
"""}
    ]
)
```

---

## 6. 컨텍스트 윈도우를 데이터베이스처럼 활용

Claude의 긴 컨텍스트(200K 토큰)를 **인메모리 DB**처럼 쓰는 패턴.
해커톤에서 복잡한 상태 관리에 자주 활용됨.

```python
# 전체 코드베이스를 컨텍스트에 올리고 질의
system = """
당신은 아래 코드베이스 전체를 알고 있는 전문가입니다.
<codebase>
{전체_파일_내용}
</codebase>
"""

# 이후 질문들은 모두 전체 코드베이스를 참조
questions = [
    "어떤 함수가 가장 많이 호출되나요?",
    "순환 의존성이 있나요?",
    "테스트가 없는 함수 목록을 뽑아주세요"
]
```

**주의:** 입력 토큰이 매우 많아짐 → 반드시 캐싱(C1~C3 규칙) 적용

---

## 7. 스트리밍 + 조기 종료

응답을 스트리밍으로 받으면서 **원하는 정보가 나오면 즉시 중단**하는 기법.
불필요한 output 토큰을 줄이면서 UX도 개선.

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=2048,
    messages=[{"role": "user", "content": "긴 보고서 작성해줘"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)

        # 특정 섹션만 필요하면 그 다음에 중단
        if "</summary>" in text:
            stream.close()
            break
```

---

## 8. 프롬프트 압축 (메타 프롬프팅)

긴 프롬프트를 **Claude 자신에게 압축**시켜서 토큰을 줄이는 기법.

```python
# 1단계: 긴 프롬프트를 압축
compression_prompt = f"""
아래 지시사항을 의미 손실 없이 최대한 짧게 압축해줘.
원본 토큰의 20% 이내로.

<original>
{긴_시스템_프롬프트}
</original>

압축 결과만 출력. 설명 없이.
"""

compressed = client.messages.create(...)

# 2단계: 압축된 프롬프트로 실제 작업
```

---

## 핵심 요약

| 기법 | 효과 | 난이도 |
|------|------|--------|
| Extended Thinking | 추론 품질 극대화 | ★★★ |
| 프리필링 | 출력 형식 완전 제어 | ★★ |
| XML 구조화 | 할루시네이션 감소 | ★ |
| 멀티 에이전트 | 복잡한 작업 분해 | ★★★ |
| 자기 비평 루프 | 결과물 품질 향상 | ★★ |
| 컨텍스트 DB화 | 대규모 탐색 | ★★ |
| 스트리밍+조기종료 | output 토큰 절약 | ★★ |
| 프롬프트 압축 | input 토큰 절약 | ★★ |

**해커톤 우승 패턴의 공통점:**
1. 단일 Claude 호출이 아닌 **멀티 에이전트 파이프라인**
2. 구조화된 중간 결과물 (JSON/XML)로 에이전트 간 통신
3. Extended Thinking으로 핵심 의사결정 품질 확보
4. 프리필링으로 파싱 오류 원천 차단
