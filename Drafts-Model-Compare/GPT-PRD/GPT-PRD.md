Product Requirement Document (v0)

- Owner 팀: Product · Engineering
- 최종 업데이트: 2025-11-13


1. 목적 및 목표

- 제품 비전 요약
  - 정부·은행·IR 공식 양식에 합격 가능한 계획서/재무 자동화와 PMF 진단, 실행-재무 연동을 하나의 SaaS로 제공해 “계획-검증-실행-재무” 단절을 해소한다.

- 북극성 KPI
  - 제출 호환성률(Validator 통과율)
    - 기준선: 75% (수기/기존 도구 대비 가정, 파일럿 Week 1에서 재측정·보정)
    - 목표: 100%
    - 측정 창구: Validator 로그(기관·양식별 pass/fail, reject_reason), 주간 대시보드

- 보조 KPI
  - 초안→제출 중앙값 소요시간
    - 기준선: 10일 → 목표: 3일
    - 측정: 문서 상태 이벤트(created → validated → submitted) 리드타임 측정, 주간
  - Outcome 커버리지율(요구 Outcome 대비 충족 비율)
    - 기준선: 60% → 목표: 90%
    - 측정: 섹션별 요구 Outcome 체크리스트 자동 채점, 주간
  - 실행-재무 불일치 건수
    - 기준선: 5건/주 → 목표: 0건
    - 측정: Sync diff 리포트(엔티티·필드별 mismatch_count), 일간
  - 투자자 질의 대응 시간(덱↔재무 동기화 기반)
    - 기준선: 48시간 → 목표: 12시간
    - 측정: 질의 등록→답변 게시 리드타임, 월간


2. 사용자 시나리오 및 JTBD

- Story 1: As an 예비창업자(김예비), I want “예창패/IR/은행 양식 자동완성과 Validator 통과” so that I can submit 합격 가능한 문서를 제때 제출한다.
  - AC1 (SLO·정확): Given 예창패 템플릿을 선택하고 핵심 질문에 답했을 때, When AI 자동작성과 Validator를 실행하면, Then 필수 필드 자동 채움 비율 p95 ≥ 85%, Validator 응답 p95 ≤ 1.0s.
  - AC2 (합격 가능성): Given 자동작성 결과를 1회 편집 후, When Validator를 재실행하면, Then 형식/누락 오류=0, 기관별 규격 검사 통과=True.
  - AC3 (실패 케이스): Given 필수 항목이 누락되었을 때, When Validator 실행 시, Then 통과=False이고 누락/형식 오류가 항목·근거와 함께 하이라이트되어 1초 내 표시된다.

- Story 2: As a 재창업자(최민혁), I want “Outcome 기반 PMF 진단 리포트” so that I can 빌드 전 피벗/범위를 데이터로 결정한다.
  - AC1 (커버리지): Given JTBD·인터뷰 입력 n≥10, When 진단 실행, Then 상위 3개 위험 가설과 관련 AOS-DOS 점수·근거 링크가 출력되고 진단 커버리지 ≥ 80%.
  - AC2 (속도·안정성): Given 입력 완결성이 충분할 때, When 리포트 생성, Then 생성 시간 p95 ≤ 4s, 오류율 ≤ 0.5%.
  - AC3 (실패 케이스): Given 입력 누락·형식 오류가 있을 때, When 진단 실행, Then “부족 데이터 체크리스트”가 표시되고 실행이 중단된다(부분 결과 미출력).

- Story 3: As a 스위처(이대표), I want “Notion/Jira ↔ 재무 계획 싱크(초기 읽기 전용)” so that 실행 데이터와 재무 지표가 항상 일치한다.
  - AC1 (정합성): Given Notion/Jira OAuth가 유효할 때, When 일일 싱크를 수행하면, Then 불일치 건수=0(읽기 전용 상호 검증), diff 리포트 생성 시간 p95 ≤ 60s.
  - AC2 (가용성): Given 커넥터가 정상일 때, When 동기화 스케줄이 동작하면, Then 동기화 성공률 월 ≥ 99.0%.
  - AC3 (실패 케이스): Given 스키마 충돌/권한 오류가 있을 때, When 싱크 실행, Then 충돌 유형·필드가 명시된 경고가 생성되고, 재무 반영은 차단된다.

