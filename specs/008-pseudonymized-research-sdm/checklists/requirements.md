# Specification Quality Checklist: 가명 연구용 SDM 생성과 내부 제공

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-08-31
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

### Validation iteration 1 — 2026-08-31

**이 명세가 만들어진 경위**

기능 004에서 SDM 워크스페이스를 환자 1명 단위 변환 전용으로 좁히면서 가명처리·재식별 위험
점검·내부 제공 요구사항 48개를 제거했다. 당시에는 "연구용 데이터는 CDM의 몫"이라고 잘못
판단해 폐기 후보로 다뤘으나, 이후 확인된 사실은 다르다.

> 가명 연구용 SDM은 OMOP CDM과 연구용이라는 목적은 같지만 **데이터 모델이 다르며,
> SDM을 가명화하여 생성한다.**

따라서 그 요구사항들은 폐기 대상이 아니라 **별도 산출물의 생성 경로**였다. 이 명세가 그
경로를 소유한다.

**004와의 처리 단위 차이 — 이 명세의 핵심**

| | 기능 004 (SDM 워크스페이스) | 이 명세 |
|---|---|---|
| 처리 단위 | 환자 1명 | 쌓인 실명 SDM **전체** |
| 계기 | 변환 요청 1건 | 연구 목적 발생 시 |
| 산출물 | 실명 SDM (누적) | 가명 연구용 SDM |
| 데이터 모델 | SDM 스키마 | **같은 SDM 스키마** (값만 가명) |

재식별 위험 점검이 이 명세에서 성립하는 것은 대상이 집단이기 때문이다. 환자 한 사람에게는
k-익명성 같은 기준을 적용할 수 없다. 004에서 이 게이트를 제거한 판단은 그 범위에서는
옳았고, 여기서는 반대로 필수다.

**복원한 내용의 출처**

이 명세의 가명처리·게이트·제공 요구사항은 004에서 제거된 것을 재구성한 것이다. **git에는
004의 원본 44개 FR 버전만 있고 이후 이터레이션 3·4·8의 보강분은 커밋된 적이 없어**, 세션
기록에서 재구성했다. 복원 대상은 다음과 같다.

- 이터레이션 3 — 가명처리 방안 문서 반영: 컬럼 판정표, 대체키 참조 무결성, 매핑정보 분리
  보관, 날짜 상대일수 변환, 자유서식 사람 검수 게이트
- 이터레이션 4 — 제공 경로 선택지 A: 제공 전용 계층 + 제공 롤 + 접속기록 + 롤 회수
- 이터레이션 8 — 철회 소급 제거 선택지 C: **복원하지 않았다.** 동의 환자 관리가 플랫폼
  역할이 아니게 되면서 철회 신호 자체가 존재하지 않아 적용 대상이 없다

**동의 관리 배제가 남기는 한계 (명세에 명시)**

동의하지 않았거나 철회한 환자의 데이터가 이미 쌓인 실명 SDM에 포함되어 있어도 시스템은
판별하지 못하며 그대로 가명처리 대상이 된다. 대상 환자를 거르는 책임은 004의 변환 요청
단계에서 요청자에게 있고, 이 명세는 그 결과물을 받는다. 이 한계를 Assumptions에 그대로 적었다.

**Constitution alignment (v2.0.0)**

- 원칙 II (데이터 주권과 PHI 경계): FR-010(주민등록번호 완전 삭제), FR-012(매핑정보 분리
  보관과 제한 롤), FR-014(절대일자 잔존 금지), FR-020(자유서식 검수 게이트), FR-033(실명
  데이터 제공 금지), FR-044(원외 반출 금지) → 반영됨. **원칙 II의 "연구용 SDM 산출물은
  병원 내부망 안에서만 연구자에게 내부 제공되어야 한다", "가명처리와 재식별 위험 점검은
  내부 제공의 통제 게이트로 유지되어야 한다"가 가리키는 대상이 바로 이 명세다.** 헌법 개정은
  필요하지 않다
- 원칙 I (폐쇄망 자립성): 문서 12단계의 다기관 결합·국외 이전이 원칙 I·II에 막혀 적용 대상이
  아니라는 판정을 Assumptions에 기록 → 위반 없음
- 원칙 V (실행 추적성과 버전 기록): FR-002(대상 확정 시점과 환자 수), FR-017(컬럼별 적용
  방법), FR-018(파기 기록), FR-021(검수 결과), FR-027(점검 결과), FR-030(기준값 변경 이력),
  FR-041·FR-042(제공 이력) → 반영됨
