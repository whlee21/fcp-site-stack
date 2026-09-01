# Specification Quality Checklist: 워크스페이스 유형 세분화

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-09-01
**Updated**: 2026-09-01 (파생 워크스페이스의 참조 자산 원천 확정 — 유형 7종, FR-062까지, clarification 5건 전부 해소)
**Superseded**: 2026-09-01 — 유형 체계(FR-001, FR-008~016, FR-050)가 기능 011로 대체됨. 나머지 요건은 유효. spec.md 상단 개정 고지의 대응표 참조
**Constitution v3.0.0 (2026-09-01)**: FR-010(가명 SDM 부분)·FR-038·SC-006 폐기 — 가명 SDM mart 개방 무효. rolling/frozen CDM mart 개방은 유효(TODO(CDM_MART_ASYMMETRY))
**Constitution v4.0.0 (2026-09-01)**: FR-010·FR-038·SC-006 **전면** 폐기 — v3.0.0의 가명 SDM 부분 폐기가 계열 무관으로 확대되어 rolling/frozen CDM mart 개방까지 폐기. US2 Independent Test·시나리오 2 개정
**Constitution v4.2.1 (2026-09-01)**: 프로젝트·추출물 전제 서술 2건(엣지 케이스, Assumptions) 범위 밖 표시
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

- [x] 원칙 I 폐쇄망 자립성: Vocabulary 관리 워크스페이스를 오프라인 반입 절차의 자리로
      정의했고(FR-023), 외부 레지스트리 조회 경로를 두지 않는다. 용어집 배포 주기는 외부
      기관이 정하되 반입은 오프라인 절차의 결과로만 시작된다. 계열마다 독립 반입 기록을
      남겨(FR-056) "자산명·버전·출처·반입일" 게이트가 계열별로 성립한다
- [x] 원칙 II 데이터 주권과 PHI 경계: 유형별 제한 범위를 명시했고(FR-008~FR-010),
      대체키 매핑정보를 실명 SDM에만 두도록 못박았다(FR-011). 원외 반출 경로는 없다
- [x] 원칙 III 로그·리포트 마스킹: FR-026이 참조 자산 반입 시 식별정보 패턴을 걸러내고,
      FR-042가 화면·오류 메시지의 PHI 노출을 금지한다
- [x] 원칙 IV 제어 평면과 데이터 평면 분리: AI 호출 경로를 바꾸지 않는다고 명시했다
- [x] 원칙 V 실행 추적성과 버전 기록: 파생 실행의 workspace_id 태그가 쓰기 대상
      워크스페이스임을 명시했고(FR-018, FR-020), 생성 조건 기록에 매핑 사전 버전과 기준
      배포 버전 조합을 포함한다(FR-029). 가명 산출물은 업스트림 바인딩을 실행 시점 값으로
      승계·고정하고(FR-059, FR-060), 동결 스냅샷은 자기 바인딩이 업스트림 생성 조건과
      일치할 때만 동결된다(FR-061, FR-062)
- [x] 준수 게이트 "외부 자산 반입 기록(자산명, 버전, 출처, 반입일)": FR-023이 용어집
      이름·배포 버전·출처·배포일·반입일을 요구해 게이트를 충족한다
- [x] 준수 게이트 "새 워크스페이스·계층·롤 추가 시 접근 제한 대상 여부 명시": 여덟
      유형 전부에 대해 제한 범위를 FR로 명시했다. SDM 계열 용어·매핑 관리 워크스페이스도
      FR-013·FR-014로 제한 대상 여부를 명시했다

## Cross-Spec Consistency

- [x] 기능 001(워크스페이스 관리): FR-001·FR-008(유형 2종 → 7종)을 개정하는 관계임을 명시했고,
      FR-007·FR-017의 SDM 전 계층 제한이 실명 SDM으로 좁혀지는 근거를 적었다
- [x] 기능 004(워크스페이스 파이프라인): 용어매핑 단계의 AI 호출 경로를 바꾸지 않는다.
      004 US3이 SDM 변환에도 용어매핑과 vocabulary 버전 기록을 요구하나 그 vocabulary의
      관리 주체를 정하지 않았던 공백을 SDM 계열 관리 워크스페이스가 채운다
