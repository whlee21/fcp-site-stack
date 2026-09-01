# Specification Quality Checklist: 워크스페이스 관리

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-08-28
**기능 011 + Constitution v4.0.0 (2026-09-01)**: FR-001·FR-008 대체(유형 2종→4종), FR-003 용어 통일(대상 표준 버전→목표 스키마), FR-007 확대·Key Entities 개정(CDM mart도 제한 대상). spec.md 상단 개정 고지 참조
**Constitution v4.1.1 (2026-09-01)**: 계층 용어 정정 — 제한 계층의 고정 집합 읽기(landing, work) 제거. US2 서술·시나리오 2·3·FR-016 개정, FR-017은 FR-016에 흡수
**Feature**: [spec.md](../spec.md)

## Content Quality

- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

## Requirement Completeness

- [x] No [NEEDS CLARIFICATION] markers remain
- [x] Requirements are testable and unambiguous
- [x] Success criteria are measurable
- [x] Success criteria are technology-agnostic (no implementation details)
- [x] All acceptance scenarios are defined
- [x] Edge cases are identified
- [x] Scope is clearly bounded
- [x] Dependencies and assumptions identified

## Feature Readiness

- [x] All functional requirements have clear acceptance criteria
- [x] User scenarios cover primary flows
- [x] Feature meets measurable outcomes defined in Success Criteria
- [x] No implementation details leak into specification

## Notes

- Items marked incomplete require spec updates before `/speckit-clarify` or `/speckit-plan`

### Validation iteration 1 — 2026-08-28

3개 마커로 시작: FR-008(ADMET 생성 입력), FR-021(사용자·롤 출처), FR-029(보존 기간).

### Validation iteration 2 — 2026-08-28

- FR-008 해소 — ADMET을 범위에서 제외하고 유형을 SDM·CDM으로 한정.
- FR-021 해소 — 사용자·롤은 통합 인증·권한 서비스(Keycloak)가 소유, 이 기능은 배정만
  관리. FR-032, FR-033, SC-011, 수용 시나리오 2건, 엣지 케이스 2건 파생.
- FR-029 미해소 — 보존 기간의 성격 정의가 선행되어야 함으로 재정의.

### Validation iteration 3 — 2026-08-28 (전 항목 통과)

- FR-029 해소 — 보존 기간은 복구 유예 기간. 유형 무관 동일 값, 기관 설정 가능, 기본
  30일. FR-034(삭제 시점 만료일 고정), FR-035(설정 변경 감사), 엣지 케이스 2건 파생.
- 도메인 정의 반영 — SDM은 실명 기반 FHIR 전송용 개인 임상의료 정보, CDM은 가명 기반
  연구용 임상의료 데이터. 이 정의가 FR-007·FR-017의 SDM 전 계층 제한 근거가 됨.

**최종 상태**: 사용자 스토리 4개, FR 35개, SC 11개, 엣지 케이스 11개. 미해결 마커 0개.

**Constitution alignment (v1.0.0)**

- 원칙 I (폐쇄망 자립성): FR-006 외부망 소스 커넥션 차단. Keycloak 내부망 배포 전제를
  Assumptions에 기록 → 반영됨
- 원칙 II (PHI 경계): FR-007·FR-016·FR-017이 landing/work 제한과 SDM 전 계층 제한을
  고정 → 반영됨
- 원칙 III (로그·리포트 마스킹): FR-012, FR-031 → 반영됨
- 원칙 V (실행 추적성): FR-030 감사 이력, FR-004 식별자 불변성이 flow run `workspace_id`
  태그의 전제를 만족 → 반영됨
- 명명 규칙(3.5): FR-004의 소문자 슬러그·불변 규칙이 헌법과 일치

**후속 확인 필요 (이 기능 범위 밖)**

- 헌법 원칙 II의 "연구용 SDM만 반출 대상" 문구와 이번 도메인 정의(연구용은 CDM) 사이에
  용어 불일치가 있다. 반출 기능 명세 전에 헌법 문구를 확인해야 한다.

### Validation iteration 2 — 2026-08-31

**헌법 v2.0.0 개정 반영 — 후속 확인 항목 종결**

이터레이션 1에서 "후속 확인 필요"로 남겨 둔 용어 불일치를 종결했다. 헌법 원칙 II가
개정되어 가명화된 연구용 SDM을 포함한 예외 없는 원외 반출 금지가 되면서, "연구용 SDM만
반출 대상"이라는 문구 자체가 헌법에서 사라졌다. 반출 대상을 판정하는 문제가 없어졌으므로
확인 대상도 없다.

- Assumptions의 해당 항목을 갱신하고, "연구용"의 지시 대상이 기능별로 다르다는 점을
  명시했다. 이 기능에서 연구용은 CDM 워크스페이스의 성격이고, 기능 004에서 연구용은
  SDM 7단계 가명처리 산출물이다. 둘은 배타적이지 않다.
- FR-007·FR-017의 SDM 전 계층 제한 근거는 SDM이 mart 계층까지 실명 기반이라는 점이며,
  헌법 v2.0.0 원칙 II에서 그대로 유지된다. 요구사항 변경은 없다.
- "SDM 연구용 반출물의 통제 절차는 별도 기능" → "SDM 연구용 데이터를 병원 내부에서
  연구자에게 제공하는 통제 절차는 별도 기능(기능 004)"으로 표현을 맞췄다.

**Constitution alignment (v2.0.0)**

- 원칙 II (데이터 주권과 PHI 경계, 개정): FR-007·FR-016·FR-017의 landing/work 제한과
  SDM 전 계층 제한이 개정된 원칙에서도 그대로 유효하다 → 반영됨. 원외 반출 금지
  (헌법 FR 대응 사항)는 이 기능의 범위 밖이며 기능 004의 FR-045·FR-046이 담당한다.
- 원칙 V (실행 추적성과 버전 기록, 개정): 내부 제공 감사 기록 의무가 추가됐으나 제공
  절차는 기능 004 범위다. 이 기능의 FR-030 감사 이력 판정은 변경 없다.
- 원칙 I·III: 이번 개정과 무관하며 이터레이션 1의 판정이 유지된다.

**체크박스 상태**

이터레이션 1에서 전 항목 통과였고, 이번 갱신은 Assumptions 표현 정리에 그쳐 요구사항과
성공 기준을 바꾸지 않았다. 전 항목 통과가 유지된다.
