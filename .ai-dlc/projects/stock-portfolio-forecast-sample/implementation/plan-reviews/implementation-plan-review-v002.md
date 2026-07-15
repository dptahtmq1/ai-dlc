# 구현 계획 독립 검토 보고서

## 메타데이터와 루프 승인

| 필드 | 값 |
|---|---|
| 프로젝트 ID | `stock-portfolio-forecast-sample` |
| 검토 대상 | `.ai-dlc/projects/stock-portfolio-forecast-sample/implementation/plans/implementation-plan-v002.md` |
| 대상 버전/상태 | `v002` / `PROPOSED` |
| AI 루프 승인 | `APR-20260715-012` |
| 작성 호출 | `INV-PLAN-AUTHOR-20260715-002` |
| 검토 호출 | `INV-PLAN-REVIEWER-20260715-002` |
| 독립성 | 작성 호출과 검토 호출이 서로 다름 |
| 요구사항 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/requirements/baseline.md` (`v006`, `HUMAN_APPROVED`) |
| 아키텍처 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/architecture/baseline.md` (`v002`, `HUMAN_APPROVED`) |
| 이전 구현 계획 기준선 | `.ai-dlc/projects/stock-portfolio-forecast-sample/implementation/baseline.md` (`v001`, `HUMAN_APPROVED`) |
| 반영 대상 사람 결정 | `DEC-20260715-004` |

## 권고

`REWORK_REQUIRED`

이 AI 검토는 권고일 뿐이며, 루프 라우팅과 사람 승인은 오케스트레이터가 통제한다.

## 기준선 및 작업 패킷 범위

- 활성 기능 요구사항 `FR-022`~`FR-026`, 비기능 요구사항 `NFR-012`~`NFR-016`, 품질 시나리오와 수용 기준은 `WP-001`~`WP-006` 및 `TEST-001`~`TEST-012`에 연결되어 있다.
- `DEC-20260715-004`의 Python 3.12/FastAPI/Pydantic, pandas/NumPy/scikit-learn, React/TypeScript/Vite, `uv`, pytest, Vitest/RTL, Playwright/axe 및 승인 경로가 계획 전반에 반영되어 있다.
- `OPEN-IMPL-010`은 해결 상태로 표시되고 더 이상 패킷 차단 조건으로 사용되지 않는다.
- `OPEN-IMPL-001`~`009`, `011`은 임의 기본값 없이 보존되고 해당 패킷의 승인 전 차단 조건으로 연결되어 있다.
- 각 패킷은 목표, 허용·금지 범위, 의존성, 수용 증거, 호환성 및 롤백 경계를 갖는다.

## 발견사항

### PLAN-FIND-001: 검증 명령의 프로젝트 작업 디렉터리가 지정되지 않음

- 심각도: `High`
- 신뢰도: `High`
- 차단 여부: `예`
- 영향받는 패킷/기준선 ID: `WP-001`~`WP-006`, `TEST-001`~`TEST-012`, `FR-022`~`FR-026`, `NFR-012`~`NFR-016`, `DEC-20260715-004`
- 증거: 계획은 Python 프로젝트 파일을 `backend/pyproject.toml`에, 프론트엔드 매니페스트와 구성을 `frontend/`에 두도록 확정한다. 그러나 검증 매트릭스와 패킷 수용 증거는 저장소 루트 기준으로 `uv run pytest backend/tests/...` 및 `npx playwright test`를 제시하고, Vitest는 실행 가능한 정확한 명령을 명시하지 않는다. 이 상태에서는 `uv`가 `backend/pyproject.toml`을 자동 선택한다는 계약이 없고, 루트의 `npx`가 `frontend/`의 잠금 의존성과 Playwright 구성을 사용한다는 보장도 없다.
- 위반 규칙: 모든 작업 패킷은 독립적으로 승인·검증할 수 있어야 하며 완료 증거는 재현 가능한 명령과 실행 경계를 가져야 한다.
- 영향: 구현자가 임의로 작업 디렉터리나 도구 옵션을 추론해야 한다. 다른 디렉터리 또는 전역/임시 패키지를 사용한 시험이 통과해도 승인된 잠금 파일과 구성으로 검증됐다고 입증할 수 없어 패킷 완료 판정이 불가능하다.
- 권고: 모든 Python 명령을 `uv run --project backend pytest ...` 형태로 저장소 루트에서 실행 가능하게 고정하거나 명시적으로 `backend/` 작업 디렉터리에서 실행하도록 정한다. 프론트엔드도 선택된 패키지 관리자의 잠금 설치 명령과 `frontend/` 작업 디렉터리를 명시하고, Vitest·Playwright·axe 명령을 매니페스트의 고정 스크립트로 호출하도록 `WP-001`, 각 패킷 수용 증거, 검증 매트릭스 및 전체 회귀 문구를 일관되게 수정한다.
- 필요한 결정 책임자: `implementation-planner` (승인된 기술 선택 범위 안의 교정이며 추가 사람 기술 결정은 불필요)

