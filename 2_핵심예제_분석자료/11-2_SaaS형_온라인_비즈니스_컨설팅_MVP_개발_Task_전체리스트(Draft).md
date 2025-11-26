# 11-3_SaaS형_온라인_비즈니스_컨설팅_MVP_개발_Task_전체리스트

### EPIC 1 – Functional Tasks (F1/F2/F3/F4)

- **EP1-F1-REQ-FUNC-001-FE-01**: 새 프로젝트 생성 화면에 템플릿 선택 섹션 UI 컴포넌트 구현  
- **EP1-F1-REQ-FUNC-001-FE-02**: 로그인 후 ‘새 프로젝트 생성’ 화면 라우팅 및 상태 관리 구현  
- **EP1-F1-REQ-FUNC-001-FE-03**: 템플릿 목록 렌더링 및 선택 상태 유지 로직 구현  
- **EP1-F1-REQ-FUNC-001-BE-01**: `POST /projects` 요청에서 템플릿 선택 파라미터 처리 및 기본값 적용  
- **EP1-F1-REQ-FUNC-001-BE-02**: 템플릿 메타데이터 조회용 서비스/리포지토리 구현  
- **EP1-F1-REQ-FUNC-001-DB-01**: 템플릿 코드/이름/버전 관리를 위한 참조 테이블 또는 enum 스키마 설계  
- **EP1-F1-REQ-FUNC-001-OPS-01**: 템플릿 목록 표시·선택을 검증하는 FE E2E 테스트 작성  
- **EP1-F1-REQ-FUNC-001-OPS-02**: 유효/무효 템플릿 코드에 대한 프로젝트 생성 API 테스트 작성  

- **EP1-F1-REQ-FUNC-002-FE-01**: Wizard 단계 진행률 표시 UI 및 상태 머신 구현  
- **EP1-F1-REQ-FUNC-002-FE-02**: 단계별 질문 폼 컴포넌트 및 입력 상태 관리 구현  
- **EP1-F1-REQ-FUNC-002-FE-03**: 필수 질문 미입력 시 다음 단계 이동 차단 및 에러 메시지/하이라이트 구현  
- **EP1-F1-REQ-FUNC-002-BE-01**: `POST /projects/{id}/wizard/steps` Request DTO 및 Validation 로직 구현  
- **EP1-F1-REQ-FUNC-002-BE-02**: 템플릿별 Wizard 단계/질문 정의를 로딩하는 설정 서비스 구현  
- **EP1-F1-REQ-FUNC-002-BE-03**: `wizard_answers` JSONB에 단계별 답변 병합/버전 관리 서비스 구현  
- **EP1-F1-REQ-FUNC-002-DB-01**: `Project.wizard_answers` JSONB 구조 및 인덱스 설계  
- **EP1-F1-REQ-FUNC-002-OPS-01**: 필수 질문 누락 시 Validation 실패를 검증하는 단위 테스트 작성  
- **EP1-F1-REQ-FUNC-002-OPS-02**: 전체 Wizard 플로우에서 필수 질문 강제 동작을 검증하는 E2E 테스트 작성  

- **EP1-F4-REQ-FUNC-003-FE-01**: ‘초안 생성’ 버튼 및 로딩/에러/성공 상태 UI 구현  
- **EP1-F4-REQ-FUNC-003-FE-02**: 생성된 사업계획서 초안을 섹션별 편집기와 연동하는 화면 구현  
- **EP1-F4-REQ-FUNC-003-BE-01**: `POST /projects/{id}/documents/business-plan:generate` Controller/Route 구현  
- **EP1-F4-REQ-FUNC-003-BE-02**: Wizard 답변·템플릿 메타데이터 → LLM Prompt Payload 매핑 로직 구현  
- **EP1-F4-REQ-FUNC-003-BE-03**: 문서 자동생성 엔진(ENG) 호출 및 응답 파싱/에러 처리 로직 구현  
- **EP1-F4-REQ-FUNC-003-BE-04**: 필수 목차 기반 `mandatory_coverage` 계산 및 BusinessPlanDocument 저장 로직 구현  
- **EP1-F4-REQ-FUNC-003-ENG-01**: 템플릿별 섹션/질문을 프롬프트로 변환하는 Prompt 템플릿 엔진 구현  
- **EP1-F4-REQ-FUNC-003-ENG-02**: LLM Gateway(Gemini) 클라이언트 및 Provider 추상화 레이어 구현  
- **EP1-F4-REQ-FUNC-003-DB-01**: `BusinessPlanDocument` 테이블/엔티티 및 `mandatory_coverage` 필드 스키마 설계  
- **EP1-F4-REQ-FUNC-003-OPS-01**: 필수 목차 누락률 0% 검증을 위한 mandatory_coverage 계산 유닛 테스트 작성  
- **EP1-F4-REQ-FUNC-003-OPS-02**: Wizard 입력 → 초안 생성 → 저장/조회 전체 플로우 통합 테스트 작성  
- **EP1-F4-REQ-FUNC-003-OPS-03**: 초안 생성 API p95 응답시간 측정을 위한 성능 테스트 스크립트 초안 작성  

