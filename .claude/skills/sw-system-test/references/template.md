# 검증 산출물 양식 (TPL-SWE6-001/002)

조직 실제 템플릿: `WP_Templates/Engineering/SoftwareVerification/`. 다른 양식이나 시트 구조를 임의로 만들지 말고 이 구조를 그대로 따르세요.

## TPL-SWE6-001 — SW 검증 명세서

- 적용 프로세스: `SWE.6` / 실제 산출물 명명 규칙: `ENG-SWE6-001_SW 검증 명세서`
- 3개 시트, 각각 하나의 표를 가집니다.

### 시트 1 — `TPLSWE6001VerificationSpecificationTable`

| 열 | 내용 | 채우는 근거 |
|---|---|---|
| Test ID | 검증 케이스 고유 식별자 | 채번 규칙(예: `SYS-FUNC-001`, 비기능은 `SYS-NFR-<품질특성>-001`처럼 접두사로 구분) |
| SW Req | 이 케이스가 검증하는 SW 요구사항 ID | `requirements-analysis` 스킬 산출물의 요구사항 ID를 그대로 인용 — 새로 지어내지 않음 |
| Level/Environment | 시험 수준/환경 식별자 | 시트 2(환경 표)의 `Environment ID`를 참조 |
| Stimulus | 시험 입력/절차 | 요구사항 문장에서 도출한 구체적 입력값·조작 순서 |
| Expected Result | 기대 결과 | 요구사항에 명시된 판정기준. 요구사항 원문에 없거나 더 엄격한 기준을 사용자 확인으로 확정했다면, 이 셀에 `[확정: YYYY-MM-DD, 승인: <역할/성명>]`을 덧붙여 근거를 남긴다(별도 "확정 근거" 열은 이 템플릿에 없음 — 임의로 열을 추가하지 않는다) |
| Technique | 적용한 시험 설계 기법 | `references/technique-selection.md`의 기법명 |
| Execution | 자동/수동 등 실행 방법 | 실제 실행 방식 |

### 시트 2 — `TPLSWE6001EnvironmentTable` (검증 환경)

| 열 | 내용 |
|---|---|
| Environment ID | 환경 식별자(시트 1의 Level/Environment에서 참조) |
| 구성 | 실행 환경 구성(SW-only PC/SIL, HIL, 실차 등) |
| 버전 및 식별 | 소프트웨어/도구 버전 |
| 사용 범위 | 이 환경으로 검증하는 범위 |
| 수집 증거 | 로그/스크린샷/리포트 등 증거 종류 |
| 명시적 제외 | 이 환경으로 확인하지 못하는 범위(예: 실차 동역학, 실제 ECU 타이밍) — SW-only 검증이면 반드시 기재 |

### 시트 3 — 변경이력 (`TPLSWE6001ChangeHistoryTable`)

다른 템플릿과 동일한 관례(Revision/변경일/작성 역할/변경 내용/검토 상태/승인 상태).

## TPL-SWE6-002 — SW 검증 결과서

- 적용 프로세스: `SWE.6` / 실제 산출물 명명 규칙: `ENG-SWE6-002_SW 검증 결과서`

### 시트 1 — `TPLSWE6002VerificationResultsTable`

| 열 | 내용 |
|---|---|
| Test ID | TPL-SWE6-001의 Test ID와 동일 |
| SW Req | TPL-SWE6-001의 SW Req와 동일 |
| Result | 판정(Pass/Fail/Blocked 등) |
| Actual Result | 실제 관측 결과 |
| Evidence Locator | 증거 위치 |
| Execution Time | 실행 시각 |
| Scope Note | 적용 범위/제한 사항 — 이 케이스가 환경의 "명시적 제외" 범위와 관련된 제한을 안고 있다면 여기에 기록 |

### 시트 2 — `TPLSWE6002SummaryTable` (집계)

| 열 | 내용 |
|---|---|
| 항목 | 집계 대상 범위(전체/기능/비기능/품질특성별 등) |
| 계획 | 계획된 케이스 수 |
| 실행 | 실행된 케이스 수 |
| Pass / Fail / 미실행 | 각 상태별 건수 |
| 판정 | 이 범위의 종합 판정 |
| 제한 | 종합 판정에 영향을 준 제한 사항 |

### 시트 3 — 변경이력

시트 구조는 TPL-SWE6-001과 동일한 관례를 따릅니다.

## 기능/비기능 요구사항 구분 — 별도 시트가 아니라 ID 접두사로

이 템플릿에는 "기능 테스트 케이스"/"비기능 테스트 케이스"를 나누는 별도 시트가 없습니다 — 모든 케이스는 `VerificationSpecificationTable` 한 표에 함께 작성합니다. 기능/비기능을 구분하고 싶다면:

- **Test ID 접두사**로 구분합니다(예: 기능 `SYS-FUNC-NNN`, 비기능 `SYS-NFR-<ISO25000 품질특성 약어>-NNN`).
- 비기능 케이스의 `SW Req` 열에는 그 요구사항이 속한 ISO 25000 품질특성을 괄호로 덧붙입니다(예: `NFR-014(신뢰성)`).

임의로 시트를 추가해 템플릿 구조를 바꾸지 마세요.
