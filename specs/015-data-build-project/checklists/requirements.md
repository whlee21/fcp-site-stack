# Specification Quality Checklist: 데이터 생성 프로젝트

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (clarification 3건 전부 해소 — 프로젝트 필수, 타깃 1:1, 스코프 롤 미신설)
**Feature**: [spec.md](../spec.md)
**기준 헌법**: v4.2.2 — 이 스펙은 `TODO(DATA_BUILD_PROJECT_MODEL)`의 확정 대상이다

## Content Quality

- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

*검증*: 언어·프레임워크·엔드포인트 언급 0건. 등장하는 기술어(`{ws}_landing` 계층 이름,
`workspace_id` 태그, 형상 관리 저장소의 커밋 식별자)는 모두 헌법 명명 규칙 또는 기능 005·009가
정한 **도메인 계약**이며, 저장소의 다른 스펙과 같은 취급이다.

## Requirement Completeness

- [x] No [NEEDS CLARIFICATION] markers remain — 3건 전부 해소
- [x] Requirements are testable and unambiguous
- [x] Success criteria are measurable
- [x] Success criteria are technology-agnostic (no implementation details)
- [x] All acceptance scenarios are defined — 5개 스토리에 36개 시나리오
- [x] Edge cases are identified — 13건
- [x] Scope is clearly bounded — Out of Scope 7건
- [x] Dependencies and assumptions identified — 의존 9건, 가정 10건

*검증*: `FR-001`~`FR-064` 중복·결번 0건. `SC-001`~`SC-022` 중복·결번 0건.
`grep -c "NEEDS CLARIFICATION" spec.md` → 0

## Feature Readiness

- [x] All functional requirements have clear acceptance criteria
- [x] User scenarios cover primary flows — 개설·파생 → 수정·확정 → 실행·산출 → 리베이스 → 조회·종료
- [x] Feature meets measurable outcomes defined in Success Criteria
- [x] No implementation details leak into specification

## Constitution Alignment (v4.2.2)

- [x] **범위 경계 — 배제 대상 여부**: 데이터 생성 프로젝트는 산출물이 물리 스키마이고 소비자가
      운영 측이므로 배제 대상이 아니다(헌법 v4.2.2 명시). 연구자 소비 경로를 만들지 않음을
      FR-057로 못 박았다
- [x] **명명 — 접두 없는 "프로젝트" 금지(MUST NOT)**: 본문의 접두 없는 사용 0건. 유일한 예외는
      사용자 입력 원문(`**Input**` 줄)과 규칙 자체를 인용한 한 곳이다
      *검증*: 공백·개행 정규화 후 `프로젝트` 전건의 접두 확인 → 위 두 곳 외 전부 접두 보유,
      이중 접두 0건
- [x] **원칙 II — PHI 경계**: 계층 구성과 제한 범위를 바꾸지 않고(FR-041), 원외 반출 경로를
      두지 않으며(FR-058), 가명 산출물은 012·008 게이트를 우회하지 못한다(FR-045)
- [x] **원칙 II — 시뮬레이션 임시 영역**: 실데이터 표본 사용 시 제한 계층과 동등한 통제와
      파기를 요구한다(FR-033, SC-019)
- [x] **원칙 III — 마스킹**: 실행 로그 화면 노출본에 마스킹·요약·분량 상한(FR-044, SC-017)
- [x] **원칙 IV — 제어 평면 분리**: 데이터 생성 프로젝트는 정의 단위이며 데이터를 통과시키지
      않는다. 산출물은 타깃 물리 스키마에 머문다(FR-035, FR-041)
- [x] **원칙 V — 추적성**: 모든 실행에 `workspace_id`(FR-038, SC-011), 타깃 생성 조건 기록에
      실제 사용된 고정 버전(FR-039, SC-010), 상태 전환 전건 감사(FR-055), 저작자·확정자 식별
      가능성(FR-064)
- [x] **준수 게이트 — 새 롤 추가 시 제한 대상 여부 명시**: 롤을 **추가하지 않기로** 확정했다
      (FR-059). 워크스페이스 스코프 롤이 모든 데이터 생성 프로젝트에 적용되며, 그 사실을 013
      카탈로그에 기록하도록 요구한다(FR-060)
- [x] **준수 게이트 — 데이터 내보내는 지점 추가 시 목적지 증명**: 내보내는 지점을 신설하지
      않는다(FR-058, SC-018)

## Resolved Clarifications