- **EP1-F2-REQ-FUNC-005-FE-01**: ‘제출 가능성 점검’ 버튼 및 체커 결과 모달/패널 UI 구현  
- **EP1-F2-REQ-FUNC-005-FE-02**: 영역별 체크리스트 항목 ✔/✖ 상태 및 보완 가이드 표시 로직 구현  
- **EP1-F2-REQ-FUNC-005-FE-03**: 90% 이상 ✔ 달성 시 ‘제출 가능’ 상태 뱃지/표시 전환 로직 구현  
- **EP1-F2-REQ-FUNC-005-BE-01**: `POST /projects/{id}/documents/business-plan:checklist` Controller/Service 구현  
- **EP1-F2-REQ-FUNC-005-BE-02**: Checklist Engine 연동 및 평가 요청/응답 매핑 로직 구현  
- **EP1-F2-REQ-FUNC-005-BE-03**: 문제정의/시장/경쟁/재무 등 영역별 체크리스트 규칙 세트 설계 및 구현  
- **EP1-F2-REQ-FUNC-005-DB-01**: 체크리스트 평가 결과를 저장할지 여부 및 저장 스키마 설계  
- **EP1-F2-REQ-FUNC-005-OPS-01**: 체크리스트 규칙별 유닛 테스트 및 필수 10개 이상 항목 보장 테스트 작성  
- **EP1-F2-REQ-FUNC-005-OPS-02**: 초안 생성 후 점검 실행 → 90% 이상 ✔ 시 상태 전환을 검증하는 E2E 테스트 작성  

- **EP1-F3-REQ-FUNC-006-FE-01**: ‘논리 정합성 검증’ 실행 버튼 및 진행/완료 상태 표시 UI 구현  
- **EP1-F3-REQ-FUNC-006-FE-02**: 불일치 경고 목록 및 각 항목별 수정 제안 표시 화면 구현  
- **EP1-F3-REQ-FUNC-006-BE-01**: `POST /projects/{id}/documents-business-plan:consistency-check` API 명세 및 Controller 구현  
- **EP1-F3-REQ-FUNC-006-BE-02**: Consistency Engine 호출용 서비스 및 입력 데이터 수집/변환 로직 구현  
- **EP1-F3-REQ-FUNC-006-BE-03**: 최소 3건 이상의 불일치 결과를 보장하는 후처리/필터링 로직 구현  
- **EP1-F3-REQ-FUNC-006-ENG-01**: 재무/시장/서술 데이터를 입력으로 받아 불일치 분석을 수행하는 Consistency Engine 구현  
- **EP1-F3-REQ-FUNC-006-ENG-02**: 불일치 메시지·수정 제안의 스키마 정의 및 LLM/규칙엔진 결합 구현  
- **EP1-F3-REQ-FUNC-006-DB-01**: 정합성 검증 결과를 로그/리포트로 보관할 경우의 저장 스키마 설계  
- **EP1-F3-REQ-FUNC-006-OPS-01**: 대표 케이스(시장 대비 과도 매출 등)에 대한 불일치 검출 유닛 테스트 작성  
- **EP1-F3-REQ-FUNC-006-OPS-02**: 재무 입력 → 정합성 검증 → 경고 표시 E2E 플로우 테스트 작성  

