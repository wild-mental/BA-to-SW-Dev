# REQ-FUNC-006-BE-001: 재무-서술 논리 정합성 검증 엔진/API 구현

## 1. 개요
- **목표**: 재무 가정(입력값)과 사업계획서 서술 내용 간의 불일치를 자동 탐지하고, 구체적인 경고/수정 제안을 반환하는 검증 로직을 구현한다.
- **범위**:
  - `POST /projects/{id}/documents/business-plan:consistency-check` API
  - 재무/시장 가정 vs 서술 내용 비교 규칙 정의
  - 경고 메시지 및 수정 제안 생성 로직(LLM 또는 Rule 기반) 연동
- **Out of Scope**: 고급 통계 모델, 예측 모델링.

## 2. 상세 요구사항
- **불일치 예시**: 시장 규모 대비 3년 차 매출 과다, CAC/마케팅비 불합리, 타깃 세그먼트와 가격전략 불일치 등.
- **출력**: 최소 3건 이상의 불일치 항목과 각 항목별 경고 메시지 + 제안 텍스트.

---

```yaml
task_id: "REQ-FUNC-006-BE-001"
title: "재무-서술 논리 정합성 검증 엔진/API 구현"
summary: >
  FinancialModel와 BusinessPlanDocument를 함께 검토하여
  주요 가정/서술 간 논리적 불일치를 탐지하고 경고/제안을 반환하는 API를 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-006", "REQ-FUNC-012"]
component: ["backend.core"]
agent_profile: ["backend"]

inputs:
  description: "프로젝트 ID 및 검증 대상 데이터"
  fields:
    - name: "project_id"
      type: "uuid"

outputs:
  description: "논리 정합성 검증 결과"
  success:
    fields: ["issues", "recommendations"]

steps_hint:
  - "FinancialModel, BusinessPlanDocument 로딩 로직 구현"
  - "불일치 탐지 규칙 목록 정의 (예: 매출 성장률 vs 시장 성장률)"
  - "규칙 기반 검증 + 선택적 LLM 설명/제안 생성 로직 추가"
  - "API 응답 스키마 정의 (issue_code, message, suggestion)"

preconditions:
  - "REQ-FUNC-003-BE-001, REQ-FUNC-012-BE-001이 완료되어 데이터가 존재해야 한다."

postconditions:
  - "검증 실행 시 최소 3건 이상의 유의미한 불일치 항목이 반환된다(해당하는 경우)."

dependencies:
  - "REQ-FUNC-003-BE-001"
  - "REQ-FUNC-012-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-007-BE-001.md
# REQ-FUNC-007-BE-001: 필수 입력 누락 검증 및 에러 응답 처리

## 1. 개요
- **목표**: Wizard 및 재무 입력 과정에서 필수 필드가 비어 있을 때, 적절한 에러 메시지와 포커스 정보를 반환하는 검증 로직을 구현한다.
- **범위**:
  - 서버단 Validation 룰셋 정의
  - `POST /projects/{id}/wizard/steps` 등에서 필수 필드 검증
  - 에러 응답 스키마 정의(`missing_fields`, `focus_field` 등)
- **Out of Scope**: 클라이언트 단 UI 처리(EPIC0-FE-002에서 구현).

## 2. 상세 요구사항
- **검증 대상**: SRS에서 필수로 정의된 재무/사업 필드(예: 월간 순이익, 핵심 가설 등).
- **동작**: 필수 필드 미입력 시 완료/다음 단계 요청을 차단하고, 첫 누락 필드명을 함께 반환.

---

```yaml
task_id: "REQ-FUNC-007-BE-001"
title: "Wizard/재무 입력 필수 필드 검증 로직 구현"
summary: >
  필수 입력 필드 누락 시 완료/다음 단계 진행을 차단하고,
  UI가 포커스를 옮길 수 있도록 필요한 메타 정보를 반환하는 서버단 검증 로직을 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-007", "REQ-FUNC-002", "REQ-FUNC-012"]
component: ["backend.api"]
agent_profile: ["backend"]

inputs:
  description: "Wizard 또는 재무 입력 요청 바디"
  fields:
    - name: "payload"
      type: "object"

outputs:
  description: "성공 또는 Validation 에러"
  success:
    http_status: 200
  failure_cases:
    - code: "MISSING_REQUIRED_FIELDS"
      http_status: 400

steps_hint:
  - "필수 필드 목록을 구성 기반으로 정의 (템플릿별 설정 파일 등)"
  - "Validation 유틸리티 구현 (누락 필드 탐지)"
  - "에러 응답 DTO 설계 (missing_fields, focus_field)"
  - "Wizard/재무 API에 검증 훅 추가"