- 헌법 준수 게이트("새 계층 추가 시 접근 제한 대상 여부 명시"): 제공 전용 계층을 Assumptions에
  규정했다. 가명 연구용 SDM만 담으므로 실명 계층과 같은 제한 롤 대상이 아니며 건 단위 제공
  롤의 통제를 받는다

**기능 001·002·004·005와의 연결**

- FR-001 ↔ 004의 SDM 워크스페이스 누적 산출물
- FR-005 ↔ 005가 확정한 SDM 표준 스키마 정의 (17테이블 204컬럼)
- FR-006 ↔ `docs/2026 SDM ver2.31_가명처리.xlsx`의 컬럼 판정표
- FR-035·FR-036 ↔ 001의 워크스페이스 스코프 롤 체계
- FR-045 ↔ 002의 실행 건, 상태 조회, 워크스페이스 단위 순차 실행

**FAIL: No [NEEDS CLARIFICATION] markers remain**

1개 남았다.

- FR-046 — 가명 연구용 SDM이 어디에 놓이는지 (scope). 별도 워크스페이스 유형을 신설해 SDM
  워크스페이스의 누적 산출물을 입력으로 받는지, 아니면 004의 SDM 워크스페이스 안에 가명
  연구용 계층을 두는지. 전자면 004 FR-001(워크스페이스 간 산출물 공유 금지)에 예외가 필요하고
  001의 유형 정의도 갱신해야 한다. 후자면 004에서 방금 좁힌 SDM 범위를 다시 넓혀야 한다

**FAIL: All functional requirements have clear acceptance criteria**

46개 FR 중 45개는 수용 시나리오 또는 성공 기준으로 검증 가능하다. FR-046만 미확정이다.

**최종 규모**

FR 46개, SC 22개, 사용자 스토리 4개, 수용 시나리오 44개, 엣지 케이스 13개.

### Validation iteration 2 — 2026-08-31

**FR-046 해소 — 가명 연구용 SDM은 SDM 워크스페이스에 놓인다. 전 항목 통과.**

별도 워크스페이스 유형을 만들지 않는다. 따라서 004 FR-001("한 워크스페이스의 산출물을 다른
워크스페이스가 입력으로 사용해서는 안 된다")에 예외가 필요 없고, 001의 유형 정의도 바꿀
필요가 없다. 실명 SDM을 만드는 것도 소비하는 것도 같은 워크스페이스 안이다.

**신설 요구사항 (FR-046 ~ FR-048)**

- FR-046 가명 연구용 SDM은 SDM 워크스페이스 안에, 별도 워크스페이스 유형 금지
- FR-047 실명 SDM과 분리된 계층에
- FR-048 환자 단위 변환 파이프라인과 별개의 실행 단위

**SDM 워크스페이스의 계층 구성이 셋이 된다**

| 계층 | 내용 | 접근 통제 |
|---|---|---|
| 실명 SDM | 004의 환자 단위 변환이 쌓는 곳 | 제한 롤 |
| 가명 연구용 SDM | 이 명세가 만드는 곳 | 워크스페이스 롤 |
| 제공 전용 | 승인된 데이터셋 | 건 단위 제공 롤 |

헌법 준수 게이트가 요구하는 "새 계층 추가 시 접근 제한 대상 여부 명시"에 따라 셋 모두
Assumptions에 규정했다.

**⚠ 기능 002의 전제가 깨진다 — 기본값으로 처리하고 근거를 남김**

002 Assumptions는 "워크스페이스마다 실행 대상 ETL flow는 하나다"라고 전제한다. SDM
워크스페이스에 환자 단위 변환과 가명처리 두 파이프라인이 생기면서 이 전제가 맞지 않게 됐다.

질문으로 올리지 않고 **002의 워크스페이스 단위 순차 실행 규칙이 두 파이프라인에 함께 적용된다**로
기본값을 잡았다. 기존 명세와 일관되고 002를 고치지 않아도 되는 선택이다. 다만 결과를 분명히
적어 두었다.

> **가명처리 실행이 도는 동안 환자 단위 변환은 대기열에서 기다린다.**

대상 전체를 도는 작업이라 오래 걸릴 수 있어 운영상 부담이 된다. 두 파이프라인을 동시에
돌려야 한다면 002의 실행 모델을 확장해야 하며, 그 판단은 이 명세의 범위 밖이다. 004의
Assumptions에도 같은 내용을 적어 두 명세가 같은 이해 위에 있게 했다.

**최종 상태 — 체크리스트 16개 항목 전부 통과**

FR 48개, SC 22개, 사용자 스토리 4개, 수용 시나리오 44개. NEEDS CLARIFICATION 0개.
