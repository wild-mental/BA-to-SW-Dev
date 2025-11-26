# REQ-NF-001-PERF-001: 주요 API 성능 테스트 환경 구축

## 1. 개요
- **목표**: Wizard 단계 전환, 문서 생성 등 주요 시나리오에 대한 부하 테스트 스크립트를 작성하고, p95 응답시간 목표(REQ-NF-001, 002) 달성 여부를 검증한다.
- **범위**:
  - k6 부하 테스트 스크립트 작성
  - CI 파이프라인 연동 (선택 사항)
  - 성능 리포트 생성
- **Out of Scope**: 극한의 DDoS 테스트.

## 2. 상세 요구사항
- **목표치**:
  - Wizard 단계 전환: p95 ≤ 800ms
  - 문서 생성: p95 ≤ 10s (Async Polling 포함)
- **시나리오**: "로그인 -> 프로젝트 생성 -> Wizard 답변 5개 입력 -> 초안 생성" 흐름 반복.

---

```yaml
task_id: "REQ-NF-001-PERF-001"
title: "API 성능 목표 검증을 위한 k6 부하 테스트 구현"
summary: >
  REQ-NF-001, 002 성능 목표(Wizard 800ms, Gen 10s)를 검증하기 위한 
  k6 테스트 스크립트와 실행 환경을 구축한다.
type: "non_functional"
epic: "EPIC_PERFORMANCE"
req_ids: ["REQ-NF-001", "REQ-NF-002", "REQ-NF-009"]
component: ["test.perf"]
agent_profile: ["infra", "qa"]

category: "performance"
labels:
  - "performance:latency"
  - "performance:load"

requirements:
  description: "동시 접속 1,000명(가정) 상황에서도 p95 목표를 준수해야 함."
  kpis:
    - "Wizard API p95 < 800ms"
    - "Generation API p95 < 10s"

steps_hint:
  - "k6 설치 및 기본 시나리오 스크립트(script.js) 작성"
  - "가상 유저(VU) 램프업 설정"
  - "Threshold(임계값) 설정 (p95 > 800ms 시 Fail)"
  - "테스트 실행 및 리포트 분석"

preconditions:
  - "백엔드 API 서버가 배포되어 있거나 로컬 실행 가능해야 한다."

postconditions:
  - "부하 테스트 결과 리포트가 생성되고, 성능 병목 구간이 식별된다."

dependencies:
  - "REQ-FUNC-003-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-006-SEC-001.md
# REQ-NF-006-SEC-001: 데이터 암호화 및 보안 설정

## 1. 개요
- **목표**: 민감 데이터(사업계획서, 개인정보)의 저장 시 암호화 및 전송 구간 암호화를 적용한다.
- **범위**:
  - DB 컬럼 암호화 (AES-256) 또는 암호화된 파일 시스템 사용 확인
  - Spring Security HTTPS 강제 설정
  - 비밀번호 단방향 해시 (BCrypt)
- **Out of Scope**: KMS(Key Management Service) 연동 (MVP에서는 환경변수/Secret 사용).

## 2. 상세 요구사항
- **저장 암호화**: `wizard_answers`, `financial_model` 등 비즈니스 데이터는 암호화하여 저장한다(선택: DB 레벨 or App 레벨). MVP는 App 레벨 컨버터 권장.
- **전송 보안**: 모든 API는 HTTPS만 허용.

---

```yaml
task_id: "REQ-NF-006-SEC-001"
title: "데이터 저장/전송 암호화 및 보안 구성"
summary: >
  사용자 비밀번호 BCrypt 해싱, 중요 데이터 AES 암호화 저장, 
  HTTPS 강제 설정을 통해 기본 보안 요구사항을 충족한다.
type: "non_functional"
epic: "EPIC_SECURITY"
req_ids: ["REQ-NF-006", "REQ-NF-007"]
component: ["backend.security"]
agent_profile: ["backend", "infra"]

category: "security"
labels:
  - "security:encryption"
  - "security:auth"

