<!--
Sync Impact Report
==================
Version change: (none) → 1.0.0
Rationale: Initial ratification. No prior constitution existed at
.specify/memory/constitution.md, so this is the first adopted version.

Modified principles: (none — initial adoption)

Added sections:
- Core Principles
  - I. 폐쇄망 자립성 (NON-NEGOTIABLE)
  - II. 데이터 주권과 PHI 경계 (NON-NEGOTIABLE)
  - III. 로그·리포트 마스킹
  - IV. 제어 평면과 데이터 평면 분리
  - V. 실행 추적성과 버전 기록
- 명명 규칙 (Naming Conventions)
- 준수 게이트 (Compliance Gates)
- Governance

Removed sections: (none)

Templates requiring review (not modified by this command; they read the
constitution at runtime):
- .specify/templates/plan-template.md
- .specify/templates/spec-template.md
- .specify/templates/tasks-template.md
- .specify/templates/checklist-template.md

Deferred items / TODOs:
- TODO(PREFECT_SERVING_EXCEPTION): 원칙 IV는 "모델 서빙·학습은 Prefect 밖"을 규정하되
  18장 미결 1을 유일한 예외 창구로 참조한다. 18장 문서가 이 저장소에 아직 없어 예외의
  범위·승인 절차를 확정하지 못했다. 18장 미결 1이 확정되면 원칙 IV를 개정해야 한다.
-->

# EVIX Site Stack Constitution

병원 내부망에서 완결적으로 동작하는 단일 기관용 데이터·AI 플랫폼의 최상위 규범이다.
이 문서의 규칙은 구현 시 위반할 수 없는 제약이며, 설계·리뷰·배포의 판정 기준이다.

## Core Principles

### I. 폐쇄망 자립성 (NON-NEGOTIABLE)

시스템 전체가 병원 내부망 안에서 인터넷 없이 동작해야 한다.

- Control Web App을 포함한 **모든** 소프트웨어 구성 요소는 병원 내부망에서 실행되어야
  한다(MUST). 런타임에 외부 네트워크로 나가는 경로를 설계에 포함해서는 안 된다(MUST NOT).
- 외부 자산(vocabulary, 모델 가중치, 패키지, 컨테이너 이미지)은 오프라인 반입 절차로만
  들어와야 한다(MUST). 빌드·배포·런타임 어느 단계에서도 외부 레지스트리·저장소·CDN을
  직접 조회해서는 안 된다(MUST NOT).
- 형상 관리(Gitea)를 포함한 모든 구성 요소가 병원 안에 존재해야 한다(MUST). 외부 원격
  저장소에 의존해서는 안 된다(MUST NOT).
- 단일 병원 내 완결 솔루션이다. 다기관 연결·중앙 관리, 기관 간 데이터·모델·설정 동기화는
  범위 밖이며 설계·구현해서는 안 된다(MUST NOT).

Rationale: 병원 내부망은 물리적으로 인터넷과 분리된다. 외부 의존이 하나라도 남으면 배포
자체가 불가능하고, 다기관 가정은 데이터 주권 경계를 무너뜨린다.

### II. 데이터 주권과 PHI 경계 (NON-NEGOTIABLE)

환자 데이터는 병원 밖으로 나가지 않으며, PHI 접근은 계층별로 제한된다.

- 환자 데이터는 어떤 경로로도 병원 밖으로 나가서는 안 된다(MUST NOT). 유일한 예외는 SDM
  워크스페이스의 연구용 반출물이며, 이 경우 별도 통제 절차를 반드시 거쳐야 한다(MUST).
- `{ws}_landing`, `{ws}_work` 계층은 제한 롤만 접근할 수 있어야 한다(MUST).
- SDM 워크스페이스는 `{ws}_mart`까지 실명 기반이므로 **전 계층**이 제한 대상이다(MUST).
- 반출 대상이 될 수 있는 것은 연구용 SDM뿐이다. 그 외 워크스페이스·계층의 반출 기능을
  구현해서는 안 된다(MUST NOT).

Rationale: PHI 노출은 되돌릴 수 없는 사고다. 경계를 계층과 워크스페이스 단위로 못 박아야
접근 제어를 코드와 롤 정의에서 검증할 수 있다.

### III. 로그·리포트 마스킹

실패 로그와 검증 결과는 원본 데이터를 그대로 담기 쉬우므로, 기본값이 마스킹이어야 한다.

- flow 레벨 로깅 필터에서 정형 식별자(주민등록번호, 환자번호, 전화번호) 패턴을 마스킹해야
  한다(MUST). 대상에는 최소한 dlt 적재 실패 레코드, dbt 실패 SQL·샘플 값, GX Core
  `unexpected_values`가 포함된다.
- 웹에 노출하는 로그는 오류 유형과 발생 위치만 추출한 요약본으로 제한해야 한다(MUST).
  전체 로그는 운영자 롤만 접근할 수 있어야 한다(MUST).
