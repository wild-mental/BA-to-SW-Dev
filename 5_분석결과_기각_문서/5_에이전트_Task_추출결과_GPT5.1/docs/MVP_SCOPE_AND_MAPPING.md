# MVP Scope and Requirement Mapping (GPT-5.1 Edition)

본 문서는 `10_GPT-SRS-V3.md`를 기준으로 **MVP 범위 요구사항**을 정리하고,  
이를 기반으로 `5_에이전트_Task_추출결과/tasks` 아래의 Task 정의와 매핑하기 위한 기초 정보를 제공합니다.

## 1. MVP Scope Definition (from SRS 1.2.1)

| Feature | Description | REQ-FUNC IDs | Priority | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **F1** | Government/Bank Submission Wizard | REQ-FUNC-001, 002, 007, 013 | Must | 프로젝트 생성, 템플릿 선택, Wizard 진행 및 필수 입력/자동저장 |
| **F2** | Template Compatibility + Export (HWP/PDF) | REQ-FUNC-003, 005, 011 | Must | 템플릿 100% 호환, 체크리스트, HWP/PDF Export |
| **F3** | Financial Automation Engine | REQ-FUNC-006, 009, 012 | Must | 재무 정합성 검증, 유닛 이코노믹스, 자동 재무 엔진 |
| **F4** | AI Draft Generation + Editor | REQ-FUNC-003, 004, 014 | Must | AI 초안 생성, 섹션별 보조, 쉬운/전문가 모드 |
| **F5** | PMF Diagnostic Framework | REQ-FUNC-008, 010 | Should | PMF 진단 설문 및 리포트, 데이터 부족 처리 |
| **F6** | On-premise/Private Cloud (Design First) | REQ-FUNC-015, 016 | Post-MVP (Design Only) | 설계/인터페이스 수준, 실제 패키징은 Phase 2 |

> **주의**: MVP 구현 Task는 F1~F5를 중심으로 정의하며, F6 관련 Task는 **설계/인터페이스 수준의 문서화**까지만 포함합니다.

## 2. Functional Requirement to Component Mapping

아래 표는 SRS의 기능 요구사항(REQ-FUNC)을 **컴포넌트 관점**으로 정리한 것입니다.  
각 REQ는 이후 `tasks/functional/*.md`의 `req_ids`, `component`, `context.apis`, `context.entities`에 매핑됩니다.

| REQ ID | Title | Components | APIs (SRS 6.1) | Entities (SRS 6.2) |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-FUNC-001** | 템플릿 목록 제공 | FE, BE | `POST /projects` (selection), `GET /projects/templates` | Project |
| **REQ-FUNC-002** | Wizard 단계 진행 | FE, BE | `POST /projects/{id}/wizard/steps`, `GET /projects/{id}` | Project |
| **REQ-FUNC-003** | 사업계획서 초안 자동 생성 | FE, BE, AI-Engine | `POST /projects/{id}/documents/business-plan:generate` | BusinessPlanDocument |
| **REQ-FUNC-004** | 섹션별 AI 작성 보조 | FE, BE, AI-Engine | `POST /projects/{id}/documents/business-plan:generate` (partial/section) | BusinessPlanDocument |
| **REQ-FUNC-005** | 제출 가능성 체크리스트 | FE, BE, AI-Engine | `POST /projects/{id}/documents/business-plan:checklist` | BusinessPlanDocument |
| **REQ-FUNC-006** | 재무-서술 논리 정합성 검증 | FE, BE, Logic-Engine | `POST /projects/{id}/documents-business-plan:consistency-check` | FinancialModel, PMFReport |
| **REQ-FUNC-007** | 필수 입력 누락 방지 | FE, BE | (Validation Logic – Wizard/Forms) | Project |
| **REQ-FUNC-008** | PMF 진단 리포트 생성 | FE, BE, AI-Engine | `POST /projects/{id}/pmf-report:generate` | PMFReport |
| **REQ-FUNC-009** | 유닛 이코노믹스 뷰 제공 | FE, BE | `GET /projects/{id}/unit-economics` | FinancialModel |
| **REQ-FUNC-010** | 데이터 부족 시 PMF 진단 차단 | FE, BE | (Validation Logic – PMF answers) | PMFReport |
| **REQ-FUNC-011** | HWP/PDF 내보내기 | FE, BE, Converter | `GET /projects/{id}/export?format=hwp|pdf` | BusinessPlanDocument |
| **REQ-FUNC-012** | 재무 자동화 엔진 | FE, BE, Logic-Engine | `POST /projects/{id}/financials:generate` | FinancialModel |
| **REQ-FUNC-013** | 자동 저장(Auto-save) | FE, BE | `POST /projects/{id}/wizard/steps` | Project |
| **REQ-FUNC-014** | 쉬운 모드/전문가 모드 전환 | FE | (Client-side state / view only) | - |
| **REQ-FUNC-015** | On-premise 배포 패키지 | Infra, DevOps | (배포 스크립트/API 없음 or 내부 Mgmt API) | WorkspaceSecuritySetting 등 |
| **REQ-FUNC-016** | 워크스페이스별 데이터 격리 설정 | BE, Infra | (Admin/Workspace Mgmt API – 설계 우선) | WorkspaceSecuritySetting |

