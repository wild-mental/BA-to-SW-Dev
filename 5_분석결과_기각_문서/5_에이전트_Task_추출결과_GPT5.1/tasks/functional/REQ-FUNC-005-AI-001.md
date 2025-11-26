# REQ-FUNC-005-AI-001: 제출 가능성 체크리스트 및 보완 가이드 엔진 구현

## 1. 개요
- **목표**: 생성된 사업계획서 초안에 대해 최소 10개 이상의 제출 가능성 체크리스트와 각 항목별 보완 가이드를 생성하는 엔진을 구현한다.
- **범위**:
  - FastAPI 내 `POST /business-plan/checklist` 엔드포인트
  - 체크리스트 항목 정의(문제정의/시장/경쟁/재무 등 영역별)
  - LLM 기반 설명/가이드 생성 + Rule-based 검증(필수 항목 누락 등)
- **Out of Scope**: 장기적인 히스토리/점수 추적(Backend/Analytics Task에서 처리).

## 2. 상세 요구사항
- **입력**: BusinessPlanDocument 섹션별 텍스트, 템플릿 유형.
- **출력**: 체크리스트 항목 ID, 상태(✔/✖), 보완 가이드 텍스트, 점수 요약.
- **AC 연계**: 사용자가 90% 이상 ✔를 달성하면 '제출 가능' 상태로 간주할 수 있는 정보 제공.

---

```yaml
task_id: "REQ-FUNC-005-AI-001"
title: "제출 가능성 체크리스트 및 보완 가이드 LLM 엔진 구현"
summary: >
  사업계획서 초안을 입력받아 영역별 체크리스트를 평가하고,
  미충족 항목에 대한 구체적인 보완 가이드를 생성하는 엔진을 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-005", "REQ-FUNC-003"]
component: ["ai.engine"]
agent_profile: ["ml"]

inputs:
  description: "사업계획서 섹션별 텍스트 및 템플릿 정보"
  fields:
    - name: "sections"
      type: "object"
    - name: "template_type"
      type: "string"

outputs:
  description: "체크리스트 평가 결과"
  success:
    fields: ["items", "summary"]

steps_hint:
  - "체크리스트 기준(최소 10개 이상) 도메인 정의"
  - "LLM Prompt에 체크리스트 기준을 포함하여 평가/가이드 생성"
  - "결과를 JSON 스키마(항목 ID, status, guide)로 파싱"
  - "요약 점수/상태(예: 준비도 %) 계산 로직 추가"

preconditions:
  - "REQ-FUNC-003-AI-001에 의해 BusinessPlanDocument 초안이 생성되어 있어야 한다."

postconditions:
  - "각 항목별 ✔/✖ 상태와 보완 가이드가 포함된 JSON 결과가 반환된다."

dependencies:
  - "REQ-FUNC-003-AI-001"
```


