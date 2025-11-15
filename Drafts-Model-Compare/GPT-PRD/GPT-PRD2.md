# SaaS형 온라인 비즈니스 컨설팅 통합 솔루션 PRD v0.1
- Owner 팀: Product · Engineering
- 최종 업데이트: 2025-11-13

## 1. 개요·목표
- 문제 정의(Pain지표 포함):
  - 양식 복잡·불일치: Validator 실패율 25% (형식/누락) → 목표 < 5% (기관·양식별)
  - 재무 장벽(비전공자 부담): 재무 초안 작성 12시간/문서, 계산 오류 6건 → 목표 2시간, ≤1건
  - 합격 기준/근거 불명확: 재검토 3회, 피드백 대기 7일 → 목표 ≤1회, 2일
  - 실행-재무 단절: 불일치 5건/주, 투자자 Q&A 리드타임 48h → 목표 0건, 12h
  - 마이그레이션 불안: 이전 8h, 데이터 손실률 5% → 목표 1h, <1%
- 목표(Desired Outcome 수치화):
  - 제출 호환성률 100%, 초안→제출 중앙값 3일, Outcome 커버리지 90%,
    실행-재무 불일치 0건, 투자자 질의 대응 12h
- 성공 지표(북극성/보조 KPI):
  - 북극성 KPI: 제출 호환성률(Validator pass rate)
    - 기준선 75% → 목표 100% / 창구: Validator 로그·대시보드(기관별 pass/fail)
  - 보조 KPI:
    - 초안→제출 리드타임 중앙값: 10일 → 3일 / 이벤트: created→validated→submitted
    - Outcome 커버리지: 60% → 90% / 섹션 체크리스트 자동 채점
    - 실행-재무 불일치: 5건/주 → 0건 / sync diff 리포트
    - 투자자 질의 대응: 48h → 12h / 덱↔모델 동기화 기반

## 2. 사용자와 페르소나
- 핵심 페르소나 및 여정 Pain·Needs:
  - 예비창업자 ‘김예비’(예창패/IR 제출·합격 필요) — 양식 복잡, 마감 스트레스
  - 재창업자 ‘최민혁’(가설 검증·피벗 결정) — 데이터 기반 검증 니즈
  - 스위처 ‘이대표’(LivePlan 이탈) — 실행(Notion/Jira)↔재무 불일치
  - 소상공 ‘박사장’(은행 대출) — 용어 장벽, 제출 형식 리젝 위험
  - IR 준비 ‘한서윤’ — 덱↔재무 지표 불일치, 커스텀 SaaS 지표 요구
- 참조 링크: `2_핵심예제 분석자료/GPT-ValueProPositionSheet/GPT-ValueProPositionSheet.md` (Persona Spectrum & CJM 요약 포함)

## 3. 사용자 스토리와 수용 기준(AC, Acceptance Criteria)
- Story 1 (김예비): As a 예비창업자, I want 예창패/은행/IR 양식 자동완성과 Validator 통과, so that 제때 합격 가능한 문서를 제출한다.
  - AC1: Given 기관 템플릿 선택 후 핵심 질문 답변, When 자동작성+Validator 실행, Then 필수 필드 자동 채움 p95 ≥ 85%, Validator 응답 p95 ≤ 1.0s.
  - AC2: Given 1회 편집, When 재검사, Then 형식/누락 오류=0, 규격 검사 통과=True.
  - AC3(실패): Given 필수 항목 누락, When Validator 실행, Then 통과=False, 누락/형식 오류 하이라이트가 1s 내 표시.
- Story 2 (최민혁): As a 재창업자, I want Outcome 기반 PMF 진단 리포트, so that 빌드 전 피벗/범위를 데이터로 결정한다.
  - AC1: Given JTBD·인터뷰 n≥10, When 진단 실행, Then 상위 3 위험 가설+AOS-DOS·근거 링크, 진단 커버리지 ≥ 80%.
  - AC2: Given 입력 완결성 충분, When 리포트 생성, Then p95 ≤ 4s, 오류율 ≤ 0.5%.
  - AC3(실패): Given 입력 누락/형식 오류, When 진단 실행, Then “부족 데이터 체크리스트” 표기 후 실행 중단(부분 결과 미출력).
