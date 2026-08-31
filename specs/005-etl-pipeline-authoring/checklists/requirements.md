# Specification Quality Checklist: ETL 파이프라인 정의 저작과 확정

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

초안 작성 후 3개 마커가 남았다. FR-031(dry-run 대상 데이터), FR-040(운영자 검토 권한
범위), FR-041(SDM 목표 스키마 정체). 전부 scope 또는 security/privacy 영향이라 임의
기본값을 둘 수 없어 질문으로 올렸다.

**원문에서 의도적으로 다르게 표현한 것**

- "dlt 스크립트" → "적재 정의", "dbt 스크립트" → "변환 정의". 구현 기술명이라 도메인
  용어로 바꿨다. 기능 004가 manifest에 "변환 정의 버전"을 기록하도록 한 것과 같은 용어다.
- "AI 에이전트"는 그대로 두었다. 이 기능에서 초안을 작성하고 dry-run을 수행하는 행위
  주체이므로 구현 기술명이 아니라 역할 이름이다. 기능 002·004의 "AI 컴포넌트"는 ETL
  실행 중 호출 대상을 가리키는 말이라 층위가 다르다.
- "human-in-the-loop"는 "운영자 검토와 확정"으로 풀어 썼다.

### Validation iteration 2 — 2026-08-31

**세 마커 전부 해소. 전 항목 통과.**

**FR-031 (dry-run 대상 데이터) → 소스의 실제 데이터를 표본으로 읽는다**

이 선택은 값 수준 검증의 신뢰도를 얻는 대신 dry-run 경로를 PHI 접촉 지점으로 만든다.
헌법 원칙 II·III을 만족시키기 위해 통제 요구사항 네 개를 함께 넣었다.

- FR-028 표본 크기 상한
- FR-029 소스 커넥션 접근 통제 준수. 저작 권한만으로 소스 데이터를 임의 조회하는 경로를
  만들지 않는다
- FR-030 dry-run 종료 후 표본 미보존
- FR-034 결과 표시 마스킹. **실패 사유 요약도 적용 대상으로 명시했다** — 헌법 원칙 III이
  지적하듯 PHI 유출의 실제 경로는 실패 경로이고, 실패 사유에 문제가 된 값이 들어가기 쉽다

부수적으로 FR-035(빈 표본을 통과 근거로 삼지 않음)를 추가했다. 표본 기반 검증은 표본이
비었을 때 조용히 통과하는 실패 양식을 갖는다.

**FR-040 (운영자 검토 권한) → 편집 가능하되 편집 시 dry-run 재실행 필수**

- FR-040 직접 편집 허용
- FR-041 편집하면 dry-run 미통과 상태로 복귀, 재통과 전 확정 거부
- FR-042 자동 수정 재시도는 AI 초안에만 적용. 사람이 고친 것을 AI가 되돌리지 않는다
- FR-043 편집 이력(편집자·시각·전후 내용)을 정의 버전과 별개로 누적
- FR-045 확정 이력에 AI 초안 이후 사람 편집 여부 기록

마지막 항목이 이 선택의 핵심이다. 편집분은 AI 산출물이 아니므로 모델·프롬프트 버전만으로는
정의의 출처를 설명할 수 없다. 사람 편집 여부가 함께 있어야 헌법 원칙 V의 추적성이 완결된다.

**FR-041 (SDM 목표 스키마) → `docs/`의 SDM 표준 스키마 정의가 정한다**

사용자가 지목한 `docs/2026 SDM ver2.31.xlsx`를 확인했다. 실제 구조는 다음과 같다.

- 4개 구분(기본정보·진료정보·검사정보·건강정보) 아래 **17개 테이블, 204개 컬럼**
- 컬럼마다 자료형(VARCHAR(n), TIMESTAMP 등), KEY(PK), 필수여부(R/O)
- 컬럼마다 **KR CDI V3 / KR Core STU2 / FHIR 경로** 대응을 함께 보유
  (예: `patient.pat_id` → `Patient.identifier`)

이로써 기능 001과 004의 서술 차이가 해소된다. **둘은 충돌이 아니라 층위가 다르다.**

- 001의 "FHIR로 전송하기 위한 워크스페이스" = SDM 데이터의 **용도**
- 004의 "실명 기반 표준 데이터 모델로 변환" = **목표 스키마**
- FHIR는 목표 스키마가 아니라 SDM 각 컬럼에 대응하는 **전송 표현**이며, 그 대응 관계가
  SDM 스키마 정의 문서 안에 이미 들어 있다

