# Clipo Mockup 작업 환경

## 역할
이 폴더에서 Claude는 **UI/UX 목업 제작 어시스턴트**로 동작한다.
사용자가 제공하는 **지침(텍스트)** 과 **화면 캡처(스크린샷)** 를 바탕으로 목업을 만든다.

## 작업 방식

### 1. 미리보기 우선 개발
- 사용자가 캡처 이미지 + 지침을 제공하면 Claude가 목업을 생성한다.
- 캡처 이미지는 `screens/` 폴더에 저장하거나 대화 중 직접 첨부해도 된다.
- **목업은 preview(채팅 오른쪽 미리보기)로 즉시 확인**하며 수정을 반복한다.
- `output/` 폴더에 저장하지 않고 preview로 빠르게 작업한다.

### 2. 산출물 저장은 사용자 요청 시에만
- 사용자가 **"저장해줘", "파일 만들어줘"** 등 명시적으로 요청할 때만 `output/`에 저장한다.
- 수정이 충분히 끝난 최종본만 output에 남긴다.

### 3. 다른 PC에서 이어서 작업
- 사용자가 **"미리보기 열어줘"** 등으로 요청하면:
  1. `output/` 폴더의 파일 목록을 조회한다.
  2. 해당 파일을 preview 서버로 바로 띄운다.
- 별도 빌드 없이 HTML 파일만으로 어디서든 열 수 있어야 한다.

## 목업 제작 원칙
- 캡처 화면의 레이아웃, 색상, 폰트, 간격을 최대한 분석하여 반영한다.
- 명시된 지침이 캡처와 충돌하면 **지침을 우선**한다.
- 기본 형식: **단일 HTML 파일** (인라인 CSS + Tailwind CDN) — 빌드 불필요, 어느 PC에서나 브라우저로 바로 열림
- 아이콘: Lucide CDN (`<script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js">`) 사용
- 반응형은 명시적으로 요청받은 경우에만 추가한다.
- 기획 논의가 끝나면 바로 코드로 진입 (중간 설명 최소화)

## 같은 위계 UI 일관성 (핵심 원칙 — 2026-06-18 강조)

> 옥빈님이 가장 중요시하는 축. **같은 위계에 놓인 요소는 라벨·상태 스타일·컴포넌트가 일관**해야 한다. 짚어주기 전에 Claude가 먼저 챙길 것.

- **라벨 평행성**: 같은 그룹(탭/카드/옵션/버튼)의 라벨은 어미·형태 통일(명사형이면 전부 명사형, 예 `가진 활동지 올리기 / 다른 선생님 활동지 가져오기 / AI로 함께 만들기`). 한 개라도 고치면 그룹 전체 평행성 먼저 검사.
- **상태 스타일 일관 + 레이아웃 안정**: 활성/hover 등 상태 변화가 **폭·위치를 바꾸지 않게** 한다. 예) 탭·세그먼트 활성 시 `font-weight` 변화 금지 → **색 + 밑줄로만 구분**(굵어지면 폭이 커져 옆 요소가 밀리며 "움찔거림"). 동일 위계 칩·배지·셀도 정렬·크기 통일.
- **공유 컴포넌트는 페이지 간 1:1 동일**: 탭 밴드·헤더·좌측 NB·반복 모달은 모든 페이지에서 동일. 한 곳 바꾸면 전수 동기화(아래 "변경 영향 범위 체크").
- **문구 역할 분리(중복 금지)**: 한 화면의 설명 문구들은 역할이 겹치지 않게. 예) 타이틀 아래 sub=부담 제거/선택 안내, 강조 배너=행동유도·가치. 같은 메시지를 두 군데 반복하지 않음.
- **정보 밀도 절제**: 카드 등은 라벨을 짧게(예 `등록완료`→`등록`), 같은 줄로 묶어 줄 수를 줄인다.

## 사용자 친화 UI — 팝업보다 인라인 (토스·네이버식, 루카스 피드백)

> 핵심: **팝업으로 과업을 길막지 않는다.** 모달은 (1) 화면을 덮어 흐름을 끊고 (2) 내용을 안 읽히게 하고 (3) "빨리 닫아야 한다"는 압박을 준다 → 토스·네이버처럼 인라인/페이지로 푼다.

