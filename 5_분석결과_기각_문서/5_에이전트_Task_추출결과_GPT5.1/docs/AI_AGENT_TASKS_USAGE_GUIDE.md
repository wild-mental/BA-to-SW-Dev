# AI Agent Task Definition Usage Guide (GPT-5.1 Edition)

## 1. Task Definition Structure

본 프로젝트의 Task 정의서는 **Human-readable Context**와  
**Machine-readable Spec (YAML)**이 결합된 형태입니다.

### 1.1 파일 구조

- `tasks/functional/REQ-FUNC-XXX-*.md`: 기능 요구사항 구현 Task
- `tasks/non-functional/REQ-NF-XXX-*.md`: 비기능 요구사항(성능, 보안 등) 구현/검증 Task
- `tasks/functional/EPIC0-FE-XXX.md`: 프론트엔드 PoC(Epic 0) Task

### 1.2 공통 섹션 (Human-readable)

- **제목**: `REQ-FUNC-XXX-YYY: 한 줄 요약` 또는 `EPIC0-FE-XXX: 한 줄 요약`
- **1. 개요**: 목표, 범위, Out of Scope
- **2. 상세 요구사항**: SRS/PRD 기반 상세 설명
- **3. 기술 스택 / 구현 가이드**: 주요 기술 선택과 구현 팁

### 1.3 YAML 필드 설명 (Machine-readable)

```yaml
task_id: "고유 식별자 (SRS ID 또는 Epic ID 포함)"
title: "Task 제목"
summary: "요약 설명 (2~3줄)"
type: "functional" # 또는 "non_functional"

epic: "상위 EPIC ID (예: EPIC_0_FE_PROTOTYPE, EPIC_1_PASS_THE_TEST)"
feature: "선택적 Feature 태그"
req_ids: ["관련된 SRS REQ ID 목록"]
component: ["backend.api", "frontend.web", "ai.engine", "infra.monitoring", ...]

agent_profile: ["frontend" | "backend" | "infra" | "ml"]  # 적합한 에이전트 유형
parallelizable: true | false | "conditional"
estimated_effort: "S" # S | M | L
priority: "Must" # Must | Should | Could

inputs:
  description: "작업 시작 시 필요한 정보/상태"
  fields:
    - name: "field_name"
      type: "string"
      required: true

outputs:
  description: "작업 완료 시 보장되는 산출물"
  success:
    http_status: 201
    ui_state: "예상되는 화면 상태"

steps_hint:
  - "에이전트가 참고할 구현 단계 힌트 1"
  - "에이전트가 참고할 구현 단계 힌트 2"

preconditions:
  - "착수 이전에 만족되어야 하는 시스템/코드 상태"

postconditions:
  - "Task 완료 이후 시스템/데이터에 대해 참이어야 하는 조건"

dependencies:
  - "선행 Task ID"
```

비기능 Task(REQ-NF)의 경우 다음 필드가 추가됩니다.

```yaml
category: "performance" # 또는 security / observability / operations / business_kpi / resilience ...
labels:
  - "performance:latency"
  - "security:encryption"

requirements:
  description: "REQ-NF 요구의 요지"
  kpis:
    - "측정 가능한 KPI 1"
    - "측정 가능한 KPI 2"

related_req_func: ["REQ-FUNC-003", "REQ-FUNC-012"]  # 영향을 받는 기능 요구사항
```

## 2. Orchestration Strategy (How to Use Tasks)

### 2.1 단일 Task 실행

1. 에이전트에게 해당 `.md` 파일 전체(본문 + YAML)를 Prompt로 제공합니다.
2. 에이전트는:
   - `req_ids`, `component`, `context` 정보를 통해 **관련 코드 영역**을 파악하고,
   - `inputs`/`outputs`/`steps_hint`/`postconditions`를 참조하여 구현/수정을 수행합니다.
3. 구현 후, `postconditions`와 필요 시 별도 테스트 시나리오를 만족했는지 자체 점검합니다.

### 2.2 병렬 실행 (Swarm Mode)