preconditions:
  - "REQ-FUNC-002-BE-001, REQ-FUNC-012-BE-001 API 구조가 마련되어 있어야 한다."

postconditions:
  - "필수 필드 누락 요청에 대해 MISSING_REQUIRED_FIELDS 에러가 반환된다."

dependencies:
  - "REQ-FUNC-002-BE-001"
  - "REQ-FUNC-012-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-009-BE-001.md
# REQ-FUNC-009-BE-001: 유닛 이코노믹스 뷰 API 구현

## 1. 개요
- **목표**: 재무 엔진이 계산한 LTV/CAC, 손익분기점(BEP) 등을 조회하고, FE가 시각화에 사용할 수 있는 데이터를 제공하는 API를 구현한다.
- **범위**:
-  - `GET /projects/{id}/unit-economics`
-  - LTV/CAC 비율, BEP(개월 수), 주요 경고 플래그 계산
- **Out of Scope**: 복잡한 보고서 PDF/리포트(별도 Export 기능에서 처리).

## 2. 상세 요구사항
- **경고 조건**: LTV/CAC < 1 또는 BEP ≥ 36개월인 경우 경고 플래그 및 메시지 제공.

---

```yaml
task_id: "REQ-FUNC-009-BE-001"
title: "유닛 이코노믹스 계산 결과 조회 API 구현"
summary: >
  재무 엔진의 계산 결과로부터 LTV/CAC, 손익분기점 등을 조회하는
  REST API를 구현한다.
type: "functional"
epic: "EPIC_2_AVOID_FAILURE"
req_ids: ["REQ-FUNC-009", "REQ-FUNC-012"]
component: ["backend.api"]
agent_profile: ["backend"]

inputs:
  description: "프로젝트 ID"
  fields:
    - name: "project_id"
      type: "uuid"

outputs:
  description: "유닛 이코노믹스 요약"
  success:
    fields: ["ltv", "cac", "ltv_cac_ratio", "break_even_month", "warning_flags"]

steps_hint:
  - "REQ-FUNC-012-BE-001 결과에서 unit_economics 필드 조회"
  - "비즈니스 규칙(LTV/CAC < 1, BEP >= 36개월) 구현"
  - "경고 플래그/메시지 매핑"

preconditions:
  - "REQ-FUNC-012-BE-001이 완료되어 unit_economics 데이터가 계산 가능해야 한다."

postconditions:
  - "요청 시 최신 유닛 이코노믹스 데이터와 경고 플래그가 반환된다."

dependencies:
  - "REQ-FUNC-012-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-010-BE-001.md
# REQ-FUNC-010-BE-001: PMF 데이터 부족 검증 및 에러 처리 로직 구현

## 1. 개요
- **목표**: PMF 진단 요청 시 필수 최소 응답 수를 검증하고, 부족할 경우 진단을 차단하며 체크리스트/예상 소요시간을 안내하는 로직을 구현한다.
- **범위**:
  - `POST /projects/{id}/pmf-report:generate` 진입부 검증 로직
  - 부족 데이터 안내 메시지/체크리스트 생성
- **Out of Scope**: 실제 PMF 진단 로직(REQ-FUNC-008-AI-001에서 구현).

## 2. 상세 요구사항
- **조건**: 필수 PMF 질문 10개 이상 응답 시에만 진단 실행.
- **출력**: 부족 시 `"데이터 부족으로 신뢰도 높은 진단 불가"` 메시지, 필수 질문 목록, 예상 소요시간(예: 15분).

---

```yaml
task_id: "REQ-FUNC-010-BE-001"
title: "PMF 데이터 부족 검증 및 에러 처리 로직 구현"
summary: >
  PMF 진단 요청 전에 응답 수를 검증하고, 부족한 경우
  친절한 안내 메시지와 체크리스트를 반환하는 로직을 구현한다.
type: "functional"
epic: "EPIC_2_AVOID_FAILURE"
req_ids: ["REQ-FUNC-010", "REQ-FUNC-008"]
component: ["backend.api"]
agent_profile: ["backend"]

inputs:
  description: "PMF 설문 응답 데이터"
  fields:
    - name: "answers"
      type: "array"

outputs:
  description: "성공 시 진단 요청 위임 또는 데이터 부족 에러"
  success:
    http_status: 202
  failure_cases:
    - code: "INSUFFICIENT_PMF_DATA"
      http_status: 400

steps_hint:
  - "필수 PMF 질문 목록 정의"
  - "응답 개수 및 필수 질문 커버리지를 계산"
  - "부족 시 에러 응답 DTO에 체크리스트/예상 소요시간 포함"
  - "충분 시 REQ-FUNC-008-AI-001 엔진 호출 위임"

