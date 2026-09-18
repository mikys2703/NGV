# 통합시험 산출물 양식 (TPL-SWE5-001/002/003)

조직 실제 템플릿 3종: `WP_Templates/Engineering/SoftwareComponentVerificationAndIntegrationVerification/`. 다른 양식을 임의로 만들지 말고 이 구조를 그대로 따르세요.

## TPL-SWE5-001 — SW 통합전략 및 통합시험 명세서

- 적용 프로세스: `SWE.5` / 실제 산출물 명명 규칙: `ENG-SWE5-001_SW 통합전략 및 통합시험 명세서`
- 표지·변경이력·작성/검토/승인 상태 관례는 `architecture-design` 스킬의 `references/template.md`와 동일합니다.

| 절 | 작성 안내(원문) | 이 스킬에서 채우는 방법 |
|---|---|---|
| 1. 목적 및 적용범위 (1.1/1.2/1.3) | 문서 필요 이유, 적용 대상, 포함/제외 범위와 경계 | 대상 소프트웨어 항목과 통합 범위를 아키텍처 산출물에서 그대로 인용 |
| 2. 통합 원칙 | 통합 단위, 단계, 위험 우선순위, 반복 가능성, 실패 격리 원칙 | SKILL.md 2단계 |
| 3. 통합 항목과 순서 | 통합 항목 ID, 선행조건, 의존성, 순서, 담당, 계획 베이스라인 | SKILL.md 1·2단계 — `architecture-design`의 `integration-order.md` 산출물(템플릿 11장 표)을 그대로 이어받음 |
| 4. 환경 및 형상 | 도구, 실행환경, 소프트웨어 버전, 시험 데이터, 형상 식별 방법 | SKILL.md 2단계 |
| 5. 진입 및 종료 기준 | 각 통합 단계의 시작·중단·재개·완료 판정 기준 | SKILL.md 2단계 |
| 6. 통합시험 케이스 요약 | 인터페이스와 통합 위험을 다루는 시험 ID, 추적 대상, 기대결과, 자동화 여부 요약 | SKILL.md 4단계, TPL-SWE5-002 요약 인용 |
| 7. 시험 설계기법 | 경계값/동등분할/의사결정표/상태전이 등 기법의 선정 근거와 적용 대상 | SKILL.md 3단계, `iso26262-part6-integration-test.md` |
| 8. 실행 및 결과 기록 규칙 | 실행 식별자, 시각, 환경, 실제결과, 증거 위치, 결함 연결 방법 | SKILL.md 5·7단계 |
| 9. 회귀 전략 | 변경 영향에 따른 회귀 범위 선정, 자동 실행과 결과 비교 방법 | SKILL.md 8단계 |
| 10. 실패 및 편차 처리 | 시험 실패·환경 문제·계획 편차의 분류, 보고, 재시험, 승인 절차 | SKILL.md 5단계 |
| 11. 추적성과 보고 | 아키텍처 인터페이스, 시험 케이스, 실행 결과, 결함, 보고서의 연결 정의 | SKILL.md 9단계, `traceability-integration.md` |
| 12. 적용 한계 | 통합시험으로 확인하지 못하는 시스템/HIL/차량/양산 환경 범위 | 이 프로젝트가 SW-only PC/SIL 검증이라면 그 경계를 명시 |
| 13. 추적성 | 입력 설계·형상, 시험 명세, 결과, 결함 기록의 양방향 추적 | SKILL.md 9단계, `traceability-integration.md` |
| 14. 참고자료 | 아키텍처, 상세설계, 검증 계획, 환경 정의, 사용 도구 자료 식별 | 참조한 ENG-SWE2-001 등 문서 ID 명시 |

## TPL-SWE5-002 — SW 통합시험 케이스 (xlsx)

- 적용 프로세스: `SWE.5` / 실제 산출물 명명 규칙: `ENG-SWE5-002_SW 통합시험 케이스`
- 표 이름 `TPLSWE5002IntegrationCasesTable`, 열 구성:

| 열 | 내용 | 채우는 근거 |
|---|---|---|
| Test ID | 통합시험 케이스 고유 식별자 | 채번 규칙(예: `IT-DOORCTRL-001`) |
| Trace | 이 케이스가 검증하는 인터페이스 ID/통합 순번/요구사항 ID | `traceability-integration.md` |
| Integration Item | 대상 통합 항목(컴포넌트/통합 단위) | 아키텍처 `integration-order.md`의 "컴포넌트/통합 단위" 열 |
| Stimulus | 시험 입력/자극(호출, 이벤트, 데이터) | 인터페이스 계약의 오퍼레이션/시그널·데이터 계약 |
| Expected Result | 기대 결과(정상/오류) | 인터페이스 계약의 사후조건·오류 계약 |
| Technique | 적용한 시험 기법 | `iso26262-part6-integration-test.md`의 기법명 |
| Automation | 자동화 여부(자동/수동) | 실행 방식 |

## TPL-SWE5-003 — SW 통합시험 결과서 (xlsx)

- 적용 프로세스: `SWE.5` / 실제 산출물 명명 규칙: `ENG-SWE5-003_SW 통합시험 결과서`
- 표 이름 `TPLSWE5003IntegrationResultsTable`, 열 구성:

| 열 | 내용 |
|---|---|
| Test ID | TPL-SWE5-002의 Test ID와 동일 |
| Trace | TPL-SWE5-002의 Trace와 동일 |
| Result | 판정(Pass/Fail/Blocked 등) |
| Actual Result | 실제 관측 결과 |
| Evidence Locator | 로그/커버리지 리포트 등 증거 위치 |
| Defect ID | 결함이 있다면 그 식별자 |
| Disposition | 처리 상태(재작업/재시험/수용 등) |

## 관련 산출물과의 경계

- 통합 순서·인터페이스 계약의 **출처**는 `architecture-design` 스킬(TPL-SWE2-001)입니다 — 이 스킬에서 새로 정의하지 마세요.
- 함수/알고리즘 내부 로직 검증(SWE.4, TPL-SWE4-001 계열)은 이 스킬의 범위가 아닙니다. 이 스킬은 컴포넌트 경계(인터페이스)에서의 검증까지입니다.
