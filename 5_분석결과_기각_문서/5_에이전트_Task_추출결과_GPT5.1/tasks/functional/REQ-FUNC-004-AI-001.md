# REQ-FUNC-004-AI-001: 섹션별 AI 작성/보완 엔드포인트 구현

## 1. 개요
- **목표**: 사업계획서의 특정 섹션을 선택했을 때, 해당 섹션에 대한 AI 초안/보완 텍스트를 생성·수정해 주는 LLM 기반 엔드포인트를 구현한다.
- **범위**:
  - FastAPI 내 `POST /sections/{section_id}:rewrite` 엔드포인트
  - 섹션별 Prompt Template 정의 (문맥 + 현재 텍스트 + 요청 타입)
  - LLM Gateway(Gemini) 연동 및 결과 트리밍/후처리
- **Out of Scope**: 복잡한 히스토리 관리(버전 관리, Diff 저장 등은 BE/DB Task로 분리).

## 2. 상세 요구사항
- **입력**: 섹션 ID, 현재 섹션 텍스트, 요청 타입(`draft`/`improve`), 톤/스타일 옵션(선택).
- **출력**: 개선된 섹션 텍스트 및 간단한 변경 요약(선택).
- **품질 제약**: 한국어 비즈니스 문체, 공고 양식에 맞는 목차/스타일 유지.

---

```yaml
task_id: "REQ-FUNC-004-AI-001"
title: "섹션별 AI 작성/보완 LLM 엔드포인트 구현"
summary: >
  기존 문서의 특정 섹션을 입력받아, 초안 생성 또는 보완 텍스트를
  반환하는 LLM 기반 API를 FastAPI로 구현한다.
type: "functional"
epic: "EPIC_1_PASS_THE_TEST"
req_ids: ["REQ-FUNC-004", "REQ-FUNC-003"]
component: ["ai.engine"]
agent_profile: ["ml", "backend"]

inputs:
  description: "섹션별 AI 보조 요청"
  fields:
    - name: "section_id"
      type: "string"
      required: true
    - name: "current_text"
      type: "string"
      required: false
    - name: "mode"
      type: "enum"
      example: "draft | improve"
    - name: "tone"
      type: "string"
      required: false

outputs:
  description: "AI가 생성/보완한 섹션 텍스트"
  success:
    fields: ["new_text", "change_summary"]

steps_hint:
  - "섹션별 Prompt Template 정의 (역할/톤/목표 포함)"
  - "REQ-FUNC-003-AI-001에서 구축한 LLM 클라이언트 재사용"
  - "LangChain Output Parser를 활용해 JSON 형태 응답 파싱"
  - "에러/토큰 초과 시 Graceful Fallback 로직 구현"

preconditions:
  - "REQ-FUNC-003-AI-001 LLM 엔진 인프라가 구동 중이어야 한다."

postconditions:
  - "유효한 요청에 대해 섹션별 보완 텍스트가 반환된다."

dependencies:
  - "REQ-FUNC-003-AI-001"
```


