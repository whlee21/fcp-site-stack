# Specification Quality Checklist: 플랫폼 롤 체계 (초안)

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (clarification 3건 전부 해소 — 워크스페이스 스코프 확정자, 검수는 제한 롤만, SoD는 매핑에만)
**Constitution v4.2.1 (2026-09-01)**: 프로젝트 스코프 롤 2종이 범위 밖으로 이동. FR-009 범위 밖 표시, FR-029 확대, SC-019 신설
**개명 (2026-09-01, 헌법 v4.2.2)**: `프로젝트 스코프 롤` → `연구 프로젝트 스코프 롤` (22건). 데이터 생성 프로젝트의 롤은 별도 정의 예정
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

## Constitution Alignment (v4.0.0)

- [x] 원칙 II — 계층 제한: 운영 권한과 데이터 접근 분리 (FR-011, FR-013, SC-004, SC-005)
- [x] 원칙 II — 연구자 접근 분리: 연구자 롤을 정의하되 부여 불가, 데이터 워크스페이스
      접근 권한 부여 금지 (FR-029, FR-030, SC-010)
- [x] 원칙 III — 마스킹: 롤 화면에 환자 식별정보 노출 금지 (FR-037, SC-015)
- [x] 원칙 V — 추적성: 롤 배정·회수·제한 계층 조회의 감사 기록 (FR-033~FR-035, SC-011)
- [x] 준수 게이트 — "새 롤 추가 시 접근 제한 대상 여부 명시": 카탈로그가 롤마다 허용·금지
      행위를 기록하도록 요구 (FR-002)

## 기존 스펙의 롤 어휘 수집 결과

| 출처 | 기존 표현 | 이 카탈로그의 롤 |
| --- | --- | --- |
| 001 | 플랫폼 운영자, 운영자 롤 | 플랫폼 운영자 (전역) |
| 001, 010, 011, 012 | 제한 롤 | 제한 롤 (워크스페이스) |
| 005 | 검토 권한자 | **파이프라인 확정자** (워크스페이스 스코프) — 해소 |
| 011 | 플랫폼 운영자 롤 보유자(확정) | 〃 — 011 FR-038 개정 완료 |
| 008 | 검수 권한 | **제한 롤로 흡수** — 별도 검수자 롤 없음. 008 FR-023 개정 완료 |
| 012 | 제한 롤 보유자(검수) | 제한 롤 — 이미 정합 |
| 010 | 매핑 작성자 / 승인자 | 참조 자산 작성자 / 승인자 |
| 009 | 개설자 / 참여자 | 프로젝트 개설자 / 참여자 (009 소유) |
| 004 | 데이터 관리자 | 실행 요청자 로 흡수 (재확인 필요) |
| 008 | 제공 롤 | **소멸** — 008 clarification에서 제공 절차 제거 시 함께 제거됨 |
| 헌법 | 연구자 | 연구자 (부여 불가 상태) |

## 신규 제안 (기존 스펙에 없던 것)

- **감사자 롤** — 헌법 준수 게이트가 감사 이력 보존을 요구하나 조회 전용 주체가 어느
  기능에도 없다. 운영자에게 맡기면 감사 대상과 감사 주체가 같아진다. 불필요하면 덜어낼 수 있다.
- **파이프라인 확정자 롤** — Q1=A로 확정. 005의 "검토 권한자"에 이름과 스코프를 부여했다.
- **조회자 롤** — 데이터 접근 없이 메타·리포트만 보는 주체. 004·002·003이 암묵적으로
  전제하나 명명된 적이 없다.
- **저작자/확정자 분리** — 005는 두 행위를 규정하나 롤로 나누지는 않았다.

## Resolved Clarifications

| # | 쟁점 | 결정 | 반영 |
| --- | --- | --- | --- |
| 1 | 파이프라인 확정 주체 | **워크스페이스 스코프 `파이프라인 확정자` 롤** | FR-018, 카탈로그 표 |
| 2 | 검수 권한 구성 | **제한 롤만** — 별도 검수자 롤 없음 | FR-021, FR-022, SC-018, 카탈로그에서 검수자 롤 제거 |
| 3 | SoD 적용 범위 | **참조 자산 매핑에만 강제** | FR-027, FR-028, US3 재작성, SC-008, SC-017 |

미해결 항목 없음.

### 결정 3에 붙인 보완

자기 승인을 강제로 막지 않는 영역(파이프라인 확정, 가명처리 검수)에서는 **확정 기록의
저작자·확정자 기록이 유일한 사후 확인 수단**이다. FR-028에 "두 사람이 같아도 그 사실이
드러나야 한다"를 명시하고, SC-017로 식별 가능성을 요구했다. 이것이 없으면 SoD를 풀어준
영역이 추적 불가 영역이 된다.

## Notes

- 모든 항목 통과. 연쇄 개정 2건도 적용 완료되어 스펙 간 정합이 확보되었다.

## 연쇄 개정 (적용 완료, 2026-09-01)

| 스펙 | 항목 | 변경 |
| --- | --- | --- |
| 011 | FR-038 | ✅ "확정 권한을 플랫폼 운영자 롤 보유자로 한정" → 워크스페이스 스코프 파이프라인 확정자 롤 |
| 008 | FR-023 | ✅ "검수 권한을 가진 사람" → 제한 롤 보유자 (012 FR-056과 통일) |
| 012 | Dependencies | ✅ 008 FR-023 대응 서술 갱신 |
- 이 스펙은 사용자가 요청한 **초안**이다. 롤의 존재와 경계를 정하는 데 집중했고, 각 롤의
  세부 권한 항목은 전수 열거하지 않았다.