- Story 3 (이대표): As a 스위처, I want Notion/Jira ↔ 재무 계획 싱크(읽기 전용), so that 실행 데이터와 재무가 일치한다.
  - AC1: Given OAuth 유효, When 일일 싱크, Then 불일치=0(읽기 전용 상호 검증), diff 리포트 p95 ≤ 60s.
  - AC2: Given 커넥터 정상, When 스케줄 동작, Then 동기화 성공률 월 ≥ 99.0%.
  - AC3(실패): Given 스키마 충돌/권한 오류, When 싱크, Then 충돌 유형·필드 경고 생성, 재무 반영 차단.
- Story 4 (박사장): As a 소상공, I want 은행 규격 재무 모델 템플릿 & 자동 채움, so that 대출 심사 서류를 단기간에 완성한다.
  - AC1: Given 업종·규모 선택, When 자동 생성, Then 제출 양식(워크북/시트/항목) 100% 호환.
  - AC2: Given 기본 매개변수, When 3년 추정 생성, Then p95 ≤ 2s, 셀 계산 오류율 ≤ 0.2%.
  - AC3(실패): Given 단위/통화 상충, When 생성, Then 단위 불일치 경고 및 제출 내보내기 차단.

## 4. 기능 요구사항(Functional)
- MSCW 우선순위와 근거(대안 대비 가치)
  - Must
    - F1 예창패/은행/IR 양식 자동완성 + Validator
      - 근거: 제출 호환성률 100% 달성의 핵심; LivePlan/수기 대비 리젝 감소
      - 의존성: 기관 양식 레지스트리, 템플릿 엔진, Validator 룰셋, LLM 작성, 문서 렌더러
    - F2 Outcome 기반 PMF 진단 리포트(AOS-DOS/JTBD 매핑)
      - 근거: 빌드 전 리스크 발견; 의사결정 품질 향상
      - 의존성: JTBD 스키마, AOS-DOS 스코어러, 리포트 생성기
    - F4 은행/IR 재무 모델 템플릿 & 자동 채움
      - 근거: 소상공/IR 요구 직결; 제출 리드타임 70% 단축
      - 의존성: 재무 템플릿 라이브러리, 수식 엔진, 내보내기(엑셀/PDF)
  - Should
    - F6 AOS-DOS 스코어링 & 타겟 세그먼트 제안
      - 근거: 세그먼트 선택·포지셔닝 가속
      - 의존성: 세그먼트 맵 데이터, 스코어링 엔진
  - Could (1 스프린트 내 구현 가능 스코프)
    - F3 Notion/Jira ↔ 재무 싱크(Phase 1: 읽기 전용 검증·diff 리포트)
      - 근거: 스위처 유입/리텐션 핵심, 위험도 낮춘 단계적 구현
      - 의존성: Notion/Jira 커넥터, 스키마 매퍼, diff 리포터
    - F5 데이터 마이그레이션 어시스턴트(기존 Notion/Excel → 신규)
      - 근거: 전환 마찰 해소, 데이터 손실률 <1% 목표
      - 의존성: CSV/Excel 파서, 매핑 규칙, 검증 리포트
  - Won’t (이번 MVP 범위 밖)
    - 외부 회계 SaaS 양방향 완전 통합, 모바일 앱, 다국어(영어 외), 전면 OCR
- 대안 대비 수치(차별 가치)
  - 제출 준비 리드타임: 10일 → 3일(70%↓), 1st-pass 통과율: 75% → 95~100%(+20~25%p)
  - TCO: 컨설턴트/분절 툴 대비 ≥ 60% 절감, 실행-재무 불일치 0건 목표

## 5. 비기능 요구사항(NFR, Non-Functional Requirement)
- 성능: p95 응답 ≤ 목표
  - Validator ≤ 800ms, 문서 자동작성 ≤ 4s, 진단 리포트 ≤ 4s, 싱크 diff ≤ 60s(50k 레코드)
- 신뢰성: 월 가용성 ≥ 99.9%, 백엔드 오류율 ≤ 0.5%, 싱크 성공률 ≥ 99.0%, RPO ≤ 1h, RTO ≤ 30m
- 보안/비용:
  - 보안: TLS 1.2+, AES-256, 민감정보 분리 저장·암호화, RBAC, 감사 로그 1년, 국내 리전 옵션
  - 비용: 문서 1건 자동작성·검증 변동비 ≤ $0.10, 일 1k 문서 처리 월 예산 준수
- 모니터링 항목: 로그·대시보드·알림 기준
  - validator_pass/fail, reject_reason 비중, generation_ms, report_ms
  - sync_success_rate, mismatch_count, connector_error_rate
  - cost_per_doc, storage_gb, SLA 초과 실시간 알림(슬랙/이메일)

