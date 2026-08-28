# Specification Quality Checklist: 품질 리포트

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-08-28
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

3개 마커로 시작: FR-023(002 실행 이력과의 관계), FR-014(실패 원인 추적 정보 수준),
FR-015(이진 대 심각도).

### Validation iteration 2 — 2026-08-28 (전 항목 통과)

**FR-023 해소 — 저장소 분리**

실행 이력·상태 저장소(기능 002 소유)와 품질검증 결과 저장소(이 기능 소유)를 분리한다.
FR-026(실행 상태 복제 금지, 식별자 참조), FR-027(두 저장소 결합은 시스템 책임) 파생.
SC-014로 복제 0건을 검증한다.

원문의 "세 출처를 단일 테이블로 정규화"에서 실행 상태가 빠졌다. 정규화 대상은 적재
검증과 변환 검증 두 출처다. "조회 측이 출처별 형식을 해석하지 않는다"는 원문의 취지는
두 검증 출처에 대해 유지된다. 이 변경을 Assumptions에 근거와 함께 기록했다.

**FR-014 해소 — 마스킹된 샘플 값**

FR-031(마스킹 후에만 저장), FR-032(원본 복원 불가), FR-033(개수 상한),
FR-034(대상 계층 접근 권한자에게만 노출) 파생.

FR-034는 사용자가 선택지에 명시하지 않은 추가 제약이다. 헌법 원칙 II가 landing·work
계층과 SDM 전 계층을 제한 롤로 묶고 있는데, 마스킹된 샘플은 실패 건수와 달리 원본에서
파생된 값이므로 같은 경계를 따라야 한다고 판단했다. 건수까지는 권한과 무관하게 보인다.
근거를 Assumptions에 기록했으므로 불필요하다면 이 항목만 제거하면 된다.

**FR-015 해소 — 검증 도구가 부여한 심각도 값을 그대로 사용**

FR-028(출처 무관 공통 항목), FR-029(표시 순서는 시스템이 제공), FR-030(미통과 집계는
심각도 정의에 비의존) 파생.

여기에 원문 취지와의 긴장이 하나 있었다. 심각도 어휘가 도구마다 다르면 조회 측이
두 어휘를 해석해야 하고, 이는 "프론트엔드가 세 포맷을 각각 해석하게 만들지 않는다"는
원문과 충돌한다. 값 자체는 변형 없이 보존하되(FR-015), 정렬·우선순위에 필요한 표시
순서를 시스템이 값별로 정의해 함께 제공하는 것으로 풀었다(FR-029). 조회 측은 심각도
값을 해석하지 않고 표시만 한다.

또한 "미통과"를 통과하지 못한 규칙 전체로 정의해(FR-030) 집계가 도구별 심각도 어휘에
의존하지 않게 했다. 심각도별 내역은 그 안의 분해로 제공한다. 이에 따라 기존
"실패 규칙 수" 표현을 "미통과 규칙 수"로 통일했다.

**최종 상태**: 사용자 스토리 3개, FR 34개, SC 15개, 엣지 케이스 13개. 미해결 마커 0개.

**원문에서 의도적으로 다르게 표현한 것**

- "GX Core", "dbt", "Prefect"는 구현 기술명이라 각각 "적재 검증", "변환 검증",
  "오케스트레이션 실행 상태"로 표현했다.
- "단일 테이블로 정규화하여 저장"의 사용자 관점 결과를 FR-002·SC-002·SC-010에서
  검증 가능하게 적었다. 저장 요구사항 자체는 FR-001에 남겼다.

**Constitution alignment (v1.0.0)**

- 원칙 III (로그·리포트 마스킹): 이 기능이 헌법 16.2가 지목한 데이터를 정면으로 다룬다.
  FR-012(원본 값 저장·노출 금지), FR-031(마스킹 후에만 저장), FR-032(복원 불가),
  FR-013(정형 식별자 마스킹)이 "GX Core는 unexpected_values 저장을 비활성화하거나
  마스킹 후 저장"에 대응 → 반영됨. Q2가 B이므로 헌법이 허용한 두 경로 중 "마스킹 후
  저장"을 택한 것이다.
- 원칙 II (PHI 경계): FR-034가 마스킹된 샘플의 노출을 대상 계층 접근 권한에 묶음 →
  반영됨
- 원칙 V (실행 추적성): FR-004가 모든 검증 결과 항목의 워크스페이스·실행 회차 귀속을
  요구 → 반영됨
- 원칙 IV (제어 평면/데이터 평면 분리): 정규화 저장은 집계 결과와 마스킹된 샘플만
  다루며 검증 대상 데이터 자체를 옮기지 않는다 → 위반 없음

**기능 001·002와의 연결**

- FR-004·FR-020·FR-026 ↔ 002의 실행 건 식별자
- FR-021 ↔ 002 FR-026·FR-033 (취소된 회차와 재적재)
- FR-022 ↔ 001 FR-026·FR-027 (정리 후에도 이력 보존)
- FR-024 ↔ 001의 워크스페이스 롤 배정
- FR-034 ↔ 001 FR-016·FR-017 (계층별 제한 롤)
- Assumptions에 002의 "검증" 실패 단계가 적재 검증과 변환 검증을 함께 가리킨다는
  해석을 기록했다. 002의 단계 구분은 바꾸지 않는다.

**002 spec 수정 필요 여부: 없음**

저장소를 분리했으므로 002의 FR-020~025(실행 이력 저장·조회)는 그대로 유효하다.
003이 002의 저장 책임을 가져가지 않는다.