- **핵심 입력·선택·다음 단계는 팝업이 아니라 페이지 안 인라인**으로 펼치거나, 페이지를 전환(스텝)해서 진행. 카드 기대 행동이 "상세 진입"이면 팝업으로 막지 말 것.
- **모달 허용(좁게)**: 되돌릴 수 없는 행동의 짧은 확인(삭제 경고), 본 과업을 벗어나지 않는 아주 짧은 보조 입력. 그 외엔 인라인/페이지 우선.
- **인라인 전환 패턴**: 방법/옵션 카드 선택 → 그 자리 아래 인라인 패널 노출 / 단계 진행은 페이지 전환 + 스텝바(현 단계·이전 이동) / 한 화면 한 과업.
- **점진적 공개**: 필요한 것만 필요할 때. 미리 다 펼쳐 부담 주지 않음.
- **맥락 유지**: 작업 중 컨텍스트(평가명·파일·문항)를 화면에 유지, 팝업으로 가리지 않음. 입력값은 단계 이동에도 보존.
- **즉각 피드백 + 강요 금지**: 결과는 토스트로, 행동 유도는 이점 먼저(압박 톤 X).
- **적용 예**: 활동지 — 인식 결과 팝업 제거→페이지 인라인, 등록 방법도 카드 선택→인라인 패널. 시안: `output/register_modal_styles_v1_260618.html`(A 리스트모달 / B 드롭다운 / **C 인라인 채택**).

## 변경 영향 범위 체크 (필수 워크플로)

> ⚠️ **목업은 기획 일관성이 핵심**이라서, 한 곳 수정에 매몰돼서 다른 동일 패턴을 빠뜨리면 안 됨.
> 옥빈님이 짚어주기 전에 Claude가 먼저 챙겨야 함.

### 1. 변경 전 — 영향 범위 grep 전수 검사
어떤 텍스트·라벨·UI 패턴을 수정하기 전에:
1. **grep으로 동일 텍스트·동일 패턴 전수 검색** (`output/` + `output/_annotated/`)
2. 발견된 위치를 **사전 보고**:
   ```
   "'점수 (총점/등급)' 텍스트가 6개 파일에서 12군데 발견됐어요.
   모두 '점수'로 변경할까요? 아니면 일부만?"
   ```
3. 옥빈님이 "여기만"이라고 명시한 경우 → **다른 곳은 손대지 않음 + 차이 명시**

### 2. 통일성 원칙이 적용되는 항목 (특별 주의)

| 항목 | 통일 대상 |
|------|----------|
| **공개 항목·표시 항목 라벨** (점수·성취기준·문항별·채점요소별 등) | 일괄 옵션 / 공개 현황 표 / 미리보기 모달 / 출력 항목 / PDF 모달 등 |
| **공개 항목 순서** | 위 모든 영역에서 동일 순서 유지 (한 곳 바꾸면 전부 동기화) |
| **모달 톤·아이콘·CTA** | 같은 기능의 모달이 여러 페이지에 있으면 1:1 일치 (예: '학생에게 결과 공개' 모달) |
| **3단계 카드·페이지 헤더 라벨** | 실제 개발 화면과 동일하게 (옥빈님이 종종 캡처로 비교 제공) |
| **메인 파일 ↔ `_annotated/` 동일 파일** | 동일 변경 모두 반영 (특히 라벨·문구 변경) |

### 3. 모달·반복 UI 패턴 작업 시 (예시)
같은 기능의 모달이 여러 페이지에 분산:
- `학생 화면 미리보기` → `class_share_settings` + `scoring_elementary_v2` + `class_print_settings` 등
- `학생에게 결과 공개` 모달 → `class_scoring_detail`
- `출력 항목 설정` 모달 → `class_print_settings`

**한 곳 변경 → 다른 곳도 동일하게 적용할지 사전 확인 + 명시.** 컨텍스트가 다르면 (예: 공개 설정 화면에서는 "공유 설정 저장" CTA, 채점 현황에서는 "학생에게 결과 공개하기" CTA) 차이를 보고.

