---
description: Claude 작업 Runbook을 생성합니다. 사용법: /new-runbook <작업유형> <작업명>
---

`$ARGUMENTS`를 파싱하여 Claude 작업 Runbook을 생성합니다.
- 첫 번째 인자: 작업 유형 (code, doc, review, debug, research)
- 나머지 인자: 작업명

## 파일 생성 규칙
1. 파일명: `runbook-{유형}-{작업명}.md`
2. 저장 위치: 프로젝트 루트
3. `templates/runbook.md` 템플릿 기반

## 작성 시 필수 포함 사항
- **예상 토큰**: 작업 전 토큰 추정량
- **권장 모델**: Haiku/Sonnet/Opus 중 선택 이유
- **캐싱 전략**: 반복 작업 시 캐시 활용 방법
- **단계별 프롬프트**: 작업을 쪼개는 방법
- **TokenOps 기록**: 작업 후 로그 기록 형식