- Story 4: As a 소상공인(박사장), I want “은행 규격 재무 모델 템플릿 & 자동 채움” so that 대출 심사에 필요한 서류를 단기간 내 완성한다.
  - AC1 (형식 호환): Given 업종·규모를 선택했을 때, When 재무 모델 자동 생성을 수행하면, Then 기관별 제출 양식(워크북/시트/항목)과 100% 호환된다.
  - AC2 (성능): Given 기본 매개변수를 제공했을 때, When 3년 추정 생성, Then 생성 시간 p95 ≤ 2s, 셀 단위 계산 오류율 ≤ 0.2%.
  - AC3 (실패 케이스): Given 단위/통화 설정이 상충할 때, When 모델 생성, Then 단위 불일치가 즉시 경고되고 제출용 내보내기는 차단된다.


3. Pain / Needs 정의 (실패 KPI 수치화)

- 양식 복잡·불일치
  - 실패 KPI: Validator 실패율(형식/누락)
  - 기준선: 25% → 목표: < 5% (기관·양식별)
  - 측정: validator_fail 비율, reject_reason 분류
- 재무 장벽(비전공자 부담)
  - 실패 KPI: 재무 초안 작성 리드타임, 계산 오류 건수
  - 기준선: 12시간/문서, 6건 → 목표: 2시간, ≤ 1건
  - 측정: 작성 시작→초안 완료 리드타임, formula_error 카운트
- 합격 기준/근거 불명확
  - 실패 KPI: 재검토 라운드 수, 피드백 대기 시간
  - 기준선: 3회, 7일 → 목표: ≤ 1회, 2일
  - 측정: review_rounds, feedback_wait_hours
- 실행-재무 단절
  - 실패 KPI: 불일치 건수, 주간 Q&A 준비 리드타임
  - 기준선: 5건/주, 48시간 → 목표: 0건, 12시간
  - 측정: mismatch_count, investor_response_lead_time
- 마이그레이션 불안
  - 실패 KPI: 마이그레이션 소요시간, 데이터 손실률
  - 기준선: 8시간, 5% → 목표: 1시간, < 1%
  - 측정: migration_duration, migration_data_loss_rate


4. 기능 요구사항 (MSCW 분류, 근거·의존성 포함)

- Must
  - F1. 예창패/은행/IR 양식 자동완성 + Validator
    - 근거: Exec Summary·Value Proposition 표, Job–MVP Feature Map(우선순위 High, ✔)
    - 요약: 공식 양식 파서·호환성 검사, 섹션별 AI 작성 가이드, 미비 항목 알림
    - 의존성: 기관 양식 레지스트리, 템플릿 엔진, Validator 룰셋, LLM 작성 서비스, 문서 렌더러
  - F2. Outcome 기반 PMF 진단 리포트(AOS-DOS/JTBD 매핑)
    - 근거: 재창업자 세그먼트 핵심 Job, Job–MVP Map(High, ✔)
    - 요약: 인터뷰→가설→리스크 매핑 및 리포트/추천 실험 설계
    - 의존성: JTBD 스키마/카드, AOS-DOS 스코어러, 리포트 생성기
  - F4. 은행/IR 규격 재무 모델 템플릿 & 자동 채움
    - 근거: 소상공/IR 페르소나 요구, Job–MVP Map(High, ✔)
    - 요약: 업종별 매개변수 기반 3년 추정 자동 생성, 제출 전 체크리스트
    - 의존성: 재무 템플릿 라이브러리, 수식 엔진, 내보내기(엑셀/CSV/PDF)