### 4. 실제 개발 화면과 대조 (옥빈님이 스크린샷 제공 시)
- 옥빈님이 "왼쪽 실제 / 오른쪽 mockup" 비교 캡처 → **모든 텍스트·라벨·아이콘·레이아웃·CTA 1:1 일치**시킴
- 차이 발견 시 명시 후 옥빈님 확정 받기 ("실제는 '~합니다' 정중체인데 mockup은 '~해요'. 어느 쪽?")

### 5. 변경 직후 — 보고 형식

```
변경 X개 파일 / Y개 위치
- Before: ...
- After: ...
- 영향 범위 (다른 화면): ✅ 모두 동기화 / ⚠️ 일부 제외 (이유)
```

### 6. iframe·미리보기 컨텍스트 체크 (자주 누락)

미리보기 iframe에 다른 화면을 띄울 때 — **컨텍스트에 맞는 URL 파라미터 자동 전달**:

| 사용처 | 필수 파라미터 | 이유 |
|--------|--------------|------|
| 학생 결과 화면을 PDF 출력 미리보기에 띄움 | `?preview=1` | `result_student_view`에서 `.period-bar`(결과 확인 기간)·헤더·사이드바 자동 숨김 |
| 학생 결과 화면을 학급 공개 설정 미리보기에 띄움 | `?preview=1` | 동일 |
| 미제출 학생 시뮬레이션 | `?missing=1` | 학생 답안·문항별 결과 카드 자동 숨김 |

**원칙**: iframe src의 컨텍스트가 "실제 사용자 시점이 아닌 미리보기"라면 `?preview=1` 무조건 추가.

### 7. 새 화면·라벨 정비 시 — 실제 개발 화면 캡처 사전 요청

새 화면 만들거나 기존 화면 라벨 정비 작업:
1. **"실제 개발 화면 캡처 있으세요?"** 먼저 확인 (있으면 텍스트·아이콘·CTA·레이아웃 1:1 일치)
2. 없으면 mockup 톤 + 기존 통일성 원칙 따름
3. 옥빈님이 사후 비교 캡처 보여주면 → 차이 일괄 정렬

이 체크리스트 못 챙기고 옥빈님이 사후 짚어주면 → 영향 범위 빠진 곳 재발견·재수정 비용 발생.

## 산출물 구성 원칙

### 하나의 기능 = 하나의 파일
- 같은 기능 내의 **화면 전환(탭, 스텝, 상태 변화 등)은 하나의 HTML 파일** 안에 모두 포함한다.
- 사용자가 목업 흐름을 한 파일에서 순서대로 확인할 수 있어야 한다.
- 예: 회원가입 기능이면 `signup_v1_260401.html` 하나에 단계별 화면이 모두 들어간다.

### 파일 명명 규칙
- `output/<기능명>_v<버전>_<날짜>.html`
- 예: `output/signup_v1_260401.html`, `output/grading_v2_260402.html`
- 수정본은 버전 번호를 올리고 날짜를 갱신한다.

## 디자인 토큰 요약
- **폰트**: `Pretendard GOV` — CDN: `https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard-gov.min.css`
- **Primary**: `#416bff` (blue/500) / 로고 컬러: `#365eef` (blue/600)
- **텍스트**: `#222631` (기본), `#3b3f4c` (보조), `#6d7381` (muted), `#808080` (subtle)
- **Border**: `#d9d9d9` / emphasized: `#b3b3b3`
- **배경**: `#f6f7f9` (subtle), `#f1f3f5` (muted)
- **Error**: `#f2525f` / **Success**: `#1ab864` / **Warning**: `#f97316`
- 상세 팔레트 → `context.md` 참조

## 타이포그래피 요약
| 용도 | 크기 |
|------|------|
| 섹션 타이틀 | 26px SemiBold |
| 카드/항목 제목 | 20px SemiBold |
| 기본 (대부분) | 16px Regular/Medium |
| 레이블·보조 | 14px Regular/Medium |

> ⚠️ **절대 규칙**: 사용자가 읽고 수정하는 본문 텍스트(AI 근거, AI 피드백, 선생님 피드백 textarea, 채점 기준명 등)는 반드시 **16px** 이상. 14px 이하로 줄이지 않는다.

## 고정 요소