### 2.1 Component Tagging 기준

- **FE (Frontend)**: React + Vite 기반 Web Client, Admin Console.
- **BE (Backend)**: Spring Boot 기반 Core API 서버.
- **AI-Engine**: FastAPI + LLM Gateway 기반 문서/PMF 엔진.
- **Logic-Engine**: 재무/정합성 등 규칙 기반 엔진(FastAPI 등).
- **Infra/DevOps**: 배포, 모니터링, 보안, On-premise 패키징 등.

## 3. Non-Functional Requirements (SRS 4.2)

다음 표는 SRS의 비기능 요구사항(REQ-NF)을 **카테고리 및 범위** 중심으로 정리한 것입니다.  
각 항목은 `tasks/non-functional/*.md` 파일과 매핑됩니다.

| ID | Category | Scope | Notes |
| :--- | :--- | :--- | :--- |
| **REQ-NF-001** | Performance | Wizard 단계 전환 응답시간(p95 ≤ 800ms) | k6 기반 부하 테스트, APM 연동 |
| **REQ-NF-002** | Performance | 문서/리포트 생성 응답시간(p95 ≤ 10초) | 비동기 처리/큐 도입 가능성 고려 |
| **REQ-NF-003** | Availability | 월간 서비스 가용성 ≥ 99.5% | 장애 시간 관리, Health Check, 롤링 배포 |
| **REQ-NF-004** | Reliability | 주요 기능 오류율 ≤ 0.5%/월 | 에러 로깅, 재시도, 회로 차단기 등 |
| **REQ-NF-005** | Reliability | 자동 저장 주기 ≤ 10초 | Auto-save 구현/검증, 타임스탬프 로그 |
| **REQ-NF-006** | Security | 저장 데이터 암호화 | AES-256, 암호화 컨버터, 키 관리 전략 |
| **REQ-NF-007** | Security | 전송 구간 암호화(TLS) | HTTPS 강제, TLS 1.2+ |
| **REQ-NF-008** | Security | 데이터 격리 | 워크스페이스별/배포옵션별 격리 |
| **REQ-NF-009** | Scalability | 동시 1,000 세션 처리 | 수평 확장, 커넥션 풀/캐시 튜닝 |
| **REQ-NF-010** | Maintainability | 템플릿 추가 리드타임 ≤ 3영업일 | 구성 기반 템플릿/Validator 설계 |
| **REQ-NF-011** | Cost | 사용자당 LLM 토큰 상한 | 토큰 한도, 업셀 플로우, 비용 모니터링 |
| **REQ-NF-012** | Monitoring / Alerting | 장애 알림 기준 | 로그/메트릭, Alerting 룰, 온콜 채널 |
| **REQ-NF-013~017** | Business KPI | TTV, Activation, NPS 등 | 제품 분석 도구/이벤트 스키마 필요 |
| **REQ-NF-018** | Resilience | RPO ≤ 1h, RTO ≤ 4h | 백업/복구, DR 리허설 |

### 3.1 카테고리 / 레이블 초안

이 카테고리와 레이블은 `tasks/non-functional/*.md`의 `category`, `labels` 필드에서 재사용합니다.

- **performance**: `"performance:latency"`, `"performance:throughput"`, `"performance:load"`.
- **security**: `"security:auth"`, `"security:encryption"`, `"security:data_isolation"`.
- **operations / observability**: `"logging:*"`, `"monitoring:*"`, `"alerting:*"`.
- **business_kpi**: `"kpi:ttv"`, `"kpi:activation"`, `"kpi:nps"`, `"kpi:pmf"`.
- **resilience**: `"resilience:backup"`, `"resilience:dr"`.

## 4. Mapping → Task 파일 구조 규칙

- **Epic 0 (FE PoC)**  
  - `tasks/functional/EPIC0-FE-XXX.md`  
  - SRS REQ-FUNC와 느슨하게 연결되며, `epic: "EPIC_0_FE_PROTOTYPE"`으로 태깅.

- **REQ-FUNC 기반 Task**  
  - `tasks/functional/REQ-FUNC-XXX-<COMP>-NNN.md`  
    - 예: `REQ-FUNC-003-BE-001`, `REQ-FUNC-003-AI-001`  
  - 최소 1개, 필요 시 컴포넌트별로 다수 정의.

- **REQ-NF 기반 Task**  
  - `tasks/non-functional/REQ-NF-XXX-<CAT>-NNN.md`  
    - 예: `REQ-NF-001-PERF-001`, `REQ-NF-006-SEC-001`  
  - `category`, `labels`, `related_req_func` 등으로 기능 요구사항과 연결.

이 문서는 이후 **Task 정의 품질 점검(11-2 단계 6 및 11-1 단계 8)** 시,  
누락된 REQ-FUNC/REQ-NF에 대한 Task가 없는지 검증하는 기준 시트로 활용됩니다.