- Should
  - F6. AOS-DOS 스코어링 & 타겟 세그먼트 제안
    - 근거: 전 페르소나 공통 의사결정 가속, Job–MVP Map(Mid, ✔)
    - 의존성: 세그먼트 맵 데이터, 스코어링 엔진

- Could (1 스프린트 내 구현 가능 범위로 스코프)
  - F3. Notion/Jira ↔ 재무 계획 싱크(Phase 1: 읽기 전용 검증·리포트)
    - 근거: 스위처 유입/리텐션 핵심(단계적 구현 권장)
    - 의존성: Notion/Jira 커넥터, 스키마 매퍼, diff 리포터
  - F5. 데이터 마이그레이션 어시스턴트(기존 Notion/Excel → 신규)
    - 근거: 전환 마찰 해소
    - 의존성: CSV/Excel 파서, 매핑 규칙, 검증 리포트

- Won’t (이번 MVP 범위 밖)
  - 외부 회계 SaaS(예: QuickBooks, ERP) 양방향 완전 통합
  - 모바일 네이티브 앱, 다국어(영어 외) 지원, PDF OCR 전면 자동화


5. 비기능 요구사항 (NFR: 임계치·측정·알림 포함)

- 성능
  - Validator 응답 p95 ≤ 800ms (기관·양식·문서 크기 기준)
  - 문서 자동작성 p95 ≤ 4s, 리포트 생성 p95 ≤ 4s
  - 동기화 diff 리포트 p95 ≤ 60s (데이터 50k 레코드 기준)
  - 측정/알림: Apdex, p95/99 라틴시, 슬랙 알림 임계치 초과 시 즉시

- 가용성·신뢰성
  - 월 가용성 ≥ 99.9%, 백엔드 오류율 ≤ 0.5%
  - 백그라운드 싱크 성공률 ≥ 99.0%, 재시도 지수백오프
  - RPO ≤ 1시간, RTO ≤ 30분
  - 측정/알림: 상태 체크, 헬스엔드포인트, 에러 버짓 대시보드

- 보안·프라이버시
  - 전송 TLS 1.2+, 저장 AES-256, 민감정보 분리 저장·암호화
  - 접근 제어 RBAC, 감사 로그 1년 보존, 데이터 레지던시(국내 리전) 옵션
  - DLP: 제출 전 문서 내 민감 키워드 탐지·마스킹
  - 측정/알림: 접근 실패/이상 패턴 경보, 감사 로그 무결성 점검

- 비용
  - 문서 1건 자동작성·검증 총 변동비 ≤ $0.10
  - 일일 1k 문서 처리 시 월 인프라비 예산 내(팀 예산표 참조)
  - 측정/알림: 워크로드별 코스트 대시보드, 임계 초과 시 주간 리포트

- 모니터링 항목
  - validator_pass/fail, reject_reason별 비중, generation_ms, report_ms
  - sync_success_rate, mismatch_count, connector_error_rate
  - cost_per_doc, storage_gb, key SLA 지표 알림 룰


6. Differential Value (대안 대비 수치 비교)

- 제출 준비 리드타임: 기존 도구/수기 대비 70% 단축(10일 → 3일)
- 1st-pass 제출 통과율: +20~25%p 개선(75% → 95~100% 목표)
- 총 소유 비용(TCO): 외부 컨설턴트·분절 툴 조합 대비 ≥ 60% 절감
- 추가: 실행-재무 불일치 0건 목표(분기 평균 5건/팀 → 0건)


7. Proof & Experiment (실험 설계·측정 연결)

- H1: “양식 자동완성+Validator”는 합격/통과 확률을 개선한다
  - Design: 파일럿 A/B(통제군: 수기/기존 툴, 실험군: 본 제품), n≥100 문서
  - Metrics: validator_pass_rate, reject_reason(형식/누락) 감소율, first_pass_accept
  - Success: 통과율 +20%p 이상, 형식/누락 리젝 비중 75% 이상 감소

