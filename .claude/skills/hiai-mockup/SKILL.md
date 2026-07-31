---
name: hiai-mockup
description: Use this skill whenever the user is creating, updating, or previewing HiAI (하이러닝 AI 서·논술형 평가 서비스) UI mockups. Triggers include — working in `G:\내 드라이브\Claude\hiai_mockup\`, referencing HiAI screens (선생님홈, 평가설계, AI채점, 평가리포트, 과제물 관리 등), asking to mockup a HiAI screen, mentioning the HiAI Figma files (`vOYXokKNMGec80BpDbIiJI`, `MM23uA7pDEmFeMKGeFVJpB`, `344b7XVs8E9KaFBhgAEhtW`, `l6ucsDgFo0Y4uOB2eIBrMu`), or any request to open/preview an existing HiAI mockup.
---

# HiAI Mockup Skill

HiAI는 교사용 서·논술형 평가 SaaS (평가 설계 → 과제물 수집 → AI 채점 → 리포트). 이 스킬은 네가 이 프로젝트에서 목업을 만들거나 기존 목업을 갱신·미리보기할 때의 **작업 순서와 원칙**을 담고 있다.

## Authoritative sources (항상 이 순서로 참조)

1. **`G:\내 드라이브\Claude\hiai_mockup\CLAUDE.md`** — 작업 규칙, GNB/파비콘/다이얼로그/토스트/버튼/인풋/뱃지 스펙, 디자인 토큰 요약
2. **`G:\내 드라이브\Claude\hiai_mockup\context.md`** — 컬러 팔레트 전체, 시멘틱 토큰, 타이포그래피, 컴포넌트 스펙, **스크린 인덱스 (피그마 파일키·노드 ID)**
3. **Figma MCP (`get_design_context`)** — 개별 컴포넌트의 실측 토큰 값을 구현 직전에 라이브 호출

스킬 본문에 디자인 수치를 복사하지 않는다. 바뀌면 stale되기 때문에, 값이 필요할 때마다 위 세 소스를 읽는다.

## 고정 Figma 파일키

| 용도 | 파일키 |
|------|--------|
| 디자인 시스템 (컬러·컴포넌트·로고) | `vOYXokKNMGec80BpDbIiJI` |
| **메인 디자인 (2026 현행)** | **`MM23uA7pDEmFeMKGeFVJpB`** |
| 구버전 (v1.0) | `344b7XVs8E9KaFBhgAEhtW` |
| 스크린맵 | `l6ucsDgFo0Y4uOB2eIBrMu` |

개별 화면의 노드 ID는 `context.md` 스크린 인덱스 표에 있다. 없는 화면을 작업하게 되면 스크린맵 파일(`l6ucsDgFo0Y4uOB2eIBrMu`)을 조회해 확보한다.

## 세션 시작 시 (자동)

1. `preview_start("hiai-mockup")` — serverId 즉시 확보. 서버 설정 디버깅 금지 (고정 인프라: `C:\hiai_serve\serve.bat` · 포트 3502)
2. `output/` 최근 파일 리스트 → 사용자에게 미리보기 URL 안내
3. 이미 실행 중이면 그대로 재사용

## 목업 생성 워크플로우

사용자가 "X 화면 목업 만들어줘" 또는 캡처+지침을 주면 **이 순서로**:

### 1. 스크린 찾기
- 요청 화면명을 `context.md` 스크린 인덱스에서 찾아 `fileKey` + `nodeId` 확보
- 없는 경우: 스크린맵(`l6ucsDgFo0Y4uOB2eIBrMu`)에서 조회하거나 사용자에게 확인

### 2. 디자인 컨텍스트 라이브 호출 (필수)
구현 시작 **전에** 대상 노드에 대해:
```
get_design_context(fileKey=<해당 파일키>, nodeId=<해당 노드 ID>)
```
- 컬러·radius·spacing·font-size 등 값은 스크린샷 육안 비교 금지. Figma 소스만 신뢰
- 같은 화면에서 재사용되는 공통 컴포넌트(버튼, 뱃지, 테이블 등)는 이미 `CLAUDE.md`에 스펙 있음 — Figma 재호출은 **새 컴포넌트나 애매한 값 확인 시에만**

### 3. 실제 서비스처럼 조립
사용자가 제공한 개별 Figma 화면들을 **그대로 화면당 한 덩어리로 붙이지 말고**, 실제 서비스 동선대로 구성:

- **GNB 고정** — `CLAUDE.md`의 로고 3이미지 구성 + 중앙 메뉴 + 우측 유저명. 내용 수정 금지
- **탭/네비 고정 위치** — 화면별 탭(평가 홈 / 평가 대상 / 평가 설계 / 과제물 관리 / AI 채점 / 평가 리포트)
- **패널 스펙 준수** — `background:white; border-radius:0 0 20px 20px; padding:28px 24px; box-shadow:0 4px 36px rgba(95,102,178,0.16)`
- **한 기능 = 한 HTML** — 탭/스텝/상태 전환은 한 파일에서 JS로. 화면마다 파일 쪼개지 않는다
- **실제 데이터** — "예시 데이터" 대신 교사·학생·학급·점수 실제값스러운 더미 (김서윤·1학년 1반·국어 등)
- **빈 상태·로딩·에러** 포함. 목업 관찰자가 서비스 흐름을 한 번에 이해할 수 있게

### 4. 필수 포함 요소 (모든 목업 HTML)
```html
<head>
  <link rel="icon" href="../screens/favicon_src.png" type="image/png">
  <link rel="stylesheet" href="https://cdn.rawgit.com/moonspam/NanumSquareRound/master/nanumsquareround.min.css">
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>
</head>
<body style="font-family:'NanumSquareRound',-apple-system,sans-serif;">
  <!-- GNB → 탭 → 패널 -->
  <script>lucide.createIcons();</script>
