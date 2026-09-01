# Quickstart — 검증 가이드

**Feature**: Backend REST API 레이어 | **Date**: 2026-09-01

이 문서는 **구현이 끝났음을 증명하는 실행 절차**다. 구현 코드는 담지 않는다.

---

## 전제

원내 4종이 기동되어 있어야 한다. 개발 검증에서는 컨테이너로 띄운다.

| 의존 | 검증에 필요한 상태 |
| --- | --- |
| PostgreSQL 18 | 빈 DB 하나. 마이그레이션은 아래 절차가 적용 |
| Keycloak | 렐름 하나 + 롤 카탈로그(013)와 같은 이름의 롤들 |
| Prefect | 최소 1개 deployment 등록 |
| Gitea | 빈 저장소 하나 + 커밋 권한 토큰 |

```bash
# 마이그레이션
alembic upgrade head

# 기동
uvicorn fcp_api.main:app --host 0.0.0.0 --port 8000
```

---

## 1. 헌법 경계 검증 (가장 먼저 돌린다)

기능 검증보다 **먼저** 돌려야 한다. 여기서 실패하면 나머지가 통과해도 머지할 수 없다.

### 1-1. 제어 평면 경계 (헌법 IV)

```bash
pytest tests/contract/test_layer_boundaries.py
```

**기대**:
- `domain/` 의 어떤 모듈도 `adapters/`·`sqlalchemy`·`httpx`·`prefect` 를 임포트하지 않는다
- `adapters/db/` 가 만드는 엔진의 DSN이 **하나뿐**이고, 워크스페이스 데이터 계층
  (`*_landing`·`*_work`·`*_mart`·`*_pseudo`)을 향하는 커넥션이 생성되지 않는다

> 이 검사는 임포트 그래프를 정적으로 훑는다. 문서로만 둔 경계는 지켜지지 않는다.

### 1-2. 마스킹이 로거 기본값인가 (헌법 III)

```bash
pytest tests/integration/test_masking.py -v
```

**기대**: 주민등록번호·환자번호·전화번호 패턴을 포함한 문자열을 **여러 경로**(요청 핸들러,
백그라운드 태스크, 예외 핸들러, 기동 로그)에서 로깅했을 때 캡처된 출력에 원본이 하나도
남지 않는다. 마스킹 필터가 등록되지 않은 로거가 하나라도 있으면 실패한다.

### 1-3. 응답에 PHI가 없는가 (헌법 II)

```bash
pytest tests/contract/test_no_phi_in_responses.py
```

**기대**: 모든 엔드포인트의 응답 스키마에 자유서식 원문·표본 값·원천 레코드 필드가 없다.
OpenAPI 스키마를 훑어 금지 필드명이 등장하지 않음을 확인한다.

### 1-4. 범위 밖 엔드포인트 부재 (헌법 v4.2.1)

```bash
pytest tests/contract/test_scope_boundary.py
```

**기대**: `/projects`, `/extracts` 로 시작하는 경로가 OpenAPI 스키마에 **존재하지 않는다.**
연구자 롤·프로젝트 스코프 롤 배정 요청이 `422` 로 거부된다.

---

## 2. 도메인 흐름 검증

### 2-1. 워크스페이스 생성 → 프로비저닝 → 운영 가능

```bash
pytest tests/integration/test_provisioning_flow.py -v
```

**시나리오** (011 US1~US6):

1. `POST /workspaces` — `type=sdm_etl` 로 생성 → `lifecycle_state=preparing`
2. 이 상태에서 `POST /jobs` → **`409`** (011 FR-041)
3. `GET /provisioning/source-schemas` → 스키마 목록. 환자 데이터 행을 읽지 않았음을 확인
4. `PUT /provisioning/endpoints` — 소스·목표 스키마 지정
5. `POST /provisioning/artifacts:author` → **4종** 초안 생성(자유서식 비식별화 포함)
6. 3종만 있는 상태로 `POST /dry-run` → **`409`** + 빠진 종류 제시
7. dry-run 통과 → `POST /simulation` → 집계 결과. 원본 값 없음
8. 저작자 롤로 `:confirm` → **`403`**
9. 확정자 롤로 `:confirm` → `lifecycle_state=operational`, 확정 기록에 저작자·확정자 각각 기록
10. 확정 후 `POST /jobs` → 접수됨

### 2-2. 잡 순차 실행과 재실행 분기

```bash
pytest tests/integration/test_jobs.py -v
```

**기대** (002):
- 같은 워크스페이스에 두 건 요청 시 두 번째가 `queued` + 순번 + **대기 사유**
- SDM ETL의 `patient_conversion` 과 `set_confirmation` 이 **서로도 순차** (FR-049)
- SDM ETL에 `resume_from_stage` 지정 → `422`
- OMOP CDM ETL에서 운영자가 지정 → 허용, 비운영자 → `403`
- 모든 flow run에 `workspace_id` 태그가 붙어 있다
- 실패 단계가 4종 중 하나이며 가명처리 실패가 `transform` 또는 `ai_call` 로 표시된다

### 2-3. 롤 판정이 카탈로그와 일치하는가

```bash
pytest tests/contract/test_role_catalog_parity.py
```

**기대** (013 FR-004): 코드의 권한 판정 테이블과 카탈로그의 허용 행위 목록이 **일치**한다.
카탈로그에 없는 허용이 코드에 있으면 실패한다.

### 2-4. 워크스페이스 스코프 격리

```bash
pytest tests/integration/test_scope_isolation.py
```

**기대**: A 워크스페이스 제한 롤 보유자가 B의 자원에 접근 시 `403`. 전역 롤 보유자는 운영
행위만 가능하고 **제한 계층 데이터 조회 엔드포인트는 존재하지 않는다**.

---

## 3. 배포 검증 (헌법 I)

```bash
# 이미지 빌드 — 이 단계에서 3.14 휠 부재를 잡아야 한다
docker build -t fcp-api:<tag> .

# 런타임 외부 호출 부재 확인
pytest tests/integration/test_offline.py
```

**기대**:
- 빌드 로그에 소스 빌드(`Building wheel for ...`)가 없다 — 전 의존이 3.14 휠로 설치됨 (R7)
- 네트워크를 4종 외부 의존으로만 제한한 상태에서 전 테스트가 통과한다
- 이미지 다이제스트와 의존성 lock을 반입 기록에 남긴다 (헌법 준수 게이트)

---

## 완료 판정

| 순서 | 항목 | 실패 시 |
| --- | --- | --- |
| 1 | §1 헌법 경계 4종 | **머지 불가.** 기능 통과와 무관 |
| 2 | §2 도메인 흐름 4종 | 해당 도메인 미완 |
| 3 | §3 배포 2종 | 원내 반입 불가 |

§1을 §2보다 먼저 두는 이유는, 기능이 동작하는 것보다 **경계를 넘지 않는 것**이 이 시스템에서
되돌릴 수 없는 쪽이기 때문이다.