- H2: “Outcome 기반 PMF 진단”은 빌드 전 의사결정 품질을 높인다
  - Design: 전/후 비교(인터뷰→가설→실험 리드타임·실패 비용), n≥25 팀
  - Metrics: hypothesis_to_experiment_lead_time, invalidated_risk_rate
  - Success: 리드타임 40%↓, 고위험 가설 사전 발견율 30%↑

- H3: “실행-재무 연동”은 리텐션·NPS를 개선한다
  - Design: 읽기 전용 싱크→부분 양방향 단계 롤아웃, n≥50 계정
  - Metrics: mismatch_count=0 유지율, NPS, retention_d90
  - Success: mismatch=0 95% 이상, NPS +10, d90 리텐션 +5%p

- 계측·대시보드
  - 이벤트: validator_* / generator_ms / report_ms / sync_* / cost_*
  - 창구: 프로덕트 로그 스토어→메트릭 파이프라인→대시보드(주간 리뷰)


8. 리스크 및 가정 (ADR, 검증 계획 포함)

- 리스크(≥3) 및 대응
  - R1 데이터 보안·개인정보 규제 리스크
    - 대응: 온프레미스/비식별화 옵션, 민감정보 분리 저장, 접근 로그·RBAC 강화
  - R2 기관 양식 변경·확장 리스크
    - 대응: 규격 레지스트리 자동 업데이트 파이프라인, 버전링·롤백
  - R3 마이그레이션 마찰로 초기 전환 저조
    - 대응: 마이그레이션 마법사·검증 리포트, 전환 크레딧 인센티브
  - R4 AI 생성(환각)으로 잘못된 수치/근거 제시
    - 대응: 근거 링크 강제, 계산식·출처 표시, 크리티컬 섹션은 룰·템플릿 우선
  - R5 외부 커넥터(노션/지라) 레이트리밋·권한 이슈
    - 대응: 캐시·지능형 폴링·백오프, 최소 권한 스코프, 재시도 큐

- 가정(ADR, Architecture Decision Record) 및 검증
  - ADR-001 데이터 레지던시: 국내 리전 저장 옵션 제공
  - ADR-002 템플릿 커버리지: 예창패/은행/IR 상위 10개 규격부터 커버
  - ADR-003 싱크 단계: Phase 1은 읽기 전용 검증 중심
  - 검증 계획: 각 ADR을 파일럿(주차별)로 A/B 또는 전/후 측정으로 검증


9. 범위 정의 (In / Out of Scope)

- In Scope
  - F1 양식 자동완성+Validator, F2 PMF 진단 리포트, F4 재무 모델 템플릿, F6 스코어링
  - F3 읽기 전용 싱크(검증·diff 리포트) — 1 스프린트 범위
  - 타겟 페르소나: 예비창업자, 재창업자, 스위처, 소상공(초기 국내)
  - 제출 대상: 예창패/은행/IR 상위 규격

- Out of Scope
  - 외부 회계 SaaS 완전 양방향, 모바일 앱, 다국어(영어 외), 전면 OCR
  - 온프레미스 전용 배포, 고급 IR 커스텀 모델링(컨설팅 성격 기능)


부록(근거 링크)

- VPS 원문: `2_핵심예제 분석자료/GPT-ValueProPositionSheet/GPT-ValueProPositionSheet.md`
- 세그먼트·TAM/SAM/SOM: `2_핵심예제 분석자료/5_SaaS형_온라인_비즈니스_컨설팅_TAM-SAM-SOM_핵심_원인_Market_Segment_분석_보고서.pdf`
- JTBD 카드·인터뷰: `2_핵심예제 분석자료/8-2_SaaS형_온라인_비즈니스_컨설팅_JTBD_가상인터뷰_결과지.md`
*** End Patch

