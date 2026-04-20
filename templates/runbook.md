# Runbook: {작업명}

> **분류**: {프롬프트 최적화 | 워크플로우 설정 | 에이전트 구성 | 토큰 관리}
> **영향 범위**: {로컬 설정 | 프로젝트 전체}
> **작성일**: {YYYY-MM-DD}
> **예상 소요 시간**: {N분}

---

## 사전 체크리스트

- [ ] 현재 토큰 예산 확인 (`budget.md`)
- [ ] 관련 설정 파일 백업
- [ ] 롤백 방법 확인
- [ ] 작업 목적 명확히 정의

---

## 환경 확인

```bash
# Claude Code 버전 확인
claude --version

# 현재 프로젝트 설정 확인
cat .claude/settings.json

# 토큰 사용 현황 확인
tail -20 token-log.md
```

---

## Step 1: 사전 상태 확인

```bash
# 현재 에이전트/명령어 목록 확인
ls .claude/commands/
ls agents/

# 현재 규칙 확인
ls rules/
```

---

## Step 2: {작업 내용}

```bash
{명령어 또는 설정 변경 내용}
```

또는

```json
// .claude/settings.json 변경사항
{
  "변경 키": "변경 값"
}
```

---

## Step 3: 완료 확인

```bash
{확인 명령어}
```

**성공 기준**:
- [ ] {조건 1}
- [ ] {조건 2}
- [ ] TokenOps 로그에 작업 기록 완료

---

## 롤백 절차

```bash
# 설정 파일 롤백
cp .claude/settings.json.bak .claude/settings.json

# 또는 git으로 복구
git checkout .claude/settings.json
```

---

## TokenOps 기록

이 작업 완료 후 `token-log.md`에 기록:

```markdown
| {날짜} | {작업명} | {입력 토큰} | {출력 토큰} | {총 토큰} | {목적} |
```
