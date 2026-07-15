# 주식 포트폴리오 예측 시스템 구현 계획

## 1. 메타데이터와 승인된 기준선

| 필드 | 값 |
|---|---|
| 프로젝트 ID | `stock-portfolio-forecast-sample` |
| 산출물 | `IMPLEMENTATION-PLAN-003` |
| 버전 | `v003` |
| 상태 | `HUMAN_APPROVED` |
| 작성 역할 | `implementation-planner` |
| AI 루프 승인 | `APR-20260715-012` |
| 작성 호출 | `INV-PLAN-AUTHOR-20260715-003` |
| 요구사항 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/requirements/baseline.md` (`v006`, `HUMAN_APPROVED`) |
| 아키텍처 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/architecture/baseline.md` (`v002`, `HUMAN_APPROVED`) |
| 이전 구현 계획 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/implementation/baseline.md` (`v001`, `HUMAN_APPROVED`) |
| 반영된 사람 결정 | `DEC-20260715-004` (`OPEN-IMPL-010`, `RESOLVED_AS_INPUT`) |

이 계획은 구현 승인이나 기준선 승인이 아니다. 각 작업 패킷은 별도 사람 승인을 받아야 하며 승인 전 제품 코드 변경을 허용하지 않는다.

## 2. 범위와 저장소 관찰

- 저장소에는 제품 코드, 빌드 구성, 테스트 러너 또는 제품 데이터가 아직 없다.
- `DEC-20260715-004`에 따라 백엔드는 Python 3.12, FastAPI, Pydantic, pandas, NumPy, scikit-learn과 `uv`를 사용한다. 웹 UX는 React, TypeScript, Vite를 사용한다.
- 백엔드 경로는 `backend/src/stock_analysis/`, `backend/tests/`, 프론트엔드 경로는 `frontend/src/`, `frontend/tests/`, `frontend/e2e/`, 공통 고정 데이터는 `fixtures/`로 확정한다.
- 백엔드 검증은 `pytest`, 프론트엔드 단위·컴포넌트 검증은 Vitest와 React Testing Library, 브라우저 종단 간·접근성 검증은 Playwright와 `@axe-core/playwright`를 사용한다.
- 개발 단계의 로컬 또는 격리 환경만 대상으로 한다. 인증·권한, 주문·체결, 외부 공개 배포, 상품 운영 모니터링, 장기 보존과 백업은 금지 범위다.
- 실제 보유 정보 대신 합성·최소화 데이터를 사용한다. 외부 데이터 공급자와 자격 증명 구현은 `OPEN-IMPL-003` 결정 전 금지한다.

### 공통 구현 경계

- `INT-001` 입력 방어의 초기 개발 한도는 보유 항목 최대 500개, 전체 요청 최대 256 KiB, 종목 식별자 최대 64자, 수량은 0보다 크고 `10^15` 이하인 유한 십진수로 한다. 허용 문자 집합은 `OPEN-IMPL-001`에서 확정한다.
- 동기 요청 제한은 30초로 두고 이를 넘기면 실행 ID와 조회 가능한 상태를 반환한다. 외부 조회 재시도는 `OPEN-IMPL-003` 결정 전 수행하지 않는다.
- 계약 버전이 없거나 지원하지 않는 필수 버전, 알 수 없는 필드, 중복 종목, 한도 초과 입력은 실행 전에 Pydantic 기반 구조화 오류로 거절한다.
- 모든 명령은 저장소 루트에서 실행한다. Python은 `uv run --project backend ...`로 프로젝트를 명시하고, Node는 `npm --prefix frontend ci`와 `npm --prefix frontend run <script>`로 `frontend/package-lock.json` 및 고정된 `package.json` 스크립트를 사용한다.
- `frontend/package.json`은 `test:unit`을 Vitest/React Testing Library, `test:e2e`를 Playwright, `test:a11y`를 `@axe-core/playwright` 접근성 스위트, `test`를 세 검증의 순차 실행으로 고정한다. 임시 `npx` 호출이나 전역 도구는 사용하지 않는다.

## 3. 순서화된 작업 패킷

### WP-001: 개발 골격과 버전 계약

- 목표: 확정된 Python/React 개발 스택 위에 컴포넌트 경계, 버전 계약, 고정 검증 데이터와 자동 테스트 골격을 만든다.
- 요구사항 및 아키텍처 추적: `FR-022`~`FR-026`, `NFR-012`~`NFR-016`; `DEC-001`~`DEC-004`, `CMP-001`~`CMP-006`, `INT-001`~`INT-006`, `DATA-001`~`DATA-005`.
- 허용 경로/컴포넌트: `backend/pyproject.toml`, `backend/uv.lock`, `backend/src/stock_analysis/contracts/`, `backend/src/stock_analysis/status/`, `backend/tests/contracts/`, `frontend/package.json`, 프론트엔드 잠금 파일, Vite/TypeScript/Vitest/Playwright 구성, `frontend/src/`, `frontend/tests/`, `frontend/e2e/`, `fixtures/`, 개발 실행 문서.
- 금지 범위: 분석 알고리즘, 실제 공급자 연결, 완성 웹 화면, 실제 개인 보유 데이터, 배포 자동화.
- 의존성: 없음. `OPEN-IMPL-010`은 `DEC-20260715-004`로 해결됨.
- 구현 개요: Python 3.12와 `uv` 프로젝트, FastAPI/Pydantic 계약 골격, React/TypeScript/Vite 골격을 만든다. 내부 데이터·상태·오류·표현 모델 스키마, 기준 시점/이용 가능 시각 타입, 실행 ID 규칙과 합성 fixture를 정의한다.
- 테스트 및 수용 증거: 저장소 루트에서 `uv run --project backend pytest backend/tests/contracts`와 `npm --prefix frontend run test:unit`을 실행해 계약 직렬화 왕복, 미지원 버전·필드 거절, 상태 전이와 API 표현 모델 fixture를 자동화한다. `uv sync --project backend --locked`와 `npm --prefix frontend ci`의 잠금 재설치 결과도 남긴다.
- 마이그레이션/호환성: 최초 스키마 버전을 명시한다. 이후 필수 필드 변경은 버전 상승과 읽기 호환 시험이 필요하다.
- 롤백 경계: 이 패킷에서 생성한 `backend/`, `frontend/`, `fixtures/` 골격과 매니페스트만 제거하면 기존 워크플로 문서 상태로 복귀한다.
- 상태: `PROPOSED_READY_FOR_AUTHORIZATION`

### WP-002: 입력 검증, 데이터 정규화와 현재 포트폴리오

- 목표: 비신뢰 보유 입력을 검증하고 기준 시점 데이터로 현재 가치·비중을 산출하며 외부 데이터를 표준 시점·상태 계약으로 격리한다.
- 요구사항 및 아키텍처 추적: `FR-022`, `NFR-012`, `NFR-013`, `NFR-015`, `AC-029`, `AC-034`, `AC-035`, `AC-037`; `CMP-001`~`CMP-003`, `CMP-005`, `INT-001`, `INT-002`, `INT-004`, `DATA-001`, `DATA-002`, `DEC-002`.
- 허용 경로/컴포넌트: `backend/src/stock_analysis/input/`, `backend/src/stock_analysis/data/`, `backend/src/stock_analysis/portfolio/current/`, `backend/tests/`, `fixtures/`의 합성 어댑터 자료.
- 금지 범위: 실제 공급자 자격 증명, 목표 비중·리밸런싱, 예측, UI 렌더링.
- 의존성: `WP-001`, `OPEN-IMPL-001`, `OPEN-IMPL-003`, `OPEN-IMPL-007`, `OPEN-IMPL-008`.
- 구현 개요: Pydantic 입력 모델과 FastAPI 경계에서 공통 한도를 검증하고 pandas/NumPy 계산 모듈로 현재 가치·총가치·비중을 산출한다. 공급자 어댑터는 관측/이용 가능 시각·출처 버전·상태를 정규화하며 오류·누락·오래됨을 정상값으로 대체하지 않는다.
- 테스트 및 수용 증거: `uv run --project backend pytest backend/tests -k "input or current or data"`로 정상·빈 값·중복·잘못된 수량·한도 초과, 독립 기준 계산, 미래 데이터 거절과 네 가지 데이터 상태를 검증한다.
- 마이그레이션/호환성: 공급자 원본 형식은 어댑터 밖으로 노출하지 않고 내부 계약 변경은 `WP-001`의 버전 규칙을 따른다.
- 롤백 경계: `backend/src/stock_analysis/input/`, `backend/src/stock_analysis/data/`, `backend/src/stock_analysis/portfolio/current/`와 대응 `backend/tests/`만 되돌리며 계약 골격은 유지한다.
- 상태: `PROPOSED_BLOCKED_BY_OPEN_DECISION`

### WP-003: 대형주 적격성, 예측과 백테스트

- 목표: 확정된 시장·정책·모델 계약으로 대형주 적격 근거, 예측·불확실성 및 시간 순서 백테스트를 생성한다.
- 요구사항 및 아키텍처 추적: `FR-023`, `FR-024`, `NFR-013`, `AC-030`, `AC-031`, `AC-035`; `CMP-003`, `CMP-004`, `INT-002`, `INT-003`, `DEC-001`, `DEC-002`, `ADR-001`, `ADR-002`.
- 허용 경로/컴포넌트: `backend/src/stock_analysis/analysis/eligibility/`, `backend/src/stock_analysis/analysis/forecast/`, `backend/src/stock_analysis/analysis/backtest/`, 모델·정책 구성, `backend/tests/`, `fixtures/`.
- 금지 범위: 임의 모델·기간·대형주 정책 선택, 미래 데이터 보정, 목표 비중 계산, 웹 UX.
- 의존성: `WP-002`, `OPEN-IMPL-001`, `OPEN-IMPL-002`, `OPEN-IMPL-003`, `OPEN-IMPL-004`.
- 구현 개요: pandas/NumPy 데이터 처리와 scikit-learn 파이프라인을 사용하되 모델·기간은 열린 결정 후 고정한다. 정책 버전별 적격 판정, 예측값, 불확실성, 기간, 백테스트 성능과 실패 원인을 계약으로 반환한다.
- 테스트 및 수용 증거: `uv run --project backend pytest backend/tests -k "eligibility or forecast or backtest or leakage"`로 적격·부적격·누락 표본, 결정적 예측, 입력 부족 실패, 시간 경계 분할과 미래 데이터 누수 0건을 증빙한다.
- 마이그레이션/호환성: 모델·정책·백테스트 버전은 불변 참조로 기록하며 교체 시 새 버전과 회귀 fixture를 요구한다.
- 롤백 경계: `backend/src/stock_analysis/analysis/`와 해당 버전 구성을 함께 제거하고 `WP-002` 기능은 유지한다.
- 상태: `PROPOSED_BLOCKED_BY_OPEN_DECISION`

### WP-004: 목표 포트폴리오, 리밸런싱과 위험 계산

- 목표: 현재 구성과 적격 후보 분석에 사람 정의 정책을 적용해 목표 비중, 차이, 후보 행동, 위험·집중도 또는 정책 충돌을 산출한다.
- 요구사항 및 아키텍처 추적: `FR-025`, `NFR-012`, `AC-032`, `AC-034`; `CMP-002`, `CMP-004`, `CMP-005`, `INT-004`, `DEC-001`, `ADR-001`.
- 허용 경로/컴포넌트: `backend/src/stock_analysis/portfolio/target/`, `backend/src/stock_analysis/policy/`, `backend/src/stock_analysis/risk/`, 대응 `backend/tests/`와 `fixtures/`.
- 금지 범위: 정책 자동 완화, 부적격 종목 포함, 주문 생성·체결, 개인 맞춤 자문 문구.
- 의존성: `WP-003`, `OPEN-IMPL-005`, `OPEN-IMPL-006`, `OPEN-IMPL-007`.
- 구현 개요: pandas/NumPy 계산으로 버전 고정 정책의 목표 합계와 제약을 검증한다. 해가 없으면 목표안을 만들지 않고 충돌 정책을 식별하며 현재 대비 차이·후보 행동·위험·집중도를 반환한다.
- 테스트 및 수용 증거: `uv run --project backend pytest backend/tests -k "target or policy or risk or rebalance"`로 합계 100%, 적격 후보 한정, 반올림 경계, 제약 충족·충돌·해 없음과 후보 포함 정책 양쪽 사례를 검증한다.
- 마이그레이션/호환성: 정책 변경은 새 버전으로 추가하고 기존 실행 참조를 덮어쓰지 않는다.
- 롤백 경계: `backend/src/stock_analysis/portfolio/target/`, `backend/src/stock_analysis/policy/`, `backend/src/stock_analysis/risk/`와 정책 버전만 되돌리며 앞선 기능은 유지한다.
- 상태: `PROPOSED_BLOCKED_BY_OPEN_DECISION`

### WP-005: 실행 조정과 증거 저장

- 목표: 단계형 실행, 불변 스냅샷, 완전성 검사와 실패 복구 계약을 구현해 결과의 최소 재현성을 제공한다.
- 요구사항 및 아키텍처 추적: `NFR-014`, `NFR-015`, `AC-036`, `AC-037`; `CMP-002`, `CMP-006`, `INT-006`, `DATA-001`~`DATA-005`, `DEC-003`, `ADR-003`, `RISK-003`.
- 허용 경로/컴포넌트: `backend/src/stock_analysis/orchestration/`, `backend/src/stock_analysis/evidence/`, 개발 저장 어댑터, `backend/tests/`와 `fixtures/`.
- 금지 범위: 장기 보존·백업·재해복구, 중단 지점 계산 재개, 운영 관찰성, 기존 결과 덮어쓰기.
- 의존성: `WP-001`, `WP-002`; 종단 간 완료 시험은 `WP-003`, `WP-004`; `OPEN-IMPL-007`, `OPEN-IMPL-011`.
- 구현 개요: FastAPI 서비스 경계 아래에서 실행 ID별 입력·기준 시점·버전·단계 상태·결과를 기록하고 완전성 검사 후에만 완료시킨다. 동일 쓰기는 멱등 성공, 충돌 및 종결 상태 변경은 거절한다.
- 테스트 및 수용 증거: `uv run --project backend pytest backend/tests -k "orchestration or evidence or idempotent or recovery"`로 정상 전이, 중단·저장 실패·필드 누락·부분 기록, 멱등 반복, 충돌 쓰기와 재현 수치 비교를 검증한다.
- 마이그레이션/호환성: 저장 형식 버전, 최소 보존 범위와 정리 절차는 `OPEN-IMPL-011`에서 확정한다.
- 롤백 경계: `backend/src/stock_analysis/orchestration/`, `backend/src/stock_analysis/evidence/`와 합성 개발 기록만 정리하며 기준선과 fixture 원본은 보존한다.
- 상태: `PROPOSED_BLOCKED_BY_OPEN_DECISION`

### WP-006: 웹 결과 UX와 종단 간 개발 검증

- 목표: 표현 모델만 사용해 입력·실행·현재/목표 구성·예측·백테스트·위험·근거·상태를 접근 가능한 반응형 웹 UX로 제공한다.
- 요구사항 및 아키텍처 추적: `FR-026`, `NFR-015`, `NFR-016`, `AC-033`, `AC-037`, `AC-038`; `CMP-001`, `CMP-002`, `CMP-006`, `INT-001`, `INT-005`, `DEC-004`, `ADR-004`.
- 허용 경로/컴포넌트: FastAPI 표현 API·조립기, `frontend/src/`, `frontend/tests/`, `frontend/e2e/`, Vitest/RTL/Playwright/axe 구성과 로컬 개발 실행 구성.
- 금지 범위: 프론트엔드의 분석 모듈 직접 호출·재계산, 외부 공개 배포, 인증·권한, 상품 고지·거래 기능.
- 의존성: `WP-001`, `WP-005`; 전체 정상 흐름은 `WP-002`~`WP-004`; `OPEN-IMPL-008`, `OPEN-IMPL-009`.
- 구현 개요: React/TypeScript가 FastAPI의 버전 고정 표현 모델만 조회한다. 상태는 텍스트·원인·영향 영역으로 구분하고 키보드 탐색, 의미적 순서와 유동 레이아웃을 적용한다.
- 테스트 및 수용 증거: `npm --prefix frontend run test:unit`으로 Vitest/RTL 컴포넌트 상태와 키보드 흐름을 검증하고, `npm --prefix frontend run test:e2e`로 Playwright 종단 간 흐름을 검증하며, `npm --prefix frontend run test:a11y`로 `@axe-core/playwright` 위반 0건을 확인한다. 결정된 화면 크기에서 겹침·잘림·색상 단독 표현 0건의 증거를 남긴다.
- 마이그레이션/호환성: 표현 모델 필수 버전을 확인하고 미지원 버전은 명시적으로 거절한다. 개발 실행은 로컬/격리 환경으로 제한한다.
- 롤백 경계: `frontend/`의 웹 구현과 FastAPI 표현 API·로컬 실행 구성만 제거하며 분석 및 실행 증거 모듈은 유지한다.
- 상태: `PROPOSED_BLOCKED_BY_OPEN_DECISION`

## 4. 의존성 그래프

```text
WP-001
WP-001 + OPEN-IMPL-001/003/007/008 -> WP-002
WP-002 + OPEN-IMPL-001/002/003/004 -> WP-003
WP-003 + OPEN-IMPL-005/006/007 -> WP-004
WP-001 + WP-002 + OPEN-IMPL-007/011 -> WP-005
WP-001 + WP-005 + OPEN-IMPL-008/009 -> WP-006
WP-003 + WP-004 -> WP-005 종단 간 완료 시험
WP-002 + WP-003 + WP-004 + WP-005 -> WP-006 종단 간 정상 흐름
```

권장 순서는 `WP-001` → `WP-002` → `WP-003` → `WP-004` → `WP-005` → `WP-006`이다. `OPEN-IMPL-010`은 더 이상 의존성 차단 요소가 아니다.

## 5. 마이그레이션과 호환성

| ID | 대상 | 계획 | 진입/완료 조건 |
|---|---|---|---|
| `MIG-001` | Python·TypeScript 내부/표현 계약 | Pydantic과 TypeScript 소비 계약의 최초 버전을 명시하고 상태·오류·시간 의미를 고정한다. | `DEC-20260715-004` 반영 / `WP-001`, `TEST-001` 통과 |
| `MIG-002` | 공급자 데이터 | 외부 원본은 Python 어댑터에서만 pandas 입력으로 변환하고 내부 fixture를 공급자와 독립시킨다. | `OPEN-IMPL-003` 결정 / `WP-002` 시험 통과 |
| `MIG-003` | scikit-learn 모델·정책 자산 | 변경 시 새 버전을 추가하며 기존 실행 참조를 덮어쓰지 않는다. | `OPEN-IMPL-002/004/005/006/007` 결정 / 회귀 시험 통과 |
| `MIG-004` | 실행 증거 형식 | 최초 저장 형식과 읽기 정책을 명시하고 개발 데이터 폐기 또는 fixture 변환 절차를 둔다. | `OPEN-IMPL-011` 결정 / 부분 기록·호환 시험 통과 |

운영 데이터 마이그레이션은 없다. Python과 Node 의존성 변경은 각 잠금 파일을 함께 갱신하고 해당 패킷 검증을 재실행한다.

## 6. 검증 매트릭스

| 테스트 ID | 작업 패킷 | 기준선 추적 | 방법과 명령 | 기대 증거 |
|---|---|---|---|---|
| `TEST-001` | `WP-001` | `DEC-001`~`DEC-004`, `INT-001`~`INT-006` | `uv run --project backend pytest backend/tests/contracts`; `npm --prefix frontend run test:unit` | 계약 왕복, 미지원 버전/전이 거절, 잠금 재현 |
| `TEST-002` | `WP-002` | `FR-022`, `AC-029` | `uv run --project backend pytest backend/tests -k "input or current"` | 가치·비중·총가치 기준값과 필드별 오류 |
| `TEST-003` | `WP-002`, `WP-004` | `NFR-012`, `QA-012`, `AC-034` | `uv run --project backend pytest backend/tests -k "precision or weights"` | 결정된 허용오차 내 일치와 합계 100% |
| `TEST-004` | `WP-002`, `WP-003` | `NFR-013`, `QA-013`, `AC-035` | `uv run --project backend pytest backend/tests -k leakage` | 기준 시점 이후 데이터 사용 위반 0건 |
| `TEST-005` | `WP-003` | `FR-023`, `AC-030` | `uv run --project backend pytest backend/tests -k eligibility` | 판정, 근거, 정책 버전과 제외 결과 |
| `TEST-006` | `WP-003` | `FR-024`, `AC-031` | `uv run --project backend pytest backend/tests -k "forecast or backtest"` | 정상 산출 필드와 입력 부족 실패 원인 |
| `TEST-007` | `WP-004` | `FR-025`, `AC-032` | `uv run --project backend pytest backend/tests -k "target or policy or risk or rebalance"` | 적격 후보 한정 목표안 또는 충돌 식별 |
| `TEST-008` | `WP-005` | `NFR-014`, `QA-014`, `AC-036`, `ADR-003` | `uv run --project backend pytest backend/tests -k "orchestration or evidence or idempotent or recovery"` | 메타데이터 누락 0건, 완료 오인 0건 |
| `TEST-009` | `WP-002`, `WP-005`, `WP-006` | `NFR-015`, `QA-015`, `AC-037` | `uv run --project backend pytest backend/tests -k status`; `npm --prefix frontend run test:unit` | 상태·기준 시각·원인·영향 영역 일치 |
| `TEST-010` | `WP-006` | `FR-026`, `AC-033` | `npm --prefix frontend run test:unit` | 현재·목표·분석 근거와 비정상 상태 구분 |
| `TEST-011` | `WP-006` | `NFR-016`, `QA-016`, `AC-038` | `npm --prefix frontend run test:a11y`; `npm --prefix frontend run test:e2e` | 접근성 위반, 겹침, 잘림, 색상 단독 표현 0건 |
| `TEST-012` | `WP-002`~`WP-006` | `FR-022`~`FR-026` | FastAPI와 Vite 로컬 실행 후 `npm --prefix frontend run test:e2e` | 실행 ID에 요청부터 표현 모델까지 연결된 재현 증거 |

모든 검증은 저장소 루트에서 실행한다. 잠금 설치는 `uv sync --project backend --locked`와 `npm --prefix frontend ci`, 전체 백엔드 회귀는 `uv run --project backend pytest backend/tests`, 프론트엔드 단위 회귀는 `npm --prefix frontend run test:unit`, 접근성 회귀는 `npm --prefix frontend run test:a11y`, 브라우저 회귀는 `npm --prefix frontend run test:e2e`, 전체 프론트엔드 회귀는 `npm --prefix frontend test`로 실행한다. `OPEN-IMPL-001`~`009`, `011`이 필요한 시험은 임의 기본값으로 합격 처리하지 않는다.

## 7. 롤아웃과 롤백

| ID | 단계 | 롤아웃 조건 | 롤백 조건과 조치 |
|---|---|---|---|
| `ROLL-001` | Python/FastAPI·React/Vite 골격 | `WP-001`, `TEST-001` 통과와 잠금 파일 재현 | 계약 불일치 시 생성한 `backend/`, `frontend/`, `fixtures/` 골격만 제거 |
| `ROLL-002` | pandas/scikit-learn 현재 구성·분석 | `WP-002`~`WP-004`, `TEST-002`~`007` 통과 | 실패 패킷의 Python 모듈·구성 버전만 되돌림 |
| `ROLL-003` | 실행 증거와 FastAPI 표현 모델 | `WP-005`, `TEST-008`~`009` 통과 | 합성 기록 정리 후 저장·조정·표현 어댑터 롤백 |
| `ROLL-004` | React 로컬 웹 UX 통합 | `WP-006`, `TEST-010`~`012` 통과 | 웹 구현과 로컬 실행 구성만 롤백하고 내부 모듈 유지 |

각 단계는 로컬 또는 격리 환경에서만 활성화한다. 롤백 시 해당 패킷에서 바뀐 `uv.lock` 또는 프론트엔드 잠금 파일도 같은 경계로 되돌리고 이전 단계 회귀 시험을 재실행한다.

## 8. 위험과 미결정 사항

| ID | 보존된 질문/결정 | 원본 추적 | 차단 대상 | 상태/결정 시점 |
|---|---|---|---|---|
| `OPEN-IMPL-001` | 대상 시장, 종목 유니버스와 식별·거래일 규칙 | `OPEN-001`, `OPEN-ARCH-001` | `WP-002`, `WP-003` | 미결정; 해당 패킷 승인 전 |
| `OPEN-IMPL-002` | 예측 대상과 예측 기간 | `OPEN-002`, `OPEN-ARCH-002` | `WP-003` | 미결정; 모델 승인 전 |
| `OPEN-IMPL-003` | 개발 시장·재무 데이터 출처, 접근 방식과 재시도 | `OPEN-003`, `OPEN-ARCH-003` | `WP-002`, `WP-003` | 미결정; 어댑터 승인 전 |
| `OPEN-IMPL-004` | 대형주 판정 규칙과 정책 버전 | `OPEN-004`, `OPEN-ARCH-004` | `WP-003` | 미결정; 적격성 승인 전 |
| `OPEN-IMPL-005` | 위험·회전율·분산·현금·종목 비중 제약 | `OPEN-005`, `OPEN-ARCH-005` | `WP-004` | 미결정; 목표안 승인 전 |
| `OPEN-IMPL-006` | 기존 보유 외 적격 대형주 추가 허용 여부 | `OPEN-006`, `OPEN-ARCH-006` | `WP-004` | 미결정; 후보 경계 승인 전 |
| `OPEN-IMPL-007` | 반올림 방식과 계산 허용오차 | `OPEN-007`, `OPEN-ARCH-007` | `WP-002`, `WP-004`, `WP-005` | 미결정; 계산 승인 전 |
| `OPEN-IMPL-008` | 데이터 종류별 최신성 기준 | `OPEN-008`, `OPEN-ARCH-008` | `WP-002`, `WP-006` | 미결정; 상태 판정 승인 전 |
| `OPEN-IMPL-009` | 개발 검증 지원 화면 크기 범위 | `OPEN-009`, `OPEN-ARCH-009` | `WP-006` | 미결정; 웹 승인 전 |
| `OPEN-IMPL-010` | 언어, 프레임워크, 테스트 러너, 프로젝트 경로 | `CON-007`, `DEC-20260715-004` | 없음 | `RESOLVED`: Python/FastAPI 및 React/Vite 스택과 경로 반영 |
| `OPEN-IMPL-011` | 개발 실행 증거 저장 기술, 최소 보존 범위와 정리 절차 | `RISK-003`, `DATA-005`, `ADR-003` | `WP-005` | 미결정; 저장 승인 전 |

- `RISK-001`: 공급자의 이용 가능 시각 품질은 `OPEN-IMPL-003`과 `TEST-004`로 완화한다.
- `RISK-002`: 예측·최적화 비용과 설명 계약은 `OPEN-IMPL-002`, `004`~`006` 결정 전 구현을 금지해 완화한다.
- `RISK-003`: 저장 기술 미정은 `OPEN-IMPL-011`과 `TEST-008`로 다룬다.
- `RISK-004`: 실제 개인 데이터는 사용하지 않고 합성·최소 데이터만 허용한다.
- `RISK-005`: Python과 TypeScript 간 계약 드리프트는 Pydantic 표현 계약 fixture를 Vitest/Playwright에서 재사용하고 `TEST-001`, `TEST-009`, `TEST-012`로 검출한다.

## 9. 추적성 매트릭스

| 기준선 항목 | 작업 패킷 | 검증 |
|---|---|---|
| `FR-022`, `AC-029` | `WP-002` | `TEST-002`, `TEST-012` |
| `FR-023`, `AC-030` | `WP-003` | `TEST-005`, `TEST-012` |
| `FR-024`, `AC-031` | `WP-003` | `TEST-004`, `TEST-006`, `TEST-012` |
| `FR-025`, `AC-032` | `WP-004` | `TEST-003`, `TEST-007`, `TEST-012` |
| `FR-026`, `AC-033` | `WP-006` | `TEST-010`, `TEST-012` |
| `NFR-012`, `QA-012`, `AC-034` | `WP-002`, `WP-004` | `TEST-003` |
| `NFR-013`, `QA-013`, `AC-035` | `WP-002`, `WP-003` | `TEST-004` |
| `NFR-014`, `QA-014`, `AC-036` | `WP-001`, `WP-005` | `TEST-001`, `TEST-008` |
| `NFR-015`, `QA-015`, `AC-037` | `WP-002`, `WP-005`, `WP-006` | `TEST-009` |
| `NFR-016`, `QA-016`, `AC-038` | `WP-006` | `TEST-011` |
| `DEC-001`, `ADR-001` | `WP-001`, `WP-003`~`WP-005` | `TEST-001`, `TEST-006`~`TEST-008` |
| `DEC-002`, `ADR-002` | `WP-001`~`WP-003` | `TEST-001`, `TEST-004`~`TEST-006` |
| `DEC-003`, `ADR-003`, `DATA-005` | `WP-001`, `WP-005` | `TEST-001`, `TEST-008` |
| `DEC-004`, `ADR-004`, `INT-005` | `WP-001`, `WP-006` | `TEST-001`, `TEST-009`~`TEST-011` |
| `DEC-20260715-004`, `OPEN-IMPL-010` | `WP-001`~`WP-006` | `TEST-001`~`TEST-012` |

활성 기능 요구사항 5개, 비기능 요구사항 5개, 품질 시나리오 5개와 아키텍처 결정 4개가 모두 작업 패킷과 검증에 연결된다. `OPEN-001`~`009` 및 `OPEN-ARCH-001`~`009`는 `OPEN-IMPL-001`~`009`로 1:1 보존했다.

## 10. 개정 요약

| 입력 | 반영 내용 | 상태 |
|---|---|---|
| 구현 계획 기준선 `v001` | 안정 ID `WP-001`~`006`, `TEST-001`~`012`, `MIG-001`~`004`, `ROLL-001`~`004`와 패킷 순서를 보존했다. | 보존 |
| `DEC-20260715-004` | Python 3.12/FastAPI/Pydantic, pandas/NumPy/scikit-learn, React/TypeScript/Vite, uv와 승인된 테스트 도구·실제 경로를 반영했다. | 반영 완료 |
| `OPEN-IMPL-010` | 모든 패킷의 스택·경로 차단을 제거하고 `WP-001`을 승인 가능한 상태로 변경했다. | `RESOLVED` |
| `OPEN-IMPL-001`~`009`, `011` | 기존 질문과 패킷 차단 시점을 임의 해결 없이 유지했다. | 미결정 유지 |
| `PLAN-FIND-001` | Python 명령에 `--project backend`를 지정하고 npm 잠금 설치 및 `test:unit`·`test:e2e`·`test:a11y` 고정 스크립트를 저장소 루트 기준으로 명시했다. | 수정 완료 |
| `PLAN-FIND-002` | `WP-002`를 포함한 모든 패킷의 축약 모듈 경로를 `backend/src/stock_analysis/...` 전체 경로로 교정했다. | 수정 완료 |

이 계획은 독립 AI 검토를 받을 준비가 되었다. 권고 상태는 `READY_FOR_AI_REVIEW`이며 구현 또는 기준선 승인을 의미하지 않는다.

이 문서는 `APR-20260715-013`에 의해 구현 계획 v003 기준선으로 사람 승인되었다. 이 승인은 작업 패킷 실행, 코드 변경, 병합, 배포 또는 릴리스를 승인하지 않는다.