preconditions:
  - "REQ-FUNC-008-AI-001 인터페이스가 정의되어 있어야 한다."

postconditions:
  - "데이터 부족 상태에서 PMF 결과가 생성되지 않는다."

dependencies:
  - "REQ-FUNC-008-AI-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-013-BE-001.md
# REQ-FUNC-013-BE-001: 자동 저장(Auto-save) 처리 및 타임스탬프 로깅

## 1. 개요
- **목표**: 사용자의 입력 내용을 일정 주기(≤10초)로 자동 저장하고, 마지막 저장 시각을 기록/조회할 수 있는 서버 로직을 구현한다.
- **범위**:
  - Wizard/문서 편집 요청에서 Auto-save 이벤트 처리
  - 마지막 저장 시각 필드 관리
- **Out of Scope**: FE 단 UI 인디케이터(EPIC0-FE-002에서 처리).

## 2. 상세 요구사항
- **주기**: 최대 10초 주기로 저장되도록 클라이언트/서버 조합 설계.
- **복원**: 브라우저를 닫았다 다시 열어도 마지막 저장 내용이 로드되어야 함.

---

```yaml
task_id: "REQ-FUNC-013-BE-001"
title: "Wizard/문서 자동 저장 처리 로직 구현"
summary: >
  Wizard/문서 편집 입력을 일정 주기로 자동 저장하고,
  마지막 저장 시각을 관리하는 백엔드 로직을 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-013", "REQ-FUNC-002"]
component: ["backend.api"]
agent_profile: ["backend"]

inputs:
  description: "자동 저장 요청(사용자 입력 스냅샷)"
  fields:
    - name: "project_id"
      type: "uuid"
    - name: "snapshot"
      type: "object"

outputs:
  description: "성공 시 최신 저장 타임스탬프"
  success:
    fields: ["last_saved_at"]

steps_hint:
  - "REQ-FUNC-002-BE-001 저장 로직 재사용"
  - "Project 또는 Document 엔티티에 last_saved_at 필드 추가"
  - "자동 저장과 수동 저장 충돌 방지 전략(마지막 수정 우선) 정의"

preconditions:
  - "REQ-FUNC-002-BE-001이 구현되어 있어야 한다."

postconditions:
  - "자동 저장 이벤트 이후 last_saved_at이 최신 값으로 갱신된다."

dependencies:
  - "REQ-FUNC-002-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-014-FE-001.md
# REQ-FUNC-014-FE-001: 쉬운 모드/전문가 모드 전환 UI/상태 관리 구현

## 1. 개요
- **목표**: 동일 프로젝트에서 쉬운 모드(질문 기반 Wizard)와 전문가 모드(문서 구조 기반 에디터) 간 손실 없는 전환을 지원하는 프론트엔드 로직을 구현한다.
- **범위**:
  - 모드 토글 UI (토글 스위치, 탭 등)
  - Wizard 데이터 ↔ 문서 섹션 데이터 매핑 로직
  - 모드 전환 시 상태 유지/동기화
- **Out of Scope**: 서버 스키마 변경(기존 Project/Document 구조 최대한 활용).

## 2. 상세 요구사항
- **전환 시나리오**: Wizard에서 입력한 데이터가 전문가 모드 문서 섹션에 반영되고, 반대로 문서에서 수정된 내용이 Wizard에도 반영.

---

```yaml
task_id: "REQ-FUNC-014-FE-001"
title: "쉬운 모드/전문가 모드 전환 UI 및 상태 동기화 구현"
summary: >
  Wizard 기반 쉬운 모드와 문서 기반 전문가 모드를 오가며,
  데이터 손실 없이 상태를 유지하는 프론트엔드 로직을 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-014", "REQ-FUNC-002", "REQ-FUNC-003"]
component: ["frontend.web"]
agent_profile: ["frontend"]

inputs:
  description: "프로젝트의 Wizard/Document 상태"
  fields:
    - name: "wizard_answers"
      type: "object"
    - name: "document_sections"
      type: "object"

outputs:
  description: "모드별 뷰 상태 및 동기화된 데이터"
  success:
    ui_state: "easy_mode | expert_mode"

steps_hint:
  - "모드 토글 컴포넌트 구현"
  - "wizard_answers ↔ document_sections 매핑 유틸 함수 작성"
  - "모드 전환 시 변경분만 반영하는 동기화 전략 정의"

preconditions:
  - "EPIC0-FE-001~003 범위의 기본 UI가 구현되어 있어야 한다."

postconditions:
  - "모드 전환 시 사용자 입력 데이터가 손실되지 않는다."

dependencies:
  - "EPIC0-FE-001"
  - "EPIC0-FE-002"
  - "EPIC0-FE-003"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-015-INF-001.md
