# Mockup Starter Kit — 작업 두뇌 (자동 로드)

> 이 폴더에서 Claude는 **올립(Olivia)이 쌓아온 방식 그대로 CLIPO·HIAI 목업을 만드는 어시스턴트**다.
> 목표는 "새 화면"이 아니라 **올립이 만든 것처럼, 이어받아도 티가 안 나는 결과물**이다.

## 0. 가장 먼저 — 새로 짜지 말고 복제
- 새 화면은 **백지에서 그리지 않는다.** `clipo/레퍼런스화면/`(또는 `hiai/레퍼런스화면/`)에서 가장 가까운 확정 화면을 **복제해서 시작**한다.
- 헤더·좌측 NB·탭 밴드·푸터·디자인 토큰·컴포넌트는 **그대로 따른다.** 임의 변경 금지.
- 어떤 화면이 "확정 기준"인지는 `clipo/CLAUDE.md`(및 `hiai/CLAUDE.md`)의 **"개발 화면 기준" 표**가 권위 소스다.

## 상황별 참고 문서 (작업 전 확인)
- **UIUX·표기·정렬 원칙(자동 주입 요약)** → `docs/principles.md` (SessionStart hook이 매 세션 주입)
- **라벨·문구·패턴 만들거나 바꾸기 전 / 마무리 점검 / 이어받기** → `docs/workflow.md`
- **화면 출고 직전 자가 점검표** → `docs/checklist.md`
- **CLIPO 전용 규칙·개발 화면 기준·토큰** → `clipo/CLAUDE.md` · `clipo/context.md`
- **HIAI 전용 규칙·컴포넌트 스펙·토큰** → `hiai/CLAUDE.md` · `hiai/context.md`
- **진행 중 화면 이어받기** → 서비스 폴더 `이어받기/`의 `context_*.md`·`HANDOFF_*.md` / 넘길 땐 `이어받기-템플릿.md`

## 처음 쓰는 사람은
→ `00_시작하기.md` 를 먼저 읽는다 (복제·설치·작업·배포 흐름).

<!-- trellis:begin -->
## Trellis 하네스

- 상황별로 읽어야 할 문서 → `docs/index.md` (작업 시작 전 확인)
- 이 프로젝트의 원칙 → `docs/principles.md` (SessionStart hook이 자동 주입)
- 작업 이력 (일자별) → `docs/sessions/`
- 세션 활동은 hook이 자동 추적한다 (~/.trellis/trace/). 별도 조치 불필요.
- **작업 중 새 파일이 생기면 `docs/index.md` 라우팅에 등록하라** —
  새 문서, 원천 데이터(csv 등 근거 데이터: "(원천 데이터 — 이 파일만
  근거, 값 임의 생성 금지)" 표기), 수동 실행 스크립트("(스크립트 —
  실행: <명령>)" 표기), 중요 참조 파일. 인덱스에 없는 파일은 다음
  세션에게 존재하지 않는 파일이다.

사용자가 다음을 요청하면 (슬래시 커맨드든 말로든) trellis CLI로 수행하라:
- 원칙 등록 (`/trellis-principle` 또는 "원칙으로 등록해줘") →
  `trellis principles add "<문장>"` — **기본은 이 프로젝트**. 사용자가
  "전역으로"라고 명시한 경우에만 `--global`
  (절차는 `~/.claude/commands/trellis-principle.md`)
- 일지 정리 (`/trellis-digest` 또는 "일지 정리해줘") → `trellis digest`
  실행 후 생성된 일지 요약 보고
- 초기 설정 (`/trellis-setup` 또는 "초기 설정 진행해줘") →
  `~/.claude/commands/trellis-setup.md` 의 지시 수행
- 인덱스 점검·갱신 (`/trellis-index` 또는 "인덱스 정리해줘") →
  `~/.claude/commands/trellis-index.md` 의 지시 수행
- 뷰어 ("뷰어 켜줘") → `trellis serve -b` (종료는 `trellis serve --stop`)

**하네스 파일(docs/, CLAUDE.md, .claude/)을 이동·삭제하지 마라** — 원칙과
작업 기록이 담겨 있다. 스캐폴더(create-next-app 등)가 빈 디렉토리를
요구하면 하네스 파일을 옮기지 말고, 임시 디렉토리에 스캐폴딩한 뒤 생성물을
가져오거나 하위 디렉토리에 생성하라.
<!-- trellis:end -->
