# Specification Quality Checklist: 워크스페이스 유형 간소화와 생성 시 파이프라인 등록

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (clarification 3건 전부 해소 — 실데이터 표본, 수동 폐기, 물리 병합. FR-063·SC-028까지 확장)
**Constitution v3.0.0 (2026-09-01)**: FR-011 개정(가명 계층군도 제한 대상), SC-004 분할, SC-035·SC-036 신설, FR-070 완화(넓어지는 것만 금지). Assumptions의 헌법 II 미결 항목 해소 — 방향은 계층 개방이 아니라 그 반대
**Constitution v4.0.0 (2026-09-01)**: FR-012 개정(OMOP CDM ETL mart도 제한 대상), SC-004 재정의, SC-036 확대, US2 서술·Independent Test·시나리오 8 개정. 계열 비대칭 해소를 Assumptions에 기록
**기능 013 (2026-09-01)**: FR-038 개정 — 확정 권한이 전역 플랫폼 운영자에서 워크스페이스 스코프 `파이프라인 확정자` 롤로 이동
**Constitution v4.2.0 (2026-09-01)**: 가명 계층군의 물리 계층을 `{ws}_pseudo`로 확정 (FR-010·FR-011·Key Entities)
**Feature**: [spec.md](../spec.md)

## Content Quality

- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

> 주: dlt, dbt, Prefect는 구현 선택이 아니라 사용자 입력에 명시된 기존 플랫폼 구성이며,
> 스펙 본문에서는 "적재 스크립트·변환 스크립트·워크플로 정의"라는 역할 이름으로 다룬다.

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

## Constitution Alignment (v2.0.0)

- [x] 원칙 II — PHI 경계 보전: 유형 7→4 통합으로 사라질 뻔한 계층 제한 판정 근거를
      식별 수준 속성으로 유지 (FR-009~FR-015, SC-003~SC-005, SC-019)
- [x] 원칙 II — 대체키 매핑정보 위치 제약 유지 (FR-014)
- [x] 원칙 III — 마스킹: 시뮬레이션 결과 표시에 요약·마스킹 요구 (FR-029, SC-013)
- [x] 원칙 I — 폐쇄망: 외부 조회 경로를 새로 만들지 않음. 시뮬레이션은 내부 격리 영역에서
      수행 (FR-027)
- [x] 원칙 II — 시뮬레이션 임시 영역: 실데이터 표본 사용 확정에 따라 제한 계층과 동등한
      접근 통제·표본 상한·파기 보증·감사를 요구 (FR-048~FR-053, SC-022~SC-024)
- [x] 명명 규칙 — ID 불변: 병합을 존속 ID 유지 + 흡수분 폐기로 정의해 저촉 회피
      (FR-059, SC-026)
- [x] 원칙 V — 추적성: 확정 기록에 확정자·시각·AI/사람 구분 (FR-037, SC-016)
- [x] 준수 게이트 — 새 유형 추가 시 접근 제한 대상 명시: 유형 4종과 식별 수준 조합별로
      제한 범위를 FR-010, FR-011에 명시

## 중복 회피 확인

이 스펙은 기능 005(ETL 파이프라인 저작)를 재규정하지 않는다. 아래는 005 소유로 남긴다.

| 항목 | 소유 |
| --- | --- |
| AI 에이전트 초안 작성 방식·품질 기준 | 005 (US3, FR-024에서 참조) |
| dry-run 동작 규칙·표본 상한·재시도 | 005 (US4, FR-025에서 진입 조건만 추가) |
| 정의 파라미터화 | 005 (US6) |
| Gitea 버전 관리와 실행 커밋 고정 | 005 (US7, FR-043에서 참조) |

이 스펙이 새로 더하는 것: 유형 4종 체계(FR-001~008), 식별 수준(FR-009~015), 생성 절차
편입과 소스·목표 스키마 지정(FR-016~021), 세 종류 완결성(FR-022~023), **샘플 데이터
시뮬레이션 단계**(FR-026~033), 준비중/운영 가능 상태(FR-040~042), 유형 이행(FR-044~047).

## Resolved Clarifications

| # | 쟁점 | 결정 | 반영 |
| --- | --- | --- | --- |
| 1 | 시뮬레이션 샘플 출처 | 소스 커넥션의 **실데이터 표본** | FR-033, FR-048~FR-053, SC-022~SC-024, US5 시나리오 7~9 |
| 2 | 중단된 준비중 워크스페이스 | **자동 정리 없음**, 운영자 명시적 폐기 | FR-054~FR-057, SC-025, US6 시나리오 7~9 |
| 3 | OMOP 관리 워크스페이스 둘 | **물리적 병합**, 존속 ID 유지 | FR-058~FR-063, SC-026~SC-028, US8 |

미해결 항목 없음.

## Notes

- 모든 항목 통과. `/speckit-plan` 진행 가능하다.
- 헌법 II가 강제하는 해석(실명/가명을 속성으로 보존)은 질문이 아니라 Assumptions에 근거와
  함께 확정했다. 이를 뒤집어 구분 자체를 없애려면 헌법 개정이 선행되어야 한다.
- 기능 010의 spec.md는 이 스펙 확정 후 유형 체계 절을 개정해야 한다. 010을 그대로 둔 채
  이 스펙만 머지하면 유형 체계가 두 벌 존재한다.
