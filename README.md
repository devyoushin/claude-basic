# claude-basic

Claude 사용법과 토큰 관리를 정리하는 개인 학습/운영 공간입니다.

핵심 흐름은 **프롬프트 설계 → 실행 → 토큰 기록 → 개선**입니다. Claude 활용 패턴과 TokenOps 원칙을 함께 관리합니다.

## 빠른 시작

- `CLAUDE.md` — Claude/Codex가 함께 참고할 프로젝트 컨텍스트와 TokenOps 규칙 원본
- `AGENTS.md -> CLAUDE.md` — Codex/agent 진입점 링크
- `INDEX.md` — 전체 문서 목차
- `docs/guides/claude-guide.md` — Claude 핵심 개념과 활용 흐름

## 폴더 구조

```text
claude-basic/
├── README.md
├── INDEX.md
├── CLAUDE.md
├── AGENTS.md -> CLAUDE.md
├── docs/
│   ├── guides/     # 학습/참고 문서
│   ├── rules/      # 프롬프트/토큰/워크플로 규칙
│   ├── templates/  # 문서/런북 템플릿
│   └── agents/     # 역할별 에이전트 프롬프트
└── ops/            # TokenOps 로그, 대시보드, 예산
```

## 문서 위치

- `docs/guides/` — Claude 가이드, 토큰 비교, 토큰 예측, 고급 활용 기법
- `docs/rules/` — 토큰 절약, 프롬프트 작성, 에이전트, 캐싱, 워크플로, 실수 방지 규칙
- `docs/templates/` — 서비스 문서, 런북, 이슈 보고서 템플릿
- `docs/agents/` — 문서 작성, 프롬프트 코칭, TokenOps 분석, 워크플로 설계 프롬프트
- `ops/` — 세션 로그, 누적 대시보드, 예산 관리