requirements:
  description: "모든 민감 데이터는 평문으로 저장되어서는 안 된다."
  kpis:
    - "보안 감사 지적 사항 0건"

steps_hint:
  - "Spring Security: PasswordEncoder(BCrypt) 빈 등록"
  - "JPA AttributeConverter를 이용한 AES-256 암호화 구현"
  - "application.yml: server.ssl.enabled=true 설정 (Self-signed for local)"

preconditions:
  - "없음"

postconditions:
  - "DB 조회 시 민감 컬럼이 암호문으로 보여야 한다."

dependencies:
  - "REQ-FUNC-001-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-012-OPS-001.md
# REQ-NF-012-OPS-001: 모니터링 및 로깅 파이프라인 구축

## 1. 개요
- **목표**: 시스템 운영 상태를 파악할 수 있는 구조화된 로깅과 기초 모니터링 대시보드를 구축한다.
- **범위**:
  - Logback 설정 (JSON 포맷, TraceId 포함)
  - Prometheus Actuator Endpoint 노출
  - Grafana 대시보드 (JVM, HTTP Request Rate/Error/Duration)
- **Out of Scope**: 정교한 알림 룰셋(MVP는 Error 로그 기반 기본 알림만).

## 2. 상세 요구사항
- **로깅**: `timestamp`, `level`, `trace_id`, `user_id`, `message`, `context` 필드 포함.
- **메트릭**: 문서 생성 API 호출 수, 평균 소요 시간, 에러율 시각화.

---

```yaml
task_id: "REQ-NF-012-OPS-001"
title: "구조화된 로깅 및 Prometheus/Grafana 모니터링 구축"
summary: >
  운영 가시성 확보를 위해 JSON 로그 포맷을 적용하고,
  Prometheus/Grafana 기반의 애플리케이션 모니터링을 설정한다.
type: "non_functional"
epic: "EPIC_OBSERVABILITY"
req_ids: ["REQ-NF-012", "REQ-NF-004"]
component: ["infra.monitoring"]
agent_profile: ["infra"]

category: "observability"
labels:
  - "logging:json"
  - "monitoring:application"

requirements:
  description: "장애 발생 시 1분 이내에 로그 및 메트릭으로 확인 가능해야 함."
  kpis:
    - "로그 인덱싱 지연 < 1min"

steps_hint:
  - "Logback xml 설정: LogstashEncoder 등 사용해 JSON 출력"
  - "Spring Boot Actuator 및 Micrometer Prometheus 의존성 추가"
  - "docker-compose.yml에 Prometheus/Grafana 추가 및 연동"
  - "기본 대시보드(Request, Error, JVM) 구성"

preconditions:
  - "Docker 환경 권장"

postconditions:
  - "API 호출 시 JSON 로그가 남고, Grafana에서 그래프가 그려진다."

dependencies:
  - "REQ-FUNC-001-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-002-PERF-002.md
# REQ-NF-002-PERF-002: 문서/PMF 생성 성능 최적화 및 검증

## 1. 개요
- **목표**: 사업계획서 초안 생성 및 PMF 진단 요청의 p95 응답시간이 10초 이내가 되도록, 비동기 처리/큐/타임아웃 및 재시도 전략을 설계·검증한다.
- **범위**:
  - LLM 호출 타임아웃/재시도 정책 정의
  - 비동기 처리(예: Job Queue) 도입 여부 검토 및 PoC
  - 성능 측정 및 튜닝
- **Out of Scope**: LLM 모델 자체 튜닝.

---

```yaml
task_id: "REQ-NF-002-PERF-002"
title: "문서/PMF 생성 성능 최적화 및 검증"
summary: >
  LLM 기반 문서/PMF 생성 플로우의 p95 응답시간을 10초 이내로 유지하기 위한
  비동기 처리/타임아웃/재시도 전략을 설계하고 검증한다.
type: "non_functional"
epic: "EPIC_PERFORMANCE"
req_ids: ["REQ-NF-002"]
component: ["backend.api", "ai.engine"]
agent_profile: ["backend", "ml"]