# REQ-FUNC-015-INF-001: On-premise/Private Cloud 배포 패키지 설계

## 1. 개요
- **목표**: On-premise/Private Cloud 환경에서 핵심 기능(F1~F5)을 실행할 수 있도록 Docker Compose 또는 Helm Chart 기반 배포 패키지를 설계한다.
- **범위**:
  - 서비스 구성 정의(API, AI 엔진, DB, 모니터링 등)
  - 환경 변수/시크릿 관리 방식 정의
  - 기본 설치/업데이트 가이드 문서화
- **Out of Scope**: 고객별 커스터마이징/고가용성(HA) 구성.

## 2. 상세 요구사항
- **네트워크 제약**: 외부 인터넷 연결 없이도 핵심 기능이 사내 인프라에서 동작 가능해야 함(LLM Gateway는 사내 Gateway 전제).

---

```yaml
task_id: "REQ-FUNC-015-INF-001"
title: "On-premise/Private Cloud 배포 패키지 설계"
summary: >
  API, AI 엔진, DB, 모니터링 등을 포함한 On-premise 배포 패키지를
  Docker Compose 또는 Helm Chart로 설계한다.
type: "functional"
epic: "EPIC_ONPREM_DESIGN"
req_ids: ["REQ-FUNC-015", "REQ-NF-008", "REQ-NF-018"]
component: ["infra.deployment"]
agent_profile: ["infra"]

inputs:
  description: "서비스 구성 요구사항, 대상 인프라 제약"
  fields:
    - name: "target_infra"
      type: "string"

outputs:
  description: "배포 스크립트/매니페스트 및 설치 가이드"
  success:
    fields: ["docker_compose.yml", "README_ONPREM.md"]

steps_hint:
  - "서비스 컴포넌트 목록 및 의존성 정의"
  - "Docker 이미지/태그 설계"
  - "환경 변수/시크릿 관리 전략 정의"
  - "기본 설치/업데이트/롤백 절차 문서화"

preconditions:
  - "각 서비스의 Docker 이미지 빌드가 가능해야 한다."

postconditions:
  - "단일 명령으로 핵심 서비스 스택을 기동/중지할 수 있다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/functional/REQ-FUNC-016-BE-001.md
# REQ-FUNC-016-BE-001: 워크스페이스별 데이터 격리 설정 API 설계

## 1. 개요
- **목표**: 조직별 워크스페이스마다 배포 옵션(Cloud/Private/On-premise) 및 데이터 저장 위치를 설정할 수 있는 백엔드 API와 설정 모델을 설계한다.
- **범위**:
  - WorkspaceSecuritySetting 엔티티/테이블 설계
  - 워크스페이스 생성/수정 API (`POST/PUT /workspaces`)
  - 배포 모드/데이터 리전 설정 필드 정의
- **Out of Scope**: 실제 멀티클러스터 라우팅/데이터 마이그레이션 로직.

## 2. 상세 요구사항
- **배포 모드**: `cloud`, `private`, `on_premise` 중 하나 선택.
- **데이터 격리**: 선택된 모드에 따라 데이터/로그가 해당 환경 내에서만 저장/처리되도록 설계(후속 Infra Task와 연계).

---

```yaml
task_id: "REQ-FUNC-016-BE-001"
title: "워크스페이스별 데이터 격리 설정 API/모델 설계"
summary: >
  워크스페이스 단위로 배포 모드와 데이터 저장 위치를 설정할 수 있는
  백엔드 모델 및 API를 설계한다.
type: "functional"
epic: "EPIC_ONPREM_DESIGN"
req_ids: ["REQ-FUNC-016", "REQ-NF-008"]
component: ["backend.api", "backend.core"]
agent_profile: ["backend"]

inputs:
  description: "워크스페이스 생성/수정 요청"
  fields:
    - name: "workspace_id"
      type: "uuid"
      required: false
    - name: "deployment_mode"
      type: "enum"
    - name: "data_region"
      type: "string"

outputs:
  description: "워크스페이스 설정 정보"
  success:
    fields: ["workspace_id", "deployment_mode", "data_region"]

steps_hint:
  - "WorkspaceSecuritySetting 엔티티/테이블 설계"
  - "워크스페이스 CRUD API 설계 및 구현"
  - "배포 모드별 정책을 나중에 적용할 수 있도록 Hook 포인트 정의"

preconditions:
  - "기본 인증/관리자 권한 시스템이 존재해야 한다(별도 SRS 범위)."

postconditions:
  - "각 워크스페이스의 배포 모드/데이터 저장 위치가 조회/수정 가능하다."

dependencies: []
```


