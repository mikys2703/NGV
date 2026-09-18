# 추적성 — 아키텍처 인터페이스 · 통합순서 · 통합시험 · 결함

조직 추적 매트릭스는 `TPL-TRC-001_양방향 요구사항 추적 매트릭스 템플릿.xlsx`(산출물 ID `ENG-TRC-001`)이며, 열 구성은 다음과 같습니다: **Upper Req | SW Req | Architecture | Detailed Design | Code | SWE.4 | SWE.5 | SWE.6 | Coverage**. 이 스킬은 그중 **SWE.5**, **Coverage** 열을 채우는 책임을 집니다(Architecture 열까지는 `architecture-design`이, Detailed Design/Code/SWE.4 열은 `detailed-design`/`tdd`가 이미 채워둔 것을 이어받습니다).

## 채우는 방법

1. `architecture-design`이 남긴 Architecture 열의 인터페이스 ID(`IF-...`)/컴포넌트 ID를 찾아, 같은 행에 이번 통합시험 케이스 ID(TPL-SWE5-002의 Test ID)를 "SWE.5" 열에 연결합니다.
2. `references/coverage-measurement.md`로 측정한 함수 커버리지·콜 커버리지 결과(퍼센트, 또는 100% 미달 시 케이스/근거 링크)를 "Coverage" 열에 남깁니다 — 이미 SWE.4 단계에서 단위 커버리지가 기록돼 있다면 그것을 덮어쓰지 말고, "SWE.5 통합수준: 함수 NN% / 콜 NN%"처럼 구분해서 이어 씁니다.
3. Architecture 열에 대응하는 행이 없다면(아키텍처 산출물의 추적성이 비어있는 경우), 새로 만들지 말고 먼저 `architecture-design` 산출물의 추적성이 채워져 있는지 확인하세요 — SWE.5는 그 뒤를 잇는 것이지 처음부터 다시 만드는 것이 아닙니다.

## TPL-SWE5-002/003의 Trace 열

- TPL-SWE5-002(통합시험 케이스)의 `Trace` 열에는 이 케이스가 검증하는 **인터페이스 ID**와 **통합 순번**을 함께 적습니다(예: `IF-DOORCTRL-001 / 통합순번 2`). 요구사항까지 직접 연결하고 싶다면 ENG-TRC-001에서 그 인터페이스에 이미 연결된 SW Req ID를 그대로 가져와 덧붙이되, 새로 지어내지 마세요.
- TPL-SWE5-003(통합시험 결과서)의 `Trace` 열은 TPL-SWE5-002의 `Test ID`/`Trace`와 동일한 값을 유지해 케이스↔결과가 항상 짝을 이루게 합니다.
- 결함이 발견되면 TPL-SWE5-003의 `Defect ID`를 채우고, 그 결함이 어느 인터페이스/통합 단계에서 발견됐는지 `Trace` 열로 역추적 가능하게 하세요.

## 규칙

- 한 통합시험 케이스가 여러 인터페이스에 걸쳐 있다면(단계적 통합의 마지막 단계에서 흔함), Trace 열에 관련 인터페이스 ID를 모두 나열하세요 — 하나만 골라 나머지를 누락시키지 마세요.
- 아키텍처가 갱신되어 인터페이스 ID가 바뀌면(재설계), 이 스킬의 산출물에서도 옛 ID를 참조하던 행을 갱신하세요. ID를 재사용하지 말고(architecture-design/detailed-design 스킬과 동일한 규칙), 폐기된 인터페이스에 대한 통합시험 케이스는 결번(폐기 표시)으로 남깁니다.
- "SWE.5" 열이 계속 비어 있는 인터페이스가 있다면, 통합시험 계획이 빠진 것이므로 SKILL.md 10단계(완료 조건) 점검에서 반드시 짚어내세요.