### 파비콘 (모든 목업 파일에 필수)
- 파일: `output/clipo_favicon.png` (CLIPO 캐릭터 이모지형 아이콘 — 2026-05-13 변경. 이전 svg는 헷갈린다는 피드백으로 교체)
- 모든 HTML 목업 파일의 `<head>`에 아래 태그를 반드시 포함한다:
  ```html
  <link rel="icon" type="image/png" href="clipo_favicon.png">
  ```
- 새 목업 파일 생성 시 자동으로 추가할 것
- 이전 `clipo_favicon.svg`는 deploy 디렉토리(training 사이트 헤더 로고용)에서 여전히 참조되므로 삭제하지 말 것

### 헤더 (절대 수정 불가)
```
[☰] [CLIPO로고] ─────── [개인 유료] D-1710 [🤖] AI 크레딧 700 | test99 선생님(담임) ▾
```
- 높이: 56px, 배경 white, 하단 border `#d9d9d9`
- **CSS**: `padding: 0 20px 0 8px` (왼쪽 여백 8px, 오른쪽 20px)
- 아이콘: Lucide (`menu`, `bot`, `chevron-down`)
- ☰ 버튼: `width:36px; height:36px; margin-right:8px` — 클릭 시 `toggleNav()` 호출

### 왼쪽 네비게이션 바 (절대 수정 불가)
- **접힘**: 56px 고정폭 / **펼침**: 200px (☰ 버튼 토글)
- **CSS 핵심**:
  - `.icon-sb`: `width:56px; transition:width .2s ease` — `overflow:hidden` 사용 금지 (툴팁 클리핑 발생)
  - `.icon-sb.expanded`: `width:200px`
  - `.si` (메뉴 아이템): `height:40px; padding:0 4px 0 16px; gap:10px` — 아이콘이 56px 중앙(28px) 정렬됨
  - `.si-icon`: `width:24px; height:24px; flex-shrink:0`
  - `.si-label`: `opacity:0; transition:opacity .1s` / `.expanded` 시 `opacity:1`
  - `.si-tip` (접힘 툴팁): `position:absolute; left:48px; display:none` / `:hover` 시 `display:block` / `.expanded` 시 `display:none!important`
- **메뉴 항목 (순서 고정)**:
  | 아이콘 | 레이블 | 비고 |
  |--------|--------|------|
  | `home` | 홈 | |
  | `scissors` | 평가계획 | |
  | `layout-grid` | 수업 | |
  | `notebook-pen` | 창의적 체험활동 | **Beta** 뱃지 레이블 옆에 표시 |
  | `zap` | 세특 간편 생성기 | |
  | `smile` | 행동특성 및 종합기록 | |
  | `bar-chart-2` | 리포트 | |
  | `building-2` | 학교 설정 | |
  | *(구분선)* | | `.nav-bottom` 영역 시작 |
  | `file-down` | 매뉴얼 다운로드 | |
  | `credit-card` | 요금제 | |
  | `help-circle` | 고객센터 | |
- 현재 페이지에 해당하는 메뉴: `.si.active` (`background:#e9f2ff; color:#416bff`)
- `body.nav-expanded` 클래스로 콘텐츠 영역 밀기 처리 (각 페이지 레이아웃에 맞게 적용)

### 페이지 헤더 — 타이틀 + 생성 버튼/드롭다운 (표준, 임의 변경 금지)

목록 화면 상단(페이지 타이틀 + 우측 생성 버튼)은 **항상 동일 패턴**. 자꾸 틀어지면 안 됨 — 일관성 최우선.

- **레이아웃**: 좌측 `page-h1`(타이틀, 예 `1학년 국어 문항`) + `page-sub`(설명) / 우측 끝에 생성 버튼. 컨테이너 `display:flex; align-items:flex-start; justify-content:space-between`.
- **생성 버튼**: `class="btn btn-primary"` (기본 40px). **`btn-lg`로 키우지 말 것, chevron 아이콘 붙이지 말 것.** 마크업 = `<i data-lucide="plus">` + 라벨.
- **드롭다운**: 개발 화면의 `create-menu` 클래스 그대로 (`top:46px; right:0; min-width:208px; border-radius:8px; box-shadow:0 0 1px #2226314D,0 8px 24px #00000026; padding:6px`). 항목 = **좌측 정렬 + 16px 아이콘 + 15px 텍스트**, padding `10px 12px`, hover `#f6f7f9`.
- **권위 소스(복제해서 시작)**: `output/task_ocr_inline_v1.html` 의 `수행평가 만들기` 버튼 + `#createMenu`. 새 화면은 새로 짜지 말고 이걸 복사.
- 참고 구현: `output/question_tab_v1_260617.html`(문항 탭).

