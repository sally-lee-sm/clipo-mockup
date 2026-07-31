# Index — 상황별 방향 지시표

문서 목록이 아니라 라우팅 테이블이다. "어떤 상황이면 → 무엇을 읽는다"를
적는다. 문서만이 아니라 이 프로젝트의 **원천 데이터 소스**(DB 스키마,
데이터셋, API 스펙, 대시보드 등)도 여기서 라우팅한다.

## 라우팅

- 어떤 작업이든 시작 전 → [principles.md](principles.md)
  (SessionStart가 자동 주입하지만, 수동 확인 시 여기)
- 최근 작업 맥락이 필요하면 → [sessions/](sessions/) 최신 2일치
- **새 화면 그리기 전(복제 시작·개발 화면 기준)** → ../clipo/CLAUDE.md · ../hiai/CLAUDE.md "개발 화면 기준" 표 + ../clipo/레퍼런스화면/ · ../hiai/레퍼런스화면/
- **라벨·문구·패턴 만들거나 바꾸기 전 / 마무리 점검 / 이어받기** → [workflow.md](workflow.md)
- **화면 출고 직전 자가 점검** → [checklist.md](checklist.md)
- CLIPO 전용 규칙·토큰 상세 → ../clipo/CLAUDE.md · ../clipo/context.md · ../clipo/service_overview.md
- HIAI 전용 규칙·컴포넌트 스펙·토큰 → ../hiai/CLAUDE.md · ../hiai/context.md
- 진행 중 화면 이어받기 → ../clipo/이어받기/ · ../hiai/ (context_*·HANDOFF_*) / 넘길 땐 ../이어받기-템플릿.md
- 처음 쓰는 사람(복제·설치·작업·배포 흐름) → ../00_시작하기.md

<!-- ⚠ 실제 라우팅은 이 주석 밖, 위의 목록에 항목으로 추가한다.
     주석 안에 쓰면 뷰어·lint·에이전트 모두에게 보이지 않는다.
형식 참고 (원천 데이터 소스·시크릿 소재도 이렇게):
- DB 스키마 변경 전 → schema/tables.sql
- 매출 데이터 다루기 전 → data/sales/README.md (원천: BigQuery ds_sales)
- 대시보드·통계 작업 전 → data/sales_2026.csv
  (원천 데이터 — 반드시 이 파일만 근거, 값 임의 생성 금지)
- 외부 API 자격증명 → .env 의 FOO_API_KEY (값 편집: sops secrets.yaml)
  ※ 시크릿은 소재·사용법만 — 값은 절대 적지 않는다
-->

## 인덱스 규칙

- docs/ 하위 모든 문서는 이 파일에서 도달 가능해야 한다 (lint가 강제).
- 새 문서·데이터 소스가 생기면 반드시 여기에 라우팅 줄을 추가한다.
  에이전트가 어떤 작업 전에 무엇을 봐야 하는가가 기준이다.