category: "performance"
labels:
  - "performance:latency"

requirements:
  description: "문서/PMF 생성 요청에 대해 p95 ≤ 10초를 만족해야 한다."
  kpis:
    - "business-plan:generate p95 < 10s"
    - "pmf-report:generate p95 < 10s"

steps_hint:
  - "현재 동기 호출 구조에서 응답시간 측정"
  - "필요 시 Job Queue 기반 비동기 구조 설계"
  - "타임아웃/재시도/백오프 정책 설정"

preconditions:
  - "REQ-FUNC-003-BE-001, REQ-FUNC-008-AI-001 구현 완료"

postconditions:
  - "부하 테스트에서 성능 목표를 만족하는 구조가 검증된다."

dependencies:
  - "REQ-NF-001-PERF-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-003-AVAIL-001.md
# REQ-NF-003-AVAIL-001: 서비스 가용성 및 Health Check 전략 설계

## 1. 개요
- **목표**: 월간 서비스 가용성 ≥ 99.5%를 달성하기 위해 Health Check, 롤링 배포, 장애 대응 전략을 설계한다.
- **범위**:
  - Liveness/Readiness Probe 설계
  - 배포 전략(Blue/Green 또는 Rolling) 정의
  - 장애 시 빠른 복구 프로세스 수립
- **Out of Scope**: 멀티 리전 DR(REQ-NF-018 Task에서 다룸).

---

```yaml
task_id: "REQ-NF-003-AVAIL-001"
title: "서비스 가용성 및 Health Check 전략 설계"
summary: >
  99.5% 이상 가용성을 달성하기 위한 Health Check 및 배포 전략을 설계하고,
  기본 설정을 인프라에 반영한다.
type: "non_functional"
epic: "EPIC_OBSERVABILITY"
req_ids: ["REQ-NF-003"]
component: ["infra.deployment"]
agent_profile: ["infra"]

category: "operations"
labels:
  - "monitoring:infrastructure"
  - "operations:availability"

requirements:
  description: "월 장애 시간 < 3.6시간을 목표로 한다."
  kpis:
    - "월간 가용성 ≥ 99.5%"

steps_hint:
  - "API 서버 Health Check 엔드포인트 정의"
  - "Kubernetes/LB Health Check 설정"
  - "롤링 배포 전략 문서화 및 적용"

preconditions:
  - "기본 배포 환경이 준비되어 있어야 한다."

postconditions:
  - "장애 시 자동 재시도/재배포로 빠른 복구가 가능하다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-004-REL-002.md
# REQ-NF-004-REL-002: 주요 기능 오류율 모니터링 및 회로 차단기 설계

## 1. 개요
- **목표**: 문서 생성·PMF 진단·내보내기 등 주요 API의 오류율 ≤ 0.5%/월을 달성하기 위한 모니터링 및 보호 장치를 설계한다.
- **범위**:
  - 오류율 모니터링 메트릭 정의
  - 회로 차단기(Circuit Breaker) 패턴 적용(필요 시)
  - 에러 알림 및 대응 플로우 정의
- **Out of Scope**: 모든 외부 시스템에 대한 세밀한 회복 전략(우선 핵심 경로에 집중).

---

```yaml
task_id: "REQ-NF-004-REL-002"
title: "주요 기능 오류율 모니터링 및 회로 차단기 설계"
summary: >
  주요 API 오류율을 추적하고, 연쇄 장애를 막기 위한 회로 차단기 및
  에러 대응 전략을 설계한다.
type: "non_functional"
epic: "EPIC_OBSERVABILITY"
req_ids: ["REQ-NF-004", "REQ-NF-012"]
component: ["backend.api", "infra.monitoring"]
agent_profile: ["backend", "infra"]

category: "reliability"
labels:
  - "monitoring:application"
  - "alerting:oncall_critical"

requirements:
  description: "문서 생성/PMF/Export API 오류율 ≤ 0.5%/월."
  kpis:
    - "주요 API 오류율 ≤ 0.5%/월"