### 콘텐츠 너비 — 1240px (표준, 절대 일관)

수업 내 모든 화면(탭 밴드 있는 평가 흐름 + 세부 페이지)의 **콘텐츠·탭밴드 너비는 `max-width:1240px`로 통일**한다. 탭 이동 시 좌우 폭·탭 위치가 튀면 안 됨 — 일관성 최우선.
- `.tab-band-inner` 와 콘텐츠 래퍼(`.page-wrap` / `.page-container` 등) 모두 `max-width:1240px; margin:0 auto`.
- **기준 구현**: `output/task_ocr_inline_v1.html` (1240px). 새 세부 페이지도 반드시 1240px로 그린다.

### 푸터는 항상 뷰포트 하단 고정 (콘텐츠 따라오지 않게)

- 콘텐츠가 짧아도 푸터가 화면 중간에 떠 있지 않고 **맨 아래에 붙어야** 한다.
- 구현: 콘텐츠 래퍼를 `flex:1; display:flex; flex-direction:column`(부모 `.main-wrap`은 `min-height:100vh; flex-direction:column`)로 두고, **`.site-footer{ margin-top:auto }`**. `margin-top` 고정값(예 48px) 쓰지 말 것 — 짧은 목록에서 푸터가 따라 올라옴.

### 수업 탭 밴드 (표준, 임의 변경 금지)

수업 내 모든 화면 상단의 탭 밴드(`.tab-band` / `.tab-band-inner` / `.sub-tab`)는 **모든 페이지에서 동일**. 페이지마다 모양·개수·동작이 다르면 탭 이동 시 들썩이고 일관성이 깨진다.

- **6탭 고정 순서**: `수업 홈 / 평가 설계 / 활동지 / 과제물 관리 / 채점 / 세부능력 및 특기사항 지원`
- **활성 탭 = 색(`#416bff`) + 하단 밑줄만.** ⚠️ **`font-weight` 변화 금지**(굵어지면 폭이 커져 옆 탭이 밀림 = 움찔거림. 2026-06-18 전 파일 수정).
  - `.sub-tab{ font-weight:500 }` / `.sub-tab.on{ color:#416bff; border-bottom:2px solid #416bff; }` ← 굵기 동일
- **링크 규약**: 수업 홈→`co_teacher_review_v1`, 평가 설계→`task_ocr_inline_v1.html#design`(=`goDesignList()`), 활동지→`question_tab_v1_260617`, 과제물 관리→`task_ocr_inline_v1.html`(같은 파일 내면 `showScreen('screen-task')`), 채점→`task_ocr_inline_v1.html#scoring`(=`goScoringList()`), 세특→`notReady()` 토스트.
- 수업 홈 파일만 클래스명이 `.class-tabs`/`.ct`로 다르나 스타일·6탭·링크 동일(`.ct.active`도 굵기 변화 금지).

### 수업 워크스페이스 공통 크롬 = 통째로 복제

새 CLIPO 수업 화면은 **상단바(크레딧 영역)+좌측 NB+탭 밴드**를 새로 짜지 말고 권위 소스에서 통째로 복제한다.
- **헤더/상단바(크레딧·D-day·사용자)** → 위 "고정 요소 › 헤더" 그대로
- **좌측 NB(`.icon-sb`)** → 위 "고정 요소 › 왼쪽 네비게이션 바" 그대로
- **탭 밴드** → 위 표준
- **권위 소스(복제 시작)**: `output/question_tab_v1_260617.html` 또는 `output/task_ocr_inline_v1.html`

## 아이콘 시스템
- **Lucide Icons** — 피그마 디자인 시스템과 동일 세트
- HTML에서: `<i data-lucide="icon-name"></i>` + `lucide.createIcons()` 호출
- 새로운 아이콘 라이브러리 추가 금지