</body>
```
- 파비콘 경로는 파일 위치에 맞춰 상대경로 조정 (`output/` 하위 → `../screens/favicon_src.png`, 루트 → `screens/favicon_src.png`)
- 로고: Figma 에셋 URL 3개 — `get_design_context(fileKey=vOYXokKNMGec80BpDbIiJI, nodeId=1722:21861)`. 에셋 URL은 **7일 만료**, 새 세션에서는 재발급

### 5. 저장 정책
- 작업 중엔 preview로만 확인 (output 저장 금지)
- 사용자가 "저장해줘" / "파일 만들어줘" 하면 `output/<기능명>_v<N>.html` 로 저장
- **이미 공유된 파일명은 바꾸지 않는다** (URL 깨짐 방지)

## 미리보기 검증 워크플로우

목업 작성/수정 후 다음 순서로 확인:

1. `preview_list` → 서버 URL 확인. 없으면 `preview_start("hiai-mockup")`
2. `preview_console_logs` + `preview_logs` — 에러 없는지
3. `preview_snapshot` — 구조와 텍스트 확인
4. `preview_inspect` — 컬러/radius/spacing 등 CSS 값이 Figma와 일치하는지
5. 상호작용 있으면 `preview_click` / `preview_fill` → `preview_snapshot`으로 결과 확인

완료 보고는 **스크린샷 찍지 않고** 클릭 가능한 로컬 URL을 마크다운 링크로 사용자에게 전달 (예: [http://localhost:3502/output/...](http://localhost:3502/)). 채팅 오른쪽 패널에서 사용자가 직접 확인.

## 절대 금지

- 서버 설정 디버깅 반복 (고정 인프라 — 실패 시 `C:\hiai_serve\serve.log` 먼저 확인)
- GNB 내용 수정
- 파비콘 재생성 (`screens/favicon_src.png` 고정)
- 스크린샷 육안 비교로 토큰 값 단정
- 16px(B1) 미만으로 핵심 콘텐츠 폰트 축소
- Primary 버튼을 화면당 2개 이상
- 반응형 자동 추가 (명시 요청 시에만)
- Lucide 외 아이콘 라이브러리 사용
- 임의 패딩 확장 (패널 좌우 패딩은 항상 24px)

## 대표 완료 화면 (참고용 피그마 링크)

상세 URL은 `context.md` 스크린 인덱스 참조. 요약:

- **HOME-001** 선생님홈 — `MM23uA7pDEmFeMKGeFVJpB` `53:1152`
- **COURSE-001** 평가홈 (6탭) — `344b7XVs8E9KaFBhgAEhtW` `2087:56337` → `output/eval_home_v6_260403.html`
- **SCORINGS-001** AI채점 학급 목록 — `MM23uA7pDEmFeMKGeFVJpB` `54:4151`
- **SCORINGS-003** 학생 채점 상세 — `MM23uA7pDEmFeMKGeFVJpB` `49:1674`
- **REPORTS-001** 평가 리포트 목록 — `344b7XVs8E9KaFBhgAEhtW` `2087:52878` → `output/report_list_v1.html`
- **TASKS-001~007** 과제물 관리 (목록 + 학급 제출 상세 + 일괄업로드 4단계) — `344b7XVs8E9KaFBhgAEhtW`
- **PROJECTS-CREATE-001** 평가설계생성 — `344b7XVs8E9KaFBhgAEhtW` `2087:49498`

## 재채점 유도(Stale 알림) 정책 요약

AI 채점 결과 화면을 목업할 때 참고:
- `needsRescoring` 단일 플래그 (평가 단위)
- 트리거: OCR 저장 / 파일 교체 / 루브릭 수정
- UI: **학생 개별 상세 상단 배너**(주황)만. 학급 배너 X, 확인 다이얼로그 X
- 교사 확정값은 재채점 후에도 보존
- 상세 조건·정책은 `context.md` 하단 참조