- **EP1-F3-REQ-FUNC-007-FE-01**: 필수 재무 필드를 시각적으로 표시(스타/색상)하는 UI 구현  
- **EP1-F3-REQ-FUNC-007-FE-02**: 완료/다음 단계 클릭 시 첫 번째 누락 필드로 포커스를 이동시키는 UX 구현  
- **EP1-F3-REQ-FUNC-007-FE-03**: 필수 항목 누락 시 완료/다음 단계 액션을 차단하는 로직 구현  
- **EP1-F3-REQ-FUNC-007-BE-01**: 완료/다음 단계 관련 API에서 필수 재무 필드 검증 로직 구현  
- **EP1-F3-REQ-FUNC-007-BE-02**: 필수 항목 누락 시 표준 에러 응답 포맷(`missing_field` 등) 정의 및 구현  
- **EP1-F3-REQ-FUNC-007-DB-01**: 필수 재무 필드 정의를 템플릿/필드 메타 구성으로 관리하기 위한 스키마 설계  
- **EP1-F3-REQ-FUNC-007-OPS-01**: 필수 필드 검증 로직에 대한 단위 테스트 작성  
- **EP1-F3-REQ-FUNC-007-OPS-02**: 필수 필드 누락 시 경고 메시지/포커스 이동/완료 차단을 검증하는 E2E 테스트 작성  

- **EP1-F2-REQ-FUNC-011-FE-01**: ‘내보내기’ 버튼 및 HWP/PDF 포맷 선택 UI 구현  
- **EP1-F2-REQ-FUNC-011-FE-02**: Export 요청 진행 상태 및 다운로드 처리 로직 구현  
- **EP1-F2-REQ-FUNC-011-BE-01**: `GET /projects/{id}/export` Controller 및 쿼리 파라미터 처리 구현  
- **EP1-F2-REQ-FUNC-011-BE-02**: BusinessPlanDocument → 변환 서비스 입력 포맷 매핑 로직 구현  
- **EP1-F2-REQ-FUNC-011-BE-03**: 변환 서비스(HWP/PDF 변환기) HTTP 클라이언트 및 에러 처리 구현  
- **EP1-F2-REQ-FUNC-011-ENG-01**: 템플릿 필드 100% 매핑 규칙 정의 및 변환 서비스 내부 매핑 구현  
- **EP1-F2-REQ-FUNC-011-DB-01**: Export 이력(시각/템플릿 버전/포맷)을 저장하는 로그 테이블 설계(필요 시)  
- **EP1-F2-REQ-FUNC-011-OPS-01**: 샘플 템플릿에 대한 Export 결과를 골든 파일과 비교하는 테스트 작성  
- **EP1-F2-REQ-FUNC-011-OPS-02**: ‘제출 가능’ 상태에서 Export 시 필수 필드 누락/플레이스홀더 없음 검증 E2E 테스트 작성  

- **EP1-F1-REQ-FUNC-013-FE-01**: 편집 입력 idle 감지 및 Auto-save 트리거 타이머 구현  
- **EP1-F1-REQ-FUNC-013-FE-02**: 마지막 저장 시각을 표시하는 UI 컴포넌트 구현  
- **EP1-F1-REQ-FUNC-013-FE-03**: 브라우저를 닫았다 다시 열 때 서버 상태와 동기화하는 복원 로직 구현  
- **EP1-F1-REQ-FUNC-013-BE-01**: Auto-save 요청에서 변경 필드만 효율적으로 저장하는 서비스 로직 구현  
- **EP1-F1-REQ-FUNC-013-BE-02**: Auto-save와 명시적 저장/완료 요청 간 충돌 방지 전략 구현  
- **EP1-F1-REQ-FUNC-013-DB-01**: Auto-save를 위한 `updated_at`/버전 관리 전략 및 스키마 보완  
- **EP1-F1-REQ-FUNC-013-OPS-01**: 10초 이내 Auto-save 동작 및 복원 시나리오를 검증하는 FE E2E 테스트 작성  
- **EP1-F1-REQ-FUNC-013-OPS-02**: Auto-save 로그 타임스탬프 기반 주기 검증 테스트 작성  

---

### EPIC 1 – NFR / DevOps·QA Tasks