steps_hint:
  - "에러율 메트릭 정의 및 Prometheus 지표 추가"
  - "회로 차단기 라이브러리(Resilience4j 등) 적용 여부 검토"
  - "오류율 임계치 초과 시 알림 발송 및 조치 플로우 문서화"

preconditions:
  - "REQ-NF-012-OPS-001 기본 모니터링이 구축되어 있어야 한다."

postconditions:
  - "에러율 증가 시 빠르게 탐지하고, 연쇄 장애를 차단할 수 있다."

dependencies:
  - "REQ-NF-012-OPS-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-005-REL-001.md
# REQ-NF-005-REL-001: 자동 저장(Auto-save) 주기 및 안정성 검증

## 1. 개요
- **목표**: 편집 중 자동 저장 주기 ≤ 10초를 달성하고, 실제로 데이터 손실이 최소화되는지 검증한다.
- **범위**:
  - Auto-save 이벤트 로그/메트릭 정의
  - E2E 테스트 시나리오 작성
  - 실패/충돌 시 복구 전략 검증
- **Out of Scope**: UI/UX 개선(별도 FE Task 참조).

---

```yaml
task_id: "REQ-NF-005-REL-001"
title: "자동 저장 주기 및 안정성 검증"
summary: >
  자동 저장 기능이 10초 이내 주기로 안정적으로 동작하는지,
  실제 편집 세션에서 데이터 손실 없이 복원 가능한지를 검증한다.
type: "non_functional"
epic: "EPIC_PERFORMANCE"
req_ids: ["REQ-NF-005", "REQ-FUNC-013"]
component: ["qa.e2e"]
agent_profile: ["qa"]

category: "reliability"
labels:
  - "performance:latency"
  - "reliability:autosave"

requirements:
  description: "자동 저장 주기 ≤ 10초, 크래시/브라우저 종료 후에도 최근 상태 복원."
  kpis:
    - "Auto-save 주기 평균 < 10초"
    - "복원 실패율 < 0.1%"

steps_hint:
  - "Auto-save 로그/메트릭 스키마 정의"
  - "E2E 테스트(브라우저 자동화)로 크래시/재접속 시나리오 검증"
  - "복원 실패 케이스 분석 및 개선"

preconditions:
  - "REQ-FUNC-013-BE-001 구현 완료"

postconditions:
  - "실제 사용 환경에서 데이터 손실 사례가 극히 드물다."

dependencies:
  - "REQ-FUNC-013-BE-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-008-SEC-002.md
# REQ-NF-008-SEC-002: 워크스페이스/배포옵션별 데이터 격리 검증

## 1. 개요
- **목표**: Cloud/Private/On-premise 각 배포 모드에서 데이터/로그가 지정된 경계 밖으로 유출되지 않는지 검증한다.
- **범위**:
  - 데이터 격리 테스트 계획 수립
  - 샘플 워크스페이스를 서로 다른 모드로 생성하여 격리 여부 검증
  - 로그/백업 경로 확인
- **Out of Scope**: 규제 기관 인증(추후 별도 컴플라이언스 프로젝트에서 다룸).

---

```yaml
task_id: "REQ-NF-008-SEC-002"
title: "워크스페이스/배포옵션별 데이터 격리 검증"
summary: >
  워크스페이스 단위로 데이터와 로그가 지정된 인프라 경계 내에만
  존재하는지 검증하는 테스트를 설계/수행한다.
type: "non_functional"
epic: "EPIC_SECURITY"
req_ids: ["REQ-NF-008", "REQ-FUNC-016"]
component: ["qa.security"]
agent_profile: ["qa", "infra"]

category: "security"
labels:
  - "security:data_isolation"

requirements:
  description: "각 모드별로 데이터/로그가 경계 밖 스토리지에 저장되지 않아야 한다."
  kpis:
    - "데이터 격리 위반 사례 0건"

steps_hint:
  - "워크스페이스를 서로 다른 deployment_mode로 생성"
  - "DB/스토리지/로그 경로를 점검"
  - "On-premise 모드에서 외부 네트워크 연결 차단 상태 테스트"

