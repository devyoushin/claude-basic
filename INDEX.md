# Claude 학습 공간

> 목적: Claude를 효율적으로 사용하고 토큰을 FinOps 방식으로 관리 (TokenOps)

---

## 폴더 구조

```
claude-practice/
├── CLAUDE.md                  ← 프로젝트 컨텍스트 + TokenOps 규칙 (자동 로드)
├── INDEX.md                   ← 지금 이 파일
├── claude-guide.md            ← 전체 개념 가이드
├── token-comparison.md        ← 질문 방식별 토큰 비교 예시
├── token-estimation.md        ← 토큰 수 예측 방법
├── advanced-techniques.md     ← 고수/해커톤 우승자 기법
├── tokenops/                  ← TokenOps 시스템
│   ├── log.md                 ← 세션별 상세 로그
│   ├── dashboard.md           ← 누적 집계 대시보드
│   └── budget.md              ← 예산 및 최적화 목표
└── rules/                     ← 카테고리별 규칙
    ├── README.md              ← 전체 규칙 요약 인덱스
    ├── token-rules.md         ← T1~T5: 토큰 절약
    ├── prompt-rules.md        ← P1~P5: 프롬프트 작성
    ├── agent-rules.md         ← A1~A5: Agent 활용
    ├── caching-rules.md       ← C1~C5: 프롬프트 캐싱
    ├── workflow-rules.md      ← W1~W5: 작업 유형별 순서
    └── mistakes.md            ← M1~M7: 흔한 실수
```

---

## 문서 역할

| 문서 | 역할 | 언제 열어볼까 |
|------|------|------------|
| `CLAUDE.md` | TokenOps 규칙 적용, 응답 동작 제어 | 규칙 변경 시 |
| `claude-guide.md` | 개념 학습용 | 개념이 헷갈릴 때 |
| `token-comparison.md` | 질문 방식 비교 예시 | 토큰 차이 체감할 때 |
| `token-estimation.md` | 토큰 수 예측 방법 | 보내기 전 토큰 가늠할 때 |
| `advanced-techniques.md` | 고급 기법 8가지 | 품질/효율 한 단계 올리고 싶을 때 |
| `tokenops/log.md` | 세션별 토큰 상세 기록 | 매 작업 후 기록 |
| `tokenops/dashboard.md` | 누적 현황 한눈에 | 주간/월간 리뷰 시 |
| `tokenops/budget.md` | 예산 설정 및 최적화 목표 | 최적화 방향 검토 시 |
| `rules/README.md` | 규칙 빠른 참조 | 규칙 찾을 때 |

---

## TokenOps 현황 요약

> 상세 내용은 `tokenops/dashboard.md` 참조

| 항목 | 값 |
|------|-----|
| 총 세션 수 | 10 |
| 총 추정 비용 | ~$0.256 |
| 이번 달 예산 소진율 | 1.7% ($15 기준) |
| 주 사용 모델 | Sonnet 4.6 |
| 캐시 절약률 | 0% (미적용) |
