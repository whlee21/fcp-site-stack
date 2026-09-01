# Specification Quality Checklist: 타깃·프로젝트·잡 모델

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (Run → Job 명칭 변경 반영)
**Constitution v4.0.0 (2026-09-01)**: 재검토 필요 — US4의 '연구자가 데이터 워크스페이스 안에서 프로젝트·추출물을 다룬다'는 전제가 개정된 원칙 II와 정합하는지 미확정. 요구사항은 변경하지 않음. spec.md 상단 재검토 고지 참조
**Constitution v4.2.1 (2026-09-01)**: 프로젝트·추출물 부분이 **범위 밖**으로 판정됨(추출물 소비자=연구자). US4, FR-026~033, FR-037·038, FR-045~052, 관련 Key Entities. 타깃·잡 모델은 범위 안. 삭제 없이 표시만 함
**개명 (2026-09-01, 헌법 v4.2.2)**: `프로젝트` → `연구 프로젝트` (145건). 운영 측의 `데이터 생성 프로젝트`와 구분하기 위함이며 범위 판정에 직결된다
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

## Constitution Compliance (v2.0.0)

- [x] 원칙 I 폐쇄망 자립성: 외부 네트워크 경로를 전제한 요구사항이 없다
- [x] 원칙 II 데이터 주권과 PHI 경계: 추출물의 원외 반출 경로를 금지했고(FR-031),
      입력의 "반출본" 표현을 원내 산출물로 재해석해 "반출" 용어를 쓰지 않는다.
      추출물 접근은 프로젝트 멤버십과 워크스페이스 계층 롤을 모두 요구한다(FR-029)
- [x] 원칙 III 로그·리포트 마스킹: FR-043이 화면·오류 메시지의 PHI 노출을 금지한다
- [x] 원칙 IV 제어 평면과 데이터 평면 분리: 대용량 데이터가 제어 평면을 지나가는
      요구사항이 없다. 레지스트리는 참조(식별자, 스키마명, 상태)만 다룬다
- [x] 원칙 V 실행 추적성과 버전 기록: 잡의 워크스페이스 귀속을 유지한 채
      타깃·프로젝트 귀속을 추가했고(FR-013), 생성 조건 기록에 모델·프롬프트 버전을
      포함한다(FR-020)
- [x] 명명 규칙: 워크스페이스 식별자 불변성을 유지하고, 타깃의 논리 이름과 물리
      스키마를 분리했다. 별칭 전환에 의한 무중단 교체 원칙과 일치한다
- [x] 준수 게이트 "데이터를 내보내는 지점": 새로 추가되는 내보내기 지점이 없다.
      복제(FR-050)는 병원 내부망 안 프로젝트 간 이동이며 접근 범위를 넓히지 않는다

## Cross-Spec Consistency

- [x] 기능 001(워크스페이스 관리): ADMET 제외 결정을 그대로 따르고, 워크스페이스 보존
      기간과 타깃 보존 만료일을 별개 정책으로 명시했다. 워크스페이스 멤버 배정(FR-014)이
      프로젝트 개설의 선행 조건임을 FR-045·FR-047로 연결했다
- [x] 기능 002(ETL 실행): 002의 실행 건(flow run)을 이 모델에서 잡(Job)이라 부르며,
      잡에 타깃(필수)·프로젝트(선택) 귀속을 추가하는 확장
      관계임을 명시했다
- [x] 기능 004(워크스페이스 파이프라인): CDM 스냅샷·manifest가 frozen 타깃과 생성 조건
      기록으로 대체되는 관계임을 명시했다
- [x] 기능 008(가명 연구용 SDM): FR-033이 제공 절차 부재를 명시적으로 요구해 008의
      "제공 승인·제공 데이터셋·제공 전용 계층·제공 롤·제공 이력 제거" 결정과 정합한다

## Notes

- Clarification 2건 해소 완료 (2026-09-01 세션):
  - 추출물의 범위 → 소유·보존 모델만. 제공 절차 없음 (FR-033)
  - 접근 판정 → 워크스페이스 멤버십 선행 + 프로젝트 개설자/참여자 권한 분리
    (FR-029, FR-045~FR-052)
- 후속 지시 반영 (2026-09-01): 실행 1회를 가리키던 이름 Run을 Job으로 바꿨다. 스펙
  디렉터리도 `009-target-project-job`으로 함께 바꿨다. 이름만 바뀌었고 대상·속성·규칙은
  그대로이며, 잡 아래에 재시도(attempt)를 두는 구조 변경은 포함하지 않는다.
- 후속 확인 대상: 헌법 v2.0.0의 TODO(INTERNAL_RELEASE_PROCEDURE)는 이 기능에서
  확정하지 않는다. 제공 절차가 필요해지면 별도 기능으로 다룬다.
- 모든 항목 통과. `/speckit-clarify` 없이 `/speckit-plan`으로 진행 가능하다.
