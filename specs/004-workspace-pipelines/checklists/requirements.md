# Specification Quality Checklist: 워크스페이스별 파이프라인 명세

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Created**: 2026-08-28
**Feature**: [spec.md](../spec.md)

## Content Quality

- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

## Requirement Completeness

- [ ] No [NEEDS CLARIFICATION] markers remain
- [x] Requirements are testable and unambiguous
- [x] Success criteria are measurable
- [x] Success criteria are technology-agnostic (no implementation details)
- [x] All acceptance scenarios are defined
- [x] Edge cases are identified
- [x] Scope is clearly bounded
- [x] Dependencies and assumptions identified

## Feature Readiness

- [ ] All functional requirements have clear acceptance criteria
- [x] User scenarios cover primary flows
- [x] Feature meets measurable outcomes defined in Success Criteria
- [x] No implementation details leak into specification

## Notes

- Items marked incomplete require spec updates before `/speckit-clarify` or `/speckit-plan`

### Validation iteration 1 — 2026-08-28

**ADMET 제외 지시 반영 (턴 중 수신)**

초안은 ADMET을 P1으로 두고 구현 범위 충돌을 마커로 남겼으나, 제외 지시를 받아 재작성했다.

- 사용자 스토리 6개 → 5개. ADMET 스토리 삭제, CDM이 P1로 올라오고 SDM 4개가 P2~P5.
- 구현 범위 마커 삭제. FR-007이 "ADMET 파이프라인을 제공하지 않는다"로 범위를 고정한다.
- 기능 001의 FR-001·FR-008(유형을 SDM·CDM으로 한정)과 일치하게 되어 두 명세 사이의
  충돌이 사라졌다. 001은 수정 불필요하다.
- 원문 12.3이 ADMET을 1차 구현 대상으로 제안한 사실과 제외 결정을 Assumptions에 기록했다.

**FAIL: No [NEEDS CLARIFICATION] markers remain**

2개 마커가 남아 있다.

- FR-023 — 동의 상태 정보의 출처 (scope + 법적 근거)
- FR-037 — 재식별 위험 기준값의 결정 주체와 변경 절차 (privacy, 규제 사항)

**FAIL: All functional requirements have clear acceptance criteria**

44개 FR 중 42개는 수용 시나리오 또는 성공 기준으로 검증 가능하다.
FR-023, FR-037은 답이 확정되기 전까지 수용 기준을 쓸 수 없다.

**원문에서 의도적으로 다르게 표현한 것**

- "dlt", "GX Core", "dbt", "DQD", "pg_parquet", "LLM"은 구현 기술명이라 각각 "적재",
  "적재 검증", "변환", "CDM 품질 검증", "스냅샷", "AI 컴포넌트"로 표현했다.
- SNOMED CT, LOINC, RxNorm, ATC, OMOP은 구현 기술이 아니라 도메인 표준 체계이므로
  그대로 두었다.

**사용자가 명시한 미결 사항 (질문 대상 아님)**

- 동의 철회 시 통지 의무 여부는 원문이 "IRB 확인 필요"로 표시했다. 우리가 결정할 수
  있는 사항이 아니므로 질문하지 않고 `TODO(IRB_WITHDRAWAL_NOTIFICATION)`로 남겼다.
  통지 기능은 IRB 확인 후 별도 기능으로 다룬다.

**Constitution alignment (v1.0.0)**

- 원칙 I (폐쇄망 자립성): vocabulary와 모델 가중치를 오프라인 반입 자산으로 전제하고
  버전 기록 의무만 정의 → 위반 없음
- 원칙 II (데이터 주권과 PHI 경계): FR-030(실명 SDM 전 계층 제한 롤), FR-040(반출
  대상을 가명처리 산출물로 한정), FR-034(자동 게이트 우회 불가) → 반영됨.
  헌법의 "연구용 SDM만 반출 대상"이 이 명세의 7단계 산출물을 가리킨다는 해석으로
  기능 001에서 남겼던 용어 불일치 우려가 해소됐다.
- 원칙 IV (제어 평면/데이터 평면 분리): FR-003이 "ETL은 AI 컴포넌트를 직접 호출하지
  않고 Backend API 경유"에 대응 → 반영됨
- 원칙 V (실행 추적성과 버전 기록): FR-004(모델·프롬프트 버전), FR-011(manifest),
  FR-027(vocabulary 버전), FR-041(반출 스냅샷 버전) → 반영됨.
  FR-012는 manifest 정보가 하나라도 없으면 스냅샷을 확정하지 않게 해 이 원칙을
  게이트로 강제한다.
- 명명 규칙(3.5): vocabulary 버전과 모델 버전이 기록 대상이라는 점이 컬렉션·인덱스
  명명 규칙과 맞물린다.

**기능 001·002·003과의 연결**

- FR-005 ↔ 001 FR-001·FR-003 (유형과 대상 표준 버전은 생성 시 확정, 불변)
- FR-015 ↔ 001의 "실행 주기" 설정. SDM은 이 항목을 사용하지 않는다는 해석을
  Assumptions에 기록했다. 001의 설정 항목 자체는 바꾸지 않는다.
- FR-030 ↔ 001 FR-007·FR-017 (SDM 전 계층 제한)
- SDM 요청 단위 실행 ↔ 002 FR-006 (워크스페이스별 순차 대기열). 여러 요청은 대기열에서
  차례로 처리된다.
- SDM 요청 취소 ↔ 002 FR-032·FR-033 (중단 실행의 적재분을 다음 실행이 삭제 후 재적재)
- 검증 게이트 ↔ 003의 두 검증 지점. SDM 3·6단계와 CDM 적재 검증·CDM 품질 검증이
  대응한다. SDM 8단계 재식별 위험 점검은 품질 검증이 아니라 반출 통제 게이트이므로
  003의 검증 결과에 포함되지 않는다는 해석을 Assumptions에 기록했다.

**후속 검토가 필요할 수 있는 지점 (이번 범위에서는 해석으로 처리)**

- 003은 검증 지점을 두 곳으로 고정했다. SDM은 게이트가 다섯 곳이고 그중 검증 성격은
  두 곳이라 현재 모델에 들어맞지만, 게이트가 늘어나면 003의 검증 지점 모델을 다시
  볼 필요가 있다.