preconditions:
  - "REQ-FUNC-016-BE-001, REQ-FUNC-015-INF-001 설계/구현 완료"

postconditions:
  - "각 모드별 데이터 경계가 문서화되고, 위반 시 조치 플로우가 정의된다."

dependencies:
  - "REQ-FUNC-016-BE-001"
  - "REQ-FUNC-015-INF-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-009-SCALE-001.md
# REQ-NF-009-SCALE-001: 동시 1,000 세션 스케일링 전략 및 검증

## 1. 개요
- **목표**: 최소 1,000 동시 세션에서 성능 목표(REQ-NF-001~002)를 유지하기 위한 스케일링 전략을 설계하고, 부하 테스트로 검증한다.
- **범위**:
  - 스케일링 정책(HPA, Auto Scaling Group 등) 정의
  - 커넥션 풀/스레드 풀 설정 튜닝
  - 대규모 부하 테스트 시나리오 작성
- **Out of Scope**: 극단적 피크 트래픽(마케팅 캠페인 등) 대응.

---

```yaml
task_id: "REQ-NF-009-SCALE-001"
title: "동시 1,000 세션 스케일링 전략 및 검증"
summary: >
  동시 1,000 세션을 처리하면서도 성능 목표를 유지할 수 있는
  스케일링/튜닝 전략을 설계하고 부하 테스트로 검증한다.
type: "non_functional"
epic: "EPIC_PERFORMANCE"
req_ids: ["REQ-NF-009", "REQ-NF-001", "REQ-NF-002"]
component: ["infra.deployment", "test.perf"]
agent_profile: ["infra", "qa"]

category: "performance"
labels:
  - "performance:throughput"
  - "performance:load"

requirements:
  description: "동시 1,000 세션에서 REQ-NF-001~002를 준수."
  kpis:
    - "동시 1,000 세션에서 p95 성능 목표 만족"

steps_hint:
  - "HPA/Auto Scaling 설정 설계"
  - "DB/캐시 커넥션 풀 사이즈 조정"
  - "대규모 부하 테스트 스크립트 작성 및 실행"

preconditions:
  - "REQ-NF-001-PERF-001 기본 부하 테스트 환경 구축"

postconditions:
  - "스케일링 정책이 실제로 성능 목표를 만족함이 검증된다."

dependencies:
  - "REQ-NF-001-PERF-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-010-MAINT-001.md
# REQ-NF-010-MAINT-001: 템플릿/Validator 구성 기반 업데이트 플로우 설계

## 1. 개요
- **목표**: 신규 정부 사업 템플릿을 3영업일 이내에 운영에 반영할 수 있도록, 코드 수정 없이 구성(설정)만으로 필드/Validator를 추가·변경하는 플로우를 설계한다.
- **범위**:
  - 템플릿/Validator 메타데이터 스키마 정의
  - 관리 UI 또는 설정 파일 구조 설계
  - 배포/롤백 플로우 정의
- **Out of Scope**: 복잡한 버전 비교 UI.

---

```yaml
task_id: "REQ-NF-010-MAINT-001"
title: "템플릿/Validator 구성 기반 업데이트 플로우 설계"
summary: >
  템플릿과 Validator를 코드 변경 없이 설정 변경만으로 업데이트할 수 있는
  구성 기반 업데이트 플로우를 설계한다.
type: "non_functional"
epic: "EPIC_OPERATIONS"
req_ids: ["REQ-NF-010", "REQ-FUNC-001", "REQ-FUNC-007"]
component: ["backend.core", "admin.ui"]
agent_profile: ["backend"]

category: "operations"
labels:
  - "operations:template_management"

requirements:
  description: "요구사항 분석 이후 3영업일 이내 템플릿 반영 가능."
  kpis:
    - "템플릿 변경 리드타임 ≤ 3영업일"

steps_hint:
  - "템플릿/Validator 메타데이터 JSON/YAML 스키마 설계"
  - "구성 파일 로더/캐시 로직 구현"
  - "운영 중 설정 변경 및 롤백 전략 수립"