- **EP1-NFR-REQ-NF-001-OPS-01**: Wizard 단계 전환 API에 대한 k6 부하 테스트 스크립트 작성  
- **EP1-NFR-REQ-NF-001-OPS-02**: Wizard 관련 API p95 ≤ 800ms를 검증하는 CI 성능 테스트 파이프라인 구성  
- **EP1-NFR-REQ-NF-001-OPS-03**: APM에서 Wizard 트랜잭션 p95 대시보드 및 경고 임계치 설정  

- **EP1-NFR-REQ-NF-002-OPS-01**: 초안 생성/체크리스트/정합성 검증 API용 부하 테스트 스크립트 작성  
- **EP1-NFR-REQ-NF-002-OPS-02**: 문서/리포트 생성 p95 ≤ 10초 목표를 모니터링하는 대시보드 구성  
- **EP1-NFR-REQ-NF-002-OPS-03**: LLM/엔진 호출 타임아웃·재시도·큐 관리 설정 적용  

- **EP1-NFR-REQ-NF-003-OPS-01**: 서비스 헬스체크/라이브니스/레디니스 엔드포인트 구성 및 모니터링 연동  
- **EP1-NFR-REQ-NF-003-OPS-02**: 월간 가용성(장애 시간) 측정을 위한 상태 페이지 및 로그 지표 설계  

- **EP1-NFR-REQ-NF-005-OPS-01**: Auto-save 주기(≤10초)를 검증하기 위한 로그 기반 QA 스크립트 작성  
- **EP1-NFR-REQ-NF-005-OPS-02**: 클라이언트/서버 시간 동기화를 위한 NTP/시계 전략 정의 및 적용  

- **EP1-NFR-REQ-NF-013-OPS-01**: 가입·첫 프로젝트 생성·첫 Export 이벤트 스키마 정의  
- **EP1-NFR-REQ-NF-013-OPS-02**: TTV(가입→첫 Export 중앙값) 분석 대시보드 구성  

- **EP1-NFR-REQ-NF-015-OPS-01**: 과제 1차 통과 여부 자기신고 플로우(인앱 설문) 설계  
- **EP1-NFR-REQ-NF-015-OPS-02**: 과제 통과율 ≥65% 모니터링을 위한 코호트/리포트 정의 및 구현  

---

### EPIC 2 – Functional Tasks (F3/F5)

- **EP2-F3-REQ-FUNC-012-FE-01**: 핵심 재무 변수 입력 UI(CAC, ARPU, 이탈률, 고객 수, 고정/변동비) 구현  
- **EP2-F3-REQ-FUNC-012-FE-02**: 생성된 손익계산서/현금흐름표 결과 테이블 뷰 UI 구현  
- **EP2-F3-REQ-FUNC-012-BE-01**: `POST /projects/{id}/financials:generate` Controller/Service 구현  
- **EP2-F3-REQ-FUNC-012-BE-02**: 3년치 손익계산서 및 연 단위 현금흐름표 계산 규칙 구현  
- **EP2-F3-REQ-FUNC-012-BE-03**: 유효 입력값 검증 및 에러 응답 처리 로직 구현  
- **EP2-F3-REQ-FUNC-012-DB-01**: `FinancialModel` 엔티티/테이블 및 `inputs`/`income_statement`/`cashflow_summary` JSON 구조 설계  
- **EP2-F3-REQ-FUNC-012-OPS-01**: 재무 계산 공식 검증을 위한 단위 테스트 세트 작성  
- **EP2-F3-REQ-FUNC-012-OPS-02**: 입력 → 재무 생성 → 저장/조회 플로우 E2E 테스트 작성  

- **EP2-F3-REQ-FUNC-009-FE-01**: LTV/CAC 및 손익분기점 시각화 컴포넌트(차트/요약값) 구현  
- **EP2-F3-REQ-FUNC-009-FE-02**: LTV/CAC<1 또는 손익분기점≥36개월일 때 적색 경고 및 가정 조정 제안 UI 구현  
- **EP2-F3-REQ-FUNC-009-BE-01**: `GET /projects/{id}/unit-economics` Controller/Service 구현  
- **EP2-F3-REQ-FUNC-009-BE-02**: `FinancialModel.unit_economics` 계산 및 저장 로직 구현  
- **EP2-F3-REQ-FUNC-009-DB-01**: `unit_economics` JSON 구조 정의 및 인덱싱 전략 설계(필요 시)  
- **EP2-F3-REQ-FUNC-009-OPS-01**: 다양한 입력 조합에 대한 LTV/CAC·손익분기점 계산 단위 테스트 작성  
- **EP2-F3-REQ-FUNC-009-OPS-02**: 유닛 이코노믹스 API와 FE 뷰를 검증하는 E2E 테스트 작성  

