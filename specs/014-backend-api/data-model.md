# Phase 1 — Data Model (제어 평면)

**Feature**: Backend REST API 레이어 | **Date**: 2026-09-01

이 서비스의 PostgreSQL 스키마는 **제어 평면 메타데이터 전용**이다. 환자 데이터, 원천 값,
정의 원문, 실행 로그 원문은 여기에 저장하지 않는다(헌법 II·III·IV).

**소유하지 않는 것** — 각각의 정본이 다른 곳에 있다.

| 대상 | 정본 |
| --- | --- |
| 사용자 계정, 롤 객체, 사용자–롤 소속 | Keycloak (001 FR-021) |
| 정의 원문(dlt·dbt·workflow) | Gitea (005) |
| 잡 실행 상태·로그 | Prefect (R4) |
| 워크스페이스 데이터 계층 | 각 워크스페이스 스키마 — **API는 연결하지 않는다** |
| 타깃의 물리 스키마 내용 | 데이터 평면 |

---

## 엔티티

### Workspace — 워크스페이스

001·011이 정의한 최상위 귀속 단위.

| 필드 | 규칙 |
| --- | --- |
| `id` | UUIDv7, 내부 PK |
| `slug` | **자연키. 소문자 슬러그, 생성 후 불변**(헌법 명명 규칙, 001 FR-004) |
| `type` | `sdm_etl` / `sdm_vocab_mapping` / `omop_cdm_etl` / `omop_cdm_vocab_mapping`. 생성 후 불변 (011 FR-001·002) |
| `target_schema_name` / `target_schema_version` | 생성 시 확정, 불변 (011 FR-019·021) |
| `lifecycle_state` | `preparing` / `operational` (011 FR-040) |
| `retention_state` | `active` / `delete_marked` / `cleanup_failed` / `cleanup_done` (001) |
| `pinned_commit` | Gitea 커밋 해시. 운영자 승격으로만 변경 (005) |
| `created_at`, `deleted_at`, `retention_expires_at` | |

**불변식**
- `slug`, `type`, `target_schema_*` 는 UPDATE 경로를 제공하지 않는다
- `lifecycle_state = preparing` 인 동안 잡 접수 거부 (011 FR-041)
- `retention_state` 와 `lifecycle_state` 는 **직교**한다 (011 Key Entities)
- 참조 자산 관리 두 유형은 `target_schema_*` 를 요구하지 않는다 (011 FR-003)

### LayerGroup — 계층군

011이 도입하고 헌법 v4.2.0이 이름을 확정.

| 필드 | 규칙 |
| --- | --- |
| `workspace_id` | FK |
| `kind` | `real_name`(실명) / `pseudo`(가명) / `cdm` |
| `layers` | SDM ETL 실명: `{ws}_landing`,`_work`,`_mart` / SDM ETL 가명: `{ws}_pseudo` / OMOP CDM ETL: `_landing`,`_work`,`_mart` |
| `restricted` | **전부 `true`** (헌법 v4.2.1) |

**불변식**
- 참조 자산 관리 유형은 계층군을 갖지 않는다 (010 FR-013)
- `restricted = false` 인 행을 만들 수 없다 — 컬럼을 두는 이유는 값이 아니라 **명시적 판정**을
  기록하기 위함이다(헌법 준수 게이트)
- 계층 구성은 유형이 결정하며 생성 후 변경 불가 (011 FR-064)

### SourceConnection — 소스 커넥션

| 필드 | 규칙 |
| --- | --- |
| `workspace_id` | FK |
| `dsn_ref` | **비밀 정보 자체를 저장하지 않는다.** 시크릿 저장소 참조만 (001) |
| `verified_at`, `verify_status` | 저장 전 연결 확인 성공 필요 (001 FR-002) |

**불변식**: 병원 내부망 밖 주소는 거부한다 (001 FR-006, 헌법 I)

### ProvisioningSession — 프로비저닝 세션

011의 생성 절차 상태기계.

| 필드 | 규칙 |
| --- | --- |
| `workspace_id` | FK |
| `state` | `schema_selection` → `authoring` → `dry_run` → `simulation` → `review` → `confirmed` / `rejected` |
| `artifacts` | 저작 대상별 상태. SDM ETL은 **4종**(적재·변환·워크플로·자유서식 비식별화), OMOP CDM ETL은 3종 (011 FR-022·023) |
| `confirmed_by`, `confirmed_at` | 확정 기록 |
| `ai_generated_ranges` / `human_edited_ranges` | AI 생성분과 사람 편집분 구분 (011 FR-037) |

**상태 전이 규칙**
- 소스·목표 스키마 미지정 시 `authoring` 진입 거부 (011 FR-020)
- 저작 대상 중 하나라도 초안 없으면 `dry_run` 진입 거부 (011 FR-023)
- dry-run 미통과 시 `simulation` 진입 거부 (011 FR-025)
- 시뮬레이션 미통과 시 `confirmed` 거부 (011 FR-032)
- 자유서식 비식별화 시뮬레이션에서 잔존 식별정보 탐지 시 확정 거부 (011 FR-066)
- 사람이 편집하면 `dry_run` 미통과로 되돌아간다 (011 FR-036)
- `confirmed` 도달 시 워크스페이스가 `operational` 로 전환