preconditions:
  - "기본 템플릿 구조 및 필드 정의 존재"

postconditions:
  - "신규 템플릿 추가 시 코드 수정 없이 설정 변경만으로 운영 반영 가능."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-011-COST-001.md
# REQ-NF-011-COST-001: LLM 토큰 사용량 제한 및 비용 모니터링

## 1. 개요
- **목표**: 사용자당 월 LLM 토큰 사용량을 100,000 토큰 이내로 제한하고, 초과 시 경고 또는 업셀 플로우를 제공한다.
- **범위**:
  - 토큰 사용량 추적 로직 구현
  - 사용자/워크스페이스 단위 한도 관리
  - 비용 모니터링 대시보드 설계
- **Out of Scope**: 복잡한 빌링/결제 시스템(결제 게이트웨이 연동은 별도).

---

```yaml
task_id: "REQ-NF-011-COST-001"
title: "LLM 토큰 사용량 제한 및 비용 모니터링 구현"
summary: >
  사용자/워크스페이스 단위 LLM 토큰 사용량을 추적하고,
  한도 초과 시 경고/업셀 플로우를 제공하는 로직을 구현한다.
type: "non_functional"
epic: "EPIC_COST"
req_ids: ["REQ-NF-011"]
component: ["backend.core", "infra.monitoring"]
agent_profile: ["backend", "infra"]

category: "cost"
labels:
  - "cost:llm_tokens"

requirements:
  description: "사용자당 월 LLM 토큰 사용량 ≤ 100,000."
  kpis:
    - "한도 초과 사용자 비율"

steps_hint:
  - "LLM Gateway 응답 헤더/메타데이터에서 사용 토큰 수 추적"
  - "월별 사용자/워크스페이스 토큰 집계 테이블 설계"
  - "한도 초과 시 경고/업셀 UI 트리거 로직 정의"

preconditions:
  - "LLM Gateway가 토큰 사용량 정보를 제공해야 한다."

postconditions:
  - "한도 관리와 비용 모니터링이 가능해진다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-013-KPI-001.md
# REQ-NF-013-KPI-001: TTV(Time-to-Value) 이벤트 설계 및 분석

## 1. 개요
- **목표**: 가입 후 첫 '내보내기' 이벤트까지의 시간 중앙값 ≤ 30분을 측정/개선할 수 있도록, 관련 이벤트 스키마와 분석 플로우를 설계한다.
- **범위**:
  - TTV 관련 이벤트 정의(가입, 첫 프로젝트 생성, 첫 내보내기 등)
  - 분석 도구 연동(GA, Amplitude 등)
  - 기본 리포트/대시보드 설계

---

```yaml
task_id: "REQ-NF-013-KPI-001"
title: "TTV(Time-to-Value) 측정을 위한 이벤트 설계"
summary: >
  가입에서 첫 내보내기까지 걸리는 시간을 측정하기 위한
  이벤트 스키마와 분석 플로우를 설계한다.
type: "non_functional"
epic: "EPIC_BUSINESS_KPI"
req_ids: ["REQ-NF-013"]
component: ["analytics"]
agent_profile: ["data"]

category: "business_kpi"
labels:
  - "kpi:ttv"

requirements:
  description: "TTV 중앙값 ≤ 30분을 목표로 한다."
  kpis:
    - "Median TTV"

steps_hint:
  - "핵심 이벤트 정의 및 속성 설계"
  - "프론트/백엔드에 이벤트 로깅 삽입"
  - "분석 도구에 대시보드 구성"

preconditions:
  - "기본 이벤트 로깅 인프라 존재"

postconditions:
  - "TTV를 정기적으로 모니터링할 수 있다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-014-KPI-002.md
# REQ-NF-014-KPI-002: Activation Rate 측정을 위한 퍼널 설계