- **EP2-F5-REQ-FUNC-008-FE-01**: PMF 진단 질문(최소 10개) 설문 UI 및 진행률 표시 구현  
- **EP2-F5-REQ-FUNC-008-FE-02**: ‘PMF 진단하기’ 실행 버튼 및 결과 화면(단계/리스크/권고) UI 구현  
- **EP2-F5-REQ-FUNC-008-BE-01**: `POST /projects/{id}/pmf-report:generate`에서 정상 진단 플로우 Controller/Service 구현  
- **EP2-F5-REQ-FUNC-008-BE-02**: PMF Engine(ENG) 호출 및 pmf_stage/risks/recommendations 매핑 로직 구현  
- **EP2-F5-REQ-FUNC-008-ENG-01**: PMF Engine 입력/출력 스키마 정의 및 PMF 진단 알고리즘 구현  
- **EP2-F5-REQ-FUNC-008-ENG-02**: LLM/룰 기반 PMF 단계 산출 및 리스크·권고 생성 로직 구현  
- **EP2-F5-REQ-FUNC-008-DB-01**: `PMFReport` 테이블/엔티티 및 `answers`/`risks`/`recommendations` JSON 구조 설계  
- **EP2-F5-REQ-FUNC-008-OPS-01**: PMF 리포트가 최소 3개 리스크와 권고를 포함하는지 검증하는 테스트 작성  
- **EP2-F5-REQ-FUNC-008-OPS-02**: PMF 질문 입력 → 진단 실행 → 리포트 표시 전체 E2E 테스트 작성  

- **EP2-F5-REQ-FUNC-010-FE-01**: PMF 데이터 부족 시 메시지/체크리스트/예상 소요시간(예: 15분) 안내 UI 구현  
- **EP2-F5-REQ-FUNC-010-FE-02**: 데이터가 충분해질 때까지 진단 결과 섹션을 숨기거나 비활성화하는 로직 구현  
- **EP2-F5-REQ-FUNC-010-BE-01**: `pmf-report:generate`에서 응답 개수 n<=5일 때 데이터 부족 오류 응답 로직 구현  
- **EP2-F5-REQ-FUNC-010-BE-02**: 필수 최소 입력 항목 목록 및 예상 소요시간을 포함한 에러 응답 포맷 정의  
- **EP2-F5-REQ-FUNC-010-OPS-01**: n<=5, n>=10 두 케이스에 대한 PMF 진단 차단/허용 동작 테스트 작성  
- **EP2-F5-REQ-FUNC-010-OPS-02**: 데이터 부족 시 진단 결과가 생성되지 않는지 검증하는 E2E 테스트 작성  

---

### EPIC 2 – NFR / DevOps·QA Tasks

- **EP2-NFR-REQ-NF-002-OPS-01**: PMF 리포트/재무 생성 API에 대한 부하 테스트 스크립트 작성  
- **EP2-NFR-REQ-NF-002-OPS-02**: EPIC 2 관련 생성 요청 p95 ≤10초를 모니터링하는 성능 대시보드 구성  

- **EP2-NFR-REQ-NF-009-OPS-01**: 최소 1,000 동시 세션을 가정한 혼합 트래픽 부하 테스트 시나리오 정의  
- **EP2-NFR-REQ-NF-009-OPS-02**: Auto Scaling·DB 커넥션 풀·캐시 전략 등 동시성 대응 인프라 튜닝 적용  

- **EP2-NFR-REQ-NF-017-OPS-01**: EPIC 1과 EPIC 2 기능 사용 이벤트 스키마 정의(Cross-sell 계산용)  
- **EP2-NFR-REQ-NF-017-OPS-02**: 과제 수행 고객 중 PMF/유닛 이코노믹스 기능 1회 이상 사용 비율 대시보드 구성  