| # | 쟁점 | 결정 | 반영 |
| --- | --- | --- | --- |
| C1 | 데이터 생성 프로젝트가 필수인가 선택인가 | **필수** — 모든 ETL 실행이 경유한다 | FR-007(경유 강제·우회 경로 금지), FR-061(기존 워크스페이스 이행), SC-020, SC-022, Edge Case 2건 |
| C2 | 데이터 생성 프로젝트와 타깃의 대응 | **1:1** | FR-034(초과 보유 금지·재실행은 갱신), FR-062·FR-063(귀결로 프로젝트 간 파생 허용), SC-021 |
| C3 | 데이터 생성 프로젝트 스코프 롤 | **신설하지 않는다** | FR-059(미신설), FR-064(보상 통제), 013 개정 불필요로 확정 |

미해결 항목 없음.

### C1에 붙은 대가 — 이행 요구

경유를 강제하면 이미 운영 중인 ETL 워크스페이스가 전부 이행 대상이 된다. FR-061이 그 절차를
규정한다 — 확정된 기준선을 파생한 기본 데이터 생성 프로젝트를 만들고 기존 타깃을 연결하며,
이행 기록을 남긴다. 1:1(C2)이 이행에도 적용되므로 타깃이 여럿인 워크스페이스는 타깃마다
데이터 생성 프로젝트를 하나씩 갖는다.

### C2에 붙은 보완 — 프로젝트 간 파생

1:1이면 연도별 동결 타깃(`cdm_2024`·`cdm_2025`)마다 데이터 생성 프로젝트를 새로 만들어야 한다.
파생원을 워크스페이스 기준선으로만 두면 해마다 같은 편집을 반복하게 되므로, FR-062가 **같은
워크스페이스의 확정된 다른 데이터 생성 프로젝트 정의**를 파생 기준선으로 허용한다. 다른
워크스페이스에서 파생하는 것과 공통 조각 라이브러리는 Out of Scope에 남는다.

### C3에 붙은 보완 — 확정 기록이 유일한 사후 확인 수단

워크스페이스 스코프 롤이 데이터 생성 프로젝트를 구분하지 않으므로, 프로젝트별 최소권한 분리는
포기된다. FR-064가 "어느 프로젝트의 정의를 누가 저작·확정했는지 확정 기록으로 식별 가능"을
요구해 이를 보완한다. 기능 013이 SoD를 풀어준 영역(파이프라인 확정, 가명처리 검수)에 붙인
보완과 같은 형태다.

## 기본값으로 처리한 항목 (Assumptions에 근거 기재)

미해결로 세우지 않고 선례를 근거로 결정한 것들이다. 결정을 되돌릴 수 있도록 근거를 함께 남겼다.

| 쟁점 | 결정 | 근거 |
| --- | --- | --- |
| 기준선 승격의 전파 | 자동 전파 없음, 명시적 리베이스 | 기능 005의 고정 버전·승격 선례 |
| 잡의 필수 귀속 | 타깃 그대로 | 1:1(C2)이라 타깃에서 데이터 생성 프로젝트가 유일하게 유도됨 |
| 실행 동시성 | 워크스페이스별 단일 대기열 유지 | 같은 워크스페이스의 프로젝트들이 `{ws}_landing`·`{ws}_work`를 공유 |
| 계층 구성 | 프로젝트마다 계층을 만들지 않음 | 헌법 명명 규칙의 계층/타깃 물리 스키마 층위 구분 |
| 워크스페이스 "운영 가능"의 의미 | 기준선 확정 기준 그대로 | 기능 011 FR-040을 재정의하지 않음 |
| 확정 권한 주체 | 워크스페이스 스코프 `파이프라인 확정자` | 기능 013 FR-018 |
| 개설 자격 | 워크스페이스 배정자 | 기능 009의 개설 자격 형태 |

## Notes

- 모든 항목 통과. **`/speckit-plan` 진행 가능.**
- 확정 결과로 **연쇄 개정 3건**이 필요하다 — 011(FR-022·FR-023의 "1벌" 전제 + FR-041 경유 강제),
  009(타깃에 데이터 생성 프로젝트 1:1 연결), 005(고정 버전 소유 주체에 프로젝트 추가). 013은
  C3=미신설로 **개정 불필요**로 확정되었다. Dependencies 표에 대상과 사유가 있다.
- 헌법 `TODO(DATA_BUILD_PROJECT_MODEL)` 해제는 이 스펙에서 하지 않는다. 헌법 개정은 별도 절차
  (`/speckit-constitution`)이며 버전 승격을 동반한다.
- 기능 014의 `G1` 보류는 이 스펙 확정으로 풀린다. 014 계획에 이 도메인을 추가하는 것은 별도 작업이다.