## 1. 개요
- **목표**: 가입 후 첫 세션 내 '제출 가능' 산출물 완성률 ≥ 40%를 측정/개선하기 위한 퍼널을 설계한다.
- **범위**:
  - Activation 관련 이벤트 정의
  - 퍼널 단계 설계(가입 → 프로젝트 생성 → Wizard 완료 → 제출 가능 상태)
  - 대시보드/리포트 구성

---

```yaml
task_id: "REQ-NF-014-KPI-002"
title: "Activation Rate 측정을 위한 퍼널 설계"
summary: >
  첫 세션 내 제출 가능 산출물 완성률을 측정하기 위한
  이벤트 퍼널과 대시보드를 설계한다.
type: "non_functional"
epic: "EPIC_BUSINESS_KPI"
req_ids: ["REQ-NF-014"]
component: ["analytics"]
agent_profile: ["data"]

category: "business_kpi"
labels:
  - "kpi:activation"

requirements:
  description: "Activation Rate ≥ 40%."
  kpis:
    - "Activation Rate"

steps_hint:
  - "Activation 정의(어떤 이벤트 조합을 Activation으로 볼지) 합의"
  - "퍼널 이벤트/속성 설계"
  - "퍼널 분석 리포트 구성"

preconditions:
  - "REQ-NF-013-KPI-001 이벤트 프레임이 존재하면 활용"

postconditions:
  - "Activation Rate를 모니터링하고 개선 시점을 판단할 수 있다."

dependencies:
  - "REQ-NF-013-KPI-001"
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-015-KPI-003.md
# REQ-NF-015-KPI-003: 과제 통과율 측정을 위한 설문/데이터 수집 플로우

## 1. 개요
- **목표**: AI Co-Pilot 사용자의 자기신고 서류 1차 통과율 ≥ 65%를 측정하기 위해, 과제 결과를 수집하는 설문/데이터 입력 플로우를 설계한다.
- **범위**:
  - 통과 여부/사유 입력 설문 설계
  - 일정 시점(예: 과제 발표 후) 리마인더/알림 플로우
  - 데이터 집계/리포트

---

```yaml
task_id: "REQ-NF-015-KPI-003"
title: "과제 통과율 측정을 위한 설문/데이터 수집 플로우 설계"
summary: >
  사용자의 과제 결과(통과/탈락)를 수집하고,
  통과율을 집계할 수 있는 설문 및 데이터 수집 플로우를 설계한다.
type: "non_functional"
epic: "EPIC_BUSINESS_KPI"
req_ids: ["REQ-NF-015"]
component: ["analytics", "frontend.web"]
agent_profile: ["data", "frontend"]

category: "business_kpi"
labels:
  - "kpi:pass_rate"

requirements:
  description: "자기신고 기반 통과율 ≥ 65%."
  kpis:
    - "Pass Rate"

steps_hint:
  - "과제 결과 입력 UI/설문 설계"
  - "리마인더 이메일/인앱 알림 플로우 설계"
  - "결과 집계/리포트 구성"

preconditions:
  - "기본 이메일/알림 인프라 존재"

postconditions:
  - "과제 통과율을 정량적으로 추적할 수 있다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-016-KPI-004.md
# REQ-NF-016-KPI-004: NPS 수집/분석 플로우 설계

## 1. 개요
- **목표**: 비전문가 사용자의 NPS ≥ 40을 측정/개선하기 위해, NPS 설문 수집 및 분석 플로우를 설계한다.
- **범위**:
  - NPS 설문(0~10점) 및 이유 입력 UI
  - 주기/트리거(예: 내보내기 후, 일정 사용 기간 이후) 정의
  - NPS 계산/분석 리포트 구성

---