### PLAN-FIND-002: 일부 허용 경로가 확정된 백엔드 루트에 대해 모호함

- 심각도: `Medium`
- 신뢰도: `High`
- 차단 여부: `아니요`
- 영향받는 패킷/기준선 ID: `WP-002`, `DEC-20260715-004`, `CMP-001`~`CMP-003`, `CMP-005`
- 증거: 범위 절은 백엔드 소스 경로를 `backend/src/stock_analysis/`로 확정하지만, `WP-002`의 허용 경로는 첫 항목만 전체 경로로 쓰고 뒤의 `data/`, `portfolio/current/`를 상대 경로로 적는다.
- 위반 규칙: 작업 패킷의 허용 범위는 독립 승인 시 수정 가능한 파일·컴포넌트 경계를 명확히 해야 한다.
- 영향: `data/`와 `portfolio/current/`가 저장소 루트인지 `backend/src/stock_analysis/` 아래인지 다르게 해석될 수 있어 실행 승인 범위가 불명확하다.
- 권고: `WP-002` 허용 경로를 `backend/src/stock_analysis/data/`, `backend/src/stock_analysis/portfolio/current/`처럼 전체 경로로 명시하고, 다른 패킷의 축약 경로도 같은 방식으로 점검한다.
- 필요한 결정 책임자: `implementation-planner`

## 의존성과 순서 평가

- 권장 순서 `WP-001` → `WP-002` → `WP-003` → `WP-004` → `WP-005` → `WP-006`은 기준선의 데이터·분석·포트폴리오·증거·표현 흐름과 부합한다.
- `WP-005`의 부분 구현과 종단 간 완료 시험 의존성을 구분했고, `WP-006`도 골격·표현 작업과 전체 정상 흐름 의존성을 구분했다.
- 미결정 항목의 차단 위치는 원본 요구사항·아키텍처 질문과 일치한다.

## 검증, 마이그레이션, 롤아웃 및 롤백 평가

- 기능·비기능 기준선의 시험 유형과 기대 증거는 충분히 연결되어 있다.
- 모델·정책·계약·증거 형식의 버전 호환성과 이전 실행 불변성, 개발 단계에서의 합성 데이터 사용 경계가 유지된다.
- 단계별 롤아웃·롤백 범위는 패킷 경계와 대체로 일치한다.
- 다만 `PLAN-FIND-001` 때문에 현재 명령만으로는 잠금 파일과 프로젝트 구성을 사용한 재현 검증을 보장할 수 없다.

## 사람 미결정 사항

- `OPEN-IMPL-001`~`009`, `011`은 계획에 보존되어 있으며 각 차단 패킷 승인 전에 사람이 결정해야 한다.
- `OPEN-IMPL-010`은 `DEC-20260715-004`로 해결되었고 추가 기술 선택을 요구하지 않는다.
- 이번 발견사항은 승인된 기술 범위 안에서 작성자가 교정할 수 있으므로 별도 사람 에스컬레이션 사유는 아니다.

## 권고되는 다음 조치

루프 용량이 남아 있으므로 `implementation-planner`가 `PLAN-FIND-001`을 필수 교정하고 `PLAN-FIND-002`를 함께 명확히 한 다음, 다음 계획 버전을 별도의 독립 검토자에게 전달한다.