- GX Core는 `unexpected_values` 저장을 비활성화하거나, 마스킹한 뒤에만 저장해야 한다(MUST).

Rationale: PHI 유출의 실제 경로는 데이터 파이프라인이 아니라 실패 경로다. 마스킹을
사후 처리가 아닌 로깅 필터 기본값으로 두어야 누락이 생기지 않는다.

### IV. 제어 평면과 데이터 평면 분리

오케스트레이션·API·서빙의 책임 경계를 넘나드는 호출을 금지한다.

- Prefect는 ETL만 오케스트레이션해야 한다(MUST). 모델 서빙과 학습은 Prefect 밖에 있어야
  한다(MUST) — 단, 18장 미결 1에서 확정되는 예외에 한한다.
- ETL은 AI 컴포넌트를 직접 호출해서는 안 된다(MUST NOT). 반드시 Backend API를 경유해야
  한다(MUST).
- vLLM은 무상태여야 한다(MUST). DB에 접근해서는 안 된다(MUST NOT).
- 대용량 데이터는 제어 평면을 통과해서는 안 된다(MUST NOT). 제어 평면은 참조(경로,
  식별자, 상태)만 주고받고, 실체는 object store와 DB에 머문다.

Rationale: 경계가 흐려지면 장애 격리와 권한 통제가 동시에 무너진다. 특히 대용량 데이터가
제어 평면을 지나가면 오케스트레이터가 병목이자 PHI 경유지가 된다.

### V. 실행 추적성과 버전 기록

모든 실행은 어느 워크스페이스의 것인지, 어떤 버전이 만들어낸 결과인지 식별 가능해야 한다.

- 모든 Prefect flow run에는 `workspace_id` 태그가 붙어야 한다(MUST). 예외는 없다.
- AI 컴포넌트의 모든 결과에는 모델 버전과 프롬프트 버전이 함께 기록되어야 한다(MUST).
  둘 중 하나라도 없는 결과는 저장·노출해서는 안 된다(MUST NOT).

Rationale: 워크스페이스 태그는 PHI 경계와 비용·감사 추적의 기준선이다. 모델·프롬프트
버전 없이는 AI 결과의 재현과 사후 검증이 불가능하다.

## 명명 규칙

명명은 식별자이자 계약이다. 아래 규칙은 코드·설정·운영 문서에서 동일하게 적용된다.

- 워크스페이스 ID: 소문자 슬러그. 한번 정해지면 변경 불가(MUST NOT change).
- Qdrant 컬렉션: `concept_{vocabversion}_{modelname}_{modelversion}`
- OpenSearch 인덱스: Qdrant와 동일 규칙 + 별칭(alias) 전환으로 무중단 교체
- Object store 경로: `{ws}/{layer}/{yyyymmdd}/{flow_run_id}/`

Rationale: vocabulary·모델 버전을 이름에 박아 두면 재색인과 롤백이 이름 교체로 끝난다.
워크스페이스 ID 불변성은 경로·태그·권한이 모두 그 위에 쌓이기 때문에 필요하다.

## 준수 게이트

- 모든 스펙·플랜·구현 리뷰는 위 원칙 위반 여부를 명시적으로 확인해야 한다(MUST).
  위반이 있으면 해당 항목은 머지될 수 없다.
- 외부 자산 반입은 오프라인 반입 절차 기록(자산명, 버전, 출처, 반입일)을 남겨야 한다(MUST).
- 새 워크스페이스·계층·롤을 추가할 때는 원칙 II의 접근 제한 대상 여부를 함께 명시해야
  한다(MUST).
- 웹에 새로운 로그·리포트 노출 지점을 추가할 때는 원칙 III의 요약본 제한과 마스킹 적용을
  증명해야 한다(MUST).

## Governance

- 이 헌법은 다른 모든 관행·문서·관습에 우선한다. 충돌 시 헌법이 이긴다.
- 개정 절차: 변경 제안은 (1) 변경 사유, (2) 영향받는 원칙과 구성 요소, (3) 기존 구현의
  마이그레이션 계획을 문서화해야 하며, 승인 후에만 반영된다.
- 버전 정책은 유의적 버전(semantic versioning)을 따른다.
  - MAJOR: 원칙의 제거 또는 하위 호환을 깨는 재정의
  - MINOR: 원칙·섹션 추가 또는 실질적으로 확장된 지침
  - PATCH: 문구 명확화, 오타 수정, 의미를 바꾸지 않는 정제
- NON-NEGOTIABLE로 표시된 원칙(I, II)은 예외를 둘 수 없다. 예외가 필요하다면 그것은
  예외가 아니라 개정 사안이다.
- 준수 검토는 각 기능의 스펙·플랜 단계와 머지 전 리뷰에서 최소 두 번 수행한다.
- 복잡도는 정당화되어야 한다. 원칙을 우회하려는 설계는 그 자체로 결함이다.

**Version**: 1.0.0 | **Ratified**: 2026-08-28 | **Last Amended**: 2026-08-28
