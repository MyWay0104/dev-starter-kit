# dev-starter-kit

Claude Code로 새 프로젝트를 시작할 때 가져다 쓰는 스타터킷입니다. 특정 프레임워크의 코드는 없고, **어떤 스택에서든 그대로 쓰는 서브에이전트 · 커맨드 · 문서 양식**만 들어 있습니다(웹, FastAPI 같은 API 서버, CLI, 데이터 파이프라인, AI Agent).

## 들어 있는 것

```
.claude/
├── agents/
│   ├── prd-generator.md     PRD 초안 작성
│   ├── prd-validator.md     PRD 기술 검증 (고치지 않고 보고만)
│   ├── dev-planner.md       ROADMAP 작성 · 구조 변경
│   ├── code-reviewer.md     구현 직후 리뷰 (고치지 않고 보고만)
│   ├── code-refactorer.md   명시적으로 요청했을 때만 리팩터링
│   ├── e2e-verifier.md      실제 실행으로 검증 (고치지 않고 보고만)
│   └── domain-expert.md     분야를 인자로 받는 사실 · 코드 · 설계 검토
└── commands/
    └── update-mds.md        /update-mds — 작업 결과를 ROADMAP 등 문서에 반영
docs/
├── DEV-FLOW-REFERENCE.md      빈 저장소에서 배포까지의 개발 플로우 교본
├── ROADMAP-REFERENCE.md       로드맵 양식과 작업 유형별 완료 기준
├── LEANCANVAS-TEMPLATE.md     린 캔버스 양식 (PRD 앞 단계)
└── OVERNIGHT-RUN-TEMPLATE.md  자리를 비운 동안 여러 Task를 이어 돌릴 때의 실행 계획 양식
```

서브에이전트는 스택을 본문에 적어 두지 않습니다. 저장소의 `CLAUDE.md`와 매니페스트(`package.json`, `pyproject.toml` 등)에서 규칙과 검증 명령을 찾아 따릅니다. 그래서 **프로젝트의 `CLAUDE.md`에 검증 명령과 금지 사항을 적어 둘수록** 잘 동작합니다.

## 쓰는 법

### 새 프로젝트에 넣기

```bash
git clone https://github.com/MyWay0104/dev-starter-kit.git
cp -r dev-starter-kit/.claude dev-starter-kit/docs <새 프로젝트 경로>/
cat dev-starter-kit/.gitignore >> <새 프로젝트 경로>/.gitignore
```

이미 `.claude/`나 `docs/`가 있는 프로젝트라면 덮어쓰지 말고 필요한 파일만 골라 복사합니다.

### 모든 프로젝트에서 쓰기 (전역 설치)

```bash
cp dev-starter-kit/.claude/agents/*.md   ~/.claude/agents/
cp dev-starter-kit/.claude/commands/*.md ~/.claude/commands/
```

같은 이름의 에이전트 · 커맨드가 이미 있으면 덮어쓰게 되니 먼저 확인합니다. 에이전트 정의는 Claude Code를 다시 시작해야 목록에 나타납니다.

## 진행 순서

자세한 내용은 [`docs/DEV-FLOW-REFERENCE.md`](docs/DEV-FLOW-REFERENCE.md)에 있습니다. 급하면 0장 치트시트만 봅니다.

| 단계 | 하는 일 | 쓰는 것 |
| --- | --- | --- |
| 0 준비 | 저장소, 설정, MCP, `/init`으로 `CLAUDE.md` 초안 | — |
| 1 기획 | (아이디어가 흐릿하면) 린 캔버스 → PRD → 기술 검증 | `LEANCANVAS-TEMPLATE.md`, `prd-generator`, `prd-validator` |
| 2 계획 | PRD → `docs/ROADMAP.md` | `dev-planner`, `ROADMAP-REFERENCE.md` |
| 3 구현 | Task마다 탐색 → 계획 → 구현 → 검증 → 마감 | `code-reviewer`, `e2e-verifier`, `/update-mds` |
| 4 마무리 | 통합 검증, 배포, 배포 환경 재검증 | `e2e-verifier`, `OVERNIGHT-RUN-TEMPLATE.md` |

처음 시작할 때 쓸 수 있는 프롬프트:

```text
prd-generator 서브에이전트로 PRD 초안을 만들어줘.

[아이디어]
(무엇을 만들고 싶은지 3~5문장)

초안이 나오면 prd-validator로 기술 검증까지 이어서 받아줘.
```

```text
dev-planner 서브에이전트로 docs/ROADMAP.md를 작성해줘.
근거: docs/PRD.md, 양식: docs/ROADMAP-REFERENCE.md
```

## 알아 둘 것

- **이 킷에 없는 것**: 스택 전용 구현 에이전트, 훅, MCP 설정, `CLAUDE.md`. 프로젝트마다 다르므로 필요해질 때 추가합니다. 추가 시점과 구성 예시는 `DEV-FLOW-REFERENCE.md` 9장과 11장에 있습니다.
- **검토 · 검증 에이전트는 코드를 고치지 않습니다.** `code-reviewer`, `e2e-verifier`, `prd-validator`, `domain-expert`는 결과만 보고하고, 수정은 메인 대화에서 결정합니다.
- **에이전트끼리 서로 부르지 않습니다.** 구현 → 리뷰 → 검증의 연결은 메인 대화가 합니다.
- **`/update-mds`는 문서만 고칩니다.** 커밋과 태스크 상태 변경은 하지 않습니다.
- `e2e-verifier`의 웹 검증은 Playwright MCP가 연결돼 있어야 합니다. 라이브러리 문서 확인에는 `context7` 같은 문서 조회 MCP를 권합니다.
- 문서 양식의 `<꺾쇠>`와 `[대괄호]`는 프로젝트에 맞게 바꾸는 자리입니다.
