# Specification Quality Checklist: 환자 단위 가명처리 실행

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (clarification 3건 전부 해소 — 전건 검수+일괄 승인, 실명 계층 원문 대조, 확정 집합=frozen 타깃. FR-062·SC-035까지 확장)
**Constitution v3.0.0 (2026-09-01)**: FR-005·FR-037 개정, FR-063 신설(임시 우회 경로 금지), SC-004 강화, SC-036·SC-037 신설. 연구자용 분석 워크스페이스는 Out of Scope로 이동
**Feature**: [spec.md](../spec.md)

## Content Quality

- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

> 주: 사용자 입력의 "DBT 스크립트"와 "LLM"은 구현 선택이 아니라 기존 플랫폼 구성이다.
> 본문에서는 각각 "변환 스크립트", "AI 컴포넌트를 통한 비식별화"라는 역할 이름으로 다뤘다.

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

- [x] 원칙 I — 원내 AI 컴포넌트: 외부 사업자 추론 서비스 호출 금지 (FR-024, SC-013)
- [x] 원칙 II — 게이트 유지: 재식별 위험 점검을 집합 단위로 보존해 게이트가 실질적으로
      성립하게 함 (FR-033, FR-036, SC-020). 환자 단위로 내렸으면 게이트가 무력화됐을 것
- [x] 원칙 II — 대체키 위치: 실명 측 제한 계층 한정 (FR-008, SC-005)
- [x] 원칙 II — 원문 위치: 자유서식 원문을 실명 제한 계층 밖으로 복제 금지, 검수자에게
      제한 롤 요구 (FR-054~FR-058, SC-030~SC-032)
- [x] 원칙 II — 미확정 산출물 격리: 연구자 접근 차단 (FR-005, SC-004)
- [x] 원칙 III — 로그 마스킹: LLM 요청·응답·실패 로그에 원문·식별정보 금지 (FR-020, SC-011)
- [x] 원칙 IV — 경유 호출: AI 컴포넌트 직접 호출 금지 (FR-017, SC-009)
- [x] 원칙 IV — 제어 평면 부하: 요청 단위를 환자 한 사람으로 한정 (FR-018, SC-012)
- [x] 원칙 V — 버전 기록: 모델·프롬프트 버전 없는 결과 저장 금지 (FR-019, SC-010)

## 충돌 해소 기록

이 스펙은 기존 스펙 두 곳의 명시적 규정을 뒤집는다. 근거와 함께 "이 기능이 대체하는 것"
절에 명시했다.

| 기존 규정 | 내용 | 이 스펙의 처리 |
| --- | --- | --- |
| 004 US3 AC12 | "환자 단위 파이프라인에는 가명처리 단계가 없다" | 가명처리 단계는 존재한다. 재식별 위험 점검은 여전히 파이프라인 밖 |
| 008 US1 / FR-033 | 가명처리는 누적 실명 SDM 전체 대상 배치 | 변환은 환자 단위, 확정은 집합 단위로 분리 |
| 008 FR-036 | 환자 단위 파이프라인과 별개 실행 단위 | 변환은 같은 파이프라인 안, 확정만 별개 실행 |
| 008 FR-019 | "자동 비식별화" (수행 주체 미지정) | LLM으로 구체화 + 헌법 제약과 실패 모드 요건 추가 |

## 중복 회피 확인

| 항목 | 소유 |
| --- | --- |
| 가명처리 지침·컬럼 판정·근거 규범·처리 방법 | 008 (FR-010에서 입력으로 참조) |
| 대체키 규칙, 축소 스키마 | 008 (FR-008에서 위치 제약만 추가) |
| 재식별 위험 기준값·판정 기준·심의 | 008 (FR-033에서 적용 위치만 지정) |
| 저작·dry-run·시뮬레이션·확정·버전 관리 | 005·011 (FR-009에서 참조) |

## Resolved Clarifications

| # | 쟁점 | 결정 | 반영 |
| --- | --- | --- | --- |
| 1 | 자유서식 검수 범위 | **전건 검수 + 신호 없는 건 일괄 승인** | FR-049~FR-053, SC-028~SC-029, US5 AC9~13 |
| 2 | 원문-결과 대조 | **원문은 실명 제한 계층에만**, 검수자가 제한 롤로 대조 | FR-054~FR-058, SC-030~SC-032, US3 AC8~9 |
| 3 | 확정 집합 수명주기 | **기능 009 타깃(frozen)** 으로 등록 | FR-048, FR-059~FR-062, SC-033~SC-035 |

미해결 항목 없음.

### 결정 1에 붙인 가드레일

"전건 검수 + 일괄 승인"은 그대로 두면 전건 검수가 이름만 남을 수 있다. 세 가지를 함께 넣어
책임이 남게 했다 — 신호 건은 일괄 승인에서 **제외**(FR-050), 개별 확인과 일괄 승인을
**구분 기록**(FR-052), 한 번에 승인 가능한 **건수 상한**(FR-053).

## Notes

- 모든 항목 통과. `/speckit-plan` 진행 가능하다.
- 헌법이 강제하는 해석(경유 호출, 원내 AI, 버전 기록, 게이트 유지)은 질문이 아니라
  Assumptions에 근거와 함께 확정했다.
- 이 스펙 확정 후 기능 004(US3 AC12)와 기능 008(US1, FR-033, FR-036, FR-019)의 개정이
  필요하다. 그대로 두면 세 스펙이 서로 모순된다.