## 6. 데이터·인터페이스 개요
- 핵심 엔터티·주요 필드
  - Document(id, institution, template_version, sections[], status, validator_score)
  - Template(id, institution, version, required_fields[], ruleset_id)
  - ValidatorRule(id, institution, field, pattern, severity)
  - JTBDCard(id, persona, job, pain, signals[], aos, dos)
  - OutcomeMetric(id, name, baseline, target, period, source)
  - Experiment(id, hypothesis, design, metrics[], success_criteria)
  - SyncConnector(id, source, schema_map, last_run_at, success_rate)
  - FinancialModel(id, industry, size, assumptions[], outputs, export_refs)
- 외부/내부 API 개요(입출력·제약)
  - POST /documents: 입력(템플릿/질문 답변) → 출력(초안 문서, validator_score)
  - POST /validator/run: 입력(document_id) → 출력(pass/fail, reject_reasons[])
  - POST /pmf/report: 입력(jtbd_cards[]) → 출력(risks[], aos_dos, coverage)
  - POST /sync/run: 입력(connector_id) → 출력(diff_report, mismatch_count)
  - GET /export: 입력(document_id, format) → 출력(file)
  - 제약: 요청 크기 ≤ 10MB, 레이트리밋 60rpm/user, OAuth 스코프 최소 권한

## 7. 범위(In/Out), 리스크·가정·의존성
- In/Out 명시
  - In: F1, F2, F4, F6, F3(읽기 전용), 마이그레이션 어시스턴트(기본)
  - Out: 회계 SaaS 완전 양방향, 모바일, 다국어(영어 외), 전면 OCR, 온프레 전용 배포
- 리스크(최소 3개)·대응
  - R1 데이터 보안·개인정보 규제: 온프레/비식별화, 분리 저장·암호화, RBAC·감사 로그
  - R2 기관 양식 변경: 규격 레지스트리 자동 업데이트, 버전링·롤백
  - R3 마이그레이션 전환 저조: 마법사·검증 리포트, 전환 크레딧
  - R4 AI 환각·잘못된 수치: 근거 링크 강제, 계산식·출처 표시, 규칙 우선
  - R5 외부 커넥터 레이트리밋: 캐시·지능형 폴링·백오프, 재시도 큐
- 가정·의존성(ADR, Architecture Decision Record 정의와 연결)
  - ADR-001 데이터 레지던시 국내 리전 지원
  - ADR-002 상위 10개 기관 규격 우선 커버
  - ADR-003 싱크 Phase 1은 읽기 전용 검증 중심

## 8. 실험·롤아웃·측정
- 베타 채널: 예창패 커뮤니티·대학 창업센터·세무사 파트너
- 실험 가설/측정/성공 기준
  - H1 “자동완성+Validator” → 통과율 개선
    - Design: A/B(통제: 기존/수기, 실험: 본 제품), n≥100 문서
    - Metrics: validator_pass_rate, first_pass_accept, reject_reason 감소율
    - Success: 통과율 +20%p, 형식/누락 리젝 75%↓
  - H2 “Outcome 기반 PMF 진단” → 의사결정 품질 향상
    - Design: 전/후 비교, n≥25 팀
    - Metrics: hypothesis_to_experiment_lead_time, invalidated_risk_rate
    - Success: 리드타임 40%↓, 고위험 가설 선제 발견 30%↑
  - H3 “실행-재무 연동” → 리텐션·NPS 개선
    - Design: 읽기 전용→부분 양방향 단계 롤아웃, n≥50 계정
    - Metrics: mismatch_count=0 유지율, NPS, retention_d90
    - Success: mismatch=0 95% 이상, NPS +10, d90 +5%p
- 경쟁 대안 대비 벤치마크 계획: LivePlan/Notion/수기 대비 리드타임·통과율·TCO 측정

## 9. 근거(Proof)
- 인터뷰/로그/벤치마크/리서치 링크
  - VPS: `2_핵심예제 분석자료/GPT-ValueProPositionSheet/GPT-ValueProPositionSheet.md`
  - TAM/SAM/SOM·세그먼트: `2_핵심예제 분석자료/5_SaaS형_온라인_비즈니스_컨설팅_TAM-SAM-SOM_핵심_원인_Market_Segment_분석_보고서.md`
  - JTBD 카드·인터뷰: `2_핵심예제 분석자료/8-2_SaaS형_온라인_비즈니스_컨설팅_JTBD_가상인터뷰_결과지.md`

