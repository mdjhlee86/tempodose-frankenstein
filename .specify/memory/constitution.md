<!--
Sync Impact Report
- Version: 0.0.0 (template) → 1.0.0 (initial ratification)
- Principles added: I–IX (all new)
- Templates: plan/spec/tasks templates unchanged; Constitution Check in plan-template must cite these principles
- Follow-up TODOs: none
-->
# TempoDose Lab (tempodose-frankenstein) Constitution

Kiroween 2026 · Frankenstein 카테고리 · 웹 PWA(GitHub Pages) · MIT.
상위 결정 문서: `/workspace/kiroween-2026/DECISIONS.md`, `SCOPE.md` (레포 밖 운영 문서). 충돌 시 DECISIONS가 우선한다.

## Core Principles

### I. 임상 안전 우선 (NON-NEGOTIABLE)
- 앱은 복약 정보 정리·주의 환기 도구이며 질병의 진단·치료를 목적으로 하지 않는다. 처방·용량 변경을 권고하지 않는다.
- 경고는 "확인 필요"(보수적) 방향으로만 실패한다(fail-safe): 데이터 누락·매칭 불확실 시 "안전함"으로 표시하지 않고 "판단 불가 — 약사·처방 의료진과 상의" 로 표시한다.
- 모든 경고 문구는 근거 규칙 ID와 데이터 출처를 함께 보여 준다.

### II. 중복약·상호작용 규칙은 데이터+테스트로만
- 성분 중복(같은 성분/같은 계열), 병용 금기·주의(DUR 성분쌍), 복용 간격 규칙은 코드에 하드코딩하지 않고 번들 DB(sql.js)의 규칙 테이블로 정의한다.
- 각 규칙은 ID·근거·심각도·버전을 가지며, 규칙마다 양성/음성 테스트 케이스가 최소 1개씩 있어야 한다.
- 규칙 변경은 별도 커밋·변경 기록(changelog)으로 남긴다.

### III. David 임상 검수 게이트 (NON-NEGOTIABLE)
- 새 규칙·규칙 변경·사용자에게 보이는 임상 경고 문구는 David의 검수 승인 전 `main`에 병합하지 않는다. 미검수 규칙은 `reviewed: false`로 표시되고 UI에 "검수 전" 배지가 붙는다.
- 검수 표기는 중립 용어(`reviewed`, "검수 완료")만 쓴다.
- 데모 제출 전 David-ready 체크리스트 통과가 필수다.

### IV. 데이터 라이선스와 프라이버시
- 레포에는 자체 작성 합성 데모/축소 데이터(MIT)만 둔다. 공공누리 제2유형 원본(DUR 파일데이터 등) 커밋 금지. 실제 출처 교체는 NOTICE에 적힌 상업 허용 출처(공개 API·제1유형 등)로 빌드 시에만.
- 시크릿·API 키·서명 키·PIN·환자 PHI·실명·실처방을 스펙·코드·커밋·이슈 어디에도 넣지 않는다. 미승인 API를 가정하지 않는다(오프라인 번들 DB로 동작).
- 기존 TempoDose Android 앱의 코드·에셋·규칙 JSON을 복사하지 않는다(참고만). 2026-10-30 이전 코드 사용 금지.

### V. 범위 고정: Frankenstein 본선 = sql.js 번들 + QR
- 본선 범위는 sql.js 번들 DB + QR(약봉투/처방전 스캔 입력)뿐이다. OCR·.ics 내보내기는 v2 백로그이며 본선 스펙에 넣지 않는다.
- 그린필드라도 DECISIONS/SCOPE 밖 기능 추가·전면 재설계 금지. Spec Kit 도입은 `.specify/`·`.clinerules/`·`specs/` 추가 성격이다.

### VI. 표기·운영 원칙
- 화면·문서·데이터 어디에도 의료인 직함 표기('의사', '전문의', 'physician', 'doctor')를 쓰지 않는다. CI에 금지어 검사 테스트를 둔다.
- 무료·무광고: 인앱 결제·광고 SDK·광고 ID·후원 유도 문구 없음.
- 출처 기관과 관계없는 개인 개발자임을 고지한다.

### VII. Spec-Driven 워크플로 준수 (NON-NEGOTIABLE)
- 순서: `/speckit-constitution` → `/speckit-specify` → (필요 시 `/speckit-clarify`) → `/speckit-plan` → `/speckit-tasks` → `/speckit-implement` → `/speckit-converge`. 각 단계 산출물 리뷰 후 다음 단계로.
- vibe 코딩으로 implement 단계를 건너뛰거나 tasks 없이 코드를 쓰지 않는다.

### VIII. 코딩 에이전트·모델
- 코딩은 Cline Pass의 DeepSeek Flash 또는 GLM 5.3 Flash만 사용한다(OpenRouter 대체 경로 폐기). 쿼터 소진 시 기존 공유 규칙의 cline-deepseek 폴백을 따른다. 해커톤 제출 빌드는 Kiro 크레딧 확보 후 Kiro로 진행한다.

### IX. 테스트·접근성
- 규칙 엔진은 테스트 우선(실패 테스트 → 구현). 오프라인 동작·한국어 UI·큰 글씨 가독성(종이 복약지도 스타일)을 수용 기준에 포함한다.

## Governance
- 이 헌법은 다른 관행보다 우선한다. 개정은 PR + 변경 사유 + David(또는 위임받은 CEO) 승인 + 버전 증가(SemVer: 원칙 삭제/재정의=MAJOR, 추가=MINOR, 문구=PATCH).
- 모든 plan은 Constitution Check 섹션에서 I–IX 준수를 확인한다. 위반은 Complexity Tracking에 사유를 기록해야 한다.

**Version**: 1.0.0 | **Ratified**: 2026-11-01 | **Last Amended**: 2026-11-01