- [x] 기능 005(ETL 파이프라인 작성): "유형이 프로세스 구성을 결정하고 그 구성은 변경
      불가"라는 규칙을 그대로 따르며, 프로세스 구성이 6종으로 늘어난다. 새 배포 위의
      재매핑 절차는 005의 프로세스 구성에 위임했다(FR-047 주변)
- [x] 기능 008(가명 연구용 SDM): 가명 SDM이 별도 워크스페이스로 이동한다는 개정 관계와
      근거를 Assumptions에 명시했다. 008의 가명처리 게이트는 그대로 유지된다
- [x] 기능 009(타깃·프로젝트·실행 모델): 타깃의 rolling/frozen 속성은 유지되고 유형이
      허용 범위를 제약한다. frozen CDM 워크스페이스가 연도별 동결 타깃 다수를 갖는
      구조(FR-037)는 009의 타깃 모델과 그대로 맞물린다. FR-014(교차 워크스페이스 금지)가
      쓰기 제약으로 좁혀지는 완화를 명시했다. 추출물 접근의 프로젝트 멤버십 요구는
      FR-038의 mart 개방에 영향받지 않는다

## Notes

- 후속 지시 반영 (2026-09-01): 실명 SDM의 Vocabulary 관리·Mapping 관리 워크스페이스를
  유형에 추가해 유형이 6종에서 8종이 되었다. 참조 자산은 SDM 계열과 OMOP CDM 계열을
  갖고 계열 간 바인딩은 금지된다(FR-050~FR-055).
- 후속 지시 3 반영 (2026-09-01): 파생 워크스페이스의 참조 자산 원천을 확정했다. 가명
  SDM은 자체 바인딩 없이 업스트림 실명 SDM의 바인딩을 실행 시점에 승계하고, frozen CDM은
  OMOP CDM Mapping 관리의 확정 매핑 사전 버전을 자기 바인딩으로 갖는다. 동결 시 그
  바인딩이 업스트림 rolling CDM 타깃의 생성 조건과 일치해야 한다는 제약을 함께 두었다
  (FR-059~FR-062).
- 후속 지시 2 반영 (2026-09-01): SDM 계열의 용어집 관리와 매핑 관리를 "SDM 용어·매핑
  관리" 한 유형으로 합쳐 유형이 7종이 되었다. 합쳐도 배포본 확정이 매핑의 선행 조건이라는
  순서와 매핑 항목의 작성자·승인자 분리는 유지된다(FR-058). 계열 안의 워크스페이스 개수는
  계열마다 다를 수 있고, 계열 격리 규칙은 개수와 무관하게 동일하다.
- 계열 관련 clarification 2건 해소 완료 (2026-09-01 세션):
  - FR-056: 계열마다 독립 반입·검증·확정. 같은 배포본이라도 반입 기록은 계열별로 따로
  - FR-057: 계열 간 매핑 결과 재사용 금지. 각 계열에서 독립 작성·검토·승인
    (FR-055의 비교 조회는 확인 전용이며 옮기는 기능을 제공하지 않는다)
- Clarification 3건 해소 완료 (2026-09-01 세션):
  - frozen CDM 단위 → 워크스페이스 1개 + 연도별 동결 타깃 다수 (FR-037, FR-049)
  - mart 접근 → 워크스페이스 배정만으로 개방, 추가 롤 없음 (FR-038)
  - 참조 자산 → vocabulary는 외부 배포 용어집, mapping은 특정 배포 버전 위의 대응 작업.
    매핑 사전은 기준 배포 버전 조합을 필수로 갖고, 소비는 확정된 원본 참조
    (FR-023, FR-039, FR-043~FR-048)
- 이 스펙 확정 시 개정이 필요한 하위 문서:
  - 기능 001: 유형 목록 2종 → 6종(FR-001, FR-008), SDM 전 계층 제한을 실명 SDM으로
    한정(FR-007, FR-017)
  - 기능 008: 가명 연구용 SDM의 소재를 가명 SDM 워크스페이스로 변경
  - 기능 009: FR-014를 쓰기 제약으로 축소, 업스트림 읽기 허용 반영
- 전 항목 통과. `/speckit-clarify` 없이 `/speckit-plan`으로 진행 가능하다.
