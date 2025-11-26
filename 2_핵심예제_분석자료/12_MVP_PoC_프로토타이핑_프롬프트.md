# SaaS형 온라인 비즈니스 컨설팅 MVP (Frontend PoC) 개발 프롬프트

이 문서는 **'초보 창업가를 위한 AI Co-Pilot (AI Business Consultant)'**의 프론트엔드 프로토타입(PoC)을 빠르게 구축하기 위한 AI 프롬프트입니다. 
Firebase Studio, Cursor, v0, Project IDX 등의 AI 코딩 도구에서 사용하여 MVP 수준의 실행 가능한 React 애플리케이션을 생성할 수 있습니다.

---

## 📋 프롬프트 전문 (Copy & Paste)

아래 내용을 복사하여 AI 개발 도구의 채팅창에 입력하세요.

```markdown
# Role
당신은 '초기 창업가를 위한 SaaS형 비즈니스 컨설팅 플랫폼'을 구축하는 수석 프론트엔드 개발자입니다.
주어지는 기획서와 요구사항을 바탕으로 **React + Vite + Tailwind CSS** 기반의 고품질 MVP 프로토타입을 구현해주세요.

# Project Goal
사용자(초기 창업가)가 복잡한 사업계획서를 쉽고 빠르게 작성하도록 돕는 "Wizard(단계별 마법사) 인터페이스"를 구현하는 것입니다.
백엔드 연동 없이 **Mock Data**와 **Client-side State**만으로 동작하는 완벽한 UI/UX를 보여주어야 합니다.

# Tech Stack
- Framework: React (Vite), TypeScript
- Styling: Tailwind CSS
- State Management: Zustand (전역 상태 및 Wizard 진행 상황 관리)
- Routing: React Router DOM
- Forms: React Hook Form + Zod (유효성 검사)
- UI Components: Radix UI 또는 Shadcn/ui 스타일의 깔끔한 컴포넌트, Lucide React (아이콘)
- Charts: Recharts (재무 데이터 시각화)
- Markdown: React Markdown (사업계획서 뷰어)

# Key Features & Requirements (By Epic)

## 1. Global Layout & Project Creation (EPIC0-FE-001)
- **Layout:** 좌측 사이드바(단계 네비게이션), 중앙 메인 컨텐츠, 상단 헤더로 구성된 대시보드 레이아웃.
- **Project Creation:**
  - 앱 시작 시 '새 프로젝트 만들기' 모달 또는 페이지 진입.
  - **템플릿 선택:** '예비창업패키지', '초기창업패키지', '은행용 대출' 3개의 카드를 보여주고 선택하게 함.
  - 선택 시 Wizard 메인 화면으로 이동.
- **Wizard Navigation:**
  - 사이드바에 '1. 아이템 개요', '2. 시장 분석', '3. 실현 방안', '4. 재무 계획', '5. PMF 진단' 등의 챕터를 표시.
  - 현재 단계 하이라이트 및 클릭 시 이동 기능.

## 2. Wizard Forms & Auto-save (EPIC0-FE-002)
- 각 단계별로 구체적인 질문(Question)과 입력창(Textarea/Input)을 제공.
- **Validation:** 필수 항목이 비어있으면 '다음' 버튼 비활성화 또는 에러 메시지 표시.
- **Auto-save Simulation:**
  - 사용자가 입력을 멈추면 1초 후 우측 상단에 "저장 중..." -> "저장됨" 상태 표시 (Debounce 적용).
  - Zustand Store에 입력값 실시간 동기화.

## 3. AI Draft Generation & Viewer (EPIC0-FE-003)
- **Action:** Wizard의 특정 단계 또는 마지막 단계에서 "AI 초안 생성" 버튼 제공.
- **Loading:** 버튼 클릭 시 3초간 로딩 스피너와 함께 "AI가 사업계획서를 작성 중입니다..." 문구 표시.
- **Result View:**
  - 로딩 후, Markdown 형태의 잘 구조화된 사업계획서 초안이 뷰어에 표시됨 (Mock Text 사용).
  - 각 섹션 우측에 'AI 다시 쓰기' 아이콘 배치 (클릭 시 텍스트 변경되는 인터랙션).
  - 'HWP/PDF 내보내기' 버튼 배치 (클릭 시 window.alert("다운로드 준비 완료")).

## 4. Financial Simulation (EPIC0-FE-004)
- **Input:** 재무 계획 단계에서 '고객 수', '객단가', 'CAC(고객획득비용)', '고정비' 등을 입력하는 테이블/폼 제공.
- **Visualization (Real-time):**
  - 입력값이 변경될 때마다 **Recharts**를 사용하여 그래프 즉시 업데이트.
  - **Chart 1 (BEP):** 매출 vs 비용 라인 차트로 손익분기점(Break Even Point) 시각화.
  - **Chart 2 (Unit Economics):** LTV(고객생애가치) 막대 vs CAC 막대 비교.
- **Warning:** LTV/CAC 비율이 3 미만이면 "수익성 경고" 뱃지 표시.

## 5. PMF Survey & Report (EPIC0-FE-005)
- **Survey:** PMF 진단을 위한 객관식 질문 5~10개 제공 (예: "타겟 고객이 명확한가요?", "경쟁 우위가 있나요?").
- **Report:**
  - 설문 완료 시 '진단 리포트' 화면 표시.
  - 게이지 차트로 'PMF 점수' 표시 (예: 75점 - Product-Market Fit 근접).
  - '핵심 리스크' 및 '개선 제언'을 카드 UI로 나열.

# Implementation Guidelines
1. **Mock Data:** 모든 데이터(템플릿 목록, 질문 리스트, 생성된 사업계획서, 차트 데이터)는 별도의 `mockData.ts` 파일로 분리하여 관리하세요.
2. **User Flow:** 로그인 -> 프로젝트 생성 -> Wizard 입력 -> AI 생성 -> 차트 확인 -> 최종 리포트 순서로 자연스럽게 이어지도록 라우팅을 설정하세요.
3. **Design:** 신뢰감을 주는 블루/그레이 톤의 전문적인 B2B SaaS 디자인을 적용하세요.
4. **Code Structure:** 컴포넌트를 기능별로 분리하고(`src/components/wizard`, `src/components/charts` 등), 커스텀 훅(`useAutoSave`, `useFinancialCalc`)을 활용하세요.

위 요구사항을 바탕으로 프로젝트 구조를 잡고, 핵심 파일들을 작성해 주세요.
```

