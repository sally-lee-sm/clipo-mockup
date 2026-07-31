---
name: clipo-mockup
description: Use this skill whenever the user is creating, updating, or previewing CLIPO UI mockups. Triggers include — working in `G:\내 드라이브\Claude\clipo_mockup\`, referencing CLIPO screens, asking to mockup a CLIPO screen, or porting HiAI flows into CLIPO (same 기획 / different design system).
---

# CLIPO Mockup Skill

CLIPO는 HiAI와 **기획·플로우는 동일하고 디자인 시스템만 다른** 자매 제품이다. 이 스킬은 CLIPO 목업 작업 시의 작업 순서를 담는다.

## Authoritative sources (항상 이 순서로 참조)

1. **`G:\내 드라이브\Claude\clipo_mockup\CLAUDE.md`** — 작업 규칙, 디자인 토큰, 헤더/네비 고정 스펙, 파비콘, 배포 가이드
2. **`G:\내 드라이브\Claude\clipo_mockup\context.md`** — 컬러 팔레트 상세, 타이포, 스크린 인덱스
3. **기획 스펙 (Notion)** — 제품 기획 변경 사항의 단일 소스
   - OCR 확인 후 채점: https://www.notion.so/34c17e5c8cf181f2a9b0cbfab7236dbd
   - 그 외 `기획` 상위 페이지에서 검색
4. **HiAI 참조 구현체** — 기획 로직·상태·라우팅의 동작 레퍼런스
   - `G:\내 드라이브\Claude\hiai_mockup\output\` 아래 HTML
5. **Figma MCP (`get_design_context`)** — CLIPO 토큰 라이브 호출

스킬 본문에 수치를 복사하지 않는다. 바뀌면 stale되기 때문.

## 세션 시작 시 (자동)

`CLAUDE.md` 지시대로:
1. `preview_start('clipo-mockup')` — 서버 시작/재사용
2. `output/` 최근 파일 확인 → 미리보기 URL 안내

## 핵심 원칙 (HiAI 포팅 작업 시)

**기획은 HiAI에서 가져오고, UI만 CLIPO로 바꾼다.**

| 항목 | 처리 |
|------|------|
| JS 상태 변수명, 이벤트 핸들러 | HiAI 그대로 복제 |
| URL 파라미터 규약 (`?eval=`, `?id=`, `?classNum=` 등) | HiAI 그대로 |
| 애니메이션 타이밍, 배너 등장/해제 규칙 | HiAI 그대로 |
| 더미 데이터 구조 (학생·학급·점수) | HiAI 그대로 |
| 컬러·타이포·spacing·radius·shadow | **CLIPO `CLAUDE.md` 토큰으로 치환** |
| 폰트 | Pretendard GOV |
| 헤더·좌측 네비 | CLIPO 고정 스펙 (CLAUDE.md) — HiAI GNB로 대체하지 말 것 |
| 파비콘 | `clipo_favicon.svg` |
| 아이콘 | Lucide (양쪽 동일) |
| 파일명 | HiAI와 동일 이름 권장 (예: `task_ocr_review_v1.html`) → 양쪽 동기화 용이 |

## 포팅 워크플로우

"CLIPO로 <기능> 목업 만들어줘" 요청 시:

### 1. 대응 HiAI 파일 식별 & 전체 읽기
- 예: 과제물 관리 → `hiai_mockup/output/task_ocr_review_v1.html`
- Read로 전체 읽어 상태·라우팅·이벤트·애니메이션 구조 파악

### 2. CLIPO 디자인 토큰 확인
- 기본 토큰은 `clipo_mockup/CLAUDE.md`에 이미 정리되어 있음 — 먼저 그걸 적용
- CLAUDE.md에 없는 컴포넌트만 `get_design_context`로 Figma 라이브 호출
- 스크린샷 육안 비교 금지

### 3. 치환 구현
- HiAI HTML을 베이스로 복제 → **시각 토큰만 치환**:
  - HiAI Primary `#7E44FB` → CLIPO Primary `#416bff`
  - NanumSquareRound → Pretendard GOV
  - HiAI 패널 shadow → CLIPO 스타일
  - 버튼·입력·뱃지 등 컴포넌트 shape 재적용
- **헤더·좌측 네비는 CLIPO CLAUDE.md의 고정 스펙으로 교체** (HiAI GNB를 그대로 쓰지 말 것)
- 파비콘 태그 교체: `<link rel="icon" type="image/svg+xml" href="clipo_favicon.svg">`

### 4. 저장
- 작업 중엔 preview로만 확인
- "저장해줘" 시 `output/<기능명>_v<N>_<날짜>.html` (CLAUDE.md 명명 규칙)
- HiAI와 동일한 기능명 권장

## 기획 동기화 규칙

1. **기획 변경 순서**: Notion 스펙 먼저 → HiAI 반영 → CLIPO 포팅
2. **CLIPO에서 먼저 작업한 신규 화면**: URL 규약·상태 변수명 HiAI 관례 준수해서 역포팅 가능하게
3. **양쪽 파일명 일치 유지** → diff로 기획 싱크 확인 쉬움

## 절대 금지

- HiAI 기획 로직을 임의 변경 (시각 토큰만 바꾼다)
- CLIPO 헤더·좌측 네비 임의 수정 (CLAUDE.md "절대 수정 불가" 명시)
- 본문 텍스트 16px 미만 축소 (CLAUDE.md 절대 규칙)
- 스크린샷 육안 비교로 토큰 값 단정
- 파비콘 누락 (`clipo_favicon.svg` 필수)
- Lucide 외 아이콘 라이브러리
- URL 파라미터 규약 변경 (HiAI 관례 유지)

## 체크리스트 (HiAI 포팅 첫 파일 완료 시)

- [ ] HiAI 원본과 **기능 동작 100% 동일** (preview_click으로 주요 플로우 확인)
- [ ] 헤더·좌측 네비는 CLIPO 스펙 적용
- [ ] Primary 컬러 `#416bff`, 폰트 Pretendard GOV
- [ ] 파비콘 태그 포함
- [ ] 본문 16px 이상
- [ ] URL 파라미터 규약 HiAI와 동일
- [ ] 파일명 HiAI와 일치 (또는 명시적 매핑 유지)