```yaml
task_id: "REQ-NF-016-KPI-004"
title: "NPS 수집/분석 플로우 설계"
summary: >
  비전문가 사용자를 대상으로 NPS를 수집하고,
  정기적으로 분석할 수 있는 플로우를 설계한다.
type: "non_functional"
epic: "EPIC_BUSINESS_KPI"
req_ids: ["REQ-NF-016"]
component: ["analytics", "frontend.web"]
agent_profile: ["data", "frontend"]

category: "business_kpi"
labels:
  - "kpi:nps"

requirements:
  description: "NPS ≥ 40."
  kpis:
    - "NPS Score"

steps_hint:
  - "NPS 설문 UI/Trigger 정의"
  - "NPS 계산 로직 및 리포트 구성"
  - "피드백 카테고리 분류 규칙 정의"

preconditions:
  - "기본 설문/이벤트 로깅 인프라 존재"

postconditions:
  - "NPS를 정기적으로 측정하고 추세를 파악할 수 있다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-017-KPI-005.md
# REQ-NF-017-KPI-005: Cross-sell Rate(학습/검증 기능 사용률) 추적

## 1. 개요
- **목표**: 과제 수행 고객 중 학습/검증 기능(PMF 진단 등) 1회 이상 사용 비율 ≥ 10%를 측정하기 위한 이벤트/지표를 설계한다.
- **범위**:
  - Cross-sell 관련 이벤트 정의(PMF 진단, 재무 정합성 검증 등)
  - 기준 모수 정의(과제 제출 사용자 집합)
  - 비율 계산 및 리포트 구성

---

```yaml
task_id: "REQ-NF-017-KPI-005"
title: "Cross-sell Rate(학습/검증 기능 사용률) 추적 설계"
summary: >
  학습/검증 기능 사용 여부를 기준으로 Cross-sell Rate를 측정하기 위한
  이벤트/지표를 설계한다.
type: "non_functional"
epic: "EPIC_BUSINESS_KPI"
req_ids: ["REQ-NF-017"]
component: ["analytics"]
agent_profile: ["data"]

category: "business_kpi"
labels:
  - "kpi:cross_sell"

requirements:
  description: "Cross-sell Rate ≥ 10%."
  kpis:
    - "Cross-sell Rate"

steps_hint:
  - "과제 제출 사용자 집합 정의"
  - "학습/검증 기능 사용 이벤트 정의"
  - "비율 계산 로직 및 리포트 구성"

preconditions:
  - "REQ-FUNC-008-AI-001, REQ-FUNC-006-BE-001 기능이 이벤트 로깅과 연동되어야 함"

postconditions:
  - "Cross-sell Rate를 모니터링하고 기능 사용 촉진 전략을 세울 수 있다."

dependencies: []
```

*** Add File: 5_에이전트_Task_추출결과/tasks/non-functional/REQ-NF-018-RESIL-001.md
# REQ-NF-018-RESIL-001: 백업 및 복구(Backup & DR) 구성 및 리허설

## 1. 개요
- **목표**: RPO ≤ 1시간, RTO ≤ 4시간을 만족하기 위한 백업/복구 전략을 설계하고, 정기적인 DR 리허설을 수행한다.
- **범위**:
  - DB/스토리지 백업 정책 설정
  - 복구 절차 문서화
  - DR 리허설 계획/수행/리포트

---

```yaml
task_id: "REQ-NF-018-RESIL-001"
title: "백업 및 복구(Backup & DR) 구성 및 리허설"
summary: >
  데이터 백업/복구 전략을 설계하고, DR 리허설을 통해
  RPO/RTO 목표를 만족하는지 검증한다.
type: "non_functional"
epic: "EPIC_RESILIENCE"
req_ids: ["REQ-NF-018"]
component: ["infra.deployment"]
agent_profile: ["infra"]

category: "resilience"
labels:
  - "resilience:backup"
  - "resilience:dr"

requirements:
  description: "RPO ≤ 1시간, RTO ≤ 4시간."
  kpis:
    - "실제 DR 리허설에서 측정된 RPO/RTO"

steps_hint:
  - "DB/스토리지 백업 주기/보존 기간 설정"
  - "복구 절차 Runbook 작성"
  - "정기 DR 리허설 계획/실행 및 결과 기록"

preconditions:
  - "기본 인프라 및 모니터링 환경 구성 완료"

postconditions:
  - "실제 장애 상황에서도 정의된 RPO/RTO 내 복구 가능함이 검증된다."

dependencies: []
```