---

## 💡 단계별 실행 가이드 (Tip)

한 번에 모든 코드를 생성하기보다, 아래 순서대로 AI에게 요청하며 점진적으로 완성하는 것을 추천합니다.

### Step 1. 기본 셋업 및 레이아웃
> "위 프롬프트의 **Tech Stack**과 **1. Global Layout** 부분을 먼저 구현해줘. React Router를 설정하고, 사이드바와 메인 영역이 있는 App Shell을 만들어줘. 프로젝트 생성 모달까지 포함해줘."

### Step 2. Wizard 및 폼 구현
> "이제 **2. Wizard Forms** 기능을 추가해줘. Zustand를 써서 상태를 관리하고, 각 단계별로 더미 질문을 보여주고 입력값을 저장하는 로직을 구현해줘. 자동 저장 UI도 우측 상단에 표시해줘."

### Step 3. 재무 및 차트
> "재무 탭을 누르면 **4. Financial Simulation** 화면이 나오게 해줘. Recharts를 설치하고, 입력값에 따라 변하는 BEP 차트와 LTV/CAC 차트를 그려줘. 계산 로직은 간단한 수식으로 Mocking 해줘."

### Step 4. 문서 생성 및 PMF 리포트
> "마지막으로 **3. AI Draft Generation**과 **5. PMF Survey** 기능을 추가해서 전체 플로우를 완성해줘. 최종적으로 그럴듯한 데이터가 채워진 MVP를 보고 싶어."