## 컴포넌트 참조
피그마 `Components` 페이지 기준 (아이스박스 제외). 목업 작업 시 아래 컴포넌트명 활용:
`Input` · `Radio` · `Radio Card` · `Menu` · `File Upload` · `Switch` · `Pagination` · `Tag` · `Accordion` · `Separator` · `ToggleTip/ToolTip` · `Toast` · `Popover` · `Pin_Input` · `Alert` · `Progress` · `Slider` · `Spinner` · `Contents` · `Button` · `Checkbox` · `Badge` · `Icon Button` · `Close Button` · `Textarea` · `Link` · `Select` · `Steps` · `Combobox` · `Checkbox Card` · `Field` · `Tabs` · `Skeleton` · `Password_Input` · `Number Input`

## 개발 화면 기준 (목업 참고 표준) — 필수

> 아래 파일들은 **실제 개발/디자인 확정된 화면**을 1:1로 옮긴 목업이다.
> 새 CLIPO 목업을 그릴 때는 **새로 짜지 말고 이 파일들을 베이스로 복제·확장**한다.
> (헤더·좌측 NB·탭 밴드·푸터·디자인 토큰·컴포넌트 패턴을 그대로 따른다.)

| 화면 | 파일 |
|------|------|
| 홈 (내 수업) | `output/home_v1_260612.html` |
| 수업 홈 | `output/co_teacher_review_v1_260611.html` |
| 평가 설계 목록 / 과제물 관리 / 채점 | `output/task_ocr_review_v2_260612.html` (`#design` / 기본 / `#scoring`) |
| 활동지 (수업 탭 — 업로드·인식·정확도 부스터) | `output/question_tab_v1_260617.html` — 상세 인수인계: `context_question_tab.md` |
| 평가(과제) 설계 상세 | `output/evaluation_design_v4_260615.html` |
| 학급 채점 현황 → 학생 채점 → 결과 공개 | `output/class_scoring_detail_v1_260512.html` |
| 창의적 체험활동 목록 | `output/creative_activity_list_v6_260507.html` |
| 세특 간편 생성기 목록 | `output/quick_seteuk_dev_list_v1_260604.html` |

- **공통 골격**: 표준 헤더(SVG 로고·학교 유료·D-180·AI 크레딧·사용자) + `icon-sb` NB(홈/평가계획/수업/창의적 체험활동 Beta/세특 간편 생성기/행동특성 및 종합기록/리포트/학교 설정 + 매뉴얼/요금제/고객센터) + 탭 밴드(수업 홈/평가 설계/활동지/과제물 관리/채점/세부능력 및 특기사항 지원) + 푸터.
- **연결 규약**: 로고·홈 아이콘 → `home_v1`, NB 수업 → `co_teacher_review`, 창체 → `creative_activity_list_v6`, 세특 → `quick_seteuk_dev_list`. 탭은 해시 라우팅(`#design`/`#scoring`).
- 신규 화면은 이 세트 중 가장 가까운 파일을 복제해 시작하고, 미구현 메뉴는 `notReady()` 토스트로 처리.
- 이 목록에 없는 신규 개발 화면이 추가되면 표에 한 줄 추가해 갱신한다.
- **과제물 양식 업로드는 (활동지 탭 정식 도입 전까진) 과제물 관리 소관** ⚠️2026-06-24 뒤집힘: 활동지 탭 정식 도입이 늦어져, 2026-06-17에 활동지 탭으로 이관했던 빈 양식(=과제물 양식) 업로드를 **과제물 관리로 되가져옴**. 라벨은 `과제물 양식`(페이지 단어 통일, '답안 양식'·'빈 양식' 아님). 3개 OCR 시안에 동일 세트 추가 완료: `task_ocr_review_v2`(시안1 단계형)/`task_ocr_inline_v1`(시안2)/`task_ocr_v3`(시안3 하이브리드). 구성 = ①각 평가 카드 헤더(과제 제출 QR 옆)에 `과제물 양식` 버튼+상태칩(미등록/공통 등록/반별 N개) ②탭 밴드 아래 풀너비 안내바(`.form-promo`, `다시 보지 않기`) ③등록 페이지(`screen-form`): 적용범위 공통/반별 토글 → 빈 문제지 업로드 → 인식 결과 인라인 편집(`.fmrg-*`, 문항별 지시문, 학생 이름·답란 제외) → 등록. 활동지·채점 배너도 같은 풀너비 바로 통일. **활동지 탭 정식 도입 시 이관 재검토.** (구버전 `task_ocr_review_v1`·`scoring_diagnosis_v1`은 미적용 — 스킵.)

