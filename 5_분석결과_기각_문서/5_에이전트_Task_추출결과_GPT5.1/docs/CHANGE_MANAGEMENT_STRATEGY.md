# Change Management Strategy: PRD-SRS-Task (GPT-5.1 Edition)

## 1. 3-Layer Traceability Structure

변경 관리는 다음 3단계 계층의 추적성(Traceability)을 기반으로 수행합니다.

1. **PRD (Business Logic)**: `2_핵심예제_분석자료/9_GPT-Gemini_Merged_PRD.md`
2. **SRS (System Requirements)**: `2_핵심예제_분석자료/10_GPT-SRS-V3.md`
3. **Task Definitions (Implementation Spec)**: `5_에이전트_Task_추출결과/tasks/**/*.md`

각 계층은 다음과 같이 연결됩니다.

- PRD Story / Feature → SRS REQ-FUNC / REQ-NF (`Traceability Matrix` 기반)
- SRS REQ-FUNC / REQ-NF → Task 정의서의 `req_ids`
- Epic / Feature / Component → Task 정의서의 `epic`, `feature`, `component`

## 2. Change Scenarios & Workflows

### 2.1 Scenario A: PRD (Business Goal) 변경

- **Trigger 예시**
  - “PMF 리포트에 추가로 NPS 요약 섹션을 넣어 달라”
  - “정부 과제 통과율 대신 다른 KPI를 핵심 지표로 삼고 싶다”

- **Action**
  1. PRD 문서 수정 및 버전 증가 (예: `PRD v1.0 → v1.1`).
  2. 영향을 받는 SRS REQ-FUNC/REQ-NF를 식별 후 수정  
     (예: `REQ-FUNC-008` 또는 `REQ-NF-013~017`).
  3. 해당 REQ ID를 `req_ids`에 포함하는 Task들을 전부 조회:
     - 예: `tasks/functional/REQ-FUNC-008-AI-001.md`
  4. 각 Task의 `summary`, `inputs`, `outputs`, `postconditions`, `tests` 등을  
     새로운 비즈니스 요구에 맞게 업데이트.
  5. `docs/MVP_SCOPE_AND_MAPPING.md`와 `docs/INTEGRATED_WBS_DAG.md`의  
     Feature / WBS / DAG 내용도 일관되게 갱신.

### 2.2 Scenario B: SRS (Requirement Details) 변경

- **Trigger 예시**
  - “문서 생성 응답시간 p95 목표를 10초 → 7초로 강화”
  - “Wizard 자동저장 주기를 10초 → 5초로 조정”

- **Action**
  1. SRS 해당 REQ-NF/REQ-FUNC 항목 업데이트
     - 예: `REQ-NF-002`, `REQ-NF-005` 변경.
  2. 해당 REQ ID와 연결된 Task들을 조회:
     - 예: `REQ-NF-001-PERF-001`, Auto-save 관련 기능 Task 등.
  3. Task 정의서의 `requirements.kpis`, `steps_hint`, `tests`, `postconditions`를  
     새 목표에 맞게 수정.
  4. 변경 난이도가 큰 경우, 해당 Task들을 WBS/DAG 상에서 **재추정**하고  
     우선순위/일정을 재조정.

### 2.3 Scenario C: Task (Implementation) 수준 변경

- **Trigger 예시**
  - 코드 리팩토링, 구현 상세의 변경, 성능 최적화, 버그 수정.

- **Action**
  1. Task 정의서의 Human-readable 섹션(설계 노트, 예외 처리 등)과  
     YAML 블록(`steps_hint`, `postconditions`, `dependencies`)을 실제 구현에 맞게 조정.
  2. 상위 SRS/PRD 내용에는 영향이 없으면,  
     Task 정의서만 버전 증가(예: YAML에 `revision` 필드 추가 등)로 처리.
  3. 만약 구현 변경으로 인해 SRS 수준의 동작이 달라진다면,  
     Scenario B 경로를 통해 상위 문서를 역으로 수정하도록 플로우를 전환.

## 3. Version Control & Sync Policy

### 3.1 Version Tags

- **PRD**: `PRD-v{Major}.{Minor}` (예: `PRD-v1.1`)
- **SRS**: `SRS-v{Major}.{Minor}` (PRD Major 버전과 동기화 권장)
- **Task Catalog**: `TASK-CATALOG-v{Date}_{ShortHash}`

각 Task 파일에는 필요시 다음과 같은 메타 필드를 둘 수 있습니다.

```yaml
catalog_version: "TASK-CATALOG-v2025-11-18_ab12cd"
last_reviewed_srs: "SRS-v1.1"
```

### 3.2 Change Log

- 선택적으로 `docs/CHANGE_LOG.md`를 운영하거나,  
  Git Commit 메시지에 다음과 같은 태그를 사용합니다.
  - `[PRD-Update]`, `[SRS-Update]`, `[TASK-Update]`
- 주요 변경 시에는:
  - 어떤 REQ-FUNC/REQ-NF/Task에 영향이 있었는지,
  - 어떤 Epic/Feature/WBS 영역에 파급되었는지를 함께 기록합니다.

### 3.3 Sync Check 주기

- 정기적으로(예: 스프린트 단위) 아래를 점검합니다.
  1. `docs/MVP_SCOPE_AND_MAPPING.md`에 정의된 REQ-FUNC/REQ-NF 목록이  
     실제 Task 파일(`tasks/**/*.md`)과 1:1 이상 매핑되는지 확인.
  2. SRS에서 **추가/삭제/Deprecated**된 REQ가 Task 쪽에도 반영되었는지 확인.
  3. `docs/INTEGRATED_WBS_DAG.md`의 WBS/DAG 구조와  
     실제 Task ID/의존성이 일치하는지 확인.

## 4. Workflow 예시 (실제 운영 관점)

1. **기획 변경 발생 (PRD)**  
   - PRD 수정 → SRS 영향 분석 → REQ-FUNC/REQ-NF 수정 → Task 영향 분석.
2. **SRS 상세 조정**  
   - 개발/QA 과정에서 발견된 제약을 반영 → SRS 업데이트 → Task 재정의.
3. **구현/테스트 과정 피드백**  
   - 에이전트/개발자가 Task 정의서의 모호성·누락을 발견 →  
     Task 정의서 개선 PR → 필요 시 상위 SRS/PRD로 피드백.

이 문서는 `11-2` 문서의 단계 7, `11-1` 문서의 단계 9에 해당하는  
PRD-SRS-Task 3단계 변경 관리 전략을 **본 레포지토리 수준에서 구체화한 결과물**입니다.


