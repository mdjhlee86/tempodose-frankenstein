# Feature Specification: 합성 데모 데이터 + sql.js 번들 성분 중복 탐지 MVP

**Feature Branch**: `001-duplicate-ingredient-mvp`
**Created**: 2026-11-01
**Status**: Draft (clarify 전 · implement 금지)
**Input**: User description: "합성 데모 데이터 + sql.js 번들로 성분 중복 탐지 MVP. QR 입력은 다음 피처."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - 여러 병원 약을 넣고 같은 성분 중복 확인 (Priority: P1)
여러 병원·약국에서 받은 약을 사용자가 목록(합성 데모 약품 검색)에서 골라 추가하면, 같은 성분이 두 번 이상 들어 있을 때 바로 경고를 본다.

**Why this priority**: 다병원 복약의 가장 흔한 위험(성분 중복)을 오프라인에서 보여 주는 최소 가치.
**Independent Test**: 데모 약품 2개(같은 성분 포함)를 추가 → 중복 경고 1건, 근거 규칙 ID·출처 표시.

**Acceptance Scenarios**:
1. **Given** 같은 성분 A를 가진 데모 약 X, Y, **When** 둘 다 추가, **Then** "성분 중복: A" 경고와 두 약 이름, 규칙 ID가 표시된다.
2. **Given** 성분이 겹치지 않는 약 2개, **When** 추가, **Then** 중복 경고 없음 + "번들 데모 데이터 기준" 안내.
3. **Given** 번들 DB에 없는 성분 매칭, **When** 추가, **Then** "판단 불가 — 약사·처방 의료진과 상의"(fail-safe) 표시.

### User Story 2 - 같은 계열(효능군) 중복 주의 (Priority: P2)
성분은 다르지만 같은 계열(합성 데모 분류 코드)인 약 조합에 "계열 중복 주의"를 보여 준다.

**Independent Test**: 같은 계열 다른 성분 약 2개 → 주의 1건(심각도가 성분 중복보다 낮음).

### User Story 3 - 오프라인 동작 (Priority: P3)
첫 로딩 후 네트워크 없이도 검색·중복 탐지가 된다(PWA 캐시 + 번들 .sqlite).

**Independent Test**: 오프라인 모드에서 Story 1 시나리오 재현.

### Edge Cases
- 같은 약을 두 번 추가(동일 품목) → 중복 품목 안내, 성분 중복과 구분.
- 복합제(성분 여러 개) 안 일부 성분만 겹침.
- 규칙 `reviewed: false` → "검수 전" 배지.
- DB 로드 실패 → 탐지 결과를 보여 주지 않고 오류 + fail-safe 문구.

## Requirements *(mandatory)*

### Functional Requirements
- **FR-001**: 앱은 sql.js로 번들 SQLite(합성 데모 데이터, MIT)를 브라우저에서 로드해야 한다.
- **FR-002**: 데모 데이터는 자체 작성 합성 품목·성분·계열만 포함하며 실제 품목명/공공누리 제2유형 원본을 포함하지 않는다.
- **FR-003**: 사용자는 데모 품목을 검색·추가·삭제할 수 있어야 한다(로컬에만 저장, 서버 전송 없음).
- **FR-004**: 시스템은 성분 단위 중복(복합제 포함)을 탐지해야 한다.
- **FR-005**: 시스템은 계열 단위 중복 주의를 성분 중복과 다른 심각도로 표시해야 한다.
- **FR-006**: 모든 경고는 규칙 ID, 심각도, 데이터 출처(“합성 데모 데이터”), 검수 상태를 보여야 한다.
- **FR-007**: 매칭 불확실·데이터 누락 시 "판단 불가" fail-safe 문구를 표시해야 한다.
- **FR-008**: 화면에 "질병의 진단·치료를 목적으로 하지 않으며 처방을 하지 않습니다"와 개인 개발자 비관계 고지를 표시해야 한다.
- **FR-009**: 금지 표기(의료인 직함) 검사와 규칙별 양성/음성 테스트가 CI에서 통과해야 한다.
- **FR-010**: 광고·결제·외부 분석 SDK를 포함하지 않는다.

### Out of Scope (다음 피처)
- **002 QR 입력**(약봉투/처방전 QR 스캔 → 품목 추가) — 다음 `/speckit-specify` 대상.
- DUR 병용 금기 성분쌍·복용 간격 규칙(이후 피처), OCR·.ics(v2 백로그).

### Key Entities
- **Product(데모 품목)**: id, 표시명(합성), 성분 목록.
- **Ingredient(성분)**: id, 표준명(합성), 계열 코드.
- **DuplicateRule**: id, 유형(성분/계열), 심각도, 근거, 버전, reviewed.
- **UserMedList**: 로컬 저장 품목 목록(PHI 없음).

## Success Criteria *(mandatory)*
- **SC-001**: 데모 시나리오(중복 있음/없음/판단 불가) 3종이 1분 안에 재현된다.
- **SC-002**: 규칙 테스트 100% 통과, 금지 표기 0건.
- **SC-003**: 오프라인에서 검색·탐지 결과가 1초 이내 표시(중간급 모바일 기준).
- **SC-004**: 레포에 공공누리 제2유형 원본·시크릿·PHI 0건(CI 검사).

## Assumptions / [NEEDS CLARIFICATION]
- [NEEDS CLARIFICATION] 계열 중복의 기준 분류 체계(합성 데모용 자체 코드 vs 공개 분류 체계 모사).
- [NEEDS CLARIFICATION] 사용자 목록 저장소(localStorage vs IndexedDB) — plan 단계에서 결정.
- [NEEDS CLARIFICATION] 데모 품목 수(예: 30~50개) 및 David 검수 범위.