## 세션 시작 시 필수 작업 (자동 실행)
**이 폴더에서 새 대화가 시작되면 즉시 아래를 실행한다:**
1. `preview_start('clipo-mockup')` 으로 미리보기 서버 시작
2. `output/` 폴더에서 가장 최근 파일을 확인하고 사용자에게 미리보기 URL 안내
3. 서버가 이미 실행 중이면 reuse하고 바로 URL만 안내

## 다른 PC / 다른 사용자 인수인계 가이드

이 폴더는 Google Drive에 동기화되어 있어 다른 PC에서도 동일하게 작업 가능하다.
**새 사용자(예: 샐리)가 이어서 작업할 때 Claude가 처리해야 할 것:**

### 첫 배포 시 자동 처리
1. **deploy 디렉토리 부재 감지**: `/tmp/clipo-deploy/` 가 없거나 `.git` 디렉토리가 손상되어 있으면
   - `git clone https://github.com/obkim-lgtm/clipo.git /tmp/clipo-deploy` 자동 실행
2. **Git 인증 실패 시**: 사용자에게 GitHub Personal Access Token 입력 요청
   - `https://github.com/settings/tokens` 에서 `repo` scope으로 발급
   - clone URL을 `https://<username>:<PAT>@github.com/obkim-lgtm/clipo.git` 형태로 재시도
3. **commit author 고정**: 사용자가 누구든 commit author는 항상 다음으로 명시
   - `-c user.name="obkim-lgtm" -c user.email="ob.kim@datadriven.kr"`
   - (히스토리 일관성 유지 — Netlify 배포자는 옥빈으로 통일)

### PC별 차이 처리
- **경로 구분자**: Windows(`F:\내 드라이브\...`) / Mac(`/Users/.../Google Drive/...`) — 절대 경로 직접 쓰지 말고 항상 `output/`, `screens/` 같은 상대경로 사용
- **임시 디렉토리**: `/tmp/`는 OS마다 다를 수 있으나 Bash 도구가 알아서 처리
- **미리보기 포트**: `3457` 고정 (`.claude/launch.json` 참조)

### 인수인계 체크리스트 (Claude가 사용자 요청 시 안내)
사용자가 "인수인계 환경 확인해줘" / "샐리 PC에서 처음 실행" 같은 요청 시:
1. ✅ Google Drive 동기화 상태 (`output/` 파일 목록 조회로 확인)
2. ✅ GitHub repo 접근 권한 (`/tmp/clipo-deploy/` git status로 확인)
3. ✅ 미리보기 서버 동작 (`preview_start` 후 카드 렌더링 확인)
4. ✅ 배포 테스트는 실제 변경 시점까지 보류

### 운영자가 바뀌어도 유지되는 것
- **GA4 측정 ID** (`G-DZBF275ZV0`): 페이지에 하드코딩, 변경 불필요
- **Search Console**: 도메인 기반 인증이라 사용자 무관
- **Netlify 자동 배포**: GitHub push 트리거, 사용자 무관
- **commit 작성자**: 위 규칙대로 obkim-lgtm 고정

## 연수 페이지 운영 가이드

### 개요
CLIPO 연수 페이지는 정적 HTML로 제작되어 Netlify에서 호스팅된다.
- **외부 공개용 (v2)**: `training_v2_260410.html` → `https://training.clipo.ai/`
- **내부용 (v1)**: `training_v1_260410.html` → `https://training.clipo.ai/training-internal/`
- v1과 v2는 콘텐츠 동일, v1은 CLIPO 사이드바 포함 / v2는 독립 헤더

### 배포 흐름
```
지침 전달 → HTML 수정 (v1 + v2) → 미리보기 확인 → "배포해줘" → git push → Netlify 자동 배포 (30초)
```