1. `docs/INTEGRATED_WBS_DAG.md`를 참고하여, **현재까지 완료된 Task**와  
   `dependencies`가 모두 충족된 **착수 가능 Task** 집합을 계산합니다.
2. `parallelizable: true`인 Task들만 병렬 실행 후보로 선택합니다.
3. `agent_profile`에 따라 Task를 에이전트 유형별 큐로 분배합니다.
   - `["frontend"]` → Frontend Specialist Agent
   - `["backend"]` → Backend/API Specialist Agent
   - `["infra"]` → DevOps/Infra Agent
   - `["ml"]` → LLM/모델링 Agent
4. 각 에이전트 실행 결과(코드 변경, MR/PR 등)를 수집한 뒤,  
   WBS/DAG 상에서 해당 Task를 **완료 상태**로 업데이트합니다.

### 2.3 WBS/DAG 해석

- **WBS (`docs/INTEGRATED_WBS_DAG.md`)**
  - 레벨 1: Epic (EPIC 0, EPIC 1, EPIC 2, NFR 등)
  - 레벨 2: Feature
  - 레벨 3: REQ-FUNC / REQ-NF
  - 레벨 4: 컴포넌트별 구현/테스트 Task
  - Epic/Feature 단위의 진행률, 누락 Task, 병목 구간을 파악할 때 사용합니다.

- **DAG (`docs/INTEGRATED_WBS_DAG.md` 내 Mermaid 그래프)**  
  - `A --> B` 는 “A가 선행, B가 후행”임을 의미합니다.
  - DB → Backend → Engine/외부 시스템 → API → Frontend → E2E Test  
    와 같은 큰 축을 따라 의존성을 설계합니다.
  - DAG를 이용해 **현재 착수 가능한 Task 집합**, **병렬 실행 영역**, **크리티컬 패스**를 도출합니다.

## 3. Best Practices

- **Context 동시 참조**
  - 가능하면 Task 정의서와 함께 다음 문서를 함께 Prompt에 포함하는 것을 권장합니다.
    - `docs/MVP_SCOPE_AND_MAPPING.md`
    - 관련 SRS 섹션(`10_GPT-SRS-V3.md`)
    - 필요한 경우 PRD(`9_GPT-Gemini_Merged_PRD.md`)

- **Incremental Build 전략**
  1. Epic 0 – FE PoC (`EPIC0-FE-***`)
  2. Core API & Data (`REQ-FUNC-001,002,...`)
  3. AI Integration (`REQ-FUNC-003,004,005,008`)
  4. Financial Engine (`REQ-FUNC-006,009,012`)
  5. Export & NFR (`REQ-FUNC-011`, REQ-NF 전반)

- **Feedback Loop**
  - 에이전트가 Task 수행 중 스펙의 모호함을 발견하면:
    - Task 정의서의 Human-readable 섹션 또는 YAML 필드를 **보완 수정**하는 PR을 먼저 제안하고,
    - 리뷰 후 확정된 정의를 기준으로 구현을 진행합니다.

## 4. 예시: 에이전트 호출 Prompt 스니펫

아래는 단일 Task를 에이전트에게 실행시키는 간단한 예시입니다.

```text
당신은 Spring Boot 백엔드 개발자입니다.

다음 Task 정의서에 따라 기능을 구현해 주세요.
- Task 정의서 (Markdown + YAML): <REQ-FUNC-002-BE-001.md 전체 내용>
- 관련 SRS: 10_GPT-SRS-V3.md의 REQ-FUNC-002, 6.1/6.2 섹션

특히 다음을 보장해야 합니다.
- postconditions 필드에 적힌 조건을 모두 만족해야 합니다.
- steps_hint는 참고용이며, 필요시 더 나은 구조로 재구성해도 됩니다.
- 테스트 코드는 최소 1개 이상의 Happy Path + 1개 이상의 Error Path를 포함해 주세요.
```

이 가이드는 `11-2_AI_에이전틱_프로그래밍을_위한_풀버전_Task_정의서_작성하기.md`의  
단계 8(에이전트용 활용 가이드)을 본 리포지토리에 적용한 결과물입니다.


