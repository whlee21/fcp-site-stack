# Contract — Jobs & Targets

**Base**: `/api/v1` · 출처: 002, 009(범위 안 부분)

## Jobs

| Method | Path | 설명 | 요구 롤 | 근거 |
| --- | --- | --- | --- | --- |
| `POST` | `/workspaces/{slug}/jobs` | 실행 요청. 진행 중이면 대기열 적재 | 실행 요청자 | 002 FR-006 |
| `GET` | `/workspaces/{slug}/jobs` | 이력. 실행 시각·결과 | 실행 요청자 | 002 |
| `GET` | `/jobs/{id}` | 상태. 진행률 없이 상태만. 대기 시 순번·사유 | 실행 요청자 | 002 FR-028·050 |
| `GET` | `/jobs/{id}/failure` | 실패 단계 + 오류 요약 1줄 | 실행 요청자 | 002 FR-051 |
| `GET` | `/jobs/{id}/logs` | **실패 건 한정, 운영자 롤 한정, 마스킹된 요약본** | 플랫폼 운영자 | 002, 헌법 III |
| `POST` | `/jobs/{id}:cancel` | 취소. 적재분은 즉시 삭제하지 않는다 | 실행 요청자 | 002 FR-026·032 |
| `POST` | `/workspaces/{slug}/jobs:rerun` | 재실행. OMOP CDM ETL만 `resume_from_stage` 허용 | 실행 요청자 / 시작 단계 지정은 운영자 | 002 FR-033·036~039 |

### 거부 규칙

| 요청 | 응답 | 근거 |
| --- | --- | --- |
| `lifecycle_state = preparing` 워크스페이스의 실행 요청 | `409` | 011 FR-041 |
| 대상 타깃 없는 실행 요청 | `422` | 009 |
| 같은 워크스페이스 두 건 동시 실행 | `409` (대기열 적재) | 002 FR-006 |
| SDM ETL에 `resume_from_stage` 지정 | `422` | 002 FR-036 |
| 완료되지 않은 단계를 시작 단계로 지정 | `422` | 002 FR-039 |
| 종료된 잡의 취소 | `409` | 002 FR-031 |
| 운영자 롤 없는 사용자의 로그 조회 | `403` | 002, 헌법 III |

### 불변 규칙

- 모든 flow run에 `workspace_id` 태그를 **API가 붙인다.** 클라이언트 지정 경로 없음(헌법 V)
- `failed_stage` 는 `load`/`validate`/`transform`/`ai_call` **4종 고정**. 가명처리는 별도 단계가
  아니라 `transform`(정형) 또는 `ai_call`(LLM 비식별화)로 매핑 (002 FR-051)
- 확정 게이트 결과는 잡 상태·실패 상세로 표시하지 않는다 (002 FR-052)
- SDM ETL의 두 실행 단위(`patient_conversion`, `set_confirmation`)에 순차 규칙을 **함께**
  적용한다 (002 FR-049)
- 로그 응답은 저장하지 않고 마스킹 필터 통과 후 전달만 한다 (R4)

## Targets

| Method | Path | 설명 | 요구 롤 | 근거 |
| --- | --- | --- | --- | --- |
| `GET` | `/workspaces/{slug}/targets` | 레지스트리. 논리 이름·물리 스키마·갱신 방식·상태·보존 만료일 | 조회자 | 009 SC-013 |
| `POST` | `/workspaces/{slug}/targets` | 등록. **소프트웨어 변경 없이 등록만으로 실행 대상** | 플랫폼 운영자 | 009 SC-001 |
| `PATCH` | `/targets/{id}` | 논리 이름 유지한 채 물리 스키마 교체 | 플랫폼 운영자 | 009 SC-002 |
| `GET` | `/targets/{id}/provenance` | 생성 조건 기록 | 조회자 | 009, 헌법 V |
| `POST` | `/targets/{id}:approve-disposal` | 폐기 승인 | 플랫폼 운영자 | 009 FR-036 |

### 거부 규칙

| 요청 | 응답 | 근거 |
| --- | --- | --- |
| 동결 타깃의 값 변경 | `409` | 009, 012 FR-062 |
| 승인 없는 폐기 | `409` | 009 FR-036 |
| provenance 항목이 빈 동결 타깃 생성 | `422` | 009 SC-005 |

> **범위 밖**: 프로젝트(Project)·추출물(Extract) 엔드포인트는 이 계약에 **존재하지 않는다**
> (헌법 v4.2.1 범위 경계). 관련 경로 요청은 `404` 다.