### 배포 환경
| 항목 | 값 |
|------|-----|
| GitHub repo | `obkim-lgtm/clipo` (private) |
| 호스팅 | Netlify |
| 도메인 | `training.clipo.ai` |
| 배포 브랜치 | `main` |
| 로컬 deploy 경로 | `/tmp/clipo-deploy/` |
| git 사용자 | `obkim-lgtm` / `ob.kim@datadriven.kr` |

### deploy 디렉토리 구조
```
/tmp/clipo-deploy/
├── index.html                    # v2 (외부용) 배포본 — training.clipo.ai/
├── clipo_favicon.svg
├── thumb_korean_april.png        # 연수 썸네일 (로컬)
├── thumb_science_march.png
├── thumb_math_feb.png
├── sitemap.xml                   # SEO 사이트맵
├── robots.txt                    # 크롤러 설정 (internal 차단)
└── training-internal/
    ├── index.html                # v1 (내부용) 배포본
    ├── clipo_favicon.svg
    ├── thumb_korean_april.png
    ├── thumb_science_march.png
    └── thumb_math_feb.png
```

### 배포 명령 (Claude가 실행)
```bash
# 1. output → deploy 복사
cp training_v2_260410.html /tmp/clipo-deploy/index.html
cp training_v1_260410.html /tmp/clipo-deploy/training-internal/index.html

# 2. 썸네일이 추가/변경된 경우 함께 복사
cp thumb_*.png /tmp/clipo-deploy/
cp thumb_*.png /tmp/clipo-deploy/training-internal/

# 3. commit & push
cd /tmp/clipo-deploy
git add -A
git -c user.name="obkim-lgtm" -c user.email="ob.kim@datadriven.kr" commit -m "변경 내용 요약"
git push
```

### 주요 운영 작업
| 작업 | 수정 위치 | 참고 |
|------|----------|------|
| 새 연수 추가 | 탭1 카드 추가 | Google Form 링크 필요 |
| 연수 마감 처리 | `tc-badge` → `.closed` + 버튼 비활성화 | |
| 지난 연수로 이동 | 탭1 → 탭2 이동 + 자료 링크 추가 | 썸네일 이미지 필요 |
| 연수 정보 수정 | 날짜, 강사, 설명 텍스트 수정 | v1, v2 모두 변경 |
| 썸네일 교체 | output/ 폴더에 새 이미지 저장 | Notion 이미지는 로컬 다운로드 필수 (S3 URL 만료) |

### GA4 트래킹
- **측정 ID**: `G-DZBF275ZV0`
- **속성명**: CLIPO 연수 페이지
- **이벤트 목록**:

| 이벤트명 | 트리거 | 파라미터 |
|---------|--------|---------|
| `switch_tab` | 탭 전환 | `tab_name` |
| `click_apply_training` | 무료로 신청하기 | `training_name` |
| `click_training_material` | 연수 자료 보기 | `training_name` |
| `click_instructor_apply` | 연수 운영 지원 신청하기 | - |
| `click_clipo_material` | 클리포 연수 자료 확인하기 | - |
| `click_inquiry` | 1:1 문의하기 | - |

### SEO 설정
- **Search Console**: `training.clipo.ai` 등록 완료
- **canonical**: `https://training.clipo.ai/`
- **내부용 (v1)**: `noindex, nofollow` 처리 (검색 노출 차단)

### 주의사항
- 새 연수 추가 시 **GA 이벤트 트래킹**도 함께 추가할 것
- 썸네일은 반드시 **로컬 파일**로 저장 (Notion S3 URL은 1시간 후 만료)
- v1, v2 **콘텐츠는 항상 동기화** 유지
- deploy 디렉토리(`/tmp/clipo-deploy/`)가 없으면 `git clone`으로 재생성

## 폴더 구조
```
clipo_mockup/
├── screens/    # 참고용 캡처 이미지 + logo.svg
├── output/     # 생성된 목업 HTML 파일 (화면명_v버전_일시.html)
├── context.md  # 서비스 배경, 디자인 가이드, 컬러 토큰 상세
└── CLAUDE.md   # 이 파일 (작업 규칙 요약)
```