반영 결과: FR-018이 유형별 목표 스키마의 출처를 확정하고(SDM 표준 스키마 정의 / OMOP
CDM), FR-019가 자료형·키·필수 여부를 초안 작성 입력으로 삼게 하며, FR-020이 필수 컬럼
미충족을 임의 값으로 채우지 않고 표시하게 한다. 목표 스키마 정의는 Key Entities에 추가하고,
헌법 원칙 I에 따라 오프라인 반입 자산으로 다룬다는 점을 Assumptions에 기록했다.

**최종 규모**

- FR 49개 (FR-001 ~ FR-049), 전부 수용 시나리오 또는 성공 기준으로 검증 가능
- SC 19개 (SC-001 ~ SC-019)
- 사용자 스토리 5개, 수용 시나리오 총 42개
- 엣지 케이스 14개

**Constitution alignment (v2.0.0)**

- 원칙 I (폐쇄망 자립성): AI 에이전트의 내부망 동작, 목표 스키마 정의의 오프라인 반입
  전제를 Assumptions에 기록 → 반영됨
- 원칙 II (데이터 주권과 PHI 경계): FR-010(스키마 조사를 메타데이터로 한정),
  FR-016(소스 명세 조회를 워크스페이스 롤로 통제), FR-028~FR-030(dry-run 표본의 상한·
  접근 통제·미보존) → 반영됨. 이터레이션 1에서 판정 보류했던 dry-run의 PHI 접촉 문제가
  해소됐다
- 원칙 III (로그·리포트 마스킹): FR-034가 dry-run 결과와 실패 사유 요약을 마스킹 대상으로
  명시 → 반영됨
- 원칙 IV (제어 평면/데이터 평면 분리): FR-022가 AI 에이전트 직접 호출을 금지하고 경유
  지점을 강제 → 반영됨. 저작은 ETL 실행이 아니지만 같은 규칙을 적용한 근거를 Assumptions에
  기록했다
- 원칙 V (실행 추적성과 버전 기록): FR-021(초안의 모델·프롬프트 버전, 없으면 저장 차단),
  FR-045(확정 이력의 모델·프롬프트 버전 + 사람 편집 여부) → 반영됨

**기능 001·002·004와의 연결**

- FR-001 ↔ 004 FR-005 (유형별 단계 구성은 생성 시 확정). 이 명세는 그 구성을 읽어 쓰고
  변경하지 않는다(FR-004)
- FR-018 ↔ 001의 대상 표준·대상 표준 버전 설정 (생성 시 확정, 변경 불가)
- FR-018 ↔ 004 FR-009 (CDM 용어매핑 대상 표준은 OMOP)
- FR-016 ↔ 001 FR-007·FR-017 (워크스페이스 스코프 롤)
- FR-008 ↔ 002의 실행 트리거. 확정되지 않은 정의는 실행 대상이 아니다
- FR-049 ↔ 002의 실행 건 단위. 실행 중 정의가 바뀌어도 시작 시점 버전을 끝까지 쓴다
- FR-023 ↔ 004 FR-028 (미매핑 값을 임의로 채우지 않는다). 004는 실행 중 데이터 값의
  미매핑을, 이 명세는 저작 시 컬럼 대응의 미매핑을 다룬다. 같은 원칙의 두 층위다

**다른 명세에 제안할 후속 작업 (이 명세의 범위 밖)**

- 기능 001 Assumptions와 기능 004의 SDM 서술에 "목표 스키마는 SDM 표준 스키마 정의,
  FHIR는 그 컬럼의 전송 표현"이라는 구분을 반영하면 세 명세의 용어가 일치한다. 지금은
  이 명세의 Assumptions에만 기록돼 있다.
- `docs/2026 SDM ver2.31_가명처리.xlsx`는 204개 컬럼을 직접식별자 67 / 준식별자 72 /
  민감정보 19 / 민감정보(자유서식) 8 / 비식별 38로 분류하고 유형별 처리 원칙을 정의한다.
  이는 기능 004의 7단계 가명처리와 8단계 재식별 위험 점검의 직접 입력이며, 004 FR-037의
  미해결 마커(재식별 위험 기준값의 결정 주체와 절차)와도 관련이 있다. 004 갱신 시 확인이
  필요하다.
- 004는 SDM 4단계(비정형구조화)와 5단계(용어매핑)에서 AI를 쓴다. 그 두 단계의 태스크
  정의도 이 저작 절차를 거치는지, 아니면 플랫폼이 제공하는 고정 동작인지는 명시하지
  않았다. 이 명세는 적재 정의와 변환 정의의 저작만 다루므로 후자로 읽히지만,
  `/speckit-plan` 단계에서 확인이 필요하다.