### SimulationRun — 시뮬레이션 실행

| 필드 | 규칙 |
| --- | --- |
| `sample_source` | **실데이터 표본**(011 FR-048) |
| `sample_count` | 상한 초과 금지 (011 FR-049) |
| `field_coverage`, `in/out/dropped_counts`, `drop_reasons` | 집계값만 |
| `temp_area_ref`, `purged_at` | 임시 영역은 **제한 계층과 동등한 통제**(011 FR-050) |

**불변식**: 표본 원본 값을 저장하지 않는다. 화면 표시용 값도 마스킹 후에만 (011 FR-029)

### Target — 타깃

009의 범위 안 부분.

| 필드 | 규칙 |
| --- | --- |
| `logical_name` / `physical_schema` | **분리**. 앱이 물리 스키마명을 하드코딩하지 않고 레지스트리에서 해석 (009 US1) |
| `mode` | `rolling` / `frozen` |
| `cadence` | `monthly` / `yearly` / `on_demand` |
| `frozen_at`, `provenance` | 용어사전·변환 정의·원천 추출 시각·대상 표준 버전, AI 개입 시 모델·프롬프트 버전 (009, 헌법 V) |
| `retention_until`, `status` | 폐기 승인 없이 제거 불가 (009 FR-036) |
| `is_confirmed_set` | 012의 확정 집합이면 `true`. 목록에서 구분 표시 (012 FR-061) |

### Job — 잡

002의 실행 건 = 009의 잡.

| 필드 | 규칙 |
| --- | --- |
| `workspace_id` | **필수, 예외 없음** (헌법 V) |
| `target_id` | 필수 (009 FR) |
| `prefect_flow_run_id` | Prefect 참조 |
| `state` | `queued` / `running` / `succeeded` / `failed` / `cancelled` |
| `queue_position` | 워크스페이스 단위 순차 실행 (002 FR-006·028) |
| `wait_reason` | 대기 사유 (002 FR-050) |
| `failed_stage` | `load` / `validate` / `transform` / `ai_call` **4종 고정** (002 FR-051) |
| `error_summary` | **한 줄, 마스킹됨.** 원본 값 금지 (002, 헌법 III) |
| `execution_unit` | `patient_conversion` / `set_confirmation` (002 FR-049) |
| `resume_from_stage` | OMOP CDM ETL만. SDM ETL은 거부 (002 FR-036·037) |

**불변식**
- 한 워크스페이스에서 두 건 이상 동시 실행 금지 (002 FR-006)
- 두 실행 단위에 순차 규칙을 **함께** 적용 (002 FR-049)
- 확정 게이트 결과를 `state`·`failed_stage` 로 표시하지 않는다 (002 FR-052)

### RoleAssignment — 롤 배정

013이 정의한 카탈로그의 배정 연결. **롤 객체 자체는 Keycloak 소유.**

| 필드 | 규칙 |
| --- | --- |
| `subject_id` | Keycloak 사용자 식별자 |
| `role_key` | 카탈로그에 있는 롤만 (013 FR-003) |
| `scope` | `global` / `workspace` |
| `workspace_id` | 워크스페이스 스코프일 때 필수. 전이되지 않는다 (013 FR-015) |
| `is_restricted_role` | 제한 롤 여부를 **구분 기록** (013 FR-034) |

**불변식**
- 카탈로그에 없는 롤 배정 거부 (013 FR-003)
- 전역 롤 보유자 최소 1명 유지 — 마지막 회수 거부 (013 FR-014)
- **범위 밖 롤**(연구자, 프로젝트 개설자·참여자)은 배정 거부 (013 FR-029, 헌법 v4.2.1)

### AuditEvent — 감사 이력

| 필드 | 규칙 |
| --- | --- |
| `actor`, `at`, `action`, `before`, `after` | |
| `category` | `workspace` / `provisioning` / `job` / `role` / `restricted_access` |

**불변식**
- **append-only.** UPDATE·DELETE 경로를 제공하지 않는다
- 워크스페이스 정리·삭제 후에도 보존 (001 FR)
- 제한 롤 부여·회수와 제한 계층 조회를 별도 카테고리로 식별 (013 FR-034·035)
- 값에 환자 식별정보가 들어가지 않도록 마스킹 필터를 통과시킨다

---

## 저장하지 않는 것 — 명시적 금지

이 목록은 "아직 안 만든 것"이 아니라 **만들면 안 되는 것**이다.

| 대상 | 근거 |
| --- | --- |
| 환자 데이터, 원천 값 | 헌법 II |
| 실행 로그 원문 | 헌법 III, R4 |
| 시뮬레이션·dry-run 표본 값 | 011 FR-028·030 |
| 정의 원문 | Gitea 정본, R5 |
| 대체키 매핑정보 | 실명 계층군에만 존재 (011 FR-014) |
| 프로젝트·추출물 | 헌법 v4.2.1 범위 밖 |
| 소스 커넥션 비밀 정보 | 시크릿 저장소 참조만 |
